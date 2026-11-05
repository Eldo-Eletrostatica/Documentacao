# Autenticação da área interna por cookie HttpOnly, com proxy no Frontend e CSRF ligado ao JWT

No site, a sessão do funcionário fica num cookie que o JavaScript não lê. O `POST /auth/login` do Backend grava o JWT no cookie `session`, com `HttpOnly`, `SameSite=Strict`, `Path=/`, `Max-Age` igual à validade do token (8 horas) e `Secure` em produção. O navegador nunca chama a API diretamente: o Next.js repassa `/api/*` ao Backend, então o cookie é de primeira parte e só vai para a origem do site.

O guard do Backend aceita o token pelo cookie ou pelo header `Authorization: Bearer`. O Bearer continua servindo ao Swagger, aos testes e a qualquer cliente que não seja o navegador. Como o navegador manda o cookie sozinho, uma escrita autenticada por ele precisa de proteção contra CSRF, num double-submit ligado ao JWT:

- no login, o Backend gera um valor aleatório, guarda como claim `csrf` dentro do JWT e manda o mesmo valor num segundo cookie, `csrf_token`, com os mesmos atributos, exceto `HttpOnly`;
- em POST, PATCH, PUT e DELETE autenticados pelo cookie, o Frontend lê o `csrf_token` e manda o valor no header `X-CSRF-Token`, e o guard compara com o claim do JWT. Header ausente ou diferente dá 403;
- leituras (GET), requisições autenticadas por Bearer e rotas públicas não exigem o header.

O `POST /auth/logout` apaga os dois cookies. Não há refresh token: quando o token expira, a API devolve 401 e o Frontend leva o funcionário ao login.

O token num cookie `HttpOnly` não pode ser lido por um script injetado na página (XSS), ao contrário de um token guardado no `localStorage`. O proxy evita os problemas de cookie entre domínios: o front e a API podem ficar em hospedagens diferentes sem `SameSite=None` nem bloqueio de cookie de terceiros. O claim `csrf` mantém o Backend sem estado, porque não existe sessão no banco, e impede que outro site forje o par cookie e header: o valor está dentro do JWT assinado.

## Considered Options

- **Token no `localStorage`, enviado como Bearer:** funcionava sem mudar o Backend e dispensava CSRF, mas qualquer XSS lê o token e usa a sessão de fora do navegador por 8 horas.
- **Token só em memória:** imune a XSS persistente, mas a sessão se perde a cada F5 ou aba nova.
- **Cookie com chamada direta à API por CORS (`credentials: 'include'`):** dispensava o proxy, mas, com o front e a API em domínios diferentes, o cookie precisaria de `SameSite=None` e seria bloqueado por navegadores que barram cookies de terceiros.
- **Só `SameSite=Strict`, sem token anti-CSRF:** cobre a maior parte dos casos, mas não protege contra um subdomínio comprometido nem contra navegadores antigos. O grupo preferiu a defesa em profundidade.
- **Double-submit simples (só comparar cookie e header):** mais simples, mas um subdomínio que consiga gravar cookies planta um par válido. Ligar o valor ao JWT assinado fecha essa brecha.
- **Token anti-CSRF guardado no banco:** exigiria uma tabela de sessões, o que contraria a ADR 0005.
- **Refresh token:** mantém a sessão viva sem novo login, mas exige um segundo token, uma rota de renovação e revogação. As 8 horas cobrem um turno de trabalho.

## Consequences

- O Frontend chama a API sempre pelo `/api` do próprio site, e a URL do Backend fica numa variável só do servidor (`API_URL`), nunca exposta ao navegador. Em desenvolvimento, o container do Frontend alcança o Backend por `host.docker.internal:3001`.
- Toda escrita feita pela área interna manda o header `X-CSRF-Token`. Um cliente HTTP único no Frontend cuida disso, para nenhuma tela esquecer.
- Em produção, o Backend roda com `NODE_ENV=production` para o cookie sair com `Secure`, e o site precisa de HTTPS.
- O Swagger e os testes de integração continuam autenticando pelo Bearer, sem cookie.
- O CORS deixa de ser o caminho do navegador, mas as origens permitidas passam a vir de uma variável de ambiente (`CORS_ORIGINS`), em vez de ficarem fixas no código.

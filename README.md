# WMS legado DSF 01

Back-end didático do desafio de retrofit de rastreabilidade em armazém. O aluno recebe um WMS funcional para investigar e integrar a uma interface própria. Não há front-end neste projeto. Todos os registros e usuários são fictícios.

## Requisitos e execução

Node.js 22 ou superior, npm e MySQL 8.4. Docker Compose é opcional e fornece somente o banco. O servidor HTTP roda no Node local.

1. Execute `npm install`.
2. Copie `.env.example` para `.env` (`Copy-Item .env.example .env` no PowerShell).
3. Escolha uma senha local para `DB_PASSWORD` no `.env`. Para Docker, defina também `$env:MYSQL_ROOT_PASSWORD='uma-senha-local-diferente'` e execute `docker compose up -d`. Aguarde o banco ficar saudável com `docker compose ps`.
4. Se usar MySQL instalado, crie a base `wms_legado` com charset `utf8mb4` e um usuário com permissões nessa base; ajuste `.env`. O script não cria base nem usuário.
5. Execute `npm run db:reset -- --confirmar`. Esse comando apaga e recria as tabelas didáticas na base configurada; confira `DB_NAME` antes de executá-lo. Nunca aponte para uma base real.
6. Execute `npm start`. Consulte `http://127.0.0.1:3000/health`.

Contas exclusivamente demonstrativas: `OP001` / PIN `1234` e `SUP001` / PIN `5678`. PINs ficam com hash e salt no banco; tokens opacos com validade de oito horas ficam armazenados somente como hash. A autenticação é uma simplificação didática, não uma implementação corporativa de identidade.

`npm test` executa os testes de domínio sem banco. `npm run test:integration` exercita a API com MySQL e servidor ativos; use uma base recém-inicializada, pois o teste movimenta e baixa paletes.

## Estrutura

- `src/app.js`: rotas, contrato legado, autorização e transações.
- `src/rules.js`: validações do domínio.
- `src/security.js`: PINs e sessões.
- `database/schema.sql`: estrutura relacional.
- `scripts/reset-db.js`: reinicialização e população fictícia.
- `docs/ALUNO.md`: missão e fluxos do sistema anterior.
- `docs/API.md` e `examples/legado.http`: integração e requisições.
- `professor/GUIA.md`: regras, critérios e roteiro de validação; distribuir separadamente dos materiais do aluno.

## Limites da simulação

A movimentação é sempre de um palete inteiro. Não há faturamento, entrada de estoque, cadastro administrativo, leitura de câmera, geração de imagens QR, fila offline ou adaptador pronto. O QR fornecido como exemplo é texto codificável em uma etiqueta; sua leitura pertence ao projeto do aluno. O aluno também decide como construir o adaptador sem alterar o contrato legado.

HTTP e bind em loopback são destinados ao laboratório local. Para acesso por tablet na rede, o professor precisa configurar proxy HTTPS, endereço de escuta, firewall e CORS adequados. Não há HTTPS, JWT, infraestrutura redundante nem garantia de desempenho implementados nesta base mínima. O sistema utiliza token opaco de sessão; JWT pode ser estudado na camada de adaptação. O relógio de negócio usa a data UTC e as validades da carga são relativas à data de reinicialização.

Capacidade é medida em posições de palete, não unidades de produto. Transações e bloqueios protegem o estado; conflitos concorrentes podem devolver `409`, exigindo reenvio com a mesma chave. O histórico guarda as últimas 100 movimentações na consulta: operador vê as próprias, supervisor vê todas. Não há paginação nem auditoria de tentativas rejeitadas. Dados persistem no volume MySQL; `docker compose down` não apaga o volume. Backup e recuperação corporativos ficam fora do protótipo.

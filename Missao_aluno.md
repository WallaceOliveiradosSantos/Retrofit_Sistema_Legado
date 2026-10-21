# Missão de retrofit do WMS

Você integra a equipe responsável por melhorar o registro de movimentações dos operadores de empilhadeira. O servidor legado já funciona e suas regras devem ser preservadas. Sua entrega será uma interface móvel com leitura de QR Code e uma integração compatível com esse servidor.

## Fluxo anterior descrito

O terminal textual antigo não acompanha este projeto. Seu comportamento operacional está descrito abaixo para orientar a reconstrução dos fluxos.

1. Acesso: operador informa código e PIN e recebe uma sessão.
2. Consulta de palete: digita o código, confere produto, lote, validade, quantidade e endereço.
3. Consulta de posição: digita o endereço e confere os paletes armazenados.
4. Transferência: informa `cod_pal`, comando `T` e `end_dest`; confirma o registro.
5. Baixa: supervisor informa `cod_pal`, comando `B` e um motivo; confirma a retirada integral.
6. Conferência: consulta o histórico de operações permitido para seu perfil.

Os nomes abreviados dos campos são parte do contrato legado. Não substitua o back-end para facilitar o front-end. Você pode criar uma camada de adaptação própria.

## Investigação

Execute o projeto, explore a API e o esquema SQL e desenhe o relacionamento entre operadores, produtos, lotes, paletes, posições e movimentos. Compare as respostas nos diferentes cenários da base. Registre quais validações precisam ser comunicadas ao operador e quais garantias permanecem no servidor.

Texto de exemplo para codificar em QR:

```json
{"versao":1,"palete":"PAL-001","lote":"LOT-001","sku":"BEB-001","quantidade":100}
```

Outro exemplo válido identifica `PAL-003`, `LOT-002`, `ALI-002` e quantidade `50`. O conteúdo da etiqueta pode estar desatualizado: consulte o palete na API antes de confirmar a operação. O servidor é a fonte de verdade; ler o QR não altera estoque.

## Entrega esperada

- Mapa do processo e das regras descobertas, modelo de dados e arquitetura.
- Interface para login, consulta e transferência; baixa disponível conforme perfil.
- Leitura de QR com validação e alternativa de entrada manual.
- Evidências de sucesso, erro de negócio e falha de comunicação.
- Comparação do tempo e dos erros entre uma simulação manual documentada e a interface criada. A referência de redução de 50% deve ser medida, não presumida.

Como evolução, implemente fila local de pendências com confirmação visual e reenvio seguro. Uma leitura desatualizada e uma resposta perdida após confirmação são cenários diferentes. Investigue como o contrato evita repetir uma operação já registrada.

Plano sugerido para 40 horas: 8 horas de investigação, 8 de desenho e contratos, 16 de construção e 8 de testes e apresentação. Não opere dispositivos enquanto conduz uma empilhadeira; a atividade é simulada em laboratório.

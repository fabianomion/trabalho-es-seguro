# Trabalho de Engenharia de Software Seguro - Aplicativo Delivery Go.

## 1. Identificação do Sistema

- **Nome do Sistema:** Delivery Go
- **Integrantes:** Fabiano Mion, Emanuel Irion
- **Repositório:** https://github.com/fabianomion/trabalho-es-seguro.git
- **Justificativa:** Para a escolha do sistema: o sistema foi escolhido por envolver múltiplos perfis de usuários com interesses e níveis de acesso distintos (clientes, restaurantes/lojistas e entregadores), grande volume de dados sensíveis (dados pessoais, localização em tempo real e pagamentos) e diversas operações críticas de segurança (autenticação, transações financeiras, avaliações e comunicação entre as partes). Essa combinação de fatores permite identificar uma ampla variedade de ameaças e casos de abuso realistas, tornando o sistema um bom objeto de estudo para a disciplina.

---

## 8.2 Descrição do sistema

O DeliveryGo é uma plataforma digital (aplicativo móvel e painel web) que conecta três tipos de usuários: **clientes**, que desejam comprar produtos (principalmente refeições) de estabelecimentos parceiros; **restaurantes/lojistas**, que cadastram seus cardápios e recebem pedidos; e **entregadores**, que retiram os pedidos nos estabelecimentos e os entregam aos clientes.

**Problema que o sistema resolve:** facilita a compra e entrega de refeições e outros produtos, eliminando a necessidade de o cliente se deslocar até o estabelecimento e permitindo que pequenos negócios ampliem seu alcance sem precisar manter uma frota própria de entrega.

**Quem utiliza o sistema:**
- Clientes finais (consumidores);
- Estabelecimentos parceiros (restaurantes, mercados, farmácias etc.);
- Entregadores parceiros (motoboys, ciclistas);
- Administradores da plataforma (suporte, financeiro, operações).

**Principais funcionalidades:**
- Cadastro e login de usuários (cliente, restaurante, entregador);
- Busca e navegação por restaurantes e cardápios;
- Criação, acompanhamento e cancelamento de pedidos;
- Pagamento on-line (cartão, PIX, carteira digital) ou na entrega;
- Atribuição de pedidos a entregadores e rastreamento de entrega em tempo real;
- Avaliação de restaurantes, produtos e entregadores;
- Chat/mensagens entre cliente, restaurante e entregador;
- Painel administrativo para gestão de pedidos, disputas e estornos.

**Informações armazenadas ou transmitidas:**
- Dados pessoais (nome, CPF, endereço, telefone, e-mail);
- Credenciais de acesso (senha ou token de autenticação);
- Dados de pagamento (número de cartão tokenizado, histórico de transações);
- Localização em tempo real (cliente e entregador durante a entrega);
- Histórico de pedidos e avaliações;
- Mensagens trocadas entre as partes.

**Recursos que precisam ser protegidos:**
- Contas de usuário (clientes, restaurantes e entregadores);
- Dados pessoais e de localização;
- Transações financeiras e dados de pagamento;
- Integridade dos pedidos (valores, itens, status);
- Disponibilidade do serviço, especialmente em horários de pico;
- Comunicação entre as partes (mensagens e notificações).

---

## 8.3 Usuários, ativos e pontos de interação

### Usuários e perfis de acesso
| Perfil | Descrição | Nível de acesso |
|---|---|---|
| Cliente | Realiza pedidos e pagamentos | Acesso à própria conta, pedidos e pagamentos |
| Restaurante/Lojista | Gerencia cardápio e recebe pedidos | Acesso ao painel do estabelecimento e aos pedidos recebidos |
| Entregador | Retira e entrega pedidos | Acesso a pedidos atribuídos e rota de entrega |
| Administrador | Suporte e operação da plataforma | Acesso amplo a dados de todos os usuários e transações |

### Ativos importantes identificados
- **Dados pessoais e sensíveis:** nome, CPF, endereço, telefone — expõem os usuários a fraude e engenharia social se vazados;
- **Credenciais de acesso:** senhas e tokens de autenticação — ponto crítico contra acesso indevido a contas;
- **Dados de pagamento:** cartões, carteiras digitais, histórico financeiro — alvo de fraude financeira;
- **Localização em tempo real:** posição do cliente e do entregador — risco à segurança física se exposta indevidamente;
- **Pedidos e seus valores:** integridade essencial para evitar prejuízo financeiro;
- **Avaliações:** ativo de reputação, suscetível a manipulação (avaliações falsas);
- **Mensagens entre usuários:** podem conter dados sensíveis ou ser usadas para golpes;
- **Banco de dados central:** armazena todos os dados acima — ativo crítico de maior impacto se comprometido;
- **APIs de integração:** comunicação entre app móvel, backend, gateway de pagamento e serviços de mapas/geolocalização;
- **Servidores e infraestrutura em nuvem:** hospedam o backend e o banco de dados;
- **Aplicativos móveis (cliente e entregador):** pontos de entrada sujeitos a engenharia reversa e adulteração local.

### Pontos de interação
- App do cliente ↔ Backend (API REST/GraphQL);
- App do entregador ↔ Backend (atualização de localização e status de entrega);
- Painel do restaurante ↔ Backend (gestão de cardápio e pedidos);
- Backend ↔ Gateway de pagamento (processamento de transações);
- Backend ↔ Serviço de geolocalização/mapas (cálculo de rotas e distância);
- Backend ↔ Banco de dados (persistência de todas as informações);
- Backend ↔ Serviço de notificações push/SMS/e-mail.

---

## 8.4 Visão geral da arquitetura ou fluxo

Fluxo simplificado, representado em texto:

1. O **cliente** navega pelo app, escolhe um restaurante e monta o pedido.
2. O app do cliente envia o pedido para o **backend**, via API.
3. O backend valida o pedido e aciona o **gateway de pagamento** para processar o pagamento.
4. Após a confirmação do pagamento, o backend notifica o **restaurante**, que prepara o pedido.
5. O backend seleciona um **entregador** disponível e envia a solicitação de coleta.
6. O entregador retira o pedido no restaurante e o sistema atualiza o status para "em rota", compartilhando a localização em tempo real com o cliente.
7. O entregador entrega o pedido ao cliente, que confirma o recebimento e pode avaliar o restaurante e o entregador.
8. Todas as informações (pedido, pagamento, avaliações, mensagens) são persistidas no **banco de dados central**.

## 8.5 Modelagem de ameaças com STRIDE

| ID | Categoria STRIDE | Componente ou ativo | Ameaça identificada | Possível impacto |
|---|---|---|---|---|
| T01 | Spoofing | Conta do cliente | Atacante utiliza credenciais vazadas (ex.: reuso de senha) para acessar a conta de outro cliente | Acesso a dados pessoais, realização de pedidos fraudulentos usando o método de pagamento da vítima |
| T02 | Spoofing | Conta do entregador | Pessoa não autorizada se passa por um entregador cadastrado para retirar pedidos | Roubo de produtos, exposição do cliente a um agente não verificado |
| T03 | Tampering | Valor do pedido | Interceptação e alteração da requisição de pedido para reduzir o valor cobrado antes do pagamento | Prejuízo financeiro ao restaurante e à plataforma |
| T04 | Tampering | Status de entrega/localização | Entregador ou app adulterado envia localização falsa para simular que está no local | Cobrança indevida de taxas, atraso não detectado, perda de confiança do cliente |
| T05 | Repudiation | Confirmação de entrega | Cliente nega ter recebido o pedido mesmo após confirmação no app, solicitando reembolso indevido | Prejuízo financeiro ao restaurante/entregador, disputas sem evidência suficiente |
| T06 | Repudiation | Ações do administrador | Administrador realiza alteração manual em pedidos ou reembolsos sem registro de auditoria | Dificuldade de rastrear fraudes internas ou abuso de poder |
| T07 | Information Disclosure | Banco de dados de usuários | Falha de autorização expõe dados pessoais e histórico de pedidos de outros usuários | Violação de privacidade, exposição a golpes e assédio |
| T08 | Information Disclosure | Localização em tempo real | API de rastreamento exposta permite que terceiros consultem a localização do cliente ou entregador sem autorização | Risco à segurança física dos usuários |
| T09 | Denial of Service | API/Backend | Ataque de sobrecarga (DDoS) ou abuso de requisições em horário de pico (ex.: sexta à noite) | Indisponibilidade do app, perda de pedidos e receita |
| T10 | Denial of Service | Gateway de pagamento | Envio massivo de tentativas de pagamento inválidas sobrecarrega a integração com o gateway | Impossibilidade de concluir pagamentos legítimos |
| T11 | Elevation of Privilege | Painel administrativo | Exploração de falha de autorização permite que um usuário comum acesse funções administrativas | Acesso e manipulação indevida de dados de todos os usuários e transações |
| T12 | Elevation of Privilege | API de restaurante | Um restaurante mal-intencionado explora falha na API para acessar dados de pedidos de outros restaurantes concorrentes | Vazamento de informações comerciais sensíveis (cardápio, preços, volume de vendas) |

Todas as seis categorias do STRIDE puderam ser aplicadas ao sistema, uma vez que ele possui múltiplos usuários, comunicação em rede, transações financeiras e dados sensíveis, o que caracteriza superfícies de ataque relevantes para cada categoria.

---
## 8.6 Casos de abuso

### CA01 — Conta falsa de entregador para roubo de pedidos
**Ator:** usuário mal-intencionado.

**Objetivo:** obter acesso a pedidos pagos para se apropriar dos produtos sem realizar a entrega.

**Condições:** o sistema permite cadastro de entregadores com verificação de identidade insuficiente (ex.: apenas foto de documento, sem validação cruzada).

**Fluxo de abuso:**
1. O atacante cria uma conta de entregador usando documento falso ou de terceiro.
2. O sistema aprova o cadastro sem validação adicional.
3. O atacante aceita pedidos legítimos atribuídos pela plataforma.
4. O atacante retira o pedido no restaurante e não realiza a entrega, desaparecendo com o produto.

**Impacto:** prejuízo financeiro ao cliente e ao restaurante, dano à reputação da plataforma, possível reincidência com múltiplas vítimas.

**Categorias STRIDE relacionadas:** Spoofing, Elevation of Privilege.

---

### CA02 — Manipulação do valor do pedido antes do pagamento
**Ator:** usuário cliente mal-intencionado com conhecimento técnico.

**Objetivo:** pagar valor menor do que o real pelo pedido.

**Condições:** o backend não revalida o valor do pedido no momento do pagamento, confiando no valor enviado pelo cliente.

**Fluxo de abuso:**
1. O atacante monta um pedido normalmente pelo app.
2. Antes de enviar a requisição de pagamento, intercepta e altera o valor total via proxy/ferramenta de interceptação.
3. O backend aceita o valor alterado sem revalidar contra o cadastro de preços do restaurante.
4. O pedido é processado e entregue com valor pago muito abaixo do real.

**Impacto:** prejuízo financeiro direto ao restaurante e à plataforma; se replicado em escala, pode gerar perdas significativas.

**Categorias STRIDE relacionadas:** Tampering, Elevation of Privilege.

---

### CA03 — Avaliações falsas para manipular reputação
**Ator:** restaurante concorrente ou o próprio dono do estabelecimento usando contas falsas.

**Objetivo:** aumentar artificialmente a nota do próprio estabelecimento ou reduzir a de concorrentes.

**Condições:** o sistema permite avaliações sem exigir vínculo comprovado com um pedido real, ou permite múltiplas contas facilmente.

**Fluxo de abuso:**
1. O atacante cria múltiplas contas falsas ou usa contas de terceiros.
2. Realiza pedidos fictícios de baixo valor (ou usa contas sem pedido real, se o sistema permitir).
3. Publica avaliações positivas para o próprio negócio ou negativas para concorrentes.
4. O sistema exibe as avaliações manipuladas normalmente aos demais usuários.

**Impacto:** distorção da confiança dos clientes, prejuízo à concorrência leal, possível perda de clientes por informações falsas.

**Categorias STRIDE relacionadas:** Repudiation, Information Disclosure (uso de dados de terceiros), Spoofing.

---

### CA04 — Exposição da localização do cliente
**Ator:** atacante externo explorando falha de autorização em API.

**Objetivo:** rastrear a localização de um cliente específico durante a entrega.

**Condições:** a API de rastreamento de entrega não verifica corretamente se quem consulta o endpoint tem permissão sobre aquele pedido específico.

**Fluxo de abuso:**
1. O atacante identifica o endpoint da API responsável por exibir a localização da entrega em andamento.
2. Ao manipular o identificador do pedido na requisição (ex.: incrementar o ID), consegue acessar dados de rastreamento de outros pedidos que não são seus.
3. O atacante obtém a localização em tempo real de clientes que não conhece.

**Impacto:** risco à segurança física dos clientes, violação grave de privacidade, possível uso para crimes como abordagens ou assaltos.

**Categorias STRIDE relacionadas:** Information Disclosure, Elevation of Privilege.

---

### CA05 — Falso reembolso por negação de recebimento
**Ator:** cliente mal-intencionado (usuário legítimo abusando do sistema).

**Objetivo:** obter reembolso ou novo pedido gratuito alegando falsamente não ter recebido a entrega.

**Condições:** o processo de confirmação de entrega não possui evidências suficientes (ex.: apenas toque em "confirmar recebimento", sem foto ou assinatura).

**Fluxo de abuso:**
1. O cliente recebe o pedido normalmente.
2. Mesmo assim, contata o suporte alegando não ter recebido nada.
3. Como não há evidência forte de entrega (foto, geolocalização no momento da entrega), a plataforma opta por reembolsar para evitar desgaste.
4. O cliente repete o comportamento em pedidos futuros.

**Impacto:** prejuízo financeiro recorrente para a plataforma e para os restaurantes/entregadores, incentivo a fraudes similares por outros usuários.

**Categorias STRIDE relacionadas:** Repudiation.

---

# Etapa 2 — Análise, Priorização e Tratamento de Riscos com o NIST CSF

## 13.1 Critérios de probabilidade

| Valor | Classificação | Critério |
|---|---|---|
| 1 | Baixa | Depende de condições incomuns, acesso muito específico ou alta capacidade técnica |
| 2 | Média-baixa | Possível, mas depende de vulnerabilidade ou condição específica |
| 3 | Média-alta | Plausível, pode ocorrer em situações comuns de uso ou ataque |
| 4 | Alta | Pode ocorrer com facilidade, frequência ou em condições previsíveis |

## 13.2 Critérios de impacto

| Valor | Classificação | Critério |
|---|---|---|
| 1 | Baixo | Pequeno transtorno, corrigido rapidamente |
| 2 | Moderado | Interrupção ou inconsistência limitada, recuperável |
| 3 | Alto | Prejuízo relevante a usuários, negócio ou privacidade |
| 4 | Muito alto | Afeta muitos usuários, compromete operações críticas ou gera prejuízo grave |

## 13.3 Cálculo e classificação

`Pontuação = Probabilidade × Impacto`

| Pontuação | Nível |
|---|---|
| 1–3 | Baixo |
| 4–7 | Médio |
| 8–11 | Alto |
| 12–16 | Crítico |


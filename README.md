# Título do trabalho

## 1. Identificação do Sistema

- **Nome do Sistema:** Delivery Go
- **Integrantes:** Fabiano Mion, Emanuel Irion
- **Repositório:**
- **Justificativa:** para a escolha do sistema:** o sistema foi escolhido por envolver múltiplos perfis de usuários com interesses e níveis de acesso distintos (clientes, restaurantes/lojistas e entregadores), grande volume de dados sensíveis (dados pessoais, localização em tempo real e pagamentos) e diversas operações críticas de segurança (autenticação, transações financeiras, avaliações e comunicação entre as partes). Essa combinação de fatores permite identificar uma ampla variedade de ameaças e casos de abuso realistas, tornando o sistema um bom objeto de estudo para a disciplina.

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

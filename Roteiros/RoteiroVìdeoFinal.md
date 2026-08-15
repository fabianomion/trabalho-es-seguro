**Roteiro Vídeo Final:**

"O sistema que escolhemos para analisar foi o **DeliveryGo**, um aplicativo de delivery de comida, no mesmo modelo do iFood. Ele conecta três tipos de usuário: clientes, restaurantes e entregadores, o que gera uma superfície de ataque bem rica para trabalharmos ao longo de todas as etapas."

"Vamos mostrar como fomos das ameaças iniciais até um plano de arquitetura, código seguro, verificação de vulnerabilidades e monitoramento  sempre mantendo a coerência entre uma etapa e outra."

---

## 2. Ameaças e casos de abuso 


"Na Etapa 1 aplicamos o modelo **STRIDE** para mapear ameaças ao sistema. Identificamos, por exemplo, ameaças de Spoofing, como um atacante usando credenciais roubadas para acessar a conta de um cliente; e ameaças de Information Disclosure, como a exposição indevida da localização em tempo real do cliente ou do entregador."


"A partir dessas ameaças, construímos casos de abuso mais concretos. O que mais nos chamou atenção foi o caso de um entregador falso, que se cadastra na plataforma sem verificação de identidade adequada, aceita pedidos legítimos e simplesmente desaparece com o produto gerando prejuízo direto ao cliente e ao restaurante."

"Outro caso relevante foi a exposição da localização em tempo real por falha de autorização na API de rastreamento, o que colocaria em risco a segurança física dos nossos usuários."

---

## 3. Riscos prioritários 


"Na Etapa 2, transformamos essas ameaças em riscos, usando uma escala de probabilidade e impacto de 1 a 4, multiplicando os dois valores para chegar a uma pontuação de risco."


"O risco mais crítico que identificamos foi o **R01**, relacionado ao comprometimento de contas de cliente. Ele recebeu probabilidade 3 e impacto 4, totalizando pontuação 12, classificado como **crítico** porque envolve tanto dados pessoais quanto meios de pagamento."


"Também priorizamos o **R09**, sobrecarga da API em horários de pico, e o **R08**, exposição da localização em tempo real. Mesmo com pontuações parecidas, priorizamos o R08 na frente de outros riscos de mesma pontuação porque o impacto risco físico ao usuário é irreversível."

"Para cada risco definimos uma estratégia: reduzir, compartilhar ou aceitar. A maioria foi tratada com **redução**, mas, por exemplo, o risco de falsificação de localização do entregador foi conscientemente **aceito**, com monitoramento, porque o custo de eliminá-lo totalmente não se justificava neste momento."

---

## 4. Decisões de arquitetura 


"Na Etapa 3 traduzimos os riscos prioritários em requisitos de segurança e decisões de arquitetura. Por exemplo, o risco de comprometimento de conta virou o requisito: o sistema deve exigir autenticação multifator antes de confirmar operações sensíveis, como troca de senha ou cadastro de cartão."


"Também mapeamos essas falhas em categorias reconhecidas, como o **OWASP Top 10** e o **CWE**. A exposição indevida de dados de outros usuários, por exemplo, está relacionada à categoria de *Broken Access Control* e ao padrão conhecido como IDOR referência insegura direta a objeto."



colocamos um API Gateway com autenticação e limitação de requisições na entrada do sistema, e uma camada de autorização específica antes de qualquer acesso ao backend — validando sempre no servidor se o usuário tem permissão sobre aquele recurso."

"Uma das nossas decisões de arquitetura mais importantes foi: nunca confiar apenas na interface para esconder opções administrativas. Toda validação de permissão precisa acontecer no servidor, porque um atacante pode enviar requisições diretamente para a API, ignorando completamente o aplicativo."

---

## 5. Código seguro


"Na Etapa 4, escolhemos duas práticas de código seguro para detalhar. A primeira foi o controle de autorização por recurso, para evitar que um usuário acesse o pedido de outra pessoa só trocando um número na URL."


"Antes de implementar, definimos os testes: um teste em que o próprio dono acessa seu pedido que deve funcionar normalmente e outro em que um usuário tenta acessar o pedido de outra pessoa, que deve ser bloqueado e registrado em log."

"A segunda prática foi a autenticação multifator. Também definimos os testes antes: login com senha correta, mas sem o segundo fator, deve ser recusado; e login completo, com senha e código correto, deve ser aprovado normalmente."


"Como o sistema ainda não está totalmente implementado, apresentamos essas práticas em pseudocódigo, mas seguindo exatamente a lógica que usaríamos em uma implementação real."

---

## 6. Resultados da verificação 

"Na Etapa 5, usamos a ferramenta **OWASP ZAP** para testar a aplicação **OWASP Juice Shop**, que é um ambiente propositalmente vulnerável, usado para fins educacionais, já que nosso sistema ainda não está implementado."

"Entre os alertas encontrados, destacamos três: ausência de cabeçalho de segurança *Content-Security-Policy*, cookies de sessão sem as flags de proteção `HttpOnly` e `Secure`, e uma vulnerabilidade de injeção de SQL em um campo de busca."


"Para cada um propusemos uma correção: configurar corretamente os cabeçalhos de segurança, ajustar as flags dos cookies de sessão e usar consultas parametrizadas para evitar a injeção de SQL em vez de concatenar diretamente a entrada do usuário na consulta ao banco."

---

## 7. Monitoramento e pipeline DevSecOps 

"Na Etapa 6 pensamos em como detectar comportamentos suspeitos depois que o sistema estiver em operação. Definimos, por exemplo, uma regra de alerta para mais de cinco tentativas de login malsucedidas na mesma conta em dez minutos, o que dispara um bloqueio temporário e um alerta para a equipe de segurança."


"Também criamos uma regra para identificar volume excessivo de requisições vindas do mesmo IP, o que pode indicar uma tentativa de sobrecarregar o sistema e nesse caso a resposta inicial é ativar limitação automática de requisições para essa origem."

"Por fim, na Etapa 7, organizamos tudo isso em um pipeline de **DevSecOps**, passando por planejamento, arquitetura, código, verificação e operação. Definimos também condições que impediriam esse pipeline de continuar, como um teste de segurança reprovado ou uma vulnerabilidade crítica encontrada e não corrigida."

"A ideia central é que a segurança não fica só no início do projeto ela acompanha o sistema em todas as fases, do planejamento até a operação em produção."

---

## 8. Encerrament


"Esse trabalho nos mostrou como um mesmo sistema pode ser analisado sob várias perspectivas de segurança: ameaças, riscos, arquitetura, código, verificação e monitoramento e como essas etapas se conectam entre si."

"O maior aprendizado para nós foi perceber que segurança não é uma etapa isolada, mas um processo contínuo, que precisa ser pensado desde a concepção do sistema."

---

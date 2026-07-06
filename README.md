# AWS Step Functions: Orquestração de Workflows e Automação Low-Code

Este repositório contém a documentação técnica do meu laboratório prático desenvolvido durante o Bootcamp GFT, em parceria com a DIO. O objetivo principal deste projeto foi explorar o AWS Step Functions para desenhar e automatizar um fluxo de trabalho (workflow) integrado, utilizando uma abordagem puramente visual (Low-Code).

Como meu foco de carreira está direcionado para Cloud e DevOps, este laboratório foi excelente para entender como arquiteturas modernas e Serverless eliminam a necessidade de servidores ligados 24/7 apenas para rodar scripts de rotina, além de centralizar o tratamento de erros da operação.

---

## O Case Prático: Processador de Pedidos Faturados

Para consolidar os conceitos de automação, mensageria e resiliência, montei um cenário real de e-commerce: um Workflow Automatizado de Pós-Venda. 

A ideia foi garantir que a infraestrutura técnica resolvesse o problema do cliente com segurança e disparasse ações de marketing de forma totalmente automatizada e desacoplada.

### Arquitetura da Máquina de Estados:

1. **Validação:** O fluxo inicia acionando a AWS Lambda ValidarVenda.
2. **Primeira Tomada de Decisão (VendaVálida):**
   * **Cenário de Falha (Default):** Se a venda for inválida ou ocorrer algum erro, o fluxo desvia imediatamente para o Amazon SQS Suporte, gerando uma mensagem na fila para que o time de operações possa atuar no problema.
   * **Cenário de Sucesso (Rule #1):** Se a venda for válida, o fluxo segue para a AWS Lambda AtualizaEstoque e, logo em seguida, executa um scan no Amazon DynamoDB SalvaDadosDaCompra para registrar os dados no banco de dados.
3. **Segunda Tomada de Decisão (ClienteVip):**
   * **Cenário VIP (Rule #1):** Caso as regras de negócio identifiquem que o cliente é VIP, o fluxo aciona o Amazon SNS EnviarCupomViaEmail para disparar um benefício exclusivo.
   * **Cenário Comum (Default):** Se for um cliente comum, o fluxo cai em um Pass state (apenas para fins de documentação da rota) e encerra o processo com sucesso (End).

---

## Arquitetura Visual do Workflow

Abaixo está o gráfico gerado diretamente no Workflow Studio da AWS, mostrando o desenho lógico de ponta a ponta:

![Design do Workflow no Step Functions](image_0a2ce0.png)

---

## Aprendizados e Insights Técnicos (Visão DevOps)

* **Consistência de Dados em Primeiro Lugar:** A decisão de colocar o filtro de ClienteVip somente após o DynamoDB garante a resiliência do sistema. Primeiro salvamos os dados da compra com segurança; processos secundários (como marketing/cupons) rodam depois, evitando que um cliente ganhe um prêmio de uma venda que falhou no banco de dados.
* **Visibilidade para o Suporte:** Para quem opera infraestrutura, o Step Functions é um escudo. Em vez de revirar linhas de código ou logs caóticos para descobrir onde uma automação quebrou, a interface visual mostra exatamente em qual estado (caixinha) o processo travou, agilizando o troubleshooting.
* **Poder do Low-Code:** Orquestrar o comportamento do fluxo usando estados nativos da AWS reduz drasticamente o volume de código que o time precisa manter (menos funções Lambda soltas), o que diminui a superfície de bugs na plataforma.

---

## Serviços AWS Utilizados
* **AWS Step Functions** (Orquestração e controle de estado)
* **AWS Lambda** (Validação de venda e atualização de estoque)
* **Amazon DynamoDB** (Persistência e banco de dados NoSQL)
* **Amazon SQS** (Fila de mensageria para contingência do suporte)
* **Amazon SNS** (Disparo de notificações de e-mail/cupom)

---
*Projeto desenvolvido por um aluno SPTech focado em automação, eficiência e alta performance na nuvem.*

# Arquitetura Analítica de E-commerce: Do Dado Bruto à Estratégia de Vendas  
# Resultado da Análise Exploratória de Dados (EDA)  

Este projeto implementa uma arquitetura de engenharia de dados escalável para um ecossistema de e-commerce, transformando registros transacionais brutos em inteligência de negócios acionável.  
  
# Objetivo: Criação de Views para responder perguntas sobre a empresa usando tabelas de dados tratadas, mas ainda sem relacionamento direto.

# Tecnologias utilizadas: Python (Pandas, Matplotlib, Scikit-learn), PostgreSQL

Perguntas:  

1. Clientes costumam comprar múltiplos itens por pedido?  

2. Existe concentração de receita em poucos clientes?  

3. Existe um padrão de carrinho por cliente?

1. Clientes costumam comprar múltiplos itens por pedido?


# 1. Clientes costumam comprar múltiplos itens por pedido?  
  
Para responder a essa pergunta com precisão matemática, os dados transacionais foram unificados através da view vw_multiple_items_per_order.  
  
## Como as tabelas Silver se relacionam para responder a pergunta?  
A construção de inteligência de vendas exige a conexão de múltiplas dimensões do negócio. Esta view utiliza Common Table Expressions (WITH) para pré-agregar valores financeiros antes do JOIN principal, evitando a duplicação de receitas caso um pedido possua vários itens e várias parcelas.  
  
<div class="table-item">
    <span class="table-name">orders_dataset_silver</span>
    <span class="role">(Tabela Central):</span> É o coração do modelo. Registra o evento de compra (order_id) e a data temporal, servindo de âncora para todas as outras tabelas.  
</div>  

<div class="table-item">
    <span class="table-name">order_customers</span>
    <span class="role">(Dimensão Cliente):</span> Conecta-se à tabela central via <code>customer_id</code>. Traz a identificação única (<code>customer_unique_id</code>) para rastrear o comportamento do indivíduo.
</div>  

<div class="table-item">
    <span class="table-name">order_items</span>
    <span class="role">(Dimensão Física):</span> Conecta-se à tabela central via <code>order_id</code>. Detalha a granularidade do carrinho, contando exatamente quantas peças físicas existem no pacote.  
</div>  

<div class="table-item">
    <span class="table-name">products_silver_tratada</span>
    <span class="role">(Dimensão Produto):</span> Conecta-se à tabela de itens via <code>product_id</code>. Garante a integridade de que o item faturado é um produto real e ativo no catálogo do e-commerce.  
</div>  

<div class="table-item">
    <span class="table-name">order_payments_silver_tratada</span>
    <span class="role">(Dimensão Financeira):</span> Conecta-se à tabela central via <code>order_id</code>. Mensura o impacto econômico total (<code>valor_total_gasto</code>), assegurando que a análise se baseie em receita real e não apenas em intenção de compra.  
</div>  
  
## Consulta de Negócios e Filtro Logístico  
A extração dos indicadores finais utiliza a consulta abaixo:  
<img width="1217" height="198" alt="image" src="https://github.com/user-attachments/assets/4e907032-ca46-4a4a-963c-1ac033a022c8" />  
  
Análises estáticas focadas apenas em delivered (entregue) ou shipped (enviado) geram um "ponto cego temporal". Os status invoiced (faturado) e processing (em processamento) representam vendas que acabaram de acontecer.
Ao injetar a regra AND valor_total_gasto > 0, garante-se que o pagamento já foi aprovado e o dinheiro já está no caixa. Essa técnica permite que a equipe de negócios avalie a eficiência de campanhas de vendas e Cross-Selling em tempo real, sem precisar esperar dias ou semanas para que o pacote chegue à casa do cliente.  



<img width="637" height="348" alt="Image4" src="https://github.com/user-attachments/assets/1d263cd1-612e-4645-b64e-434096f79d0e" />   



# 2. Existe concentração de receita em poucos clientes?  

Para responder a questão foi criada a view vw_analise_de_concentracao_receita. As consultas permitem identificar a existência de concentração de receita (Princípio de Pareto clássico), avaliar a volumetria, a penetração e os tickets médios de diferentes coortes de clientes e mapear a distribuição do faturamento do e-commerce por cliente único para fornecer insumos estratégicos para alavancagem de vendas, maximização de lucro logístico e fidelização da base.  

## Como as tabelas Silver se relacionam para responder a pergunta?  

Para viabilizar esta análise sem falhas de performance ou distorções, os dados transacionais brutos foram unificados na camada Silver através da view silver.vw_analise_de_concentracao_receita. Esta estrutura conecta três tabelas fundamentais: o cadastro de clientes (order_customers), o registro central de pedidos (orders_dataset_silver) e a tabela de fluxos financeiros (order_payments_silver_tratada).  

## Resultados Estatísticos das Consultas de Negócio  
  
A extração de indicadores foi realizada em duas etapas complementares, cujos dados possuem uma amarração matemática perfeita entre si:
Consulta 1: Resumo da Regra de Pareto (O Corte de 80%) A primeira consulta apurou que o volume necessário de clientes para compor os 80% do faturamento da empresa corresponde a 47.929 clientes de um universo de 96.095, representando exatos 49,88% de penetração da base.  

Consulta 2: Matriz de Coortes (Fatiamento Detalhado da Receita) Ao separarmos toda a base em 4 grupos estruturados de influência financeira, geramos a seguinte matriz representada em HTML:


<table border="1" cellpadding="6" cellspacing="0" style="border-collapse: collapse; font-family: Arial, sans-serif;">
  <thead>
    <tr style="background-color: #f2f2f2;">
      <th>Grupos de Clientes</th>
      <th>Total Clientes</th>
      <th>Penetração Base</th>
      <th>Faturamento do Grupo</th>
      <th>Part. Receita</th>
      <th>Ticket Médio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1. Top VIP (0 a 20%)</td>
      <td>3.622</td>
      <td>3,77%</td>
      <td>R$ 3.074.763,10</td>
      <td>20,00%</td>
      <td>R$ 848,91</td>
    </tr>
    <tr>
      <td>2. Alta Relevância (21 a 50%)</td>
      <td>14.266</td>
      <td>14,85%</td>
      <td>R$ 4.612.008,30</td>
      <td>30,00%</td>
      <td>R$ 323,29</td>
    </tr>
    <tr>
      <td>3. Relevância Média (51 a 80%)</td>
      <td>30.041</td>
      <td>31,26%</td>
      <td>R$ 4.611.935,65</td>
      <td>30,00%</td>
      <td>R$ 153,52</td>
    </tr>
    <tr>
      <td>4. Cauda Longa (81 a 100%)</td>
      <td>48.166</td>
      <td>50,12%</td>
      <td>R$ 3.074.000,06</td>
      <td>20,00%</td>
      <td>R$ 63,82</td>
    </tr>
  </tbody>
</table>








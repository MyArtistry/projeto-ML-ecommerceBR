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

<img width="712" height="466" alt="Image1" src="https://github.com/user-attachments/assets/241fa203-efa6-44aa-add1-bc879a89fd1b" />  
  

<img width="671" height="275" alt="Image2" src="https://github.com/user-attachments/assets/eab26b8c-c0f6-4d94-bed8-0c505d10e126" />    

## Consulta de Negócios e Filtro Logístico  
A extração dos indicadores finais utiliza a consulta abaixo:  
<img width="1217" height="198" alt="image" src="https://github.com/user-attachments/assets/4e907032-ca46-4a4a-963c-1ac033a022c8" />  

Análises estáticas focadas apenas em delivered (entregue) ou shipped (enviado) geram um "ponto cego temporal". Os status invoiced (faturado) e processing (em processamento) representam vendas que acabaram de acontecer.
Ao injetar a regra AND valor_total_gasto > 0, garante-se que o pagamento já foi aprovado e o dinheiro já está no caixa. Essa técnica permite que a equipe de negócios avalie a eficiência de campanhas de vendas e Cross-Selling em tempo real, sem precisar esperar dias ou semanas para que o pacote chegue à casa do cliente.

<img width="650" height="278" alt="Image3" src="https://github.com/user-attachments/assets/7472e2f5-c7b5-485a-b39b-7d4d216ae107" />   



<img width="637" height="348" alt="Image4" src="https://github.com/user-attachments/assets/1d263cd1-612e-4645-b64e-434096f79d0e" />   



# 2. Existe concentração de receita em poucos clientes?  










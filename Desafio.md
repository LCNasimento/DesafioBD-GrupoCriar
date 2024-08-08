===Grupo CRIAR- Desafio Banco de Dados ===
 
 Vaga: Analista de Banco de Dados
 Área: Desenvolvimento de Software

 Definições do teste
 > As perguntas abaixo compreendem apenas uma visão geral de como o candidato se
 comportaria em determinadas situações técnicas.
 > Esse teste compreende apenas banco de dados relacionais, caso acredite que seja
 necessário utilizar sintaxe ou funções pode utilizar as sintaxes do MySQL,
 PostgreSQL ou Oracle.

 CONSTRUÇÃO

/* Provavelmente antes de iniciar consultas e subconsultas eu verificaria as tabelas existentes com: */
SHOW TABLES;
/* Depois verificaria a estrutura da tabela que vou utilizar para encontrar os dados que preciso: */
DESCRIBE nome_da_tabela;
/* após analisar como é a estrutura do banco de dados que vou trabalhar eu faria as solicitações*/

1. Consultas básicas:
a. Como você encontraria todos os clientes de uma determinada cidade?

/* Primeiramente verificaria a situação das tabelas como descrito anteriormente, ciente dessas informações realizaria uma consulta na tabela clientes, selecionado todas as colunas da tabela, com uma condição para filtrar a cidade: */
SELECT * 
FROM clientes
WHERE cidade =  'NomeDaCidade';

b. Como você calcularia a média de vendas por produto?

/* Para calcular a média de vendas de um produto é preciso selecionar a coluna de produto e usar AVG na coluna de valor de venda mostrar a média, o group by seria para uma melhor visualização dos produtos agrupados, a consulta ficaria assim: */
SELECT id_produto, AVG(valor_venda) AS media_vendas
FROM vendas
GROUP BY id_produto;

c. Como você listaria os produtos que nunca foram vendidos?

/* Supondo que os produtos estão em uma planilha e vendas está em outra para listar os produtos não vendidos eu utilizaria a cosulta com junção entre a tabela de produtos e de vendas, aplicando a condição de nulo para identificação do produto: */
SELECT p.*
FROM produtos p LEFT JOIN vendas v ON v.id_produto = p.id_produto
WHERE v.id_produto IS NULL;
/* Caso na aplicação de um describe na tabela fosse verificado que produtos e vendas estão em uma mesma tabela seria apena um aconsulta simples com a clausula where para identificar os não vendidos */

2.  Joins:
a. Explique os diferentes tipos de junções (INNER JOIN, LEFT JOIN, RIGHT JOIN,
 FULL OUTER JOIN).

INNER JOIN é a junção interna que retorna somente registros que possuim correspondência em ambas as tabelas;
LEFT JOIN é a junção externa esquerda, que retorna todos os registros da tabela esquerda com correspondência com a tabela direita mais as que não houver correspondência  como NULL;
RIGHT JOIN é a junção externa direita, que retorna todos os registros da tabela direita com correspondência com a tabela esquerda mais as que não houver correspondência  como NULL;
FULL OUTER JOIN é uma junção externa que retorna todos os registros das tabelas com correspondência e sem correspondência como NULL;

 b. Como você uniria duas tabelas para encontrar os pedidos de um determinado
 cliente?

/* Como se trata apenas de retornar registros que correspondem a tabela cliente e pedidos seria usado uma Junção interna 
SELECT *
FROM pedidos p INNER JOIN clientes c ON p.id_cliente = c.id_cliente
WHERE c.nome = 'NomeDoCliente';

 3. Agregação e agrupamento:
a. Como você calcularia o número total de pedidos por mês?

/* Para calcula o numero total de pedidos por mês é necessário fazer uma consulta com MONTH com o COUNT para realizar a contagem dentro da tabela pedidos, eu utilizaria o group by para agrupar as datas e deixar mais organizado a exibição: */
SELECT MONTH(data_pedido) AS mes, COUNT(*) AS total_pedidos
FROM pedidos
GROUP BY MONTH (data_pedido);

 b. Como você encontraria os clientes que fizeram mais de 5 pedidos?

/* Neste caso é feito uma consulta com select e a contagem de pedidos na tabela pedidos, após isso será utilizado um agrupamento e um filtro com having dentro do agrupamento para mostrar os pedidos com mais de 5 pedidos */
SELECT id_cliente, nome, COUNT(*) AS total_pedidos 
FROM pedidos GROUP BY id_cliente HAVING COUNT(*) >5;

 4. Subconsultas:
a. Quando você usaria uma subconsulta?

/* Uma subconsulta é utilizada quando uma consulta principal depende de uma outra consulta para ser executada, ela pode ser util para filtrar resultados, calcular valores intermediários e outras coisas */

 b. Dê um exemplo de uma subconsulta correlacionada.

SELECT nome 
FROM clientes
WHERE id_cliente IN( 
SELECT id_cliente
FROM pedidos
WHERE valor_total >1000);

 5. Funções:
  a. Quais são as principais funções de agregação (COUNT, SUM, AVG, MAX, MIN)?

COUNT é para o numero de registros de uma determinada coluna;
SUM soma os valores;
AVG faz a média dos valores;
MAX retorna o maior valor;
MIN retorna o menor valor;

 b. Como você usaria a função CASE WHEN?

/*  Essa função permite implementar lógica condicional diretamente dentro das consultas, sendo util na categorizalçao de dados, driação de colunas calculadas e consições complexas,  no exempo abaixo seria uma situação de categorizar as idades na consulta da tabela clientes: */
SELECT nome,
	CASE 
		WHEN idade < 18 THEN 'Menor'
		WHEN idade BETWEEN 18 AND 64 THEN 'Adulto'
		ELSE 'Idoso'
	END AS categoria_idade
FROM clientes;

 6. Criação e alteração de objetos:
 a. Como você criaria uma tabela com uma chave primária e índices?

/* Para MySQL seria : */
CREATE TABLE produtos (
id_produto         INT AUTO_INCREMENT PRIMARY KEY,
nome                   VARCHAR(100) NOT NULL,
preco                   DECIMAL(10, 2) NOT NULL,
INDEX(nome)
);

/* Para o Oracle  a criação de index seria separado: */

CREATE INDEX idx_nome ON produtos(nome);

/* Na criação de tabelas na Oracle costuma-se usa também ao invés de VARCHAR a opção de VARCHAR2 onde os caracteres preenchidos não são fixo ocupados, é melhor para otimização das tabelas */

 b. Como você alteraria uma coluna existente em uma tabela?

ALTER TABLE produtos MODIFY COLUMN nome VARCHAR(200);

 7. Otimização de consultas:
 a. Quais são as principais técnicas de otimização de consultas?

/* As principais tecnicas de otimização de consultas são a indexação para acelerar busca de dados, análise de plano de execução com uso de ferramentas como EXPLAIN, selecionar colunas necessárias para não trazer dados obsoletos, uso de joins eficientes que retorne menos dados, usar views materializadas para armazenar resultados de consultas que não mudam com frequência, colocar lógicas complexas dentro de views e procedures pode simplificar a mantençaõ e reutilização do código, com o procedure é possivel que multiplas operações sejam executadas no servidor em uma unica chamada. */


 b. Como você identificaria uma consulta lenta?

/* Para identificar consultas lentas usa-se EXPLAIN que analisa como a consulta é exectada */
EXPLAIN ANALYZE SELECT * FROM tabela WHERE coluna = 'valor	';

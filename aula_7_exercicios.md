# Exercícios

## Exercício 1

Encontre o preço médio arredondado com 2 casas decimais dos produtos em cada uma das categorias.

```sql
SELECT category_id, ROUND(AVG(price), 2) AS media_categorias from products
group by category_id

```

## Exercício 2

Busque todas as informações sobre os produtos que nunca foram comprados (inclusive a descrição da categoria e todos os dados do fornecedor).

```sql
SELECT
    p.*,
	s.supplier_name, s.supplier_email, s.supplier_phone, s.supplier_address
FROM
    products p
JOIN
    categories c ON p.category_id = c.category_id
JOIN
	suppliers s ON s.supplier_id = p.supplier_id
WHERE
    p.product_id NOT IN (SELECT product_id FROM order_items);

```

## Exercício 3

Encontre os top 5 clientes que mais gastaram dinheiro em compras, exibindo o nome completo e o valor gasto formatado como dinheiro.

Dica: Formatar como dinheiro pode ser feito assim usando o comando `CAST(xxxxxx AS MONEY)`, como em `SELECT CAST(1000 AS MONEY)`

```sql
SELECT CONCAT(first_name,' ', last_name) AS nome_completo,
 CAST(SUM(o.total_amount) AS MONEY) AS total_gasto
FROM
    customers c
JOIN
    orders o ON o.customer_id = c.customer_id
GROUP BY
    c.first_name, c.last_name
ORDER BY
    total_gasto DESC
LIMIT 5;

```

## Exercício 4

Dado o modelo textual/lógico abaixo, escreva os comandos SQL para criar as tabelas, suas restrições e relações quando aplicáveis e insira pelo menos 5 registros em cada uma das tabelas.

    alunos(nome, numero_aluno, tipo_aluno, curso)

    disciplinas(nome_disciplina, numero_disciplina, creditos, departamento)

    turmas(identificacao_turma, numero_disciplina, semestre, ano, professor)

    pre_requisitos(numero_disciplina, numero_pre_requisito)

    historico_escolar(numero_aluno, identificacao_turma, nota)

Feito, isso, execute o comando SQL abaixo, para inserir mais registros

```sql
INSERT INTO alunos
(nome, numero_aluno, tipo_aluno, curso)
VALUES('Silva', 17, 1, 'CC');

INSERT INTO alunos
(nome, numero_aluno, tipo_aluno, curso)
VALUES('Braga', 8, 2, 'CC');

INSERT INTO disciplinas
(numero_disciplina, creditos, departamento, nome_disciplina)
VALUES('CC1310', 4, 'CC', 'Introd. à ciência da computação');

INSERT INTO disciplinas
(numero_disciplina, creditos, departamento, nome_disciplina)
VALUES('CC3320', 4, 'CC', 'Estruturas de dados');

INSERT INTO disciplinas
(numero_disciplina, creditos, departamento, nome_disciplina)
VALUES('MAT2410', 3, 'MAT', 'Matemática discreta');

INSERT INTO disciplinas
(numero_disciplina, creditos, departamento, nome_disciplina)
VALUES('CC3380', 3, 'CC', 'Banco de dados');

INSERT INTO turmas
(identificacao_turma, numero_disciplina, semestre, ano, professor)
VALUES(85, 'MAT2410', 'Segundo', 2007, 'Kleber');

INSERT INTO turmas
(identificacao_turma, numero_disciplina, semestre, ano, professor)
VALUES(92, 'CC1310', 'Segundo', 2007, 'Anderson');

INSERT INTO turmas
(identificacao_turma, numero_disciplina, semestre, ano, professor)
VALUES(102, 'CC3320', 'Primeiro', 2008, 'Carlos');

INSERT INTO turmas
(identificacao_turma, numero_disciplina, semestre, ano, professor)
VALUES(112, 'MAT2410', 'Segundo', 2008, 'Chang');

INSERT INTO turmas
(identificacao_turma, numero_disciplina, semestre, ano, professor)
VALUES(119, 'CC1310', 'Segundo', 2008, 'Anderson');

INSERT INTO turmas
(identificacao_turma, numero_disciplina, semestre, ano, professor)
VALUES(135, 'CC3380', 'Segundo', 2008, 'Santos');

INSERT INTO historicos_escolares
(numero_aluno, identificacao_turma, nota)
VALUES(17, 112, 'B');

INSERT INTO historicos_escolares
(numero_aluno, identificacao_turma, nota)
VALUES(17, 119, 'C');

INSERT INTO historicos_escolares
(numero_aluno, identificacao_turma, nota)
VALUES(8, 85, 'A');

INSERT INTO historicos_escolares
(numero_aluno, identificacao_turma, nota)
VALUES(8, 92, 'A');

INSERT INTO historicos_escolares
(numero_aluno, identificacao_turma, nota)
VALUES(8, 102, 'B');

INSERT INTO historicos_escolares
(numero_aluno, identificacao_turma, nota)
VALUES(8, 135, 'A');

INSERT INTO pre_requisitos
(numero_disciplina, numero_pre_requisito)
VALUES('CC3380', 'CC3320');

INSERT INTO pre_requisitos
(numero_disciplina, numero_pre_requisito)
VALUES('CC3380', 'MAT2410');

INSERT INTO pre_requisitos
(numero_disciplina, numero_pre_requisito)
VALUES('CC3320', 'CC1310');
```

Executar as seguintes consultas:

- Recuperar uma lista de todas as disciplinas e notas de Silva.
- Listar os nomes dos alunos que realizaram a disciplina Banco de dados oferecida no segundo semestre de 2008 e suas notas nessa turma.
- Listar os pré-requisitos do curso de Banco de dados.

Executar as seguintes atualizações no banco de dados

- Alterar o tipo de aluno de Silva para segundo ano.
- Criar outra turma para a disciplina Banco de dados para este semestre.
- Inserir uma nota A para Silva na turma Banco de dados do último semestre.

&nbsp;

## Resposta

```sql
-- create tables
create table tb_alunos(nome character varying(50) not null,
num_aluno numeric(4) not null,
tipo_aluno numeric(4)not null,
curso character varying(10)not null,
CONSTRAINT id_aluno PRIMARY KEY (num_aluno)
);

create table tb_disciplinas(nome_disciplina character varying(50) not null,
num_disciplina character varying(10) not null,
creditos numeric (4)not null,
departamento character varying(10)not null,
CONSTRAINT id_disciplina PRIMARY KEY (num_disciplina)
);

create table tb_turmas(id_turma numeric(4) not null,
num_disciplina character varying(10) not null,
semestre character varying(10) not null,
ano numeric (4)not null,
professor character varying(20) not null,
CONSTRAINT id_turma PRIMARY KEY (id_turma),
CONSTRAINT num_disciplina FOREIGN KEY (num_disciplina) REFERENCES tb_disciplinas(num_disciplina)
);

create table tb_pre_requisitos(num_disciplina character varying(10) not null,
num_pre_requisito character varying(10) not null,
CONSTRAINT num_disciplina FOREIGN KEY (num_disciplina) REFERENCES tb_disciplinas(num_disciplina),
CONSTRAINT num_pre_requisito FOREIGN KEY (num_pre_requisito) REFERENCES tb_disciplinas(num_disciplina);
);

create table tb_historico_escola(num_aluno numeric(4) not null,
id_turma numeric(4) not null,
nota character varying(10) not null,
CONSTRAINT num_aluno FOREIGN KEY (num_aluno) REFERENCES tb_alunos(num_aluno),
CONSTRAINT id_turma FOREIGN KEY (id_turma) REFERENCES tb_turmas(id_turma)
)

```

--Recuperar uma lista de todas as disciplinas e notas de Silva.

```sql
select alunos.nome as nome_aluno, turmas.num_disciplina as disciplina, historico.id_turma as turma, historico.nota as nota
from tb_alunos as alunos
join tb_historico_escola as historico on alunos.num_aluno = historico.num_aluno
join tb_turmas as turmas on historico.id_turma = turmas.id_turma
where alunos.nome = 'Silva';
``` 

--Listar os nomes dos alunos que realizaram a disciplina Banco de dados oferecida no segundo semestre de 2008 e suas notas nessa turma.

```sql
select alunos.nome as nome_aluno, historico.nota as nota
from tb_disciplinas as disciplinas
join tb_turmas as turmas on turmas.num_disciplina = disciplinas.num_disciplina
join tb_historico_escola as historico on historico.id_turma = turmas.id_turma
join tb_alunos as alunos on alunos.num_aluno = historico.num_aluno
where disciplinas.nome_disciplina = 'Banco de dados'and turmas.ano = 2008 and turmas.semestre = 'Segundo';
``` 

--Listar os pré-requisitos do curso de Banco de dados.

```sql
select requisitos.num_pre_requisito as requisitos
from tb_pre_requisitos as requisitos
join tb_disciplinas as disciplinas on requisitos.num_disciplina = disciplinas.num_disciplina
where disciplinas.nome_disciplina = 'Banco de dados'
``` 


--Executar as seguintes atualizações no banco de dados
--Alterar o tipo de aluno de Silva para segundo ano.

``` sql
update tb_alunos set tipo_aluno = 2 where nome = 'Silva';
``` 

--Criar outra turma para a disciplina Banco de dados para este semestre.

```sql
insert into tb_turmas (id_turma,num_disciplina, semestre, ano, professor)
values (145,'CC3380','Segundo', 2023, 'Bruna');
select * from tb_turmas;
``` 

--Inserir uma nota A para Silva na turma Banco de dados do último semestre.

```
    alunos(nome, numero_aluno, tipo_aluno, curso)
```sql
insert into tb_historico_escola (num_aluno, id_turma, nota)
values (17, 145, 'A');
```

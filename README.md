# CidadeAtiva API

API da Global Solution 2026/2 da FIAP para registrar e acompanhar problemas urbanos, como buracos, iluminação quebrada e descarte irregular de lixo.

| Entrega | Preencher antes do envio |
|---|---|
| Integrantes, RMs e turmas | Ver `integrantes.txt` |
| URL pública da API | Após o deploy no Render |
| Vídeo não listado de até 5 minutos | Após gravar o front integrado à API |

## Rodar com Oracle

1. Use o JDK 21 e Maven 3.9, ou importe o `pom.xml` no IntelliJ.
2. No Oracle da FIAP, execute `sql/01_criar_tabelas.sql` uma vez no schema do grupo. Se as tabelas já existirem, compare os nomes das colunas, restrições e sequences com o script antes de usar a API.
3. No PowerShell, na pasta do projeto, configure as variáveis com os dados do seu ambiente:

```powershell
$env:DB_URL = "jdbc:oracle:thin:@//HOST:PORTA/SERVICO"
$env:DB_USER = "SEU_USUARIO"
$env:DB_PASSWORD = "SUA_SENHA"
mvn spring-boot:run
```

No IntelliJ, você também pode definir essas variáveis em **Run → Edit Configurations → Environment variables** e iniciar `CidadeAtivaApplication`. Use a URL JDBC indicada pela FIAP.

4. No Insomnia, faça um **GET** em `http://localhost:8080/health`. O status 200 confirma que a aplicação está respondendo. Um **GET /categorias** confirma também o acesso à tabela no Oracle.

## Testar no Insomnia

Importe `CidadeAtiva_Insomnia.json` pelo botão **Import**. O ambiente vem com `base_url` igual a `http://localhost:8080`. Rode as requisições de cadastro na ordem cidadão, bairro, categoria e ocorrência. Use os IDs retornados nos campos do ambiente da coleção antes de executar as requisições seguintes. Os exemplos de cadastro usam dados fictícios.

Para demonstrar a regra de negócio, use as requisições de resolver, tentar reabrir sem justificativa e reabrir com justificativa. Os códigos esperados são **201**, **409** e **201**.

## Integrar o front

Use `GET /ocorrencias` para listar e `POST /ocorrencias` para cadastrar. Preencha as opções do formulário com `GET /bairros` e `GET /categorias`. O cadastro exige os IDs de cidadão, bairro e categoria. Veja os exemplos na documentação e na coleção. O CORS permite `http://localhost:5173` por padrão.

## Publicar no Render

1. Envie o projeto para um repositório GitHub e crie um **Web Service** no Render com runtime **Docker**, apontando para o `Dockerfile` da raiz.
2. Configure `DB_URL`, `DB_USER`, `DB_PASSWORD` e `CORS_ORIGINS` nas variáveis do serviço. Em `CORS_ORIGINS`, informe as origens do front separadas por vírgula. Use só a origem, sem caminho nem barra no fim. O aplicativo usa `PORT` do ambiente ou 8080 localmente.
3. Teste a URL pública em `/health` e `/categorias` pelo Insomnia. Troque `base_url` pela URL pública e valide o fluxo pelo front.
4. Registre a URL da API e do vídeo neste README e na capa da documentação.

## Organização

`resource` recebe HTTP e aplica `@Valid`. `bo` concentra regras de negócio e usa ModelMapper. `dao` usa JDBC via JdbcTemplate com DataSource e parâmetros preparados. `model` contém as entidades e `dto` define os dados expostos. `exception` trata erros e `config` configura CORS e ModelMapper.

A API do MVP não exige autenticação. Use dados fictícios na demonstração e não use este ambiente para coletar dados reais. O e-mail de cadastro não aparece nas respostas da API.

## Uso de IA generativa

ChatGPT foi usado para criar a base da API Java, o script SQL, a coleção do Insomnia e a documentação a partir do briefing e das aulas da FIAP. Prompt representativo do pedido: “faça um zip com tudo”. O grupo deve revisar o código, testar no ambiente da FIAP e registrar os ajustes que fizer antes da entrega.

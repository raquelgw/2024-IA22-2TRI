# Iniciando um projeto Node.js com TypeScript

Crie um diretório para o projeto e acesse-o pelo vscode, abra o terminal e siga os passos abaixo.

``` bater
npm init -y
npm instalar express cors sqlite3 sqlite
npm install --save-dev typescript nodemon ts-node @tipos/express @tipos/cors
npx tsc --init
mkdir fonte
toque em src/app.ts
```

## Configurado ou ` tsconfig.json `

Mude a linha ``` "outDir": "./", ``` para ``` "outDir": "./dist", ``` e adicione a linha ``` "rootDir": "./src ", ``` , seu arquivo de configuração do compilador do TypeScript ficará mais ou menos assim.

``` json
{
  "compilerOptions" : {
    "alvo" : " ES2017 " ,
    "módulo" : " commonjs " ,
    "outDir" : " ./dist " ,
    "rootDir" : " ./src " ,
    "strict" : verdadeiro ,
    "esModuleInterop" : verdadeiro ,
    "skipLibCheck" : verdadeiro ,
    "forceConsistentCasingInFileNames" : verdadeiro
  }
}
```

## Configurando o ` package.json `

Adicione o seguinte script ao seu ` package.json `

``` json
"scripts" : {
  "dev" : " npx nodemon src/app.ts "
}
```

## Criando arquivo inicial do servidor

Adicione o seguinte código ao arquivo ` src/app.ts `

``` datilografado
importar  expresso  de  ' expresso ' ;
importar  cors  de  ' cors ' ;

const porta =  3333 ;
const app =  express ();

aplicativo . usar ( cors ());
aplicativo . usar ( express . json ());

aplicativo . obter ( ' / ' , ( req , res ) => {
  res . enviar ( ' Olá Mundo ' );
});

aplicativo . ouvir ( porta , () => {
  console . log ( ` Servidor em execução na porta ${ porta } ` );
});
```

## Inicializando o servidor

``` bater
npm executar dev
```

Se tudo acontecer bem, você verá a mensagem ` Servidor rodando na porta 3333` no terminal.

## Testando o servidor

Abra o navegador e acesse ` http://localhost:3333` , você verá a mensagem ` Hello World` .

## Configurando o banco de dados

Crie um arquivo ` database.ts ` dentro da pasta ` src ` e adicione o seguinte código.

``` datilografado
importar { abrir } de  ' sqlite ' ;
importar  sqlite3  de  ' sqlite3 ' ;

deixe instância :  sqlite3 . Banco de dados  |  nulo  =  nulo ;

exportar  função assíncrona  connect() {
  se ( instância ) retornar  instância ;

  const db =  aguardar  abertura ({
     nome do arquivo: ' ./src/database.sqlite ' ,
     driver: sqlite3 . Banco de dados
   });

  aguarde  db .exec ( `​
    CRIAR TABELA SE NÃO EXISTIR usuários (
      id CHAVE PRIMÁRIA INTEIRA AUTOINCREMENTO,
      nome TEXTO,
      e-mail TEXTO
    )
  ` );

  instância  =  db ;
  retornar  db ;
}
```

## Adicionando o banco de dados ao servidor

``` datilografado
importar  expresso  de  ' expresso ' ;
importar  cors  de  ' cors ' ;
importar { conectar } de  ' ./banco de dados ' ;

const porta =  3333 ;
const app =  express ();

aplicativo . usar ( cors ());
aplicativo . usar ( express . json ());

aplicativo . obter ( ' / ' , ( req , res ) => {
  res . enviar ( ' Olá Mundo ' );
});

aplicativo . post ( ' /usuários ' , async ( req , res ) => {
  const db =  aguarda  conexão ();
  const { nome, email } =  req . corpo ;

  const result =  await  db . run ( ' INSERT INTO users (nome, email) VALUES (?, ?) ' , [ nome , email ]);
  const user =  await  db . get ( ' SELECIONE * DE usuários ONDE id = ? ' , [ resultado . lastID ]);

  res . json ( usuário );
});

aplicativo . ouvir ( porta , () => {
  console . log ( ` Servidor em execução na porta ${ porta } ` );
});
```

## Testando a inserção de dados

Abra o Postman e faça uma requisição POST para ` http://localhost:3333/users ` com o seguinte corpo.

``` json
{
  "nome" : " John Doe " ,
  "e-mail" : "
}
```

Se tudo ocorrer bem, você verá uma resposta com o usuário inserido.

``` json
{
  "id" : 1 ,
  "nome" : " John Doe "
  "e-mail" : "
}
```

## Listando os usuários

Adicione a rota ` /users ` ao servidor.

``` datilografado
aplicativo . obter ( ' /usuários ' , async ( req , res ) => {
  const db =  aguarda  conexão ();
  const usuários =  await  db . all ( ' SELECIONE * DE usuários ' );

  res . json ( usuários );
});
```
Editando um usuário
Adicione a rota /users/:id ao servidor.

app.put('/users/:id', async (req, res) => {
  const db = await connect();
  const { name, email } = req.body;
  const { id } = req.params;

  await db.run('UPDATE users SET name = ?, email = ? WHERE id = ?', [name, email, id]);
  const user = await db.get('SELECT * FROM users WHERE id = ?', [id]);

  res.json(user);
});
Deletando um usuário
Adicione a rota /users/:id ao servidor.

app.delete('/users/:id', async (req, res) => {
  const db = await connect();
  const { id } = req.params;

  await db.run('DELETE FROM users WHERE id = ?', [id]);

  res.json({ message: 'User deleted' });
});

# Iniciando um projeto Node.js com TypeScript


### Crie uma conta no GitHub:
- Se você ainda não tem uma conta, acesse github.com e crie uma.


### Crie um Repositório no GitHub:
No GitHub, clique no botão New para criar um novo repositório.
<img src ="imagens/new.png">
- Dê um nome ao seu repositório.
- Selecione a opção **Public** (para que todos possam ver).
- Clique em “adicionar um arquivo README” caso queira escrever uma descrição para seu projeto.
- Clique em **Create repository**.
<img src ="imagens/repository.png">
- Após criar um repositório clique em “<> code”
<img src ="imagens/code.png">
- Clique em “Create codespace on main” para iniciar o projeto.
<img src ="imagens/codespace.png">
- Abra o terminal com o comando CTRL + ‘
- Execute os seguintes comandos no terminal para configurar o projeto:


```bash
npm init -y
npm install express cors sqlite3 sqlite
npm install --save-dev typescript nodemon ts-node @types/express @types/cors
npx tsc --init
mkdir src
touch src/app.ts
```


## Configuranado o `tsconfig.json`


- Abra o arquivo tsconfig.json.
- Altere a linha "outDir": "./", para "outDir": "./dist",.
- Adicione a linha "rootDir": "./src", logo abaixo.
- O arquivo final deve parecer com isto:


```json
{
  "compilerOptions": {
    "target": "ES2017",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```


## Configurando o `package.json`


Adicione o seguinte script ao seu `package.json`


```json
"scripts": {
  "dev": "npx nodemon src/app.ts"
}
```


## Criando arquivo inicial do servidor


Adicione o seguinte código ao arquivo `src/app.ts`


```typescript
import express from 'express';
import cors from 'cors';


const port = 3333;
const app = express();


app.use(cors());
app.use(express.json());


app.get('/', (req, res) => {
  res.send('Hello World');
});


app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```


## Inicializando o servidor


- No terminal, execute o comando abaixo:
```bash
npm run dev
```


Se tudo ocorrer bem, você verá a mensagem `Server running on port 3333` no terminal.


## Testando o servidor


- Clique em "Ports" ao lado do terminal.
- Após isto, abra no navegador.
<img src ="imagens/ports.png">
<img src ="imagens/browser.png">


 E você verá a mensagem `Hello World`.

## Configurando o banco de dados

Crie um arquivo ` database.ts ` dentro da pasta ` src ` e adicione o seguinte código.

``` import { Database, open } from 'sqlite';
import sqlite3 from 'sqlite3';

let instance: Database | null = null;

export async function connect() {
  if (instance) return instance;

  const db = await open({
     filename: './src/database.sqlite',
     driver: sqlite3.Database
   });
  
  await db.exec(`
    CREATE TABLE IF NOT EXISTS users (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      name TEXT,
      email TEXT
    )
  `);

  instance = db;
  return db;

}```

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

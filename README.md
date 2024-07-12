# 2024-IA22-2TRI

Programação II

Vamos programar nosso primeiro servidor com rotas!! :)

# Iniciando um Repositório  no CodeSpace
Crie um Repositório  com o nome "2024-IA24".

Lembrando: O Repositório  deve ser publico e com o arquivo README incluso.

# Iniciando um projeto Node.js com TypeScript

No Terminal digite os seguintes comandos, lembre de executar um por vez.

```
npm init -y
npm install express cors sqlite3 sqlite
npm install --save-dev typescript nodemon ts-node @types/express @types/cors
npx tsc --init
mkdir src
touch src/app.ts

```

# Configurando o ` tsconfig.json ` 

Mude a linha ` "outDir": "./" ` , para 
` "outDir": "./dist" `, e adicione a linha ` "rootDir": "./src"`, seu arquivo de configuração do compilador do TypeScript ficará mais ou menos assim.

```javascript
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
```` 

Dica: Para encontrar rapidamente: `Control + F` e coloque `outDir`.


# Configurando o `package.jon`
Adicione o seguinte script ao seu package.json

```javascript
 "scripts": {
  "dev": "npx nodemon src/app.ts"
}
```` 
E o seu codigo ficara assim: 
```javascript
 "scripts": {
    "test": "echo \"Error: no test 
     specified\" && exit 1",
    "dev": "npx nodemon src/app.ts"
  },
```` 


# Criando arquivo inicial do servidor
No arquivo app.ts adicione: 


```javascript
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
```` 

# Iniciando o Servidor
Para iniciar, você  devera digitar o seguinte comando no Terminal.

```javascript
npm run dev
```` 
Se tudo ocorrer bem, você  verá a mensagem Server running on port 3333 no terminal.

# Testado o servidor

Apos executar o comando anterior, aparecera uma opção de execução e você  devera clicar no botao `Abrir no navegador`.
E você  vera a mensagem `Hello World`

# Configurando Banco de dados
Crie um arquivo `database.ts` dentro da pasta `src` e adicione o seguinte código.

```javascript
import { Database, open } from 'sqlite';
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
}
```` 

# Adicionando o banco de dados
No antigo arquivo `app.ts` troque todo o codigo por este

```javascript
import express from 'express';
import cors from 'cors';
import { connect } from './database';

const port = 3333;
const app = express();

app.use(cors());
app.use(express.json());

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.post('/users', async (req, res) => {
  const db = await connect();
  const { name, email } = req.body;

  const result = await db.run('INSERT INTO users (name, email) VALUES (?, ?)', [name, email]);
  const user = await db.get('SELECT * FROM users WHERE id = ?', [result.lastID]);

  res.json(user);
});

app.get('/users', async (req, res) => {
  const db = await connect();
  const users = await db.all('SELECT * FROM users');

  res.json(users);
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
````

# Testando a inserção de dados

Crie um arquivo chamada `teste.http` e adicione o seguinte codigo

```javascript
POST https://musical-space-tribble-7697wjv5qj63p4qr-3333.app.github.dev/users HTTP/1.1
Content-Type: application/json
Authorization: token xxx

{
  "name": "John Doe",
  "email": "john@example.com"
}

````

Se tudo ocorreu bem, voce abrira o link da primeira linha e aparecera o seguinte resultado
````javascript
{
  "id": 1,
  "name": "John Doe",
  "email": "
}
````




## Titulo

- Lista1
- Lista2

```javascript
const oi = 0102
```` 

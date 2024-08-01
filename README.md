# 2024-IA22-2TRI

Programação II

Vamos programar nosso primeiro servidor com rotas!! :)

# Iniciando um Repositório  no CodeSpace
Crie um Repositório  com o nome "2024-IA24".

Lembrando: O Repositório  deve ser publico e com o arquivo README incluso.

Agora, voce ira clicar no botao `code` e depois em `codespace on main` e espere aparecer a logo do github para continuar os proximos passos. 

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
` "outDir": "./dist" `, e adicione embaixo a linha ` "rootDir": "./src"`, seu arquivo de configuração do compilador do TypeScript ficará mais ou menos assim.

Dica: Para encontrar rapidamente: `Control + F` e coloque `outDir`.

Lembrando: Cuidado com as ""  para nao ter nenhum erro.

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




# Configurando o `package.jon`
Adicione o seguinte dentro de script(**Que voce pode encontar utilizando Control+F**) no seu package.json

!Cuidado para nao adicionar ao arquivo `package-lock-json`

```javascript
  "dev": "npx nodemon src/app.ts"
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
No arquivo app.ts (que esta dentro da `pasta src` ) adicione: 


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
Para iniciar, você  devera instalar a biblioteca **REST Client**, para fazer isso clique no icone que parece um cubo que esta sendo desmontado e pesquise `REST client` e clique em instalar. Aguarde a instalaçao para seguir o proximo passo.

Agora para ver sua pagina, digite o seguinte comando no Terminal.

```javascript
npm run dev
```` 
Se tudo ocorrer bem, você  verá a mensagem Server running on port 3333 no terminal.

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
No antigo arquivo `app.ts` troque **todo** o codigo por este

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

Crie um arquivo chamada `teste.http` **fora da pasta src** e adicione o seguinte codigo

```javascript
POST (>>>>>>>>>>SEU LINK /users<<<<<<<<<<) HTTP/1.1
Content-Type: application/json
Authorization: token xxx

{
  "name": "John Doe",
  "email": "john@example.com"
}

````

Ao lado de `terminal` tera a opção portas (tambem pode estar dentro dos 3 pontinhos :))

Clique em `Portas` e aparecera a sua porta, com o endereco e a visibilidade.

Com o ponteiro do mouse encima do numero da porta(3333), clique com o botao direito e encontre a opção `Visibilidade da porta`. Troque de **Private** para **Public**

Agora clique no link (Endereço Encaminhado) e o copie. Após copiado, substitua no codigo anterior onde esta escrito **>SEU LINK<**

No final do seu link, lembre de manter no final `/users`.

Seu codigo ficara mais ou menos assim

````javascript
POST https://meusite/users HTTP/1.1
Content-Type: application/json
Authorization: token xxx

{
  "name": "John Doe",
  "email": "john@example.com"
}

````
Agora, clique na opção **'Send Request'** que aparecera encima da primeira linha em **Post**.

Se tudo ocorreu bem, voce abrira o link da primeira linha e aparecera o seguinte resultado
````javascript
{
  "id": 1,
  "name": "John Doe",
  "email": "
}
````

## Vamos aprendar mais um pouco de bd!

Alem da requisição Post ( que envia os dados para o servidor) tambem temos o Update( que atualiza os dados) e Delete ( que apaga os dados).

# Atualize ``teste.http``
Ele ficará assim


```javascript
POST (>>>>>>>>>>SEU LINK /users<<<<<<<<<<) HTTP/1.1
Content-Type: application/json
Authorization: token xxx

{
  "name": "John Doe",
  "email": "john@example.com"
}
####

PUT  (>>>>>>>>>>SEU LINK /users/1<<<<<<<<<<) HTTP/1.1
Content-Type: application/json

{
  "name": "John Doe update",
  "email": "john@example.com"
}

####

DELETE  (>>>>>>>>>>SEU LINK /users/1<<<<<<<<<<) HTTP/1.1


```` 
Tambem alteraremos ``app.ts``

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

app.put('/users/:id', async (req, res) => {
  const db = await connect();
  const { name, email } = req.body;
  const { id } = req.params;

  await db.run('UPDATE users SET name = ?, email = ? WHERE id = ?', [name, email, id]);
  const user = await db.get('SELECT * FROM users WHERE id = ?', [id]);

  res.json(user);
});

app.delete('/users/:id', async (req, res) => {
  const db = await connect();
  const { id } = req.params;

  await db.run('DELETE FROM users WHERE id = ?', [id]);

  res.json({ message: 'User deleted' });
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```` 

## Titulo

- Lista1
- Lista2

```javascript
const oi = 0102
```` 

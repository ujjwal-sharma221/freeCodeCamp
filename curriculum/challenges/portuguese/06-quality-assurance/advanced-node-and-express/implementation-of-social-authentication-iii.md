---
id: 589a8eb3f9fc0f352b528e72
title: Implementar a autenticação social III
challengeType: 2
forumTopicId: 301558
dashedName: implementation-of-social-authentication-iii
---

# --description--

A parte final da estratégia é tratar do perfil retornado do GitHub. Precisamos carregar o banco de dados do usuário, se ele existir, ou criar um, se não existir. Além disso, temos que preencher os campos do perfil e, em seguida, retornar o objeto do usuário. O GitHub nos fornece um *id* único dentro de cada perfil que podemos usar para pesquisar e para serializar o usuário (já implementado). Abaixo, vemos um exemplo de implementação que você pode usar em seu projeto. Ele vai dentro da função, que é o segundo argumento para a nova estratégia, logo abaixo onde `console.log(profile);` está atualmente:

```js
myDataBase.findOneAndUpdate(
  { id: profile.id },
  {
    $setOnInsert: {
      id: profile.id,
      username: profile.username,
      name: profile.displayName || 'John Doe',
      photo: profile.photos[0].value || '',
      email: Array.isArray(profile.emails)
        ? profile.emails[0].value
        : 'No public email',
      created_on: new Date(),
      provider: profile.provider || ''
    },
    $set: {
      last_login: new Date()
    },
    $inc: {
      login_count: 1
    }
  },
  { upsert: true, new: true },
  (err, doc) => {
    return cb(null, doc.value);
  }
);
```

`findOneAndUpdate` permite pesquisar um objeto e atualizá-lo. Se o objeto não existir, ele será inserido e disponibilizado para a função de callback. Neste exemplo, sempre definimos o `last_login`, incrementamos a `login_count` em `1` e somente preenchemos a maioria dos campos quando um novo objeto (novo usuário) for inserido. Observe o uso dos valores padrão. Às vezes, um perfil retornado não terá todas as informações preenchidas. O usuário também pode mantê-las privadas. Neste caso, você faz o tratamento para evitar um erro.

You should be able to login to your app now. Try it!

Envie sua página quando você achar que ela está certa. If you're running into errors, you can <a href="https://forum.freecodecamp.org/t/advanced-node-and-express/567135#implementation-of-social-authentication-iii-5" target="_blank" rel="noopener noreferrer nofollow">check out the project completed up to this point</a>.

# --hints--

A configuração da estratégia do GitHub deve estar concluída.

```js
async (getUserInput) => {
  const url = new URL("/_api/auth.js", getUserInput("url"));
  const res = await fetch(url);
  const data = await res.text();
  assert.match(
    data,
    /GitHubStrategy[^]*myDataBase/gi,
    'Strategy should use now use the database to search for the user'
  );
  assert.match(
    data,
    /GitHubStrategy[^]*return cb/gi,
    'Strategy should return the callback function "cb"'
  );
}
```

# --solutions--

```js
/**
  Backend challenges don't need solutions, 
  because they would need to be tested against a full working project. 
  Please check our contributing guidelines to learn more.
*/
```

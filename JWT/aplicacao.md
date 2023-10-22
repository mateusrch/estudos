# Uso

## estrutura bácisa

```

jwt.sign(payload, secretOrPrivateKey, [options, fnCallback])

```

O **payload** pode ser um objeto literal, buffer ou string representando JSON válido. Se payload não for um buffer ou uma string, ele será forçado a se transformar em uma string usando JSON.stringify.

**LEMBRANDO**: _**O payload em um JWT não é criptografado**_, e as informações nele contidas podem ser lidas por qualquer pessoa que tenha acesso ao token. Portanto, informações confidenciais não devem ser armazenadas no payload. **A segurança do JWT é garantida pela terceira parte**, **a assinatura**, que verifica a autenticidade do token e se as informações nele não foram adulteradas.

Em **options** temos várias opções para utilizar vou citar algumas:

- algorithm: (padrão HS256)
- expiresIn: é o tempo que o token levará para expirar:
  - um valor numérico é interpretado como uma contagem de segundos.
  - uma string, certifique-se de fornecer as unidades de tempo ("10h", "7d", etc.), caso contrário, a unidade de milissegundos será usada por padrão ( "120"é igual a "120ms").

#### | Exemplo 1 usando options:

```JS
import jwt from 'jsonwebtoken';

const privateKey = 'suaChaveSecreta';

const token = jwt.sing({id: user.id}, privateKey, {expiresIn: '1h'});

```

No exemplo acima no playload foi passado o id do usuario pois como o payload é público não posso passar dados sensíveis, em seguida foi passado minha chave secreta e por final em options foi passado o tempo em que o token irá expirar.

#### | Exemplo 2 usando options:

```JS
import jwt from 'jsonwebtoken';

const myPrivateKey = 'suaChaveSecreta';

const token = jwt.sing({id: user.id, name: user.name}, myPrivateKey, {expiresIn: '1h',  issuer: 'suaAplicacao'});
```

Nesse segundo exemplo foi adicionado a propriadade "**issuer**" que é uma das reivindicações padrão (claims) que podem ser incluídas no payload de um JWT. Ele é usado para especificar quem emitiu o token, ou seja, qual aplicação ou serviço gerou o token.

A reivindicação "_issuer_" é útil para verificar a origem do token e garantir a autenticidade. Por exemplo, quando um sistema recebe um token JWT, ele pode verificar se o emissor (issuer) é confiável antes de aceitar o token como válido.

#### | Exemplo usando callback:

```JS
import jwt from 'jsonwebtoken';

const myPrivateKey = 'suaChaveSecreta';
const payload = { id: user.id, name: user.name }
const options = { expiresIn: '1h',  issuer: 'suaAplicacao' }

const token = jwt.sing(payload, myPrivateKey, options, (error, token) =>
  error ? error.message : token);
```

Neste exemplo, usei uma função callback para tratar possíveis erros durante a criação do token e para receber o token gerado com sucesso. A função callback é chamada de forma assíncrona após a assinatura do token.

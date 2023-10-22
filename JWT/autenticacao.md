## O que é JSON Web Tokens?

JSON Web Token(JWT) é um padrão aberto [RFC 7519](https://datatracker.ietf.org/doc/html/rfc7519) que define uma maneira compacta e independente de transmitir informações com segurança
entre as partes como um objeto JSON.

Essas informações podem ser verificadas e confiáveis porque são assinadas digitalmente.
Os JWTs podem ser assinados usando um segredo(com o algoritmo **HMAC**) ou um par de chaves pública/privada usando **RSA** ou **ECDSA**.

Os tokens _assinados_ podem verificar a _integridade_ das declarações contidas neles, enquanto os tokens criptografados _ocultam_ essas declarações de outras partes. Quando os tokens são assinados usando pares de chaves pública/privada, a assinatura também certifica que apenas a parte que detém a chave privada é quem a assinou.

### Quando usar JSON Web Tokens ?

**Autorização**: Este é o cenário mais comum para usar JWT. Depois que o usuário estiver logado, cada solicitação subsequente incluirá o JWT, permitino que o usuário acesse rotas, serviços e recursos permitidos com aquele token.

Single Sign On(SSO) é um processo de autenticação e autorização que permite a um usuário acessar várias aplicações ou serviços com um único conjunto de credenciais de login (como nome de usuário e senha). É um recurso que usa amplamente o JWT hoje em dia, devido à sua pequena sobrecarga e à capacidade de ser facilmente usado em diferentes domínios.

**Trooca de informações**: JSON Web Tokens são uma boa maneira de transmitir com segurança entre as partes. Como os JWTs podem ser assinados(usando pares de chaves pública/privada), você pode ter certeza de que os remetentes são quem dizem ser. Além disso, como a assinatura é calculada usando o cabeçalho e a carga útil, vocÊ também pode verificar se o conteúdo não foi adulterado.

### Qual é a estrutura do JWT?

Em sua forma compacta, JSON Web Tokens consistem em três partes separadas por pontos ( .), que são:

- Cabeçalho
- Carga útil
- Assinatura

Portanto, um JWT normalmente se parece com o seguinte.  
<span style="color:red;">xxxxx.yyyyy.zzzzz</span>

#### Cabeçalho

O cabeçalho _normalmente_ consiste em duas partes: o _tipo_ de token, que é JWT, e o algoritmo de assinatura usado, como **HMAC SHA256** ou **RSA**.

```JSON
{
  "alg": "HS256",
  "typ": "JWT"
}
```

Então, este JSON é codificado em Base64Url para formar a primeira parte do JWT.

#### Carga útil

A segunda parte do token é a carga útil, que contém as declarações. As declarações são declarações sobre uma entidade (normalmente, o usuário) e dados adicionais. Existem três tipos de reivindicações: _reivindicações registradas_ , _públicas_ e _privadas_.

- **Declarações registradas**: são um conjunto de declarações predefinidas que não são obrigatórias, mas recomendadas, para fornecer um conjunto de declarações úteis e interoperáveis. Alguns deles são: **iss** (emissor), **exp** (tempo de expiração), **sub** (assunto), **aud** (público) e [outros](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1).

<p align="center">
  Observe que os nomes das declarações têm apenas três caracteres, pois o JWT deve ser compacto.
</p>

- **Declarações públicas** : podem ser definidas à vontade por aqueles que usam JWTs. Mas, para evitar colisões, eles devem ser definidos no IANA JSON Web Token Registry ou como um URI que contém um namespace resistente a colisões.

```JSON
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

A carga útil é então codificada em Base64Url para formar a segunda parte do JSON Web Token.

<p align="center">
  Observe que, para tokens assinados, essas informações, embora protegidas contra adulteração, podem ser lidas por qualquer pessoa. Não coloque informações secretas na carga útil ou nos elementos de cabeçalho de um JWT, a menos que estejam criptografadas.
</p>

- #### Assinatura

Para criar a parte da assinatura, você deve pegar o cabeçalho codificado, a carga útil codificada, um segredo, o algoritmo especificado no cabeçalho e assiná-lo.

Por exemplo, se você quiser usar o algoritmo HMAC SHA256, a assinatura será criada da seguinte forma:

```
  HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

A assinatura é usada para verificar se a mensagem não foi alterada ao longo do caminho e, no caso de tokens assinados com chave privada, também pode verificar se o remetente do JWT é quem diz ser.

### Juntando tudo

A saída são três strings de URL Base64 separadas por pontos que podem ser facilmente passadas em ambientes HTML e HTTP, sendo mais compactas quando comparadas a padrões baseados em XML, como SAML.

O exemplo a seguir mostra um JWT que possui o cabeçalho e a carga anteriores codificados e é assinado com um segredo.

<span style="color: red;">ofdnosdvjhosduhvb.</span><span style="color:pink;">293847wkdho8er7esdkm.</span><span style="color:lightblue;">098ldkjfSJKDHKADSJ</span>

### Como funcionam os tokens JSON Web?

Na autenticação, quando o usuário fizer login com sucesso usando suas credenciais, um JSON Web Token será retornado. Como os tokens são credenciais, deve-se tomar muito cuidado para evitar problemas de segurança. Em geral, você não deve manter os tokens por mais tempo do que o necessário.

Você também não [deve armazenar dados confidenciais da sessão no armazenamento do navegador devido à falta de segurança](https://cheatsheetseries.owasp.org/cheatsheets/HTML5_Security_Cheat_Sheet.html#local-storage).

Sempre que o usuário desejar acessar uma rota ou recurso protegido, o agente do usuário deverá enviar o JWT, normalmente no cabeçalho Authorization usando o esquema Bearer . O conteúdo do cabeçalho deve ser semelhante ao seguinte:

```
Authorization: Bearer <token>
```

Isto pode ser, em certos casos, um mecanismo de autorização apátrida. As rotas protegidas do servidor verificarão um JWT válido no _**Authorization**_ cabeçalho e, se estiver presente, o usuário terá permissão para acessar recursos protegidos. Se o JWT contiver os dados necessários, a necessidade de consultar o banco de dados para determinadas operações poderá ser reduzida, embora nem sempre seja esse o caso.

Observe que se você enviar tokens JWT por meio de cabeçalhos HTTP, tente evitar que eles fiquem muito grandes. Alguns servidores não aceitam mais de 8 KB em cabeçalhos. Se você estiver tentando incorporar muitas informações em um token JWT, incluindo todas as permissões do usuário, poderá precisar de uma solução alternativa, como Auth0 Fine-Grained Authorization.

Se o token for enviado no Authorizationcabeçalho, o Cross-Origin Resource Sharing (CORS) não será um problema, pois não usa cookies.

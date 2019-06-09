## JwtUpickle Object

### Basic usage

```scala
scala> import java.time.Instant
import java.time.Instant

scala> import upickle.default._
import upickle.default._

scala> import pdi.jwt.{JwtUpickle, JwtAlgorithm, JwtClaim}
import pdi.jwt.{JwtUpickle, JwtAlgorithm, JwtClaim}

scala> val claim = JwtClaim(
     |   expiration = Some(Instant.now.plusSeconds(157784760).getEpochSecond),
     |   issuedAt = Some(Instant.now.getEpochSecond)
     | )
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@d1c466f0

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtUpickle.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3MTc4Nzk0MTYsImlhdCI6MTU2MDA5NDY1Nn0.1SRjUdaS9a6Jm82EJr88OUEH2j4oaOvR--GMaz1bh9Q

scala> JwtUpickle.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res0: scala.util.Try[ujson.Value] = Success({"exp":1717879416,"iat":1560094656})

scala> JwtUpickle.decode(token, key, Seq(JwtAlgorithm.HS256))
res1: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@d1c466f0)
```

### Encoding

```scala
scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val claimJson = read[ujson.Value](s"""{"expires":${Instant.now.getEpochSecond}}""")
claimJson: ujson.Value = {"expires":1560094656}

scala> val header = read[ujson.Value]( """{"typ":"JWT","alg":"HS256"}""")
header: ujson.Value = {"typ":"JWT","alg":"HS256"}

scala> // From just the claim to all possible attributes
     | JwtUpickle.encode(claimJson)
res3: String = eyJhbGciOiJub25lIn0.eyJleHBpcmVzIjoxNTYwMDk0NjU2fQ.

scala> JwtUpickle.encode(claimJson, key, algo)
res4: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHBpcmVzIjoxNTYwMDk0NjU2fQ.xi1LsriJyobdxIcwAPXO-w8In0R5ukUqzXp1mxlgn1Y

scala> JwtUpickle.encode(header, claimJson, key)
res5: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHBpcmVzIjoxNTYwMDk0NjU2fQ.xi1LsriJyobdxIcwAPXO-w8In0R5ukUqzXp1mxlgn1Y
```

### Decoding

```scala
scala> val claim = JwtClaim(
     |   expiration = Some(Instant.now.plusSeconds(157784760).getEpochSecond),
     |   issuedAt = Some(Instant.now.getEpochSecond)
     | )
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@d913818c

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtUpickle.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3MTc4Nzk0MTcsImlhdCI6MTU2MDA5NDY1N30.9te5ISsUrbmLHLNFIbgEv9GlasN1Bwz8bG3toPbjBfc

scala> // You can decode to JsObject
     | JwtUpickle.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res7: scala.util.Try[ujson.Value] = Success({"exp":1717879417,"iat":1560094657})

scala> JwtUpickle.decodeJsonAll(token, key, Seq(JwtAlgorithm.HS256))
res8: scala.util.Try[(ujson.Value, ujson.Value, String)] = Success(({"typ":"JWT","alg":"HS256"},{"exp":1717879417,"iat":1560094657},9te5ISsUrbmLHLNFIbgEv9GlasN1Bwz8bG3toPbjBfc))

scala> // Or to case classes
     | JwtUpickle.decode(token, key, Seq(JwtAlgorithm.HS256))
res10: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@d913818c)

scala> JwtUpickle.decodeAll(token, key, Seq(JwtAlgorithm.HS256))
res11: scala.util.Try[(pdi.jwt.JwtHeader, pdi.jwt.JwtClaim, String)] = Success((pdi.jwt.JwtHeader@ac020068,pdi.jwt.JwtClaim@d913818c,9te5ISsUrbmLHLNFIbgEv9GlasN1Bwz8bG3toPbjBfc))
```

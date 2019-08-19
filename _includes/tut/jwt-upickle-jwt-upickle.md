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
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@2de72671

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtUpickle.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3MjQwMjk4NjksImlhdCI6MTU2NjI0NTEwOX0.Q-ggOZ7WWrrnXgbRmcGd6r660CJKQOn4fqvsCBoxfNs

scala> JwtUpickle.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res0: scala.util.Try[ujson.Value] = Success({"exp":1724029869,"iat":1566245109})

scala> JwtUpickle.decode(token, key, Seq(JwtAlgorithm.HS256))
res1: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@2de72671)
```

### Encoding

```scala
scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val claimJson = read[ujson.Value](s"""{"expires":${Instant.now.getEpochSecond}}""")
claimJson: ujson.Value = {"expires":1566245110}

scala> val header = read[ujson.Value]( """{"typ":"JWT","alg":"HS256"}""")
header: ujson.Value = {"typ":"JWT","alg":"HS256"}

scala> // From just the claim to all possible attributes
     | JwtUpickle.encode(claimJson)
res3: String = eyJhbGciOiJub25lIn0.eyJleHBpcmVzIjoxNTY2MjQ1MTEwfQ.

scala> JwtUpickle.encode(claimJson, key, algo)
res4: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHBpcmVzIjoxNTY2MjQ1MTEwfQ.TKGGuZCd9L_kS9imkD3T-JXMNcBKsrpgXJaGMpR3fhs

scala> JwtUpickle.encode(header, claimJson, key)
res5: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHBpcmVzIjoxNTY2MjQ1MTEwfQ.TKGGuZCd9L_kS9imkD3T-JXMNcBKsrpgXJaGMpR3fhs
```

### Decoding

```scala
scala> val claim = JwtClaim(
     |   expiration = Some(Instant.now.plusSeconds(157784760).getEpochSecond),
     |   issuedAt = Some(Instant.now.getEpochSecond)
     | )
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@8cd04acb

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtUpickle.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3MjQwMjk4NzAsImlhdCI6MTU2NjI0NTExMH0.JgAPUZeYzLeqsv-cwTBCwKTFK2A3HRWQnJMFldESU_Q

scala> // You can decode to JsObject
     | JwtUpickle.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res7: scala.util.Try[ujson.Value] = Success({"exp":1724029870,"iat":1566245110})

scala> JwtUpickle.decodeJsonAll(token, key, Seq(JwtAlgorithm.HS256))
res8: scala.util.Try[(ujson.Value, ujson.Value, String)] = Success(({"typ":"JWT","alg":"HS256"},{"exp":1724029870,"iat":1566245110},JgAPUZeYzLeqsv-cwTBCwKTFK2A3HRWQnJMFldESU_Q))

scala> // Or to case classes
     | JwtUpickle.decode(token, key, Seq(JwtAlgorithm.HS256))
res10: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@8cd04acb)

scala> JwtUpickle.decodeAll(token, key, Seq(JwtAlgorithm.HS256))
res11: scala.util.Try[(pdi.jwt.JwtHeader, pdi.jwt.JwtClaim, String)] = Success((pdi.jwt.JwtHeader@ac020068,pdi.jwt.JwtClaim@8cd04acb,JgAPUZeYzLeqsv-cwTBCwKTFK2A3HRWQnJMFldESU_Q))
```

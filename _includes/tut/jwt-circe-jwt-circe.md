## JwtCirce Object

### Basic usage

```scala
scala> import java.time.Instant
import java.time.Instant

scala> import pdi.jwt.{JwtCirce, JwtAlgorithm, JwtClaim}
import pdi.jwt.{JwtCirce, JwtAlgorithm, JwtClaim}

scala> val claim = JwtClaim(
     |     expiration = Some(Instant.now.plusSeconds(157784760).getEpochSecond)
     |   , issuedAt = Some(Instant.now.getEpochSecond)
     | )
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@fc2d4491

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtCirce.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3MjQwMjk4NzMsImlhdCI6MTU2NjI0NTExM30.mZPrWB9HXcPZubPlA57ao5nLUhei4CjwuPGjdRquz0c

scala> JwtCirce.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res0: scala.util.Try[io.circe.Json] =
Success({
  "exp" : 1724029873,
  "iat" : 1566245113
})

scala> JwtCirce.decode(token, key, Seq(JwtAlgorithm.HS256))
res1: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@fc2d4491)
```

### Encoding

```scala
scala> import java.time.Instant
import java.time.Instant

scala> import cats.syntax.either._
import cats.syntax.either._

scala> import io.circe._, syntax._, jawn.{parse => jawnParse}
import io.circe._
import syntax._
import jawn.{parse=>jawnParse}

scala> import pdi.jwt.{JwtCirce, JwtAlgorithm, JwtClaim}
import pdi.jwt.{JwtCirce, JwtAlgorithm, JwtClaim}

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val Right(claimJson) = jawnParse(s"""{"expires":${Instant.now.getEpochSecond}}""")
claimJson: io.circe.Json =
{
  "expires" : 1566245114
}

scala> val Right(header) = jawnParse( """{"typ":"JWT","alg":"HS256"}""")
header: io.circe.Json =
{
  "typ" : "JWT",
  "alg" : "HS256"
}

scala> // From just the claim to all possible attributes
     | JwtCirce.encode(claimJson)
res3: String = eyJhbGciOiJub25lIn0.eyJleHBpcmVzIjoxNTY2MjQ1MTE0fQ.

scala> JwtCirce.encode(claimJson, key, algo)
res4: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHBpcmVzIjoxNTY2MjQ1MTE0fQ.CYzh98E8ttXRoe41lF2YctchjTX1Nqg8b1PrZMWOpE4

scala> JwtCirce.encode(header, claimJson, key)
res5: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHBpcmVzIjoxNTY2MjQ1MTE0fQ.CYzh98E8ttXRoe41lF2YctchjTX1Nqg8b1PrZMWOpE4
```

### Decoding

```scala
scala> import java.time.Instant
import java.time.Instant

scala> import pdi.jwt.{JwtCirce, JwtAlgorithm, JwtClaim}
import pdi.jwt.{JwtCirce, JwtAlgorithm, JwtClaim}

scala> val claim = JwtClaim(
     |     expiration = Some(Instant.now.plusSeconds(157784760).getEpochSecond)
     |   , issuedAt = Some(Instant.now.getEpochSecond)
     | )
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@bd5450a6

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtCirce.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3MjQwMjk4NzUsImlhdCI6MTU2NjI0NTExNX0.ThsWiMIx93MYN8kIMBgUa520rN4meWwAWd4PLndc_PE

scala> // You can decode to JsObject
     | JwtCirce.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res7: scala.util.Try[io.circe.Json] =
Success({
  "exp" : 1724029875,
  "iat" : 1566245115
})

scala> JwtCirce.decodeJsonAll(token, key, Seq(JwtAlgorithm.HS256))
res8: scala.util.Try[(io.circe.Json, io.circe.Json, String)] =
Success(({
  "typ" : "JWT",
  "alg" : "HS256"
},{
  "exp" : 1724029875,
  "iat" : 1566245115
},ThsWiMIx93MYN8kIMBgUa520rN4meWwAWd4PLndc_PE))

scala> // Or to case classes
     | JwtCirce.decode(token, key, Seq(JwtAlgorithm.HS256))
res10: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@bd5450a6)

scala> JwtCirce.decodeAll(token, key, Seq(JwtAlgorithm.HS256))
res11: scala.util.Try[(pdi.jwt.JwtHeader, pdi.jwt.JwtClaim, String)] = Success((pdi.jwt.JwtHeader@ac020068,pdi.jwt.JwtClaim@bd5450a6,ThsWiMIx93MYN8kIMBgUa520rN4meWwAWd4PLndc_PE))
```

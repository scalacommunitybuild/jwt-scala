## JwtSprayJson Object

### Basic usage

```scala
scala> import java.time.Instant
import java.time.Instant

scala> import pdi.jwt.{JwtSprayJson, JwtAlgorithm, JwtClaim}
import pdi.jwt.{JwtSprayJson, JwtAlgorithm, JwtClaim}

scala> val claim = JwtClaim(
     |     expiration = Some(Instant.now.plusSeconds(157784760).getEpochSecond),
     |     issuedAt = Some(Instant.now.getEpochSecond)
     | )
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@9f3696eb

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtSprayJson.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3MTk2OTc5MzgsImlhdCI6MTU2MTkxMzE3OH0.7orfCyjb0JlazI4p-Y92OVAAi0iMn9mIDMwkrNmELZo

scala> JwtSprayJson.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res0: scala.util.Try[spray.json.JsObject] = Success({"exp":1719697938,"iat":1561913178})

scala> JwtSprayJson.decode(token, key, Seq(JwtAlgorithm.HS256))
res1: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@9f3696eb)
```

### Encoding

```scala
scala> import java.time.Instant
import java.time.Instant

scala> import spray.json._
import spray.json._

scala> import pdi.jwt.{JwtSprayJson, JwtAlgorithm, JwtClaim}
import pdi.jwt.{JwtSprayJson, JwtAlgorithm, JwtClaim}

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val claimJson = s"""{"expires":${Instant.now.getEpochSecond}}""".parseJson.asJsObject
claimJson: spray.json.JsObject = {"expires":1561913180}

scala> val header = """{"typ":"JWT","alg":"HS256"}""".parseJson.asJsObject
header: spray.json.JsObject = {"alg":"HS256","typ":"JWT"}

scala> // From just the claim to all possible attributes
     | JwtSprayJson.encode(claimJson)
res3: String = eyJhbGciOiJub25lIn0.eyJleHBpcmVzIjoxNTYxOTEzMTgwfQ.

scala> JwtSprayJson.encode(claimJson, key, algo)
res4: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHBpcmVzIjoxNTYxOTEzMTgwfQ.cp8Fm8Z9d5YjsuGvt8wZClJz50nZaUltYdKl04HVqLc

scala> JwtSprayJson.encode(header, claimJson, key)
res5: String = eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHBpcmVzIjoxNTYxOTEzMTgwfQ.I2XpPv-Ddg7PNBZGi4iml-00RhrbQ4W54niSfj3l0-4
```

### Decoding

```scala
scala> import java.time.Instant
import java.time.Instant

scala> import pdi.jwt.{JwtSprayJson, JwtAlgorithm, JwtClaim}
import pdi.jwt.{JwtSprayJson, JwtAlgorithm, JwtClaim}

scala> val claim = JwtClaim(
     |     expiration = Some(Instant.now.plusSeconds(157784760).getEpochSecond),
     |     issuedAt = Some(Instant.now.getEpochSecond)
     | )
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@db87b548

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtSprayJson.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3MTk2OTc5NDAsImlhdCI6MTU2MTkxMzE4MH0.Mk__LjVJAN5nlxkG_dT-W_DjL7muOjbWB943VdtcXts

scala> // You can decode to JsObject
     | JwtSprayJson.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res7: scala.util.Try[spray.json.JsObject] = Success({"exp":1719697940,"iat":1561913180})

scala> JwtSprayJson.decodeJsonAll(token, key, Seq(JwtAlgorithm.HS256))
res8: scala.util.Try[(spray.json.JsObject, spray.json.JsObject, String)] = Success(({"alg":"HS256","typ":"JWT"},{"exp":1719697940,"iat":1561913180},Mk__LjVJAN5nlxkG_dT-W_DjL7muOjbWB943VdtcXts))

scala> // Or to case classes
     | JwtSprayJson.decode(token, key, Seq(JwtAlgorithm.HS256))
res10: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@db87b548)

scala> JwtSprayJson.decodeAll(token, key, Seq(JwtAlgorithm.HS256))
res11: scala.util.Try[(pdi.jwt.JwtHeader, pdi.jwt.JwtClaim, String)] = Success((pdi.jwt.JwtHeader@ac020068,pdi.jwt.JwtClaim@db87b548,Mk__LjVJAN5nlxkG_dT-W_DjL7muOjbWB943VdtcXts))
```

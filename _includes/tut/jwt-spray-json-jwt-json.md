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
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@408918e5

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtSprayJson.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3MDg3ODkxMzgsImlhdCI6MTU1MTAwNDM3OH0.eaLWeqGq_lUo8jcrw0aNJUs8rXoCnIp3z_qw4kNsoVg

scala> JwtSprayJson.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res0: scala.util.Try[spray.json.JsObject] = Success({"exp":1708789138,"iat":1551004378})

scala> JwtSprayJson.decode(token, key, Seq(JwtAlgorithm.HS256))
res1: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@408918e5)
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
claimJson: spray.json.JsObject = {"expires":1551004379}

scala> val header = """{"typ":"JWT","alg":"HS256"}""".parseJson.asJsObject
header: spray.json.JsObject = {"typ":"JWT","alg":"HS256"}

scala> // From just the claim to all possible attributes
     | JwtSprayJson.encode(claimJson)
res3: String = eyJhbGciOiJub25lIn0.eyJleHBpcmVzIjoxNTUxMDA0Mzc5fQ.

scala> JwtSprayJson.encode(claimJson, key, algo)
res4: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHBpcmVzIjoxNTUxMDA0Mzc5fQ.XJ7mvbk4mAqH9hLeCGeHzMvLar2IH9KBNGNLDnBfn_o

scala> JwtSprayJson.encode(header, claimJson, key)
res5: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHBpcmVzIjoxNTUxMDA0Mzc5fQ.XJ7mvbk4mAqH9hLeCGeHzMvLar2IH9KBNGNLDnBfn_o
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
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@32af3fd1

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtSprayJson.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3MDg3ODkxMzksImlhdCI6MTU1MTAwNDM3OX0.Qh-dCSssOrWAGVGLa6QW4rwF__PnFGFWpIxJnvNYODo

scala> // You can decode to JsObject
     | JwtSprayJson.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res7: scala.util.Try[spray.json.JsObject] = Success({"exp":1708789139,"iat":1551004379})

scala> JwtSprayJson.decodeJsonAll(token, key, Seq(JwtAlgorithm.HS256))
res8: scala.util.Try[(spray.json.JsObject, spray.json.JsObject, String)] = Success(({"typ":"JWT","alg":"HS256"},{"exp":1708789139,"iat":1551004379},Qh-dCSssOrWAGVGLa6QW4rwF__PnFGFWpIxJnvNYODo))

scala> // Or to case classes
     | JwtSprayJson.decode(token, key, Seq(JwtAlgorithm.HS256))
res10: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@32af3fd1)

scala> JwtSprayJson.decodeAll(token, key, Seq(JwtAlgorithm.HS256))
res11: scala.util.Try[(pdi.jwt.JwtHeader, pdi.jwt.JwtClaim, String)] = Success((pdi.jwt.JwtHeader@ac020068,pdi.jwt.JwtClaim@32af3fd1,Qh-dCSssOrWAGVGLa6QW4rwF__PnFGFWpIxJnvNYODo))
```

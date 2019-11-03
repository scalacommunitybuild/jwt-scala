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
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@eb950d42

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtSprayJson.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3MzA1OTMzNjksImlhdCI6MTU3MjgwODYwOX0.aDFrdrsCMzRXbTvoKg2wqNAA8bFjb4stQkkddOyDSVM

scala> JwtSprayJson.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res0: scala.util.Try[spray.json.JsObject] = Success({"exp":1730593369,"iat":1572808609})

scala> JwtSprayJson.decode(token, key, Seq(JwtAlgorithm.HS256))
res1: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@eb950d42)
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
claimJson: spray.json.JsObject = {"expires":1572808609}

scala> val header = """{"typ":"JWT","alg":"HS256"}""".parseJson.asJsObject
header: spray.json.JsObject = {"alg":"HS256","typ":"JWT"}

scala> // From just the claim to all possible attributes
     | JwtSprayJson.encode(claimJson)
res3: String = eyJhbGciOiJub25lIn0.eyJleHBpcmVzIjoxNTcyODA4NjA5fQ.

scala> JwtSprayJson.encode(claimJson, key, algo)
res4: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHBpcmVzIjoxNTcyODA4NjA5fQ.kJVYuh1Agw9gcPETspbb_hcLpuWOOgLOFIN0qnSUiFY

scala> JwtSprayJson.encode(header, claimJson, key)
res5: String = eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHBpcmVzIjoxNTcyODA4NjA5fQ.JO2CL2fmwnWKtCtFlzLHXHV2_YhF6FMaWpFHBv8IDUQ
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
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@5a6d55e9

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtSprayJson.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3MzA1OTMzNzAsImlhdCI6MTU3MjgwODYxMH0.OdfwVHggBQ-Xi-4-sW2EOcy-4pTCw400g7ecvvPiZ1s

scala> // You can decode to JsObject
     | JwtSprayJson.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res7: scala.util.Try[spray.json.JsObject] = Success({"exp":1730593370,"iat":1572808610})

scala> JwtSprayJson.decodeJsonAll(token, key, Seq(JwtAlgorithm.HS256))
res8: scala.util.Try[(spray.json.JsObject, spray.json.JsObject, String)] = Success(({"alg":"HS256","typ":"JWT"},{"exp":1730593370,"iat":1572808610},OdfwVHggBQ-Xi-4-sW2EOcy-4pTCw400g7ecvvPiZ1s))

scala> // Or to case classes
     | JwtSprayJson.decode(token, key, Seq(JwtAlgorithm.HS256))
res10: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@5a6d55e9)

scala> JwtSprayJson.decodeAll(token, key, Seq(JwtAlgorithm.HS256))
res11: scala.util.Try[(pdi.jwt.JwtHeader, pdi.jwt.JwtClaim, String)] = Success((pdi.jwt.JwtHeader@ac020068,pdi.jwt.JwtClaim@5a6d55e9,OdfwVHggBQ-Xi-4-sW2EOcy-4pTCw400g7ecvvPiZ1s))
```

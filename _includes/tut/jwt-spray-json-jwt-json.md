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
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@3250b4be

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtSprayJson.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3MjQ2MDQ2NTQsImlhdCI6MTU2NjgxOTg5NH0.bI3tmHEOFvF-HXqc23qKx1Vg3DJrzolNdl4QWip_NCQ

scala> JwtSprayJson.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res0: scala.util.Try[spray.json.JsObject] = Success({"exp":1724604654,"iat":1566819894})

scala> JwtSprayJson.decode(token, key, Seq(JwtAlgorithm.HS256))
res1: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@3250b4be)
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
claimJson: spray.json.JsObject = {"expires":1566819895}

scala> val header = """{"typ":"JWT","alg":"HS256"}""".parseJson.asJsObject
header: spray.json.JsObject = {"alg":"HS256","typ":"JWT"}

scala> // From just the claim to all possible attributes
     | JwtSprayJson.encode(claimJson)
res3: String = eyJhbGciOiJub25lIn0.eyJleHBpcmVzIjoxNTY2ODE5ODk1fQ.

scala> JwtSprayJson.encode(claimJson, key, algo)
res4: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHBpcmVzIjoxNTY2ODE5ODk1fQ.ZcVJqP-ACPUpHrMoRU2V0gRD7-b7WW7Kpk8z3ijWAT0

scala> JwtSprayJson.encode(header, claimJson, key)
res5: String = eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHBpcmVzIjoxNTY2ODE5ODk1fQ.6jMqHVEQR4QDwqb6IQBQcIqBfHnqeCZukaS_iEzwbr8
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
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@397b33da

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtSprayJson.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3MjQ2MDQ2NTUsImlhdCI6MTU2NjgxOTg5NX0.wAy-w6DzpAaeOcny0KK7rOr4MIcRJfpwXBrPEVBI-LM

scala> // You can decode to JsObject
     | JwtSprayJson.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res7: scala.util.Try[spray.json.JsObject] = Success({"exp":1724604655,"iat":1566819895})

scala> JwtSprayJson.decodeJsonAll(token, key, Seq(JwtAlgorithm.HS256))
res8: scala.util.Try[(spray.json.JsObject, spray.json.JsObject, String)] = Success(({"alg":"HS256","typ":"JWT"},{"exp":1724604655,"iat":1566819895},wAy-w6DzpAaeOcny0KK7rOr4MIcRJfpwXBrPEVBI-LM))

scala> // Or to case classes
     | JwtSprayJson.decode(token, key, Seq(JwtAlgorithm.HS256))
res10: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@397b33da)

scala> JwtSprayJson.decodeAll(token, key, Seq(JwtAlgorithm.HS256))
res11: scala.util.Try[(pdi.jwt.JwtHeader, pdi.jwt.JwtClaim, String)] = Success((pdi.jwt.JwtHeader@ac020068,pdi.jwt.JwtClaim@397b33da,wAy-w6DzpAaeOcny0KK7rOr4MIcRJfpwXBrPEVBI-LM))
```

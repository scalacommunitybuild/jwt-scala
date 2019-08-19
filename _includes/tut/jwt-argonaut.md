## JwtArgonaut object

### Basic usage

```scala
scala> import java.time.Instant
import java.time.Instant

scala> import scala.util.Try
import scala.util.Try

scala> import pdi.jwt.{JwtAlgorithm, JwtArgonaut, JwtClaim}
import pdi.jwt.{JwtAlgorithm, JwtArgonaut, JwtClaim}

scala> import argonaut.Json
import argonaut.Json

scala> val claim = JwtClaim(
     |   expiration = Some(Instant.now().plusSeconds(157784760).getEpochSecond),
     |   issuedAt = Some(Instant.now.getEpochSecond)
     | )
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@1949856e

scala> val key = "secretKey"
key: String = secretKey

scala> val alg = JwtAlgorithm.HS512
alg: pdi.jwt.JwtAlgorithm.HS512.type = HS512

scala> val token = JwtArgonaut.encode(claim, key, alg)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHAiOjE3MjQwMjc0ODAsImlhdCI6MTU2NjI0MjcyMH0.GzX2s117sJ4Be0cKk45dlFK4yreRfGx4kcSw_gMfpplHVDUQ2U4KduaN3qqChHOlEe4FBqTOuQQaffCR7Y_vAg

scala> val decodedJson: Try[Json] = JwtArgonaut.decodeJson(token, key, Seq(alg))
decodedJson: scala.util.Try[argonaut.Json] = Success({"exp":1724027480,"iat":1566242720})

scala> val decodedClaim: Try[JwtClaim] = JwtArgonaut.decode(token, key, Seq(alg))
decodedClaim: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@1949856e)
```

### Encoding

```scala
scala> import argonaut.Parse
import argonaut.Parse

scala> import pdi.jwt.{JwtAlgorithm, JwtArgonaut}
import pdi.jwt.{JwtAlgorithm, JwtArgonaut}

scala> val key = "secretKey"
key: String = secretKey

scala> val alg = JwtAlgorithm.HS512
alg: pdi.jwt.JwtAlgorithm.HS512.type = HS512

scala> val jsonClaim = Parse.parseOption(s"""{"expires":${Instant.now().getEpochSecond}}""").get
jsonClaim: argonaut.Json = {"expires":1566242721}

scala> val jsonHeader = Parse.parseOption("""{"typ":"JWT","alg":"HS512"}""").get
jsonHeader: argonaut.Json = {"typ":"JWT","alg":"HS512"}

scala> val token1: String = JwtArgonaut.encode(jsonClaim)
token1: String = eyJhbGciOiJub25lIn0.eyJleHBpcmVzIjoxNTY2MjQyNzIxfQ.

scala> val token2: String = JwtArgonaut.encode(jsonClaim, key, alg)
token2: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHBpcmVzIjoxNTY2MjQyNzIxfQ.AcCvJT2J43pmd-GNB054cILJtEq4Advi_eNgKDdQMqLBrfZuVrKeLsYbps_NBrfMwhf1Xg2DaMyhaOaVg3Dlmw

scala> val token3: String = JwtArgonaut.encode(jsonHeader, jsonClaim, key)
token3: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHBpcmVzIjoxNTY2MjQyNzIxfQ.AcCvJT2J43pmd-GNB054cILJtEq4Advi_eNgKDdQMqLBrfZuVrKeLsYbps_NBrfMwhf1Xg2DaMyhaOaVg3Dlmw
```

### Decoding

```scala
scala> import scala.util.Try
import scala.util.Try

scala> import argonaut.Json
import argonaut.Json

scala> import pdi.jwt.{JwtAlgorithm, JwtArgonaut, JwtClaim, JwtHeader}
import pdi.jwt.{JwtAlgorithm, JwtArgonaut, JwtClaim, JwtHeader}

scala> val claim = JwtClaim(
     |   expiration = Some(Instant.now.plusSeconds(157784760).getEpochSecond),
     |   issuedAt = Some(Instant.now.getEpochSecond)
     | )
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@45992f06

scala> val key = "secretKey"
key: String = secretKey

scala> val alg = JwtAlgorithm.HS512
alg: pdi.jwt.JwtAlgorithm.HS512.type = HS512

scala> val token = JwtArgonaut.encode(claim, key, alg)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHAiOjE3MjQwMjc0ODIsImlhdCI6MTU2NjI0MjcyMn0.ZtfuUgjpcZyBQp84vDlw-VZF08_gLgAYM0hohi1Rlgfv0vpzpfe8NKlamSnb42R_QzaGDwSGZpqgOAG5V1PbjA

scala> val decodedJsonClaim: Try[Json] = JwtArgonaut.decodeJson(token, key, Seq(alg))
decodedJsonClaim: scala.util.Try[argonaut.Json] = Success({"exp":1724027482,"iat":1566242722})

scala> val decodedJson: Try[(Json, Json, String)] = JwtArgonaut.decodeJsonAll(token, key, Seq(alg))
decodedJson: scala.util.Try[(argonaut.Json, argonaut.Json, String)] = Success(({"typ":"JWT","alg":"HS512"},{"exp":1724027482,"iat":1566242722},ZtfuUgjpcZyBQp84vDlw-VZF08_gLgAYM0hohi1Rlgfv0vpzpfe8NKlamSnb42R_QzaGDwSGZpqgOAG5V1PbjA))

scala> val decodedClaim: Try[JwtClaim]  = JwtArgonaut.decode(token, key, Seq(alg))
decodedClaim: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@45992f06)

scala> val decodedToken: Try[(JwtHeader, JwtClaim, String)] = JwtArgonaut.decodeAll(token, key, Seq(alg))
decodedToken: scala.util.Try[(pdi.jwt.JwtHeader, pdi.jwt.JwtClaim, String)] = Success((pdi.jwt.JwtHeader@7e1481b1,pdi.jwt.JwtClaim@45992f06,ZtfuUgjpcZyBQp84vDlw-VZF08_gLgAYM0hohi1Rlgfv0vpzpfe8NKlamSnb42R_QzaGDwSGZpqgOAG5V1PbjA))
```

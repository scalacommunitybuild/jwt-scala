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
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@cc736269

scala> val key = "secretKey"
key: String = secretKey

scala> val alg = JwtAlgorithm.HS512
alg: pdi.jwt.JwtAlgorithm.HS512.type = HS512

scala> val token = JwtArgonaut.encode(claim, key, alg)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHAiOjE3NDA3Njg2MDYsImlhdCI6MTU4Mjk4Mzg0Nn0.E0Rf9cxIchy5DReTkQNo21h1kJ-CyGTdYKhbzN-n_bELm49UjSGMgmWaLcV37y_sEgoLC_fpcF6tzGPXwLCmvQ

scala> val decodedJson: Try[Json] = JwtArgonaut.decodeJson(token, key, Seq(alg))
decodedJson: scala.util.Try[argonaut.Json] = Success({"exp":1740768606,"iat":1582983846})

scala> val decodedClaim: Try[JwtClaim] = JwtArgonaut.decode(token, key, Seq(alg))
decodedClaim: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@cc736269)
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
jsonClaim: argonaut.Json = {"expires":1582983847}

scala> val jsonHeader = Parse.parseOption("""{"typ":"JWT","alg":"HS512"}""").get
jsonHeader: argonaut.Json = {"typ":"JWT","alg":"HS512"}

scala> val token1: String = JwtArgonaut.encode(jsonClaim)
token1: String = eyJhbGciOiJub25lIn0.eyJleHBpcmVzIjoxNTgyOTgzODQ3fQ.

scala> val token2: String = JwtArgonaut.encode(jsonClaim, key, alg)
token2: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHBpcmVzIjoxNTgyOTgzODQ3fQ.NJ1nwjZ8XijR2oJm9NKAs2CiZxFl4c0eOrDLUEti5eUXSwOQrein6g1UeHAd2jqPdTnw9GuRUMxixxZeyYj-hg

scala> val token3: String = JwtArgonaut.encode(jsonHeader, jsonClaim, key)
token3: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHBpcmVzIjoxNTgyOTgzODQ3fQ.NJ1nwjZ8XijR2oJm9NKAs2CiZxFl4c0eOrDLUEti5eUXSwOQrein6g1UeHAd2jqPdTnw9GuRUMxixxZeyYj-hg
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
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@aab1533f

scala> val key = "secretKey"
key: String = secretKey

scala> val alg = JwtAlgorithm.HS512
alg: pdi.jwt.JwtAlgorithm.HS512.type = HS512

scala> val token = JwtArgonaut.encode(claim, key, alg)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHAiOjE3NDA3Njg2MDgsImlhdCI6MTU4Mjk4Mzg0OH0.H_9HL2Jee3LvW73xqC8EN5SulQD_mwQCqX2BDOjPim-rA8Yvoi4iWzTv44HLIgH5r_zrzVuazMKHEFeh56HfPg

scala> val decodedJsonClaim: Try[Json] = JwtArgonaut.decodeJson(token, key, Seq(alg))
decodedJsonClaim: scala.util.Try[argonaut.Json] = Success({"exp":1740768608,"iat":1582983848})

scala> val decodedJson: Try[(Json, Json, String)] = JwtArgonaut.decodeJsonAll(token, key, Seq(alg))
decodedJson: scala.util.Try[(argonaut.Json, argonaut.Json, String)] = Success(({"typ":"JWT","alg":"HS512"},{"exp":1740768608,"iat":1582983848},H_9HL2Jee3LvW73xqC8EN5SulQD_mwQCqX2BDOjPim-rA8Yvoi4iWzTv44HLIgH5r_zrzVuazMKHEFeh56HfPg))

scala> val decodedClaim: Try[JwtClaim]  = JwtArgonaut.decode(token, key, Seq(alg))
decodedClaim: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@aab1533f)

scala> val decodedToken: Try[(JwtHeader, JwtClaim, String)] = JwtArgonaut.decodeAll(token, key, Seq(alg))
decodedToken: scala.util.Try[(pdi.jwt.JwtHeader, pdi.jwt.JwtClaim, String)] = Success((pdi.jwt.JwtHeader@7e1481b1,pdi.jwt.JwtClaim@aab1533f,H_9HL2Jee3LvW73xqC8EN5SulQD_mwQCqX2BDOjPim-rA8Yvoi4iWzTv44HLIgH5r_zrzVuazMKHEFeh56HfPg))
```

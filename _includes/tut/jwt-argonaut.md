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
claim: pdi.jwt.JwtClaim = JwtClaim({},None,None,None,Some(1707805012),None,Some(1550020252),None)

scala> val key = "secretKey"
key: String = secretKey

scala> val alg = JwtAlgorithm.HS512
alg: pdi.jwt.JwtAlgorithm.HS512.type = HS512

scala> val token = JwtArgonaut.encode(claim, key, alg)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHAiOjE3MDc4MDUwMTIsImlhdCI6MTU1MDAyMDI1Mn0.GBCRp1pbZIVk2-gQ1xc8o525x5qOXjco3Ls-A78lr6PUtSE1h2cyj-_PJtNLMy40h3O_qKg0HF84Wp9hVCIwbA

scala> val decodedJson: Try[Json] = JwtArgonaut.decodeJson(token, key, Seq(alg))
decodedJson: scala.util.Try[argonaut.Json] = Success({"exp":1707805012,"iat":1550020252})

scala> val decodedClaim: Try[JwtClaim] = JwtArgonaut.decode(token, key, Seq(alg))
decodedClaim: scala.util.Try[pdi.jwt.JwtClaim] = Success(JwtClaim({},None,None,None,Some(1707805012),None,Some(1550020252),None))
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
jsonClaim: argonaut.Json = {"expires":1550020253}

scala> val jsonHeader = Parse.parseOption("""{"typ":"JWT","alg":"HS512"}""").get
jsonHeader: argonaut.Json = {"typ":"JWT","alg":"HS512"}

scala> val token1: String = JwtArgonaut.encode(jsonClaim)
token1: String = eyJhbGciOiJub25lIn0.eyJleHBpcmVzIjoxNTUwMDIwMjUzfQ.

scala> val token2: String = JwtArgonaut.encode(jsonClaim, key, alg)
token2: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHBpcmVzIjoxNTUwMDIwMjUzfQ.Cyfex8d0TmOjJluQGgNKD5PtJ_exQ-SWJyVU6Sp2qgMf0cYsRhskX-pZV4_90W7Jr3GylLcpQXICey6K2b15TA

scala> val token3: String = JwtArgonaut.encode(jsonHeader, jsonClaim, key)
token3: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHBpcmVzIjoxNTUwMDIwMjUzfQ.Cyfex8d0TmOjJluQGgNKD5PtJ_exQ-SWJyVU6Sp2qgMf0cYsRhskX-pZV4_90W7Jr3GylLcpQXICey6K2b15TA
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
claim: pdi.jwt.JwtClaim = JwtClaim({},None,None,None,Some(1707805013),None,Some(1550020253),None)

scala> val key = "secretKey"
key: String = secretKey

scala> val alg = JwtAlgorithm.HS512
alg: pdi.jwt.JwtAlgorithm.HS512.type = HS512

scala> val token = JwtArgonaut.encode(claim, key, alg)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHAiOjE3MDc4MDUwMTMsImlhdCI6MTU1MDAyMDI1M30.uVyYwfdEnn4RZvDqD2drmsf4kbybzkCkRQezwZ5ruf20HSsZ5cMt7GbUHs0kl70dqyx1eUw04D8ekIXTmk2mnQ

scala> val decodedJsonClaim: Try[Json] = JwtArgonaut.decodeJson(token, key, Seq(alg))
decodedJsonClaim: scala.util.Try[argonaut.Json] = Success({"exp":1707805013,"iat":1550020253})

scala> val decodedJson: Try[(Json, Json, String)] = JwtArgonaut.decodeJsonAll(token, key, Seq(alg))
decodedJson: scala.util.Try[(argonaut.Json, argonaut.Json, String)] = Success(({"typ":"JWT","alg":"HS512"},{"exp":1707805013,"iat":1550020253},uVyYwfdEnn4RZvDqD2drmsf4kbybzkCkRQezwZ5ruf20HSsZ5cMt7GbUHs0kl70dqyx1eUw04D8ekIXTmk2mnQ))

scala> val decodedClaim: Try[JwtClaim]  = JwtArgonaut.decode(token, key, Seq(alg))
decodedClaim: scala.util.Try[pdi.jwt.JwtClaim] = Success(JwtClaim({},None,None,None,Some(1707805013),None,Some(1550020253),None))

scala> val decodedToken: Try[(JwtHeader, JwtClaim, String)] = JwtArgonaut.decodeAll(token, key, Seq(alg))
decodedToken: scala.util.Try[(pdi.jwt.JwtHeader, pdi.jwt.JwtClaim, String)] = Success((JwtHeader(Some(HS512),Some(JWT),None,None),JwtClaim({},None,None,None,Some(1707805013),None,Some(1550020253),None),uVyYwfdEnn4RZvDqD2drmsf4kbybzkCkRQezwZ5ruf20HSsZ5cMt7GbUHs0kl70dqyx1eUw04D8ekIXTmk2mnQ))
```

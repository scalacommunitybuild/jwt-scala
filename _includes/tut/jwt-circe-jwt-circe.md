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
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@abb7f27d

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtCirce.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3NDA3Njg2MjAsImlhdCI6MTU4Mjk4Mzg2MH0.lduGGe8FK4_Rw5RqBiwOgpWXXEhBHXBBQUg23RaRIBE

scala> JwtCirce.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res0: scala.util.Try[io.circe.Json] =
Success({
  "exp" : 1740768620,
  "iat" : 1582983860
})

scala> JwtCirce.decode(token, key, Seq(JwtAlgorithm.HS256))
res1: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@abb7f27d)
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
  "expires" : 1582983860
}

scala> val Right(header) = jawnParse( """{"typ":"JWT","alg":"HS256"}""")
header: io.circe.Json =
{
  "typ" : "JWT",
  "alg" : "HS256"
}

scala> // From just the claim to all possible attributes
     | JwtCirce.encode(claimJson)
res3: String = eyJhbGciOiJub25lIn0.eyJleHBpcmVzIjoxNTgyOTgzODYwfQ.

scala> JwtCirce.encode(claimJson, key, algo)
res4: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHBpcmVzIjoxNTgyOTgzODYwfQ.cEiyjasEB0F3dL6ALpVeBtEfR8-hpCRN9FYOne-atXw

scala> JwtCirce.encode(header, claimJson, key)
res5: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHBpcmVzIjoxNTgyOTgzODYwfQ.cEiyjasEB0F3dL6ALpVeBtEfR8-hpCRN9FYOne-atXw
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
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@8b8d1df8

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtCirce.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3NDA3Njg2MjEsImlhdCI6MTU4Mjk4Mzg2MX0.lxyg6CMfwMmDetRqkaQu8Yz0XEUKZLYQ-JDdz_AmWIc

scala> // You can decode to JsObject
     | JwtCirce.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res7: scala.util.Try[io.circe.Json] =
Success({
  "exp" : 1740768621,
  "iat" : 1582983861
})

scala> JwtCirce.decodeJsonAll(token, key, Seq(JwtAlgorithm.HS256))
res8: scala.util.Try[(io.circe.Json, io.circe.Json, String)] =
Success(({
  "typ" : "JWT",
  "alg" : "HS256"
},{
  "exp" : 1740768621,
  "iat" : 1582983861
},lxyg6CMfwMmDetRqkaQu8Yz0XEUKZLYQ-JDdz_AmWIc))

scala> // Or to case classes
     | JwtCirce.decode(token, key, Seq(JwtAlgorithm.HS256))
res10: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@8b8d1df8)

scala> JwtCirce.decodeAll(token, key, Seq(JwtAlgorithm.HS256))
res11: scala.util.Try[(pdi.jwt.JwtHeader, pdi.jwt.JwtClaim, String)] = Success((pdi.jwt.JwtHeader@ac020068,pdi.jwt.JwtClaim@8b8d1df8,lxyg6CMfwMmDetRqkaQu8Yz0XEUKZLYQ-JDdz_AmWIc))
```

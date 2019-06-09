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
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@48670544

scala> val key = "secretKey"
key: String = secretKey

scala> val alg = JwtAlgorithm.HS512
alg: pdi.jwt.JwtAlgorithm.HS512.type = HS512

scala> val token = JwtArgonaut.encode(claim, key, alg)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHAiOjE3MTc4Nzk0MDcsImlhdCI6MTU2MDA5NDY0N30.hiA84h41aHisPjgtfblP_SNxFc7wMU9aLsGo5Jy9iygK3IWxKnbyLr3bykxQepk-YvvmnPxGpzxaPKBNstOU9Q

scala> val decodedJson: Try[Json] = JwtArgonaut.decodeJson(token, key, Seq(alg))
decodedJson: scala.util.Try[argonaut.Json] = Success({"exp":1717879407,"iat":1560094647})

scala> val decodedClaim: Try[JwtClaim] = JwtArgonaut.decode(token, key, Seq(alg))
decodedClaim: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@48670544)
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
jsonClaim: argonaut.Json = {"expires":1560094648}

scala> val jsonHeader = Parse.parseOption("""{"typ":"JWT","alg":"HS512"}""").get
jsonHeader: argonaut.Json = {"typ":"JWT","alg":"HS512"}

scala> val token1: String = JwtArgonaut.encode(jsonClaim)
token1: String = eyJhbGciOiJub25lIn0.eyJleHBpcmVzIjoxNTYwMDk0NjQ4fQ.

scala> val token2: String = JwtArgonaut.encode(jsonClaim, key, alg)
token2: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHBpcmVzIjoxNTYwMDk0NjQ4fQ.fknnD5k8kujY7hPbRYtku_Qvr5a8G1Ed7PCPbFMvOUJJP9dWDhnD81GZmqV_bHgO74UdTrSMmFE9uydz-6Vuwg

scala> val token3: String = JwtArgonaut.encode(jsonHeader, jsonClaim, key)
token3: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHBpcmVzIjoxNTYwMDk0NjQ4fQ.fknnD5k8kujY7hPbRYtku_Qvr5a8G1Ed7PCPbFMvOUJJP9dWDhnD81GZmqV_bHgO74UdTrSMmFE9uydz-6Vuwg
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
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@adbd6bfc

scala> val key = "secretKey"
key: String = secretKey

scala> val alg = JwtAlgorithm.HS512
alg: pdi.jwt.JwtAlgorithm.HS512.type = HS512

scala> val token = JwtArgonaut.encode(claim, key, alg)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHAiOjE3MTc4Nzk0MDgsImlhdCI6MTU2MDA5NDY0OH0.e6YWCctICopGzhuojeYxPlm-AAqukW69NtKvVVislR_uDuRQlgpyk83ax8xotv3AlJffRV9wHGK0UEXGUbXmlQ

scala> val decodedJsonClaim: Try[Json] = JwtArgonaut.decodeJson(token, key, Seq(alg))
decodedJsonClaim: scala.util.Try[argonaut.Json] = Success({"exp":1717879408,"iat":1560094648})

scala> val decodedJson: Try[(Json, Json, String)] = JwtArgonaut.decodeJsonAll(token, key, Seq(alg))
decodedJson: scala.util.Try[(argonaut.Json, argonaut.Json, String)] = Success(({"typ":"JWT","alg":"HS512"},{"exp":1717879408,"iat":1560094648},e6YWCctICopGzhuojeYxPlm-AAqukW69NtKvVVislR_uDuRQlgpyk83ax8xotv3AlJffRV9wHGK0UEXGUbXmlQ))

scala> val decodedClaim: Try[JwtClaim]  = JwtArgonaut.decode(token, key, Seq(alg))
decodedClaim: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@adbd6bfc)

scala> val decodedToken: Try[(JwtHeader, JwtClaim, String)] = JwtArgonaut.decodeAll(token, key, Seq(alg))
decodedToken: scala.util.Try[(pdi.jwt.JwtHeader, pdi.jwt.JwtClaim, String)] = Success((pdi.jwt.JwtHeader@7e1481b1,pdi.jwt.JwtClaim@adbd6bfc,e6YWCctICopGzhuojeYxPlm-AAqukW69NtKvVVislR_uDuRQlgpyk83ax8xotv3AlJffRV9wHGK0UEXGUbXmlQ))
```

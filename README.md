# fs2-cron
[![Build Status](https://travis-ci.org/fthomas/fs2-cron.svg?branch=master)](https://travis-ci.org/fthomas/fs2-cron)
[![codecov](https://codecov.io/gh/fthomas/fs2-cron/branch/master/graph/badge.svg)](https://codecov.io/gh/fthomas/fs2-cron)
[![Join the chat at https://gitter.im/fthomas/fs2-cron](https://badges.gitter.im/fthomas/fs2-cron.svg)](https://gitter.im/fthomas/fs2-cron?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Scaladex](https://index.scala-lang.org/fthomas/fs2-cron/latest.svg?color=blue)](https://index.scala-lang.org/fthomas/fs2-cron/fs2-cron-core)
[![Scaladoc](https://www.javadoc.io/badge/eu.timepit/fs2-cron-core_2.12.svg?color=blue&label=Scaladoc)](https://javadoc.io/doc/eu.timepit/fs2-cron-core_2.12)

**fs2-cron** is a microlibrary that provides [FS2][FS2] streams based
on [Cron4s][Cron4s] cron expressions.

## Quick example

```scala
import cats.effect.{IO, Timer}
import cron4s.Cron
import eu.timepit.fs2cron.awakeEveryCron
import fs2.Stream
import java.time.LocalTime
import scala.concurrent.ExecutionContext
```
```scala
implicit val timer: Timer[IO] = IO.timer(ExecutionContext.global)
// timer: cats.effect.Timer[cats.effect.IO] = cats.effect.internals.IOTimer@7b423f90

val evenSeconds = Cron.unsafeParse("*/2 * * ? * *")
// evenSeconds: cron4s.expr.CronExpr = */2 * * ? * *

val printTime = Stream.eval(IO(println(LocalTime.now)))
// printTime: fs2.Stream[cats.effect.IO,Unit] = Stream(..)

val scheduled = awakeEveryCron[IO](evenSeconds) >> printTime
// scheduled: fs2.Stream[[x]cats.effect.IO[x],Unit] = Stream(..)

scheduled.take(3).compile.drain.unsafeRunSync
// 23:21:42.107
// 23:21:44.005
// 23:21:46.003
```

## Using fs2-cron

The latest version of the library is available for Scala 2.12.

If you're using sbt, add the following to your build:

```sbt
libraryDependencies ++= Seq(
  "eu.timepit" %% "fs2-cron-core" % "0.0.6"
)
```

## License

**fs2-cron** is licensed under the Apache License, Version 2.0, available at
http://www.apache.org/licenses/LICENSE-2.0 and also in the
[LICENSE](https://github.com/fthomas/status-page/blob/master/LICENSE) file.

[Cron4s]: https://github.com/alonsodomin/cron4s
[FS2]: https://github.com/functional-streams-for-scala/fs2

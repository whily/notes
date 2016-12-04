Notes of using sbt.

### Repositories

According to [sbt doc](http://www.scala-sbt.org/0.13/docs/Proxy-Repositories.html),
to ensure that the repositories configured in `~/.sbit/repositories`
is used, add the following `-Dsbt.override.build.repos=true` when
invoking `sbt` script. This is mainly needed when downloading packages
due to e.g. bumping scala/sbt versions.

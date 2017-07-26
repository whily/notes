Notes of using sbt.

### Repositories

According to [sbt doc](http://www.scala-sbt.org/0.13/docs/Proxy-Repositories.html),
to ensure that the repositories configured in `~/.sbit/repositories`
is used, add the following `-Dsbt.override.build.repos=true` when
invoking `sbt` script. This is mainly needed when downloading packages
due to e.g. bumping scala/sbt versions.

### Using IntelliJ IDEA and sbt-android

[General guideline from JebBrains on using SBT in IntelliJ IDEA](https://www.jetbrains.com/help/idea/getting-started-with-sbt.html)

Reflecting dependencies in `build.sbt` to IDEA can be done by opening
SBT tool window (View -> Tool Windows -> SBT), and press button
`Refresh all SBT projects`.

### Ensime

After installing Ensime for Emacs, and [setting up in sbt as per user
configuration](http://ensime.github.io/build_tools/sbt/), for each
project, run `sbt` with target `ensimeConfig` to generate `.ensime`,
then open any file of the project in Emacs, and type `M-x ensime` to
start Ensime.

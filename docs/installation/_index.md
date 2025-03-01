+++
title = "Setup & Configuration"
menuTitle = "Installation"
weight = 1
+++

## Installation

Tyrian is a Scala 3 Web UI library, so please set your Scala version to `@SCALA_VERSION@` or higher.

> You can use Tyrian with Scala 2 thanks to cross versions and the magic of TASTy.

Please note that both the sbt and Mill instructions below assume you intend to work with some sort of web packager/bundler, and therefore emit common js modules.

The [examples in the Tyrian repo](https://github.com/PurpleKingdomGames/tyrian/tree/main/examples) almost all use [Parcel.js](https://parceljs.org/) as the bundler.

### sbt

Add the [Scala.js](https://www.scala-js.org/) plugin to your `project/plugins.sbt` file.

```scala
addSbtPlugin("org.scala-js" % "sbt-scalajs"  % "@SCALAJS_VERSION@")
```

Enable the plugin and add the Tyrian library to your `build.sbt` file.

```scala
enablePlugins(ScalaJSPlugin)

libraryDependencies ++= Seq(
  "io.indigoengine" %%% "tyrian" % "@VERSION@"
)

scalaJSUseMainModuleInitializer := true,
scalaJSLinkerConfig ~= { _.withModuleKind(ModuleKind.CommonJSModule) }
```

Optionally, you can also include the Tyrian/Indigo Bridge library if you plan to embed an Indigo game in your page:

```scala
libraryDependencies ++= Seq(
  ...
  "io.indigoengine" %%% "tyrian-indigo-bridge" % "@VERSION@"
)
```

### Mill

Below is a complete, basic Mill `build.sc` file including MUnit for testing.

```scala
import $ivy.`com.lihaoyi::mill-contrib-bloop:$MILL_VERSION`
import mill._
import mill.scalalib._
import mill.scalajslib._
import mill.scalajslib.api._

object counter extends ScalaJSModule {
  def scalaVersion   = "@SCALA_VERSION@"
  def scalaJSVersion = "@SCALAJS_VERSION@"

  def ivyDeps = Agg(ivy"io.indigoengine::tyrian::@VERSION@")

  override def moduleKind = T(mill.scalajslib.api.ModuleKind.CommonJSModule)
  override def useECMAScript2015 = T(true) // required for the tyrian/indigo bridge

  object test extends Tests {
    def ivyDeps = Agg(ivy"org.scalameta::munit::0.7.29")

    def testFramework = "munit.Framework"

    override def moduleKind = T(mill.scalajslib.api.ModuleKind.CommonJSModule)
    override def jsEnvConfig = T(JsEnvConfig.NodeJs(args = List("--dns-result-order=ipv4first")))
    override def useECMAScript2015 = T(true)
  }

}
```

Optionally, you can also include the Tyrian/Indigo Bridge library if you plan to embed an Indigo game in your page:

```scala
  def ivyDeps = Agg(
    ivy"io.indigoengine::tyrian::@VERSION@",
    ivy"io.indigoengine::tyrian-indigo-bridge::@VERSION@"
  )
```

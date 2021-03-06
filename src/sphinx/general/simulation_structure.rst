.. _simulation_structure:

####################
Simulation structure
####################

A ``Simulation`` is a real Scala class containing 4 different parts:

  * The HTTP protocol configuration
  * The headers definition
  * The scenario definition
  * The simulation definition

To illustrate this point we will take one of the sample simulation: `computerdatabase.BasicSimulation <https://github.com/excilys/gatling/blob/master/gatling-bundle/src/universal/user-files/simulations/computerdatabase/BasicSimulation.scala>`_

HTTP protocol configuration
===========================

The first element is the configuration of the HTTP protocol.
In this example, this configuration is very basic and just defines the base URL, that will be prepended to all the relative paths in the scenario definition, and common headers, that will be added on each request.
Here, the base URL is http://computer-database.heroku.com.

The HTTP Configuration is stored as a Scala value so that we can set it later in the simulation definition.

Headers definition
==================

As you'll see later, when we define the scenario for Gatling, we can provide the headers used for each request sent to the server.
As the file was generated by the recorder, all headers are declared in the beginning of the file and used in the scenario definition.

.. warning:: Adding common headers can seem useless, but don't forget that they add load on your server, thus they are important and should not be forgotten.

Headers are declared as Scala Maps::

  val headers_10 = Map("Content-Type" -> """application/x-www-form-urlencoded""")

Scenario definition
===================

After the headers definition, you'll find the scenario definition.
This definition has a name because you can define several scenarios in the same simulation.
It is usually stored in a Scala value: ``val scn = scenario("Name of my Scenario")...``.

The scenario structure is basically made of two methods: ``exec`` and ``pause``.
The first one is used to describe an action, usually a request sent to the tested application; the second one is used to simulate the thinking time of the user between pages.

HTTP requests are defined as follows in a scenario::

  http("request_10") // Here's an example of a POST request
			.post("/computers")
			.headers(headers_10)
			.param("""name""", """Beautiful Computer""")
			.param("""introduced""", """2012-05-30""")
			.param("""discontinued""", """""")
			.param("""company""", """37"""))

The example above produces a POST HTTP request that create a new computer model::

  HTTP request:
  POST http://computer-database.heroku.com//computers
  headers=
    Accept: [text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8]
    Content-Type: [application/x-www-form-urlencoded]
    DNT: [1]
    Accept-Language: [en-US,en;q=0.5]
    Accept-Encoding: [gzip, deflate]
    User-Agent: [Mozilla/5.0 (Macintosh; Intel Mac OS X 10.8; rv:16.0) Gecko/20100101 Firefox/16.0]
    Referer: [http://computer-database.heroku.com//computers/new]
  params=
    company: [37]
    discontinued: []
    name: [Beautiful Computer]
    introduced: [2012-05-30]

Simulation definitions
======================

The last part of the file contains the simulation definition, this is where you define the load you want to inject to your server, eg::

  setUp(
    scn.inject(atOnceUsers(1)).protocols(httpConf)
  )

Here Gatling will simulate **one user** behaving as we did.
The protocolConfig is also added to the simulation to set the base URL and common headers.
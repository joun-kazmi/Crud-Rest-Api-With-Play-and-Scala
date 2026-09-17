# Crud-Rest-Api-With-Play-and-Scala

A minimal JSON REST API built with the [Play Framework](https://www.playframework.com/) and Scala. It exposes basic CRUD-style endpoints over an in-memory list of `Product` records.

This is a **2017 proof-of-concept / learning project**, not production software: there is no persistent database (products live in a mutable in-memory `ListBuffer` and reset on restart), no authentication, and no automated tests. It's kept here as a small example of Play/Scala usage.

## Stack

- Play Framework 2.3.10 (Scala flavor, `PlayScala` plugin)
- sbt 0.13.5
- Scala (2.11.x, the default for Play 2.3.10 / sbt 0.13.5)

## Data model

```scala
case class Product(ean: Long, name: String, price: String)
```

Products are seeded in memory at startup with two example entries (`Moto X`, `Moto G`) and are read/written via `models.Product`.

## Endpoints

All endpoints are defined in [`conf/routes`](conf/routes) and implemented in [`app/controllers/Application.scala`](app/controllers/Application.scala).

| Method | Path                          | Description                                  |
|--------|-------------------------------|-----------------------------------------------|
| GET    | `/products`                   | List all products (sorted by EAN)             |
| GET    | `/products/find/:ean`         | Look up a single product by EAN code          |
| POST   | `/products`                   | Create a product (JSON body)                  |
| GET    | `/products/remove/:ean`       | Remove a product by EAN                       |
| POST   | `/products/update/:ean`       | Update an existing product by EAN (JSON body) |

Request/response bodies are JSON, matching the `Product` case class, e.g.:

```json
{ "ean": 401120545, "name": "Moto X", "price": "1800 Rs" }
```

Note: a couple of these routes use `GET` for state-changing operations (e.g. `/products/remove/:ean`), which is a REST anti-pattern left over from how the project was originally scaffolded — flagged here rather than silently fixed, since changing it would change the API.

## Running it

Requires a JDK and either [sbt](https://www.scala-sbt.org/) or the (long-deprecated) Typesafe Activator.

```bash
sbt run
```

The app starts on `http://localhost:9000` by default. Play compiles on first request, so the first hit to any endpoint will be slow.

### Configuration

`conf/application.conf` reads the Play application secret from an environment variable instead of hardcoding it:

```
application.secret=${?APPLICATION_SECRET}
```

Set `APPLICATION_SECRET` before running, e.g.:

```bash
export APPLICATION_SECRET=$(openssl rand -hex 32)
sbt run
```

## License

MIT — see [LICENSE](LICENSE). The license file originates from the Play Scala REST API activator template this project was scaffolded from.

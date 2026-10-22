# guess-market

A prediction-market app in Java. Users deposit money, a market maker opens events with two possible outcomes, and other users trade on them. The server runs on Tomcat, and the client is a JavaFX desktop app. Built as a university client-server course project.

**Stack:** Java · Jakarta Servlet 6.0 (Tomcat 10.1) · JavaFX · Gson

## Architecture

Three modules, under `src`, `src-server` and `src-client`:

- **engine** is the core: model, XML event loader, trading maths and `GuessMarketEngine`. It has no idea who is calling it, and it holds the shared data classes (DTOs) so client and server agree on the wire format.
- **server** is a web application that exposes the engine over HTTP. A single servlet, `ApiServlet`, dispatches every call under `/api/*`.
- **client** is the JavaFX UI. It keeps no market state of its own and only shows what the server reports.

Clients never talk to each other. Everything goes through the server.

Two pricing mechanisms are implemented: an **LMSR** automated market maker (`LmsrCalculator`) and an **order book** with order matching (`OrderBookMatcher`).

## API

| Method | Path | Parameters |
|---|---|---|
| POST | `/api/login` | `name` |
| POST | `/api/logout` | |
| GET | `/api/version` | cheap poll, returns a change counter |
| GET | `/api/state` | everything this client can see |
| POST | `/api/upload` | multipart `file` (XML), uploader becomes market maker |
| POST | `/api/deposit` | `amount` |
| POST | `/api/openEvent` | `event` |
| POST | `/api/closeEvent` | `event`, `winner` |
| POST | `/api/lmsrBuy` | `event`, `option`, `quantity` |
| POST | `/api/placeOrder` | `event`, `option`, `side` (BUY/SELL), `price`, `quantity` |

## Design decisions

- **Polling with a version number.** The client asks for a single version number about once a second and fetches the full state only when it changes. Regular traffic stays tiny, and other users' actions show up within about a second.
- **Session-based identity.** Login stores the user name in the HTTP session, so later calls don't repeat it.
- **Everything lives in memory.** Users, events and trading history are cleared when the server stops. Uploaded files are validated in memory and never written to disk.
- **Strict file validation.** An uploaded XML file is rejected as a whole if any event fails: bad commission, anything other than two distinct options, duplicate or already-existing event names. The client is told exactly which events failed and why.

## Repository layout

- `src/` is the engine, `src-server/` the servlet, `src-client/` the JavaFX client.
- `testdata/` has sample event files, including deliberately invalid ones (`error-2.xml`, `error-3.xml`).

## What I'd improve

- **Add a build file** (Maven or Gradle) so the project builds from source.
- **Add automated tests** for the LMSR maths and the order matching.
- **Persist state** instead of keeping it in memory.
- **Replace polling** with server push (WebSocket or SSE).

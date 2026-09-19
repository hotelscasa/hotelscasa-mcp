# HotelsCasa MCP server

A remote [Model Context Protocol](https://modelcontextprotocol.io) server for **[HotelsCasa](https://hotelscasa.com/)**, a travel marketplace for Europe, Turkey, the Caucasus, Central Asia and Latin America.

It lets an AI assistant search more than 1.3 million hotels and apartments, homes and cars listed directly by their owners, tickets and tours, and get the right links for flights, taxi, shipments and car rental. Every result comes with a page on hotelscasa.com where the traveller sees the exact price for the dates and books.

- **Endpoint:** `https://mcp.hotelscasa.com/mcp`
- **Transport:** Streamable HTTP (no SSE)
- **Authentication:** none. The server is public and read-only.
- **Languages:** `es`, `en`, `de`, `fr`, `it`, `pt`, `tr`, `ru` (the `lang` argument of every tool)
- **Registry name:** `com.hotelscasa/hotelscasa-mcp`
- **Video (2 min):** https://youtu.be/gHR8DM62KB0

This repository holds the public description of the server: this README and `server.json` for the MCP Registry. The server itself is hosted by HotelsCasa; there is nothing to install.

## Connect

**Claude (claude.ai, Claude Desktop):** Settings → Connectors → Add custom connector → `https://mcp.hotelscasa.com/mcp`

**Claude Code:**

```sh
claude mcp add --transport http hotelscasa https://mcp.hotelscasa.com/mcp
```

**Cursor, Cline, VS Code and other clients** (`mcp.json`):

```json
{
  "mcpServers": {
    "hotelscasa": {
      "url": "https://mcp.hotelscasa.com/mcp"
    }
  }
}
```

**Clients that only speak stdio:**

```json
{
  "mcpServers": {
    "hotelscasa": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.hotelscasa.com/mcp"]
    }
  }
}
```

## Tools

All 13 tools only read the public catalogue. None of them creates a booking, sends a message or changes anything: booking always happens on the hotelscasa.com page that the tool returns.

| Tool | What it does |
|---|---|
| `search_hotels` | Hotels, apartments and holiday homes by city or coordinates, with indicative nightly prices, rating, type and page link. Filters: stars, type, maximum price. Sorting: recommended (mixed price levels, best rating for the price), price, rating. |
| `get_hotel` | One hotel in full: description, amenities, photos, check-in times, places nearby. |
| `search_activities` | Tickets, tours and attractions by city, country or free text. |
| `get_activity` | One activity with what is included, duration and the next available dates with prices. |
| `find_flights` | The flight search page for a destination and its IATA code. It does not quote fares: the traveller searches them live on the page. |
| `book_ground_transport` | The right link to order a taxi or transfer, send a parcel or a move on the transport exchange, or rent a car. It prepares the link only; nothing is ordered by the tool. |
| `partner_with_hotelscasa` | For businesses: how taxi fleets, drivers, carriers, shipping companies, car rental fleets and property owners join HotelsCasa, with the sign-up page for each profile. |
| `search_properties` | Homes listed directly by private owners, with booking mode, price and booking link. |
| `get_property` | One owner-listed home in full, with rooms, conditions and cancellation policy. |
| `check_availability` | Availability and total price of up to 10 owner-listed homes for given dates. |
| `search_vehicles` | Cars listed by their owners, by city, dates, seats and transmission. |
| `get_vehicle` | One car in full: specs, rental conditions, deposit, busy dates. |
| `check_vehicle_availability` | Availability and total price of up to 10 cars for given dates. |

Tool names are at most 26 characters and the largest answer is under 30,000 characters.

## Example prompts

1. *"Find me a place to stay in Prague for two adults, 10–13 November, under 120 € a night, with good reviews."*
   The assistant calls `search_hotels` (`city: "Prague"`, `country: "CZ"`, dates, `price_max: 120`) and then `get_hotel` for the ones the traveller likes.

2. *"What can we do in Rome on a rainy day? Show tickets that are available this weekend."*
   The assistant calls `search_activities` (`city: "Rome"`) and `get_activity` for the next dates and prices.

3. *"I land in Valencia on Friday at 18:30 with two suitcases. Get me a taxi to the old town and a rental car from Saturday to Monday."*
   The assistant calls `book_ground_transport` with `service: "taxi"` for a pre-filled order link, and `search_vehicles` with the dates for the car.

## Prices and data

- Hotel prices are indicative nightly rates for two adults. The exact price for the dates is shown on the page.
- A hotel with no published rate is returned without any figure, and its page lets the traveller request price and availability.
- Supplier contact details are removed from every text. The only contacts shown are those of HotelsCasa.
- Requests are rate-limited per IP address.

## Links

- Website: https://hotelscasa.com/
- Server card: https://hotelscasa.com/.well-known/mcp/server-card.json
- For AI agents: https://hotelscasa.com/llms.txt
- Privacy policy: https://hotelscasa.com/politica-de-privacidad/
- Contact: 1@hotelscasa.com

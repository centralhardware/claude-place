# claude-place

Given a GPS fix, this service returns a short description of the public place you are at. It uses headless `claude -p` on a Claude subscription, with no API key.

```
POST /place  {"lat": 42.4247, "lon": 18.7712, "acc": 12}
-> 200 {"place": "Senso pastry shop, Kotor Old Town"}
-> 200 {"place": ""}        # not clearly a public place
GET /health
```

How it works: [Photon](https://photon.komoot.io) supplies the named OpenStreetMap features within 120 m, and [Nominatim](https://nominatim.org) supplies the address. Claude (no tools, MCP, plugins or settings) picks the most likely feature and writes one line of at most 60 characters. It never invents a name. Residential or uncertain spots return an empty string.

It was built for [current_country_updater](https://github.com/centralhardware/current_country_updater), which adds a 📍 line to channel posts.

## Run

```sh
python3 place-server.py
```

There is no auth, so run it on a private network only. Environment variables: `PLACE_PORT` (8788), `PLACE_HOST` (0.0.0.0), `PLACE_MODEL` (sonnet), `CLAUDE_BIN` (~/.local/bin/claude), `CLAUDE_CONFIG_DIR`.

To keep it running with cron:

```
* * * * * /usr/bin/flock -n /tmp/place-server.lock /usr/bin/systemd-cat -t claude-place /usr/bin/python3 /path/to/place-server.py
```

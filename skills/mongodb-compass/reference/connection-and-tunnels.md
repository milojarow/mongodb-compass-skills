# Connection & SSH tunnels

## Where Compass stores connections

Config root: `~/.config/MongoDB Compass/`. Each connection is a JSON file at `~/.config/MongoDB Compass/Connections/<uuid>.json`. Editing these JSONs directly is how the launcher pattern (see [password-persistence.md](password-persistence.md)) injects credentials.

## Always use directConnection=true

```
mongodb://USER:PASS@localhost:PORT/DB?authSource=admin&directConnection=true
```

Without `directConnection=true`, Compass attempts **replica-set discovery**: it connects, reads the replica-set config, gets back the members' **internal hostnames**, and tries to reach those — which don't resolve on your local machine (you only have the tunnel's `localhost:PORT`). The result is a **silent failure**, not a clear error. `directConnection=true` tells Compass to talk to exactly the host you gave it and skip discovery.

## Favorite, not recent

Set `savedConnectionType: "favorite"` in the connection JSON. Compass periodically **cleans up `"recent"` connections**, so a tunneled connection you set up once will silently vanish if it's left as recent. If one already vanished, recreate it and then set it to favorite so it sticks.

## SSH-tunnel troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| **ECONNREFUSED** | tunnel not running | check `pgrep -a -f "ssh.*-L <port>"` and `ss -tlnp \| grep <port>` |
| **Timeout / hangs** | tunnel process alive but the SSH session is dead (a zombie) | kill and re-establish the tunnel: `pkill -f "ssh.*-L <port>"`, then bring it back up |
| **"connects" but no data** | TCP is open but MongoDB isn't answering — usually a zombie SSH session (port still forwarded, far end dead) | `nc` only proves TCP; confirm Mongo really answers: `mongosh "mongodb://localhost:<port>/?directConnection=true" --eval 'db.runCommand({ping:1})'` |

The recurring trap: **`nc` success ≠ MongoDB responding.** A zombie SSH session leaves the local port open (so `nc` passes) while nothing answers on the far end — you must test at the MongoDB protocol level to be sure.

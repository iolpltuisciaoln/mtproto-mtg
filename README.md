# Setup MTProto Proxy MTP MTG

### 0. Install Docker
	https://docs.docker.com/engine/install/
### 1. Generate SECRET

```sh
docker run --rm nineseconds/mtg:2 generate-secret -x www.google.com
-> ee9e475e53eab349c002d4068778a5e1087777772e676f6f676c652e636f6d <-
```

### 2. docker-compose.yml
```yml
services:
  mtg:
    container_name: mtg
    image: nineseconds/mtg:2
    cap_add:
        - NET_ADMIN
    restart: always
    ports:
      - "443:3128"
    volumes:
      - ./mtg.toml:/config/mtg.toml
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    command: run /config/mtg.toml
```

### 3. Update SECRET in mtg.toml
```toml
# DisneyLand/mtg.toml
secret = "ee9e475e53eab349c002d4068778a5e1087777772e676f6f676c652e636f6d"

bind-to = "0.0.0.0:3128"
debug = false
tolerate-time-skewness = "5m"

[network]
  domain = "www.google.com"

[stats]
  enabled = false
```

```
❯ tree
.                                                                                
├── docker-compose.yml
└── mtg.toml      
```

### 4. Set correct time (optional)
```bash
# Check current status
timedatectl status

# If NTP is not syncing, enable it
timedatectl set-ntp true

# Force immediate sync (pick one depending on what's installed)
chronyc makestep
# or
ntpdate pool.ntp.org
```

### 5. Start Proxy
```sh
docker compose up -d
```

#### 6. Profit

```
https://t.me/proxy?server=<DisneyLand_IP>&port=443&secret=ee9e475e53eab349c002d4068778a5e1087777772e676f6f676c652e636f6d
```

```
╭───────────────────────────────────────────────────────────────────────────────╮
│   1/1──────────────────────────────────────────────────────────────────────── │
│ ▌ mtg    (nineseconds/mtg:2) 0.0.0.0:443->3128/tcp, [::]:443->3128/tcp        │
╰───────────────────────────────────────────────────────────────────────────────╯
```
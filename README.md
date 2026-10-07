# Multiplayer Game Distribution Platform

A Python game distribution platform developed as an individual Network Programming project. Separate developer and player clients support game publishing, downloads, multiplayer rooms, and plugins.

## Features

- Developer registration, game creation, version uploads, and publishing.
- Player registration, store browsing, and game downloads.
- Multiplayer room creation, joining, and game launching.
- CLI and GUI game templates, plus plugin registration, installation, and dynamic loading.

## Architecture

| Component | Responsibility | Default port |
|---|---|---|
| Developer Server | Developer operations and publishing | 23001 |
| Lobby Server | Player operations and multiplayer rooms | 23002 |
| DB Server | Shared SQLite-backed data access | 23000 |

Developer Client → Developer Server → DB Server  
Player Client → Lobby Server → DB Server

Socket connections use a shared length-prefixed JSON protocol with multithreaded connection handling.

## Requirements

Python 3.10+, including Tkinter and SQLite support. Run the commands below from the directory containing `server/`, `common/`, `developer_client/`, and `player_client/` (the repository's `codebase` directory).

## Run Locally

Start each server in a separate terminal:

```bash
python -m server.db_server --port 23000
python -m server.developer_server --port 23001
python -m server.lobby_server --port 23002
```

Start the developer interface:

```bash
python -m developer_client.gui
```

Connect to the **Developer Server** on port **23001**, register or sign in, and use **Create Game**, **Upload Version**, and **Publish** to manage releases.

Start the player interface:

```bash
python -m player_client.gui
```

Connect to the **Lobby Server** on port **23002**, register or sign in, download a game from **Store**, and create or join a room under **Rooms**. The room host starts the game.

For deployment across machines, configure the servers' bind addresses, the Developer/Lobby servers' `--db-host` and `--db-port`, and the Lobby Server's `--public-host` to match your network.

## Game Templates

| Template | Interface | Players |
|---|---|---|
| `connect4_cli` | Terminal | 2 |
| `tetris_gui` | GUI | 2 |
| `rps_gui` | GUI | 2–8 |

## Message Framing

```text
[4-byte unsigned length, big-endian][UTF-8 JSON body]
```

TCP is a byte stream: a single receive operation may return only part of a message. The receive loop reads the complete header and declared body to reconstruct message boundaries. The shared protocol module limits each frame to 4 MiB.

## Technologies

Python, TCP sockets, threading, SQLite, Tkinter, and JSON.

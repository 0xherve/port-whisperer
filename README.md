# port-whisperer

**A beautiful CLI tool to see what's running on your ports.**

Stop guessing which process is hogging port 3000. `port-whisperer` gives you a color-coded table of every dev server, database, and background process listening on your machine -- with framework detection, Docker container identification, and interactive process management.

## What it looks like

```
$ ports

 ┌─────────────────────────────────────┐
 │  Port Whisperer                     │
 │  listening to your ports...         │
 └─────────────────────────────────────┘

┌───────┬─────────┬───────┬──────────────────────┬────────────┬────────┬───────────┐
│ PORT  │ PROCESS │ PID   │ PROJECT              │ FRAMEWORK  │ UPTIME │ STATUS    │
├───────┼─────────┼───────┼──────────────────────┼────────────┼────────┼───────────┤
│ :3000 │ node    │ 42872 │ frontend             │ Next.js    │ 1d 9h  │ ● healthy │
├───────┼─────────┼───────┼──────────────────────┼────────────┼────────┼───────────┤
│ :3001 │ node    │ 95380 │ preview-app          │ Next.js    │ 2h 40m │ ● healthy │
├───────┼─────────┼───────┼──────────────────────┼────────────┼────────┼───────────┤
│ :4566 │ docker  │ 58351 │ backend-localstack-1 │ LocalStack │ 10d 3h │ ● healthy │
├───────┼─────────┼───────┼──────────────────────┼────────────┼────────┼───────────┤
│ :5432 │ docker  │ 58351 │ backend-postgres-1   │ PostgreSQL │ 10d 3h │ ● healthy │
├───────┼─────────┼───────┼──────────────────────┼────────────┼────────┼───────────┤
│ :6379 │ docker  │ 58351 │ backend-redis-1      │ Redis      │ 10d 3h │ ● healthy │
└───────┴─────────┴───────┴──────────────────────┴────────────┴────────┴───────────┘

  5 ports active  ·  Run ports <number> for details  ·  --all to show everything
```

Colors: green = healthy, yellow = orphaned, red = zombie.

## Install

```bash
npm install -g port-whisperer
```

Or run it directly without installing:

```bash
npx port-whisperer
```

### Or let Claude Code install it for you

If you use [Claude Code](https://claude.ai/code), you can ask it to `npm install -g port-whisperer` and start using `ports` right away -- no setup steps needed.

## Usage

### Show dev server ports

```bash
ports
```

Shows dev servers, Docker containers, and databases. System apps (Spotify, Raycast, etc.) are filtered out by default.

### Show all listening ports

```bash
ports --all
```

Includes system services, desktop apps, and everything else listening on your machine.

### Inspect a specific port

```bash
ports 3000
# or
whoisonport 3000
```

Detailed view: full process tree, repository path, current git branch, memory usage, and an interactive prompt to kill the process.

### Show all dev processes

```bash
ports ps
```

A beautiful `ps aux` for developers. Shows all running dev processes (not just port-bound ones) with CPU%, memory, framework detection, and a smart description column. Docker processes are collapsed into a single summary row.

```bash
ports ps --all    # show all processes, not just dev
```

### Clean up orphaned processes

```bash
ports clean
```

Finds and kills orphaned or zombie dev server processes. Only targets dev runtimes (node, python, etc.) -- won't touch your desktop apps.

### Watch for port changes

```bash
ports watch
```

Real-time monitoring that notifies you whenever a port starts or stops listening.

## How it works

Three shell calls, runs in ~0.2s:

1. **`lsof -iTCP -sTCP:LISTEN`** -- finds all processes listening on TCP ports
2. **`ps`** (single batched call) -- retrieves process details for all PIDs at once: command line, uptime, memory, parent PID, status
3. **`lsof -d cwd`** (single batched call) -- resolves the working directory of each process to detect the project and framework

For Docker ports, a single `docker ps` call maps host ports to container names and images.

Framework detection reads `package.json` dependencies and inspects process command lines. Recognizes Next.js, Vite, Express, Angular, Remix, Astro, Django, Rails, FastAPI, and many others. Docker images are identified as PostgreSQL, Redis, MongoDB, LocalStack, nginx, etc.

## Platform support

| Platform | Status |
|----------|--------|
| macOS    | Supported |
| Linux    | Planned |
| Windows  | Not planned |

## License

[MIT](LICENSE)

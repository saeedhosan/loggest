# loggest

[![npm version](https://badgen.net/npm/v/loggest)](https://www.npmjs.com/package/loggest)
[![downloads](https://badgen.net/npm/dm/loggest)](https://www.npmjs.com/package/loggest)
[![types](https://badgen.net/npm/types/loggest)](https://github.com/saeedhosan/loggest/blob/main/src/types.ts)
[![license](https://badgen.net/npm/license/loggest)](https://github.com/saeedhosan/loggest/blob/main/LICENSE.md)

A lightweight, dependency-free logging utility that's fast, flexible, and easy to extend. Build structured logs your way with a simple plugin-based architecture.

## Installation

```bash
npm install loggest
```

## Quick Start

Set up a logger with the built-in console plugin:

```ts
import { Logger } from "loggest";

const logger = new Logger({
    plugins: [
        {
            handle: (level, context, message, ...optional) => {
                console.log(level, message, ...optional);
            },
        },
    ],
});

logger.info({ name: "app", version: "1.0" });
logger.info("Application started", "additional info");
```

## Features

-   **Lightweight** – No dependencies, minimal footprint
-   **Plugin-based** – Mix and match logging destinations
-   **Structured logging** – Built-in support for context and metadata
-   **Flexible** – File, console, API, or custom logging destinations
-   **Configurable** – Control log levels, formatting, and filtering
-   **Type-safe** – Full TypeScript support

## Configuration

Here's a more complete example with multiple plugins and custom options:

```ts
import { Logger } from "loggest";
import FileLog from "loggest/dist/plugins/FileLog";
import ApiLog from "loggest/dist/plugins/ApiLog";

const logger = new Logger({
    levels: ["info", "warn", "error"],
    plugins: [new FileLog(), new ApiLog({ url: "https://example.com/logs" })],
    context: { app: "my-app", env: "production" },
    format: (level, ctx, msg, ...rest) => `[${level}] ${msg}`,
    filter: (level) => level !== "debug",
});

logger.info("Server initialized");
logger.warn("High memory usage detected");
logger.error("Connection failed");
```

## Using Built-in Plugins

The package includes several ready-to-use plugins:

**FileLog** – Write logs to a file

```ts
import FileLog from "loggest/dist/plugins/FileLog";

const logger = new Logger({
    plugins: [new FileLog()],
});
```

**ApiLog** – Send logs to a remote endpoint

```ts
import ApiLog from "loggest/dist/plugins/ApiLog";

const logger = new Logger({
    plugins: [new ApiLog({ url: "https://example.com/logs" })],
});
```

**ConsoleLog** – Output to the console

```ts
import ConsoleLog from "loggest/dist/plugins/ConsoleLog";

const logger = new Logger({
    plugins: [new ConsoleLog()],
});
```

## Creating Custom Plugins

You can create plugins as objects or classes to handle logs however you need.

**Object-based plugin:**

```ts
const databaseLogger = {
    handle: async (level, ctx, message, ...optional) => {
        await db.logs.insert({ level, message, timestamp: new Date() });
    },
};

const logger = new Logger({ plugins: [databaseLogger] });
```

**Class-based plugin:**

```ts
export class ColoredConsoleLog {
    async handle(level, ctx, message, ...optional) {
        const colors = {
            info: "\x1b[36m",
            warn: "\x1b[33m",
            error: "\x1b[31m",
            debug: "\x1b[90m",
        };
        const color = colors[level] || "";
        console.log(`${color}[${level}]\x1b[0m ${message}`, ...optional);
    }
}

const logger = new Logger({ plugins: [new ColoredConsoleLog()] });
```

**With TypeScript types:**

```ts
import { LogLevel, Plugin } from "loggest";

export class MyCustomLogger implements Plugin {
    async handle(level: LogLevel, ctx: any, message: any, ...optional: any[]): Promise<void> {
        // Your custom logging logic here
    }
}
```

## Contributing

We welcome contributions! To get started:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## License

MIT © Saeed Hosan

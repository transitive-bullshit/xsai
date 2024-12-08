# xsAI

extra-small AI SDK for Browser, Node.js, Deno, Bun or Edge Runtime.

<!-- automd:badges name="xsai" provider="badgen" color="cyan" license bundlephobia -->

[![npm version](https://flat.badgen.net/npm/v/xsai?color=cyan)](https://npmjs.com/package/xsai)
[![npm downloads](https://flat.badgen.net/npm/dm/xsai?color=cyan)](https://npm.chart.dev/xsai)
[![bundle size](https://flat.badgen.net/bundlephobia/minzip/xsai?color=cyan)](https://bundlephobia.com/package/xsai)
[![license](https://flat.badgen.net/github/license/moeru-ai/xsai?color=cyan)](https://github.com/moeru-ai/xsai/blob/main/LICENSE)

<!-- /automd -->

xsAI is a series of utils to help you use OpenAI or OpenAI-compatible API.

```ts
import { generateText } from '@xsai/generate-text'
import { createOpenAI } from '@xsai/providers'
import { env } from 'node:process'

const openai = createOpenAI({
  apiKey: env.OPENAI_API_KEY
})

const { text } = await generateText({
  ...openai.chat('gpt-4o'),
  messages: [
    {
      content: 'You are a helpful assistant.',
      role: 'system',
    },
    {
      content: 'This is a test, so please answer \'YES\' and nothing else.',
      role: 'user',
    },
  ],
})

// "YES"
console.log(text)
```

## Features

### <small><ruby>extra<rp>(</rp><rt>x</rt><rp>)</rp>-small<rp>(</rp><rt>s</rt><rp>)</rp></ruby></small>

xsAI uses a variety of methods to make itself smaller.

It's just a wrapper for [Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API), introducing additional dependencies only when absolutely necessary and ESM Only.

How xsAI small? you can [try install it with pkg-size.dev](https://pkg-size.dev/xsai):

`xsai@0.0.18` is 111KB install size and 11KB bundle size (3KB gzipped).

Notably, this contains dependencies introduced to support tool calls and structured output.

At xsAI you can install only the packages you need:

If you only need the basic `generateText`, `@xsai/generate-text@0.0.18` is only 12KB install size and 1.2KB bundle size (678B gzipped). ([try install it with pkg-size.dev](https://pkg-size.dev/@xsai/generate-text))

### Runtime-agnostic

xsAI doesn't depend on Node.js Built-in Modules, it works well in Browsers, Deno, Bun and even the Edge Runtime.

## Usage

### Install

> You can also install only some of the utils of xsAI, such as `@xsai/generate-text` and `@xsai/stream-text`.

<!-- automd:pm-install name="xsai" auto=false -->

```sh
# npm
npm install xsai

# yarn
yarn add xsai

# pnpm
pnpm install xsai

# bun
bun install xsai

# deno
deno install xsai
```

<!-- /automd -->

### Getting Started

Read the [documentation](https://xsai.js.org/docs) to get started.

### Examples

###### Streaming Text [(see above)](#xsai)

###### Streaming Text

```ts
import { createOpenAI } from '@xsai/providers'
import { streamText } from '@xsai/stream-text'
import { env } from 'node:process'

const openai = createOpenAI({
  apiKey: env.OPENAI_API_KEY,
})

const { textStream } = await streamText({
  ...openai.chat('gpt-4o'),
  messages: [
    {
      content: 'You are a helpful assistant.',
      role: 'system',
    },
    {
      content: 'This is a test, so please answer \'The quick brown fox jumps over the lazy dog.\' and nothing else.',
      role: 'user',
    },
  ],
})

const text: string[] = []

for await (const textPart of textStream) {
  text.push(textPart)
}

// "The quick brown fox jumps over the lazy dog."
console.log(text)
```

###### Tool Calling

```ts
import { generateText } from '@xsai/generate-text'
import { createOpenAI } from '@xsai/providers'
import { tool } from '@xsai/tool'
import { env } from 'node:process'
import { description, object, pipe, string } from 'valibot'

const openai = createOpenAI({
  apiKey: env.OPENAI_API_KEY,
})

const weather = await tool({
  description: 'Get the weather in a location',
  execute: ({ location }) => JSON.stringify({
    location,
    temperature: 42,
  }),
  name: 'weather',
  parameters: object({
    location: pipe(
      string(),
      description('The location to get the weather for'),
    ),
  }),
})

const { text } = await generateText({
  ...openai.chat('gpt-4o'),
  messages: [
    {
      content: 'You are a helpful assistant.',
      role: 'system',
    },
    {
      content: 'What is the weather in San Francisco? do not answer anything else.',
      role: 'user',
    },
  ],
  toolChoice: 'required',
  tools: [weather],
})

// "In San Francisco, it's currently 42°F."
console.log(text)
```

### Status

xsAI is currently in an early stage of development and may introduce breaking changes at any time.

## License

[MIT](LICENSE.md)

Moeru AI / xsAI is not affiliated with OpenAI.

# d2n

Prototype for a Deno to Node output CLI tool. The idea is you would run this and then run a bundler on its output, which would create the final Node distribution along with `.d.ts` files then publish to npm.

Example:

```bash
# clone a Deno repo with no remote dependencies (because this does not support dependencies yet)
git clone https://github.com/dsherret/code-block-writer.git

# clone this repo
git clone https://github.com/dsherret/d2n.git
cd d2n

cargo run -- ../code-block-writer/mod.ts --out dist
```

Then in *dist/mod.ts* the file will contain:

```ts
import { CommentChar } from "./comment_char";
import { escapeForWithinString, getStringFromStrOrFunc } from "./utils/string_utils";
```

If you run with `cargo run -- ../code-block-writer/mod.ts --out dist --keep-extensions` it will then contain:

```ts
import { CommentChar } from "./comment_char.js";
import { escapeForWithinString, getStringFromStrOrFunc } from "./utils/string_utils.js";
```

## Future Goals

1. Programmatic API available via Rust and Wasm (this would be used by bundlers to avoid writing the intermediary files to the disk).
1. Support Deno.json to get compiler options.
1. Handle mapping from remote specifiers to bare specifiers and transforming them in the file.
1. Handle dynamic imports (at least ones that are statically analyzable and maybe warn on others)
1. Support creating or modifying a package.json and using that for publish.

Notes from Kitson:

- We would need to rewrite triple slash references
- We might need to deal with the types in the tsconfig.json
- How do we cleanly supply a deno.ns lib so type checking works?
- How do we handle remote URLs, data URLs and blob dynamic imports?
- We should go from ./foo.ts to ./foo.js by default, with a flag to go from ./foo.ts to ./foo, assume people are supporting a browser or ESM Node.js

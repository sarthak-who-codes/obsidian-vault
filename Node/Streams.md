Allows handling large amounts of data efficiently by processing it piece by piece (chunks) instead of loading everything into memory at once. 

Useful for handling large files, network requests, and real-time data processing.

### **Types of Streams**

Node.js provides four types of streams:

1. **Readable Streams** → Used to read data (e.g., `fs.createReadStream()`).
    
2. **Writable Streams** → Used to write data (e.g., `fs.createWriteStream()`).
    
3. **Duplex Streams** → Both readable and writable (e.g., TCP sockets, `net.Socket()`).
    
4. **Transform Streams** → A special type of Duplex stream that modifies data as it passes through (e.g., `zlib.createGzip()`).

## Readable Streams
### **Stream Modes**

Streams operate in two modes:
1. **Flowing Mode**: Data is automatically read and provided in chunks.
2. **Paused Mode**: Data is read manually using `.read()`.

By default, streams start in **paused mode** and need to be manually switched to **flowing mode** using `.pipe()`, `.resume()`, or event listeners.

|Event|Description|
|---|---|
|`'data'`|Emitted when a chunk of data is available. (Only in **flowing mode**).|
|`'end'`|Emitted when no more data is available from the stream.|
|`'error'`|Emitted if an error occurs while reading.|
|`'readable'`|Emitted when the stream has data available for reading (in **paused mode**).|
|`'close'`|Emitted when the stream is closed.|

Here is a example of **pause mode**:

```js
const fs = require('fs');

const readableStream = fs.createReadStream('input.txt', { encoding: 'utf8' });

readableStream.on('readable', () => {
    let chunk;
    while ((chunk = readableStream.read()) !== null) {
        console.log('Read chunk:', chunk);
    }
});

readableStream.on('end', () => {
    console.log('No more data.');
});
```

Here is a example of **flow mode**:

```js
const fs = require('fs');

const readableStream = fs.createReadStream('input.txt', { encoding: 'utf8' });

readableStream.on('readable', () => {
    let chunk;
    while ((chunk = readableStream.read()) !== null) {
        console.log('Read chunk:', chunk);
    }
});

readableStream.on('end', () => {
    console.log('No more data.');
});
```

Flow of events:
- The stream starts in **paused mode**.
- Attaching the `'data'` event listener switches it to **flowing mode**.
- Data is received in chunks, and the `'data'` event fires automatically.
- When there is no more data, the `'end'` event fires.

You can even switch between modes:

```js
const fs = require('fs');

const readableStream = fs.createReadStream('input.txt', { encoding: 'utf8' });

readableStream.on('data', (chunk) => {
    console.log('Received chunk:', chunk);
    readableStream.pause(); // Pause reading
    setTimeout(() => readableStream.resume(), 2000); // Resume after 2s
});
```

Here are few more examples:

```js
import {Readable} from 'stream'; 
async function readableToString2(readable) { 
	let result = ''; 
	
	for await (const chunk of readable) { 
		result += chunk; 
	} 
	
	return result; 
} 

const readable = Readable.from('Good morning!', {encoding: 'utf8'}); 

assert.equal(await readableToString2(readable), 'Good morning!');
```


## Writable Streams

| Event      | Description                                                                  |
| ---------- | ---------------------------------------------------------------------------- |
| `'drain'`  | Emitted when it’s safe to write more data after `.write()` returned `false`. |
| `'finish'` | Emitted when `.end()` is called and all data is flushed.                     |
| `'error'`  | Emitted if an error occurs while writing.                                    |
| `'close'`  | Emitted when the stream is closed.                                           |

```js
const fs = require('fs');

const writableStream = fs.createWriteStream('output.txt');

writableStream.write('First line.\n');
writableStream.write('Second line.\n');

writableStream.on('finish', () => {
    console.log('Writing completed!');
});

writableStream.end('Final line.');
```


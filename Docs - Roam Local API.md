- The desktop app exposes a local HTTP API on port 3333 for external applications to interact with [[Roam Alpha API]].
- **Enable**
    - Open a graph in the desktop app
    - Go to **Settings → Graph → Local API**
    - Toggle it on
- **Endpoint**
    - ```plain text
      POST http://localhost:3333/api/:graph-name
      Content-Type: application/json
      
      {"action": "...", "args": [...]}
      ```
    - `action` - the [[Roam Alpha API]] method path (e.g., `data.q`, `data.block.create`)
    - `args` - array of arguments to pass to the method
- **Port**
    - The port is usually on 3333, but if that port is taken then we will increment until we find a usable port.
    - You can discover this yourself or read the file `~/.roam-api-port` which contains the current port number. 
        - This file only exists when the desktop app is running
- **Examples**
    - Query all page titles:
        - ```shell
          curl -X POST http://localhost:3333/api/my-graph \
          -H "Content-Type: application/json" \
          -d '{"action": "data.q", "args": ["[:find ?title :where [?e :node/title ?title]]"]}'
          ```
    - Create a block:
        - ```shell
          curl -X POST http://localhost:3333/api/my-graph \
          -H "Content-Type: application/json" \
          -d '{"action": "data.block.create", "args": [{"location": {"parent-uid": "abc123", "order": 0}, "block": {"string": "Hello"}}]}'
          ```
- **Responses**
    - Success:
        - ```javascript
          {"success": true, "result": [...]}
          ```
    - Error:
        - ```javascript
          {"success": false, "error": "Local API is not enabled for this graph"}
          ```

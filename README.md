# MCP Neo4j Server
[![LightNow](https://lightnow.ai/badge/io.github.da-okazaki/mcp-neo4j-server)](https://lightnow.ai/servers/io.github.da-okazaki/mcp-neo4j-server)

An MCP server that provides integration between Neo4j graph database and Claude Desktop, enabling graph database operations through natural language interactions.

<a href="https://glama.ai/mcp/servers/qjpsxn4zlh"><img width="380" height="200" src="https://glama.ai/mcp/servers/qjpsxn4zlh/badge" alt="Neo4j Server MCP server" /></a>

## Quick Start

You can run this MCP server directly using npx:

```bash
npx @alanse/mcp-neo4j
```

Or add it to your Claude Desktop configuration:

```json
{
  "mcpServers": {
    "neo4j": {
      "command": "npx",
      "args": ["@alanse/mcp-neo4j-server"],
      "env": {
        "NEO4J_URI": "bolt://localhost:7687",
        "NEO4J_USERNAME": "neo4j",
        "NEO4J_PASSWORD": "your-password",
        "NEO4J_DATABASE": "neo4j"
      }
    }
  }
}
```

## Features

This server provides tools for interacting with a Neo4j database:

### Neo4j Enterprise Support

This server now supports connecting to specific databases in Neo4j Enterprise Edition. By default, it connects to the "neo4j" database, but you can specify a different database using the `NEO4J_DATABASE` environment variable.

### Tools

- `execute_query`: Execute Cypher queries on the Neo4j database
  - Supports all types of Cypher queries (READ, CREATE, UPDATE, DELETE)
  - Returns query results in a structured format
  - Parameters can be passed to prevent injection attacks

- `create_node`: Create a new node in the graph database
  - Specify node labels and properties
  - Returns the created node with its internal ID
  - Supports all Neo4j data types for properties

- `create_relationship`: Create a relationship between two existing nodes
  - Define relationship type and direction
  - Add properties to relationships
  - Requires node IDs for source and target nodes

## Installation

### Installing via Smithery

To install MCP Neo4j Server for Claude Desktop automatically via [Smithery](https://smithery.ai/server/@alanse/mcp-neo4j-server):

```bash
npx -y @smithery/cli install @alanse/mcp-neo4j-server --client claude
```

### For Development

1. Clone the repository:
```bash
git clone https://github.com/da-okazaki/mcp-neo4j-server.git
cd mcp-neo4j-server
```

2. Install dependencies:
```bash
npm install
```

3. Build the project:
```bash
npm run build
```

## Configuration

The server requires the following environment variables:

- `NEO4J_URI`: Neo4j database URI (default: bolt://localhost:7687)
- `NEO4J_USERNAME`: Neo4j username (default: neo4j)
- `NEO4J_PASSWORD`: Neo4j password (required)
- `NEO4J_DATABASE`: Neo4j database name (default: neo4j) - Use this to connect to a specific database in Neo4j Enterprise

## Usage Examples

Here are examples of how you can interact with the Neo4j database using natural language:

### Connecting to a Specific Database

For Neo4j Enterprise users with multiple databases, you can specify which database to connect to:

```json
{
  "env": {
    "NEO4J_URI": "bolt://localhost:7687",
    "NEO4J_USERNAME": "neo4j",
    "NEO4J_PASSWORD": "your-password",
    "NEO4J_DATABASE": "myCustomDatabase"
  }
}
```

This will connect to "myCustomDatabase" instead of the default "neo4j" database.

### Querying Data

You can ask questions like:
- "Show me all employees in the Sales department"
- "Find the top 5 oldest customers"
- "Who has purchased more than 3 products in the last month?"

Example:
```
User: "Show me all employees in the Sales department"

Claude: Let me query the database for Sales department employees.

[Executes query internally: MATCH (e:Employee)-[:WORKS_IN]->(d:Department {name: 'Sales'}) RETURN e.name, e.position]

Result:
- John Smith (Sales Manager)
- Sarah Johnson (Sales Representative)
- Mike Brown (Account Executive)
```

### Creating Data

You can give instructions like:
- "Add a new person named John Doe who is 30 years old"
- "Create a product called 'Premium Coffee' with price $24.99"
- "Add a new department called 'Research & Development'"

Example:
```
User: "Add a new person named John Doe who is 30 years old"

Claude: I'll create a new Person node with the provided information.

[Creates node internally with label 'Person' and properties {name: 'John Doe', age: 30}]

Result: Created new person node with ID: 123
```

### Creating Relationships

You can request relationships like:
- "Make John Doe friends with Jane Smith"
- "Set Product A as a component of Product B"
- "Assign John Doe to the Sales department"

Example:
```
User: "Make John Doe friends with Jane Smith"

Claude: I'll create a friendship relationship between these two people.

[Creates relationship internally: MATCH (a:Person {name: 'John Doe'}), (b:Person {name: 'Jane Smith'})
CREATE (a)-[:FRIENDS_WITH {since: '2024-01-05'}]->(b)]

Result: Created friendship relationship between John Doe and Jane Smith
```

### Complex Operations

You can perform more complex operations with natural language:
- "Find all products purchased by customers who live in New York"
- "Show me friends of friends of John Doe"
- "Calculate the average age of employees in each department"

Example:
```
User: "Find all products purchased by customers who live in New York"

Claude: I'll query the database for this information.

[Executes query internally:
MATCH (c:Customer {city: 'New York'})-[:PURCHASED]->(p:Product)
RETURN c.name, collect(p.name) as products]

Result:
- Alice Wilson: [Premium Coffee, Tea Set, Cookies]
- Bob Miller: [Premium Coffee, Water Bottle]
```

## Testing

Run the test suite:

```bash
npm test
```

## License

MIT


<html>
<head>
  <title>Shortest Path Visualization</title>
  <style>
    canvas {
      border: 1px solid #000000;
    }
  </style>
</head>
<body>
  <h1>Shortest Path Visualization</h1>
  <canvas id="canvas" width="500" height="500"></canvas>

  <script>
    // Vertex class to represent each HTML element
    class Vertex {
      constructor(id, x, y) {
        this.id = id; // id of the vertex
        this.x = x; // x-coordinate of the vertex
        this.y = y; // y-coordinate of the vertex
        this.adjacent = []; // array to store adjacent vertices
      }

      // Function to add an adjacent vertex
      addAdjacent(vertex) {
        this.adjacent.push(vertex);
      }
    }

    // Graph class to hold all the vertices
    class Graph {
      constructor() {
        this.vertices = []; // array to store all vertices
        this.map = {}; // hash map to store vertices by their ids
      }

      // Function to add a vertex to the graph
      addVertex(vertex) {
        this.vertices.push(vertex);
        this.map[vertex.id] = vertex; // add vertex to the map
      }
    }

    // Function to calculate the Euclidean distance between two vertices
    function getDistance(v1, v2) {
      const dx = v1.x - v2.x;
      const dy = v1.y - v2.y;
      return Math.sqrt(dx * dx + dy * dy);
    }

    // Dijkstra's algorithm implementation
    function dijkstra(graph, startId) {
      const distances = {}; // object to store distances from start vertex to all other vertices
      const previous = {}; // object to store previous vertex in the shortest path
      const unvisited = new Set(); // set to store unvisited vertices

      // Initialize distances and previous objects
      graph.vertices.forEach((vertex) => {
        distances[vertex.id] = Infinity;
        previous[vertex.id] = null;
        unvisited.add(vertex.id);
      });

      distances[startId] = 0; // distance to start vertex is 0

      while (unvisited.size > 0) {
        let minId = null;

        // Find the unvisited vertex with the smallest distance
        unvisited.forEach((vertexId) => {
          if (minId === null || distances[vertexId] < distances[minId]) {
            minId = vertexId;
          }
        });

        unvisited.delete(minId); // remove the vertex from the unvisited set

        const current = graph.map[minId]; // use the map to access the vertex in constant time

        // Update distances and previous for each adjacent vertex
        current.adjacent.forEach((neighbor) => {
          const alt = distances[minId] + getDistance(current, neighbor);

          if (alt < distances[neighbor.id]) {
            distances[neighbor.id] = alt;
            previous[neighbor.id] = current.id;
          }
        });
      }

      return previous; // return the previous object
    }

    // Function to draw the shortest path on the canvas
    function drawShortestPath(graph, previous) {
      const canvas = document.getElementById("canvas");
      const ctx = canvas.getContext("2d");

      ctx.clearRect(0, 0, canvas.width, canvas.height); // clear the canvas

      // Draw all vertices as white circles
      graph.vertices.forEach((vertex) => {
        ctx.beginPath();
        ctx.arc(vertex.x, vertex.y, 10, 0, 2 * Math.PI);
        ctx.fillStyle = "#FFFFFF";
        ctx.fill();
        ctx.closePath();
      });

      // Draw the shortest path as a red line
      ctx.beginPath();
      ctx.strokeStyle = "#FF0000";
      ctx.lineWidth = 3;

      graph.vertices.forEach((vertex) => {
        let currentId = vertex.id;
        let nextId = previous[currentId];

        while (nextId !== null) {
          const current = graph.map[currentId]; // use the map to access the vertex in constant time
          const next = graph.map[nextId];
          ctx.moveTo(current.x, current.y);
          ctx.lineTo(next.x, next.y);

          currentId = nextId;
          nextId = previous[nextId];
        }
      });

      ctx.stroke();
      ctx.closePath();
    }

    // Function to handle mouse move event
    function handleMouseMove(event) {
      const canvas = document.getElementById("canvas");
      const rect = canvas.getBoundingClientRect();
      const mouseX = event.clientX - rect.left;
      const mouseY = event.clientY - rect.top;

      // Snap to the closest vertex if within 5 pixels
      let closestVertex = null;
      let closestDistance = Infinity;

      graph.vertices.forEach((vertex) => {
        const distance = getDistance({ x: mouseX, y: mouseY }, vertex);
        if (distance < closestDistance && distance <= 5) {
          closestVertex = vertex;
          closestDistance = distance;
        }
      });

      if (closestVertex) {
        mouseX = closestVertex.x;
        mouseY = closestVertex.y;
      }

      drawShortestPath(graph, previous);

      // Draw the current line segment from the last vertex to the mouse position
      ctx.beginPath();
      ctx.moveTo(graph.vertices[graph.vertices.length - 1].x, graph.vertices[graph.vertices.length - 1].y);
      ctx.lineTo(mouseX, mouseY);
      ctx.strokeStyle = "#000000";
      ctx.lineWidth = 2;
      ctx.stroke();
      ctx.closePath();
    }

    // Function to handle mouse down event
    function handleMouseDown(event) {
      const canvas = document.getElementById("canvas");
      const rect = canvas.getBoundingClientRect();
      const mouseX = event.clientX - rect.left;
      const mouseY = event.clientY - rect.top;

      // Add a new vertex at the mouse position
      const newVertex = new Vertex(`V${graph.vertices.length + 1}`, mouseX, mouseY);
      const lastVertex = graph.vertices[graph.vertices.length - 1];
      lastVertex.addAdjacent(newVertex);
      graph.addVertex(newVertex);

      drawShortestPath(graph, previous);

      canvas.addEventListener("mousemove", handleMouseMove);
    }

    // Function to handle mouse up event
    function handleMouseUp(event) {
      const canvas = document.getElementById("canvas");
      canvas.removeEventListener("mousemove", handleMouseMove);
    }

    const graph = new Graph();

    // Define HTML elements and their positions
    const elementA = new Vertex("A", 120, 32);
    const elementB = new Vertex("B", 3, 11);
    const elementC = new Vertex("C", 420, 420);
    const elementD = new Vertex("D", 34, 22);
    const elementE = new Vertex("E", 90, 56);

    // Define adjacency relationships
    elementA.addAdjacent(elementB);
    elementA.addAdjacent(elementD);
    elementB.addAdjacent(elementC);
    elementC.addAdjacent(elementD);
    elementC.addAdjacent(elementE);
    elementD.addAdjacent(elementE);

    // Add vertices to the graph
    graph.addVertex(elementA);
    graph.addVertex(elementB);
    graph.addVertex(elementC);
    graph.addVertex(elementD);
    graph.addVertex(elementE);

    // Find the shortest path from elementA to all other elements
    const previous = dijkstra(graph, "A");

    const canvas = document.getElementById("canvas");
    const ctx = canvas.getContext("2d");

    canvas.addEventListener("mousedown", handleMouseDown);
    canvas.addEventListener("mouseup", handleMouseUp);
  </script>
</body>
</html>

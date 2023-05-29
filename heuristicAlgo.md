<!DOCTYPE html>
<html>
<head>
    <title>Drag and Snap Nodes</title>
    <style>
        #canvas {
            border: 1px solid black;
        }
    </style>
</head>
<body>
    <canvas id="canvas" width="400" height="400"></canvas>

    <script>
        // Initialize canvas and context
        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');

        // Create nodes
        const nodes = [
            { x: 100, y: 100, radius: 20, color: 'blue', snapped: false },
            { x: 200, y: 200, radius: 20, color: 'red', snapped: false },
            { x: 300, y: 300, radius: 20, color: 'green', snapped: false }
        ];

        // Variables for tracking dragging state
        let selectedNode = null;
        let offsetX = 0;
        let offsetY = 0;

        // Add event listeners
        canvas.addEventListener('mousedown', handleMouseDown);
        canvas.addEventListener('mousemove', handleMouseMove);
        canvas.addEventListener('mouseup', handleMouseUp);

        // Render the scene
        function render() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Draw nodes
            nodes.forEach(node => {
                ctx.fillStyle = node.color;
                ctx.beginPath();
                ctx.arc(node.x, node.y, node.radius, 0, 2 * Math.PI);
                ctx.fill();
            });

            // Draw snapped lines
            nodes.forEach(node => {
                if (node.snapped) {
                    const snappedNode = nodes.find(n => n !== node && n.snapped);

                    if (snappedNode) {
                        ctx.strokeStyle = 'black';
                        ctx.beginPath();
                        ctx.moveTo(node.x, node.y);
                        ctx.lineTo(snappedNode.x, snappedNode.y);
                        ctx.stroke();
                    }
                }
            });

            requestAnimationFrame(render);
        }

        // Handle mouse down event
        function handleMouseDown(event) {
            const { offsetX: clickX, offsetY: clickY } = event;

            // Find the selected node
            selectedNode = nodes.find(node =>
                Math.sqrt(Math.pow(clickX - node.x, 2) + Math.pow(clickY - node.y, 2)) <= node.radius
            );

            if (selectedNode) {
                // Calculate the offset between the mouse click and the node position
                offsetX = clickX - selectedNode.x;
                offsetY = clickY - selectedNode.y;
            }
        }

        // Handle mouse move event
        function handleMouseMove(event) {
            if (selectedNode) {
                // Update the position of the selected node based on mouse movement
                selectedNode.x = event.offsetX - offsetX;
                selectedNode.y = event.offsetY - offsetY;
            }
        }

        // Handle mouse up event
        function handleMouseUp() {
            if (selectedNode) {
                // Check if the node is snapped onto another node
                const snappedNode = nodes.find(node =>
                    node !== selectedNode &&
                    Math.sqrt(Math.pow(selectedNode.x - node.x, 2) + Math.pow(selectedNode.y - node.y, 2)) <= node.radius
                );

                if (snappedNode) {
                    // Snap the node to the snappedNode
                    selectedNode.x = snappedNode.x;
                    selectedNode.y = snappedNode.y;
                    selectedNode.snapped = true;
                } else {
                    selectedNode.snapped = false;
                }

                // Check if all nodes are snapped
                const allNodesSnapped = nodes.every(node => node.snapped);
                if (allNodesSnapped) {
                    console.log('All nodes are snapped!');
                    // You can perform additional actions here when all nodes are snapped
                }
            }

            selectedNode = null;
        }

        // Start rendering the scene
        render();
    </script>
</body>
</html>

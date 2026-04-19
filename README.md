# SCT_DS_01
Bar chart to visualize the distribution of  a categorical or continuous variable such as the distribution of ages or genders in the population
const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");

let nodes = [];
let edges = [];
let mode = "node";
let selectedNode = null;

canvas.addEventListener("click", function(e) {
    const x = e.offsetX;
    const y = e.offsetY;

    if (mode === "node") {
        nodes.push({x, y});
        draw();
    }

    if (mode === "edge") {
        nodes.forEach((node, index) => {
            const dist = Math.hypot(node.x - x, node.y - y);

            if (dist < 15) {
                if (selectedNode === null) {
                    selectedNode = index;
                } else {
                    const weight = prompt("Enter weight:");
                    edges.push({from: selectedNode, to: index, weight: Number(weight)});
                    selectedNode = null;
                    draw();
                }
            }
        });
    }
});

function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    edges.forEach(edge => {
        ctx.beginPath();
        ctx.moveTo(nodes[edge.from].x, nodes[edge.from].y);
        ctx.lineTo(nodes[edge.to].x, nodes[edge.to].y);
        ctx.stroke();
    });

    nodes.forEach((node, index) => {
        ctx.beginPath();
        ctx.arc(node.x, node.y, 15, 0, Math.PI * 2);
        ctx.fillStyle = "lightblue";
        ctx.fill();
        ctx.stroke();
        ctx.fillStyle = "black";
        ctx.fillText(index, node.x - 5, node.y + 5);
    });
}

function addNodeMode() {
    mode = "node";
}

function addEdgeMode() {
    mode = "edge";
}

function runKruskal() {
    let parent = Array(nodes.length).fill(0).map((_, i) => i);

    function find(i) {
        if (parent[i] === i) return i;
        return parent[i] = find(parent[i]);
    }

    function union(a, b) {
        let rootA = find(a);
        let rootB = find(b);
        if (rootA !== rootB) parent[rootA] = rootB;
    }

    let sortedEdges = [...edges].sort((a, b) => a.weight - b.weight);

    let mst = [];

    sortedEdges.forEach(edge => {
        if (find(edge.from) !== find(edge.to)) {
            union(edge.from, edge.to);
            mst.push(edge);
        }
    });

    highlightMST(mst);
}

function highlightMST(mst) {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    edges.forEach(edge => {
        ctx.beginPath();
        ctx.moveTo(nodes[edge.from].x, nodes[edge.from].y);
        ctx.lineTo(nodes[edge.to].x, nodes[edge.to].y);
        ctx.strokeStyle = "gray";
        ctx.stroke();
    });

    mst.forEach(edge => {
        ctx.beginPath();
        ctx.moveTo(nodes[edge.from].x, nodes[edge.from].y);
        ctx.lineTo(nodes[edge.to].x, nodes[edge.to].y);
        ctx.strokeStyle = "green";
        ctx.lineWidth = 3;
        ctx.stroke();
    });

    draw();
}

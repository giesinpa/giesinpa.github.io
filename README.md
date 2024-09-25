# giesinpa.github.io


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Morphing Blob with Border Glow</title>
    <style>
        body, html {
            margin: 0;
            padding: 0;
            height: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
            background-color: #000;
            overflow: hidden;
        }
        #canvas {
            position: absolute;
        }
    </style>
</head>
<body>
    <canvas id="canvas"></canvas>
    <script>
        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');
        let width, height;
        let time = 0;
        function resize() {
            width = canvas.width = window.innerWidth;
            height = canvas.height = window.innerHeight;
        }
        window.addEventListener('resize', resize);
        resize();
        function drawShape(t) {
            ctx.beginPath();
            for (let i = 0; i < Math.PI * 2; i += 0.01) {
                const noise = Math.sin(t * 2 + i * 5) * 30 + Math.sin(t * 3 + i * 3) * 20;
                const r = 100 + noise;
                const x = Math.cos(i) * r;
                const y = Math.sin(i) * r;
                ctx.lineTo(x, y);
            }
            ctx.closePath();
        }
        function hslToRgb(h, s, l) {
            let r, g, b;
            if (s === 0) {
                r = g = b = l;
            } else {
                const hue2rgb = (p, q, t) => {
                    if (t < 0) t += 1;
                    if (t > 1) t -= 1;
                    if (t < 1/6) return p + (q - p) * 6 * t;
                    if (t < 1/2) return q;
                    if (t < 2/3) return p + (q - p) * (2/3 - t) * 6;
                    return p;
                };
                const q = l < 0.5 ? l * (1 + s) : l + s - l * s;
                const p = 2 * l - q;
                r = hue2rgb(p, q, h + 1/3);
                g = hue2rgb(p, q, h);
                b = hue2rgb(p, q, h - 1/3);
            }
            return [Math.round(r * 255), Math.round(g * 255), Math.round(b * 255)];
        }
        function animate() {
            ctx.clearRect(0, 0, width, height);
            ctx.save();
            ctx.translate(width / 2, height / 2);
            // Dynamic colorful border glow
            const hue1 = (Math.sin(time * 0.1) + 1) / 2;
            const hue2 = (Math.cos(time * 0.15) + 1) / 2;
            const hue3 = (Math.sin(time * 0.2 + Math.PI/2) + 1) / 2;
            const [r1, g1, b1] = hslToRgb(hue1, 1, 0.5);
            const [r2, g2, b2] = hslToRgb(hue2, 1, 0.5);
            const [r3, g3, b3] = hslToRgb(hue3, 1, 0.5);
            // Outer glow
            ctx.shadowColor = `rgb(${r1}, ${g1}, ${b1})`;
            ctx.shadowBlur = 20;
            drawShape(time);
            ctx.lineWidth = 15;
            ctx.strokeStyle = `rgba(${r2}, ${g2}, ${b2}, 0.5)`;
            ctx.stroke();
            // Inner glow
            ctx.shadowColor = `rgb(${r3}, ${g3}, ${b3})`;
            ctx.shadowBlur = 10;
            ctx.lineWidth = 8;
            ctx.strokeStyle = `rgba(${r1}, ${g1}, ${b1}, 0.7)`;
            ctx.stroke();
            // Main shape
            ctx.shadowBlur = 0;
            ctx.fillStyle = '#fff';
            drawShape(time);
            ctx.fill();
            ctx.restore();
            time += 0.03;
            requestAnimationFrame(animate);
        }
        animate();
    </script>
</body>
</html>

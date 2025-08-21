---
title: "Get In Touch"
date: 2024-01-01T08:00:00-00:00
draft: false
description: "Contact Aidan Law for healthcare IT consulting. Answer a simple question to access direct contact or send a message."
---

To help reduce spam while keeping things accessible for healthcare professionals, please answer this simple question:

<div class="email-challenge-page">
<div id="challenge-form">
<div class="challenge-question">
<h3>Visual Illusion Challenge</h3>
<p class="question-text">What do you see in this image?</p>
<div class="illusion-captcha">
<canvas id="captcha-canvas" width="300" height="200"></canvas>
</div>
<p class="hint">💡 <em>Look at the whole image - what object do you recognize?</em></p>

<div id="challenge-options" style="margin-top: 1rem;">
<div class="option-group">
<label class="option-label">
<input type="radio" name="answer" value="A" />
<span class="option-text" id="option-a"></span>
</label>
<label class="option-label">
<input type="radio" name="answer" value="B" />
<span class="option-text" id="option-b"></span>
</label>
<label class="option-label">
<input type="radio" name="answer" value="C" />
<span class="option-text" id="option-c"></span>
</label>
<label class="option-label">
<input type="radio" name="answer" value="D" />
<span class="option-text" id="option-d"></span>
</label>
</div>
</div>

<button onclick="checkAnswer()" class="challenge-button">Continue</button>
<button onclick="generateCaptcha()" class="refresh-button" type="button">🔄 New Image</button>
</div>
</div>

<div id="contact-options" style="display: none;">
<h3>✅ Perfect! Choose how you'd like to contact me:</h3>

<div class="contact-methods">
<div class="contact-option">
<h4>📧 Direct Email</h4>
<p><strong>Email:</strong> <a href="mailto:aidan@aidanlaw.xyz">aidan@aidanlaw.xyz</a></p>
<p><strong>Response Time:</strong> Within 24 hours</p>
<p class="email-tips"><strong>Subject Line Suggestion:</strong> "Healthcare IT Consultation Request"</p>
</div>

<div class="contact-divider">
<span>OR</span>
</div>

<div class="contact-option">
<h4>📝 Send Message Now</h4>
<form id="contact-form" onsubmit="sendMessage(event)">
<div class="form-group">
<label for="name">Name *</label>
<input type="text" id="name" name="name" required />
</div>

<div class="form-group">
<label for="practice">Practice/Organization</label>
<input type="text" id="practice" name="practice" />
</div>

<div class="form-group">
<label for="email">Your Email *</label>
<input type="email" id="email" name="email" required />
</div>

<div class="form-group">
<label for="subject">Subject</label>
<input type="text" id="subject" name="subject" value="Healthcare IT Consultation Request" />
</div>

<div class="form-group">
<label for="message">Message *</label>
<textarea id="message" name="message" rows="5" placeholder="Tell me about your practice size, main IT challenges, and preferred meeting time..." required></textarea>
</div>

<button type="submit" class="submit-button">Send Message</button>
</form>
</div>
</div>

<div id="message-sent" style="display: none;">
<h3>✅ Message Sent Successfully!</h3>
<p>Thank you for reaching out. I'll respond within 24 hours.</p>
<p><strong>Next Steps:</strong></p>
<ul>
<li>Check your email for a confirmation</li>
<li>I'll review your message and get back to you with initial thoughts</li>
<li>We can schedule a call if it looks like a good fit</li>
</ul>
</div>
</div>
</div>

<script>
let correctAnswer;
let canvas, ctx;
let currentChallengeType;

// Different types of visual illusions
const illusions = [
    {
        type: 'kanizsa_triangle',
        correctAnswer: 'C',
        options: ['A square', 'A circle', 'A triangle', 'A diamond'],
        inducement: 'This image shows pac-man shapes arranged randomly. Look for the basic geometric shape formed by the negative space.',
        draw: drawKanizsaTriangle
    },
    {
        type: 'hermann_grid',
        correctAnswer: 'B', 
        options: ['White dots only', 'Gray dots at intersections', 'Black squares only', 'A checkerboard pattern'],
        inducement: 'This is a simple grid pattern. Count the actual drawn elements you can see.',
        draw: drawHermannGrid
    },
    {
        type: 'necker_cube',
        correctAnswer: 'A',
        options: ['A cube or 3D box', 'A flat hexagon', 'Two triangles', 'A star shape'],
        inducement: 'This shows intersecting lines forming a 2D geometric pattern. What 2D shape do you see?',
        draw: drawNeckerCube
    },
    {
        type: 'rubin_vase',
        correctAnswer: 'D',
        options: ['Only a vase', 'Only two faces', 'A butterfly', 'Either a vase or two faces'],
        inducement: 'This is a simple silhouette drawing. What single object is clearly depicted?',
        draw: drawRubinVase
    },
    {
        type: 'muller_lyer',
        correctAnswer: 'B',
        options: ['The top line is longer', 'Both lines are the same length', 'The bottom line is longer', 'The lines are curved'],
        inducement: 'These are two horizontal lines with arrow heads. Measure the length of each line segment.',
        draw: drawMullerLyer
    },
    {
        type: 'penrose_triangle',
        correctAnswer: 'C',
        options: ['A normal triangle', 'Three separate L-shapes', 'An impossible triangle', 'A hexagon'],
        inducement: 'This shows three connected rectangular beams forming a triangular structure. What geometric shape is formed?',
        draw: drawPenroseTriangle
    },
    {
        type: 'zollner_lines',
        correctAnswer: 'A',
        options: ['The lines are parallel', 'The lines converge at the top', 'The lines converge at the bottom', 'The lines form a zigzag'],
        inducement: 'These are diagonal lines with small intersecting segments. Trace the direction of the main lines.',
        draw: drawZollnerLines
    },
    {
        type: 'ebbinghaus_circles',
        correctAnswer: 'B',
        options: ['The left circle is bigger', 'Both circles are the same size', 'The right circle is bigger', 'One is a square'],
        inducement: 'Two circles surrounded by other shapes. Compare the area of the two central circles.',
        draw: drawEbbinghausCircles
    },
    {
        type: 'rotating_snakes',
        correctAnswer: 'D',
        options: ['The circles are actually rotating', 'The pattern is animated', 'The image is moving', 'The circles appear to move but are static'],
        inducement: 'This shows a series of circular patterns. Observe if there is actual motion in the image.',
        draw: drawRotatingSnakes
    },
    {
        type: 'fraser_spiral',
        correctAnswer: 'A',
        options: ['Concentric circles', 'A continuous spiral', 'Overlapping squares', 'Random dots'],
        inducement: 'This pattern appears to spiral inward continuously. What is the actual underlying structure?',
        draw: drawFraserSpiral
    },
    {
        type: 'adelson_checker',
        correctAnswer: 'B',
        options: ['Square A is lighter', 'Squares A and B are the same shade', 'Square B is lighter', 'There are no squares'],
        inducement: 'This is a checkerboard with two marked squares A and B. Compare their actual RGB color values.',
        draw: drawAdelsonChecker
    },
    {
        type: 'ponzo_illusion',
        correctAnswer: 'B',
        options: ['The top line is longer', 'Both lines are the same length', 'The bottom line is longer', 'The lines are at an angle'],
        inducement: 'Two horizontal lines between converging lines. Measure the pixel length of each horizontal line.',
        draw: drawPonzoIllusion
    }
];

function generateCaptcha() {
    canvas = document.getElementById('captcha-canvas');
    ctx = canvas.getContext('2d');
    
    // Select random illusion
    const illusion = illusions[Math.floor(Math.random() * illusions.length)];
    currentChallengeType = illusion;
    correctAnswer = illusion.correctAnswer;
    
    // Clear canvas
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.fillStyle = '#f9f9f9';
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    
    // Draw the illusion
    illusion.draw();
    
    // Update question text with inducement prompt
    document.querySelector('.question-text').textContent = illusion.inducement;
    
    // Update options
    const options = ['option-a', 'option-b', 'option-c', 'option-d'];
    options.forEach((id, index) => {
        document.getElementById(id).textContent = illusion.options[index];
    });
}

function drawKanizsaTriangle() {
    // Draw three "pac-man" shapes that create the illusion of a white triangle
    ctx.fillStyle = '#000';
    
    // Top pac-man (pointing down)
    ctx.beginPath();
    ctx.arc(150, 60, 30, Math.PI * 0.2, Math.PI * 1.8);
    ctx.lineTo(150, 60);
    ctx.fill();
    
    // Bottom left pac-man (pointing right)
    ctx.beginPath();
    ctx.arc(90, 140, 30, -Math.PI * 0.3, Math.PI * 0.7);
    ctx.lineTo(90, 140);
    ctx.fill();
    
    // Bottom right pac-man (pointing left)
    ctx.beginPath();
    ctx.arc(210, 140, 30, Math.PI * 0.3, Math.PI * 1.7);
    ctx.lineTo(210, 140);
    ctx.fill();
    
    // Add some circles to make it look more random
    ctx.beginPath();
    ctx.arc(50, 50, 15, 0, Math.PI * 2);
    ctx.fill();
    
    ctx.beginPath();
    ctx.arc(250, 80, 18, 0, Math.PI * 2);
    ctx.fill();
}

function drawHermannGrid() {
    // Draw Hermann grid - black squares with white intersections that appear gray
    ctx.fillStyle = '#000';
    const squareSize = 25;
    const spacing = 35;
    
    for (let row = 0; row < 6; row++) {
        for (let col = 0; col < 8; col++) {
            const x = col * spacing + 20;
            const y = row * spacing + 20;
            ctx.fillRect(x, y, squareSize, squareSize);
        }
    }
}

function drawNeckerCube() {
    // Draw wireframe cube that can be perceived in two orientations
    ctx.strokeStyle = '#000';
    ctx.lineWidth = 2;
    
    // Front face
    ctx.strokeRect(100, 80, 80, 80);
    
    // Back face (offset)
    ctx.strokeRect(130, 50, 80, 80);
    
    // Connect corners
    ctx.beginPath();
    // Top connections
    ctx.moveTo(100, 80); ctx.lineTo(130, 50);
    ctx.moveTo(180, 80); ctx.lineTo(210, 50);
    // Bottom connections  
    ctx.moveTo(100, 160); ctx.lineTo(130, 130);
    ctx.moveTo(180, 160); ctx.lineTo(210, 130);
    ctx.stroke();
}

function drawRubinVase() {
    // Draw Rubin's vase - can be seen as vase or two faces in profile
    ctx.fillStyle = '#000';
    
    // Create the vase/faces shape
    ctx.beginPath();
    ctx.moveTo(80, 50);
    
    // Left profile
    ctx.bezierCurveTo(70, 60, 75, 80, 85, 100);
    ctx.bezierCurveTo(80, 120, 85, 140, 90, 160);
    ctx.lineTo(90, 180);
    
    // Bottom of vase
    ctx.lineTo(210, 180);
    
    // Right profile (mirrored)
    ctx.lineTo(210, 160);
    ctx.bezierCurveTo(215, 140, 220, 120, 215, 100);
    ctx.bezierCurveTo(225, 80, 230, 60, 220, 50);
    
    // Top of vase
    ctx.lineTo(80, 50);
    ctx.fill();
}

function drawMullerLyer() {
    ctx.strokeStyle = '#000';
    ctx.lineWidth = 2;
    
    // Two horizontal lines of equal length
    const lineLength = 100;
    const y1 = 80, y2 = 130;
    const startX = 100;
    
    // Top line with outward arrows
    ctx.beginPath();
    ctx.moveTo(startX, y1);
    ctx.lineTo(startX + lineLength, y1);
    // Left arrow (outward)
    ctx.moveTo(startX, y1);
    ctx.lineTo(startX + 15, y1 - 10);
    ctx.moveTo(startX, y1);
    ctx.lineTo(startX + 15, y1 + 10);
    // Right arrow (outward)
    ctx.moveTo(startX + lineLength, y1);
    ctx.lineTo(startX + lineLength - 15, y1 - 10);
    ctx.moveTo(startX + lineLength, y1);
    ctx.lineTo(startX + lineLength - 15, y1 + 10);
    ctx.stroke();
    
    // Bottom line with inward arrows
    ctx.beginPath();
    ctx.moveTo(startX, y2);
    ctx.lineTo(startX + lineLength, y2);
    // Left arrow (inward)
    ctx.moveTo(startX, y2);
    ctx.lineTo(startX - 15, y2 - 10);
    ctx.moveTo(startX, y2);
    ctx.lineTo(startX - 15, y2 + 10);
    // Right arrow (inward)
    ctx.moveTo(startX + lineLength, y2);
    ctx.lineTo(startX + lineLength + 15, y2 - 10);
    ctx.moveTo(startX + lineLength, y2);
    ctx.lineTo(startX + lineLength + 15, y2 + 10);
    ctx.stroke();
}

function drawPenroseTriangle() {
    ctx.strokeStyle = '#000';
    ctx.lineWidth = 2;
    ctx.fillStyle = '#ddd';
    
    // Draw the impossible Penrose triangle
    ctx.beginPath();
    
    // Bottom horizontal bar
    ctx.moveTo(90, 160);
    ctx.lineTo(210, 160);
    ctx.lineTo(210, 140);
    ctx.lineTo(180, 140);
    ctx.lineTo(150, 70);
    ctx.lineTo(130, 70);
    ctx.lineTo(90, 160);
    
    ctx.fill();
    ctx.stroke();
    
    // Right bar
    ctx.beginPath();
    ctx.moveTo(180, 140);
    ctx.lineTo(210, 140);
    ctx.lineTo(180, 60);
    ctx.lineTo(160, 60);
    ctx.lineTo(130, 70);
    ctx.lineTo(150, 70);
    ctx.lineTo(180, 140);
    
    ctx.fill();
    ctx.stroke();
    
    // Left bar  
    ctx.beginPath();
    ctx.moveTo(130, 70);
    ctx.lineTo(160, 60);
    ctx.lineTo(120, 140);
    ctx.lineTo(100, 150);
    ctx.lineTo(90, 160);
    ctx.lineTo(120, 140);
    ctx.lineTo(130, 70);
    
    ctx.fill();
    ctx.stroke();
}

function drawZollnerLines() {
    ctx.strokeStyle = '#000';
    ctx.lineWidth = 2;
    
    // Draw parallel lines that appear to converge due to hash marks
    for (let i = 0; i < 4; i++) {
        const y = 60 + i * 25;
        
        // Main horizontal line
        ctx.beginPath();
        ctx.moveTo(50, y);
        ctx.lineTo(250, y);
        ctx.stroke();
        
        // Hash marks alternating direction
        for (let x = 60; x < 240; x += 20) {
            ctx.beginPath();
            if (i % 2 === 0) {
                ctx.moveTo(x, y - 8);
                ctx.lineTo(x + 12, y + 8);
            } else {
                ctx.moveTo(x, y + 8);
                ctx.lineTo(x + 12, y - 8);
            }
            ctx.stroke();
        }
    }
}

function drawEbbinghausCircles() {
    ctx.fillStyle = '#000';
    
    // Left central circle (surrounded by large circles)
    ctx.beginPath();
    ctx.arc(100, 100, 15, 0, Math.PI * 2);
    ctx.fill();
    
    // Large surrounding circles (left)
    for (let i = 0; i < 6; i++) {
        const angle = (i * Math.PI * 2) / 6;
        const x = 100 + Math.cos(angle) * 40;
        const y = 100 + Math.sin(angle) * 40;
        ctx.beginPath();
        ctx.arc(x, y, 20, 0, Math.PI * 2);
        ctx.fill();
    }
    
    // Right central circle (same size, surrounded by small circles)
    ctx.beginPath();
    ctx.arc(200, 100, 15, 0, Math.PI * 2);
    ctx.fill();
    
    // Small surrounding circles (right)
    for (let i = 0; i < 8; i++) {
        const angle = (i * Math.PI * 2) / 8;
        const x = 200 + Math.cos(angle) * 30;
        const y = 100 + Math.sin(angle) * 30;
        ctx.beginPath();
        ctx.arc(x, y, 6, 0, Math.PI * 2);
        ctx.fill();
    }
}

function drawRotatingSnakes() {
    // Create rotating snakes illusion pattern
    const colors = ['#000', '#666', '#aaa', '#fff'];
    
    for (let i = 0; i < 3; i++) {
        for (let j = 0; j < 2; j++) {
            const centerX = 80 + i * 80;
            const centerY = 80 + j * 60;
            
            // Draw circular segments
            for (let k = 0; k < 4; k++) {
                ctx.fillStyle = colors[k];
                ctx.beginPath();
                ctx.arc(centerX, centerY, 25, 
                    (k * Math.PI) / 2 + i * 0.3, 
                    ((k + 1) * Math.PI) / 2 + i * 0.3);
                ctx.lineTo(centerX, centerY);
                ctx.fill();
            }
        }
    }
}

function drawFraserSpiral() {
    // Fraser spiral - appears spiral but actually concentric circles
    ctx.strokeStyle = '#000';
    ctx.lineWidth = 1;
    
    for (let radius = 20; radius < 100; radius += 15) {
        // Draw circle with alternating black/white segments
        for (let angle = 0; angle < Math.PI * 2; angle += 0.1) {
            const intensity = Math.sin(angle * 8) > 0 ? 255 : 0;
            ctx.strokeStyle = `rgb(${intensity}, ${intensity}, ${intensity})`;
            
            const x1 = 150 + Math.cos(angle) * radius;
            const y1 = 100 + Math.sin(angle) * radius;
            const x2 = 150 + Math.cos(angle + 0.1) * radius;
            const y2 = 100 + Math.sin(angle + 0.1) * radius;
            
            ctx.beginPath();
            ctx.moveTo(x1, y1);
            ctx.lineTo(x2, y2);
            ctx.stroke();
        }
    }
}

function drawAdelsonChecker() {
    // Adelson's checker shadow illusion
    const squareSize = 20;
    
    // Draw checkerboard
    for (let row = 0; row < 8; row++) {
        for (let col = 0; col < 12; col++) {
            const isLight = (row + col) % 2 === 0;
            ctx.fillStyle = isLight ? '#ddd' : '#666';
            ctx.fillRect(col * squareSize + 30, row * squareSize + 20, squareSize, squareSize);
        }
    }
    
    // Add cylinder shadow
    const gradient = ctx.createLinearGradient(120, 40, 200, 120);
    gradient.addColorStop(0, 'rgba(0,0,0,0)');
    gradient.addColorStop(0.5, 'rgba(0,0,0,0.4)');
    gradient.addColorStop(1, 'rgba(0,0,0,0)');
    
    ctx.fillStyle = gradient;
    ctx.fillRect(120, 40, 80, 80);
    
    // Mark squares A and B
    ctx.fillStyle = 'red';
    ctx.font = '12px Arial';
    ctx.fillText('A', 85, 95);
    ctx.fillText('B', 165, 95);
}

function drawPonzoIllusion() {
    ctx.strokeStyle = '#000';
    ctx.lineWidth = 2;
    
    // Draw converging lines (railroad tracks)
    ctx.beginPath();
    ctx.moveTo(80, 50);
    ctx.lineTo(150, 150);
    ctx.moveTo(220, 50);
    ctx.lineTo(150, 150);
    ctx.stroke();
    
    // Draw two horizontal lines of equal length
    ctx.lineWidth = 3;
    ctx.strokeStyle = '#ff0000';
    
    // Top line
    ctx.beginPath();
    ctx.moveTo(120, 70);
    ctx.lineTo(180, 70);
    ctx.stroke();
    
    // Bottom line (same length)
    ctx.beginPath();
    ctx.moveTo(135, 110);
    ctx.lineTo(165, 110);
    ctx.stroke();
}

function checkAnswer() {
    const selectedOption = document.querySelector('input[name="answer"]:checked');
    
    if (!selectedOption) {
        alert('Please select an answer before continuing.');
        return;
    }
    
    if (selectedOption.value === correctAnswer) {
        document.getElementById('challenge-form').style.display = 'none';
        document.getElementById('contact-options').style.display = 'block';
    } else {
        alert('That doesn\'t match what humans typically see. Generating a new challenge...');
        // Clear selection
        document.querySelectorAll('input[name="answer"]').forEach(radio => radio.checked = false);
        // Generate new challenge to prevent brute force
        generateCaptcha();
    }
}

// Generate captcha when page loads
document.addEventListener('DOMContentLoaded', function() {
    generateCaptcha();
});

function sendMessage(event) {
    event.preventDefault();
    
    const formData = new FormData(event.target);
    const name = formData.get('name');
    const practice = formData.get('practice');
    const email = formData.get('email');
    const subject = formData.get('subject');
    const message = formData.get('message');
    
    // Create mailto link with form data
    const practiceText = practice ? `\nPractice/Organization: ${practice}` : '';
    const emailBody = `From: ${name}${practiceText}\nEmail: ${email}\n\nMessage:\n${message}`;
    const mailtoLink = `mailto:aidan@aidanlaw.xyz?subject=${encodeURIComponent(subject)}&body=${encodeURIComponent(emailBody)}`;
    
    // Open email client
    window.location.href = mailtoLink;
    
    // Show success message
    document.getElementById('contact-options').style.display = 'none';
    document.getElementById('message-sent').style.display = 'block';
    
    return false;
}
</script>

---

## Why This Visual Challenge?

I was getting absolutely bombarded with spam bots and AI-generated sales pitches. Traditional CAPTCHAs are getting cracked left and right, so I built something more interesting - a visual illusion challenge that exploits the fundamental differences between how humans and machines process visual information.

### How This CAPTCHA Works

**Visual Illusion Technology:** Unlike traditional text-based CAPTCHAs, this system uses well-researched visual illusions that exploit fundamental differences between human perception and computer vision:

- **Human Visual Processing:** Your brain automatically processes these illusions due to evolutionary visual perception patterns
- **AI Limitations:** Current AI systems struggle with perceptual illusions because they analyze images pixel-by-pixel rather than using human-like gestalt perception
- **Dynamic Generation:** Each wrong answer generates a completely new challenge from our library of 12+ different illusion types

**Why It's More Effective:**
- **LLM-Resistant:** The system includes "inducement prompts" - misleading text designed to trick language models into selecting wrong answers
- **Brute Force Protection:** New challenges prevent automated attempts to solve through trial and error
- **Accessibility:** Visual illusions work across languages and don't require typing or complex interactions
- **User-Friendly:** Most humans solve these intuitively within seconds

### Technical Background
This implementation is based on research in computational vision and the IllusionCAPTCHA framework, which demonstrates that classic optical illusions remain one of the most reliable methods for distinguishing human perception from artificial intelligence systems.
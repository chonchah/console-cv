<template>
  <div class="terminal-container w-full max-w-[900px] min-h-[60vh] 
         bg-black border-2 border-[#00ff00]
         shadow-[0_0_15px_rgba(0,255,0,0.5)] rounded-lg 
         p-3 sm:p-5 flex flex-col font-mono">
    <div class="terminal-container w-[95%] max-w-[900px] min-h-[70vh] bg-black border-2 border-[#00ff00] shadow-[0_0_15px_rgba(0,255,0,0.5)] rounded-lg p-5 flex flex-col">

      <!-- HEADER -->
      <div
        class="terminal-header bg-[#333] text-white px-3 py-1 rounded-t-md text-sm flex justify-between items-center border-b border-[#00ff00] -mx-5 -mt-5 mb-5"
      >
        <div class="dots flex gap-2">
          <div class="dot red"></div>
          <div class="dot yellow"></div>
          <div class="dot green"></div>
        </div>
        <span>Alexis Betancourt - CV (bash)</span>
      </div>

      <!-- TERMINAL BODY -->
      <div
        id="terminal-body"
        ref="terminalBody"
        class="terminal-body flex-grow overflow-y-auto 
         max-h-[50vh] sm:max-h-[60vh] md:max-h-[70vh]
         overflow-x-hidden"
      >
        <div class="command-output">
          <span class="prompt"></span>Welcome to Alexis Betancourt's CV Console.
          <br />Type 'ls' or use the navigation links below to explore the sections.
        </div>
      </div>

      <!-- NAV -->
      <div class="mt-4 pt-4 border-t border-gray-700 flex flex-wrap gap-4 justify-center text-sm">
        <a
          v-for="(item, key) in CV_DATA"
          :key="key"
          href="#"
          class="nav-link"
          @click.prevent="runSection(key)"
        >
          ./{{ key }}
        </a>
      </div>

    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

// Terminal reference
const terminalBody = ref(null);

const TYPING_DELAY = 3;
const COMMAND_DELAY = 50;
const SECTION_DURATION = 3000;

const CV_DATA = {
  summary: {
                title: "Professional Summary",
                content: `Systems Engineer with more than six years of experience in software development, cloud services, Linux systems, infrastructure automation, and distributed applications. Skilled in Java, Python, Node.js, containerization, and modern web frameworks. Experienced in deploying cloud-native systems using GCP, Firebase, and Linux servers. Strong background in troubleshooting, API integration, backend development, microservices, and operational monitoring. Solid communication skills and a consistent track record of delivering reliable technical solutions.
`,
                command: "/summary"
            },
            skills: {
                title: "Core Skills",
                content: `Software Development: Java, Python, Node.js, JavaScript, TypeScript.
Cloud & Infrastructure: GCP, Firebase, Linux servers, Postgres, Caddy.
IaC & Automation: Docker, scripting, CI/CD fundamentals.
Backend Development: REST APIs, authentication, cloud SDKs.
Frameworks & Tools: Angular, Vue, Docker, Hugging Face, Google Cloud SDK.
Observability & DevOps: Basic monitoring, diagnostics, logs analysis, Containerization, automated deployments, version control.
`,
                command: "/skills"
            },
            experience: {
                title: "Professional Experience",
                content: `
> TuHabi (Apr 2022 – Apr 2023) - Software Developer (Microservices Focus)
  - Focused on building scalable backend solutions for the real estate sector using cloud-native technologies (AWS ecosystem).
  - Designed robust REST APIs for high-volume transactions, ensuring low latency.
  - Tech Stack: Python, AWS, Serverless Framework, MySQL, React.

> Ag Tools Inc. (Jan 2018 – Nov 2020) - Full Stack Developer & Tech Lead
  - Led development for a Business Intelligence platform in the agriculture sector.
  - Developed a high-performance API Endpoint capable of querying 50M+ records in under 1 second.
  - Engineered a multi-threaded data scraping microservice in Python (Pandas).
  - Managed Big Data infrastructure using MongoDB, Redis.
  - Tech Stack: Python, PHP (Laravel), MongoDB, Docker.

> Soltar Impresores / Local SMES (Sep 2019 – Present) - IT Consultant & Systems Engineer
  - Lead digital transformation and commercial application development (CRM, POS systems).
  - Architected a Pub/Sub event-based system to automate print file processing, reducing operational costs.
  - Used Docker for service packaging and reproducible deployments.
`,
                command: "/experience"
            },
            projects: {
                title: "Relevant Technical Projects",
                content: `
> Custom Access Management (Java Developer)
  - Developed a Java-based system to manage user access permissions for a custom enterprise solution.
  - Implemented business logic using Google Cloud SDK for identity, authorization, and secure resource access (Firebase + Google Cloud environment).
  - Delivered a robust backend integrating cloud IAM operations, REST APIs, and authentication flows.

> Infrastructure Modernization (Cloud & Infrastructure Implementation)
  - Deployed a production-ready Postgres + Caddy server stack on Linux-based environments (including ARM).
  - Built distributed microservices combining Python, Node.js, and ESP32 hardware for IoT integration.
  - Integrated PAC API for digital invoicing with secure token-based workflows.
`,
                command: "/projects"
            },
            education: {
                title: "Education",
                content: `
> UTEL University (2019-2023)
  - Bachelor's Degree in Computer Systems Engineering.
  - Grade: 89/100. Professional License in process.

> Languages
  - Spanish: Native
  - English: Professional Technical Proficiency
`,
                command: "/education"
            },
            contact: {
                title: "Contact Info",
                content: `
> Email: alexis@caveri.mx | chonchah@gmail.com
> Phone: +52 443 529 0328
> LinkedIn: linkedin.com/in/alexis-betancourt
> GitHub: github.com/chonchah
> Location: Los Reyes de Salgado, Michoacán, Mexico
`,
                command: "/contact"
            }
        };

let isTyping = false;

// ---------------------------------------------------------
// Scroll Helper
// ---------------------------------------------------------
function scrollBottom() {
  const el = terminalBody.value;
  el.scrollTop = el.scrollHeight;
}

// ---------------------------------------------------------
// Typewriter for terminal
// ---------------------------------------------------------
function typeText(el, text, delay) {
  return new Promise((resolve) => {
    let i = 0;
    let buffer = "";

    const span = document.createElement("span");
    const cursor = document.createElement("span");
    cursor.className = "cursor";

    el.appendChild(span);
    el.appendChild(cursor);

    function escapeHtml(str) {
      return str
        .replace(/&/g, "&amp;")
        .replace(/</g, "&lt;")
        .replace(/>/g, "&gt;")
        .replace(/"/g, "&quot;")
        .replace(/'/g, "&#039;");
    }

    const interval = setInterval(() => {
      if (i < text.length) {
        const char = text[i];

        if (char === "\n") buffer += "<br>";
        else if (char === " ") buffer += " ";
        else buffer += escapeHtml(char);

        span.innerHTML = buffer;
        scrollBottom();
        i++;
      } else {
        clearInterval(interval);
        el.removeChild(cursor);
        resolve();
      }
    }, delay);
  });
}

// ---------------------------------------------------------
// Main command executor
// ---------------------------------------------------------
async function typeToTerminal(command, content) {
  if (isTyping) return;
  isTyping = true;

  const commandLine = document.createElement("div");
  commandLine.className = "command-output prompt";
  terminalBody.value.appendChild(commandLine);

  await typeText(commandLine, command, COMMAND_DELAY);

  const output = document.createElement("div");
  output.className = "command-output";
  terminalBody.value.appendChild(output);

  const delay = SECTION_DURATION / content.length;
  await typeText(output, content, delay);

  scrollBottom();
  isTyping = false;
}

// ---------------------------------------------------------
// Clear terminal except first message
// ---------------------------------------------------------
function clearTerminal() {
  const body = terminalBody.value;
  while (body.children.length > 1) {
    body.removeChild(body.lastChild);
  }
}

// ---------------------------------------------------------
// Run a section
// ---------------------------------------------------------
async function runSection(key) {
  clearTerminal();
  const data = CV_DATA[key];
  await typeToTerminal(data.command, data.content);
}

onMounted(() => {
  // Reset initial message
  terminalBody.value.innerHTML = `
      <div class="command-output">
        <span class="prompt"></span>Welcome to Alexis Betancourt's CV Console.
        <br>Type 'ls' or use the navigation links below to explore the sections.
      </div>
  `;
  runSection("summary");
});
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Fira+Code:wght@400;600&display=swap");

.font-mono {
  font-family: "Fira Code", monospace;
}

.prompt::before {
  content: "chonchah@gmail.com:~$ ";
  color: white;
  margin-right: 5px;
}

.cursor {
  background-color: #00ff00;
  width: 8px;
  height: 1.1em;
  margin-left: 2px;
  display: inline-block;
  animation: blink 1s step-end infinite;
}

@keyframes blink {
  0%, 100% {
    visibility: visible;
  }
  50% {
    visibility: hidden;
  }
}

.dot {
  width: 12px;
  height: 12px;
  border-radius: 50%;
}
.red { background: #ff5f56; }
.yellow { background: #ffbd2e; }
.green { background: #27c93f; }

.nav-link {
  color: #00ff00;
  cursor: pointer;
  text-decoration: none;
}
.nav-link:hover {
  color: white;
  text-shadow: 0 0 5px rgba(0, 255, 0, 0.8);
}

.command-output {
  margin-bottom: 15px;
  white-space: pre-wrap;
  word-break: break-word;
  overflow-wrap: break-word;
  line-height: 1.4;
  font-size: 0.85rem;
}

@media (min-width: 640px) { /* sm */
  .command-output {
    font-size: 1rem;
  }
}

</style>

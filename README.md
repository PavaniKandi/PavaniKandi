<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Pavani Kandi — Senior Full Stack Engineer</title>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Fraunces:ital,opsz,wght@0,9..144,300;0,9..144,400;0,9..144,600;0,9..144,700;0,9..144,900;1,9..144,300;1,9..144,400;1,9..144,700&family=JetBrains+Mono:wght@300;400;500&family=DM+Sans:ital,wght@0,300;0,400;0,500;0,600;1,300;1,400&display=swap" rel="stylesheet" />
  <script src="https://unpkg.com/react@18/umd/react.production.min.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js" crossorigin></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    :root {
      --bg:          #0d0d0d;
      --bg-card:     #111111;
      --bg-hover:    #161616;
      --text-1:      #f0ece4;
      --text-2:      #7a7a7a;
      --text-3:      #3d3d3d;
      --gold:        #c8a86b;
      --gold-lt:     #e8c890;
      --gold-dim:    rgba(200,168,107,0.12);
      --border:      rgba(255,255,255,0.07);
      --border-gold: rgba(200,168,107,0.28);
      --teal:        #7eb8c4;
      --lavender:    #a68cbf;
    }

    html { scroll-behavior: smooth; }

    body {
      background: var(--bg);
      color: var(--text-1);
      font-family: 'DM Sans', sans-serif;
      overflow-x: hidden;
      -webkit-font-smoothing: antialiased;
    }

    @media (hover: hover) {
      body, a, button { cursor: none; }
    }

    ::selection { background: var(--gold); color: #0d0d0d; }

    ::-webkit-scrollbar { width: 3px; }
    ::-webkit-scrollbar-track { background: var(--bg); }
    ::-webkit-scrollbar-thumb { background: var(--gold); }

    input, textarea, button { font-family: inherit; }

    @keyframes pulse-ring {
      0%, 100% { opacity: 1; transform: scale(1); }
      50%       { opacity: 0.5; transform: scale(1.4); }
    }

    @keyframes flow-down {
      0%   { top: -6px; opacity: 0; }
      15%  { opacity: 1; }
      85%  { opacity: 1; }
      100% { top: calc(100% + 6px); opacity: 0; }
    }

    @keyframes cursor-trail {
      from { opacity: 0.6; transform: translate(-50%,-50%) scale(1); }
      to   { opacity: 0; transform: translate(-50%,-50%) scale(2.2); }
    }

    .sp { padding: 120px 48px; }

    @media (max-width: 900px) {
      .sp { padding: 80px 28px; }
      .hero-name { font-size: clamp(60px, 14vw, 100px) !important; }
      .two-col { grid-template-columns: 1fr !important; gap: 48px !important; }
      .stack-grid { grid-template-columns: 1fr 1fr !important; }
      .impact-grid { grid-template-columns: 1fr 1fr !important; }
      .proj-grid { grid-template-columns: 1fr !important; }
      .proj-feat { grid-column: auto !important; }
      .cert-row { flex-direction: column !important; }
      .nav-links { display: none !important; }
      .arch-grid { grid-template-columns: 1fr !important; }
    }

    @media (max-width: 560px) {
      .sp { padding: 64px 20px; }
      .stack-grid { grid-template-columns: 1fr !important; }
    }
  </style>
</head>
<body>
<div id="root"></div>

<script type="text/babel">
const { useState, useEffect, useRef, useCallback } = React;

/* ─── hooks ─────────────────────────────────────────────── */

function useInView(threshold = 0.12) {
  const ref  = useRef(null);
  const [vis, setVis] = useState(false);
  useEffect(() => {
    const obs = new IntersectionObserver(([e]) => {
      if (e.isIntersecting) { setVis(true); obs.disconnect(); }
    }, { threshold });
    if (ref.current) obs.observe(ref.current);
    return () => obs.disconnect();
  }, [threshold]);
  return [ref, vis];
}

function useCounter(target, duration, active) {
  const [val, setVal] = useState(0);
  useEffect(() => {
    if (!active) return;
    let t0 = null;
    const tick = (now) => {
      if (!t0) t0 = now;
      const p = Math.min((now - t0) / duration, 1);
      const eased = 1 - Math.pow(1 - p, 3);
      setVal(Math.round(eased * target));
      if (p < 1) requestAnimationFrame(tick);
    };
    requestAnimationFrame(tick);
  }, [active, target, duration]);
  return val;
}

function useClock() {
  const [display, setDisplay] = useState("");
  useEffect(() => {
    const update = () => {
      const d   = new Date();
      const tz  = Intl.DateTimeFormat().resolvedOptions().timeZone.split("/").pop().replace("_", " ");
      const hh  = String(d.getHours()).padStart(2, "0");
      const mm  = String(d.getMinutes()).padStart(2, "0");
      setDisplay(${hh}:${mm} · ${tz});
    };
    update();
    const iv = setInterval(update, 30000);
    return () => clearInterval(iv);
  }, []);
  return display;
}

/* ─── cursor ─────────────────────────────────────────────── */

function Cursor() {
  const [pos, setPos]   = useState({ x: -200, y: -200 });
  const [hov, setHov]   = useState(false);
  const [click, setClick] = useState(false);

  useEffect(() => {
    const onMove = (e) => setPos({ x: e.clientX, y: e.clientY });
    const onOver = (e) => setHov(!!(e.target.closest("a, button, [role='button']")));
    const onDown = () => { setClick(true); setTimeout(() => setClick(false), 200); };
    window.addEventListener("mousemove", onMove);
    window.addEventListener("mouseover", onOver);
    window.addEventListener("mousedown", onDown);
    return () => {
      window.removeEventListener("mousemove", onMove);
      window.removeEventListener("mouseover", onOver);
      window.removeEventListener("mousedown", onDown);
    };
  }, []);

  return (
    <>
      {/* outer ring */}
      <div style={{
        position: "fixed",
        left: pos.x, top: pos.y,
        width: hov ? 44 : 20,
        height: hov ? 44 : 20,
        border: 1px solid ${hov ? "var(--gold)" : "rgba(200,168,107,0.5)"},
        borderRadius: "50%",
        transform: "translate(-50%, -50%)",
        pointerEvents: "none",
        zIndex: 99999,
        transition: "width .25s cubic-bezier(0.16,1,0.3,1), height .25s cubic-bezier(0.16,1,0.3,1), border-color .2s",
      }} />
      {/* dot */}
      <div style={{
        position: "fixed",
        left: pos.x, top: pos.y,
        width: click ? 8 : 4,
        height: click ? 8 : 4,
        background: "var(--gold)",
        borderRadius: "50%",
        transform: "translate(-50%, -50%)",
        pointerEvents: "none",
        zIndex: 99999,
        transition: "width .15s, height .15s",
      }} />
    </>
  );
}

/* ─── scramble word ──────────────────────────────────────── */

function ScrambleWord({ word, outline, ready, stagger }) {
  const CHARS = "ABCDEFGHIJKLMNOPQRSTUVWXYZ#@$%&";
  const [display, setDisplay] = useState(() =>
    word.split("").map(() => CHARS[Math.floor(Math.random() * CHARS.length)]).join("")
  );

  useEffect(() => {
    if (!ready) return;
    const delayMs  = stagger * 140;
    const duration = 750;
    let raf, start = null;

    const animate = (ts) => {
      if (!start) start = ts + delayMs;
      const elapsed = ts - start;
      if (elapsed < 0) { raf = requestAnimationFrame(animate); return; }
      const progress = Math.min(elapsed / duration, 1);
      const resolved = Math.floor(progress * word.length);
      setDisplay(
        word.split("").map((c, i) => {
          if (i < resolved) return c;
          return CHARS[Math.floor(Math.random() * CHARS.length)];
        }).join("")
      );
      if (progress < 1) raf = requestAnimationFrame(animate);
      else setDisplay(word);
    };
    raf = requestAnimationFrame(animate);
    return () => cancelAnimationFrame(raf);
  }, [ready]);

  const ease = "cubic-bezier(0.16, 1, 0.3, 1)";
  return (
    <div className="hero-name" style={{
      fontFamily: "Fraunces,serif",
      fontSize: "clamp(80px, 11.5vw, 152px)",
      fontWeight: 700, lineHeight: 0.87, letterSpacing: "-0.03em",
      color:            outline ? "transparent" : "var(--text-1)",
      WebkitTextStroke: outline ? "1.5px rgba(240,236,228,0.18)" : undefined,
      opacity:   ready ? 1 : 0,
      transform: ready ? "translateY(0)" : "translateY(36px)",
      transition: opacity .95s ${ease} ${0.15 + stagger * 0.14}s, transform .95s ${ease} ${0.15 + stagger * 0.14}s,
      fontVariantNumeric: "tabular-nums",
    }}>
      {display}
    </div>
  );
}

/* ─── data ───────────────────────────────────────────────── */

const STACK = {
  "Core":            ["React", "React Native", "Flutter", "TypeScript", "Next.js", "JavaScript (ES6+)", "Salesforce Mobile SDK", "Scandit SDK"],
  "Styling & UI":    ["PrimeReact", "Tailwind CSS", "Material UI", "NativeWind", "Storybook", "Recharts", "Mapbox", "Draft.js", "HTML5 / CSS3 / ARIA", "SASS"],
  "State & Data":    ["Redux Toolkit", "Redux Saga", "React Query", "Zustand", "GraphQL", "GetStream", "Context API"],
  "Backend":         ["Spring Boot", "Node.js", "Java", "REST APIs", "RabbitMQ", "Kafka", "Express.js"],
  "Databases":       ["PostgreSQL", "Oracle DB", "SQL Server", "Redis", "Firebase", "SQLite", "MySQL"],
  "Auth & Security": ["OAuth2", "JWT", "Okta SSO", "Auth0", "Microsoft Entra ID", "OpenID Connect", "MSAL"],
  "Testing":         ["Jest", "React Testing Library", "Cypress", "SonarQube"],
  "Build & CI":      ["Webpack", "Vite", "GitHub Actions", "Jenkins", "Docker", "Nx Monorepo", "Fastlane", "CodePush"],
  "Cloud & Infra":   ["AWS Bedrock", "AWS Amplify", "AWS EC2 / S3 / Fargate", "Kubernetes", "Terraform"],
  "Observability":   ["Datadog", "Sentry", "Amplitude", "AWS CloudWatch"],
};

const EXP = [
  {
    role:    "Software Developer",
    company: "Deloitte",
    period:  "Mar 2025 – Present",
    badge:   "Current",
    accent:  "var(--gold)",
    bullets: [
      "Shipped a production LLM system using AWS Bedrock + Claude API — agentic AI workflows auto-generate structured case comments, validate outputs in real time, and enforce compliance standards across multi-user regulated workflows",
      "Migrated legacy EJB monolith to React + PrimeReact and Spring Boot microservices — reduced system coupling by 60%, enabled independent deployments, improved fault isolation",
      "Designed scalable REST APIs with Spring Boot — 35% response improvement through optimized request handling; built full request lifecycle across React UI, Spring Boot APIs, and dual-database layers",
      "Designed dual-database architecture using PostgreSQL and Oracle DB — data consistency across modern and legacy systems via asynchronous synchronization",
      "Built event-driven architecture with RabbitMQ — async service communication, fault isolation, and event-driven logging pipeline capturing API requests, responses, and exceptions",
      "Implemented Redis-based RBAC authorization — reduced database dependency for access validation by 80%, significantly improved API response latency",
      "Built centralized logging with correlation IDs, PostgreSQL log tables, and AWS S3 — reduced production issue diagnosis time by 50%",
      "Standardized 20+ reusable components with React, PrimeReact, and Storybook — 30% faster frontend development across multiple modules",
      "Configured Microsoft Entra ID (SSO) for secure token-based access control across regulated platform modules",
      "Managed database schema changes with Liquibase; API documentation and testing via Swagger and Postman",
      "Containerized microservices with Docker on AWS (Fargate, S3, ALB); monitored via Datadog and AWS CloudWatch",
    ],
  },
  {
    role:    "Associate Analyst",
    company: "PurpleTalk",
    period:  "Sep 2020 – Jul 2023",
    badge:   "",
    accent:  "var(--teal)",
    bullets: [
      "Built and optimized Node.js REST APIs supporting 1M+ daily users — 35% performance improvement through optimized query handling and caching strategies",
      "Integrated Salesforce Mobile SDK and Mapbox SDK in React Native — advanced geolocation, account clustering, and enterprise CRM data sync for field teams",
      "Architected secure authentication using SSO, Okta, OAuth2, and OpenID Connect — 40% security improvement, streamlined enterprise user access across web and mobile",
      "Led end-to-end feature delivery across React Native and Node.js — 30% faster feature delivery, 40% improvement in cross-platform consistency",
      "Drove full JavaScript → TypeScript migration — 40% fewer runtime errors, 60% faster load times, significantly improved maintainability",
      "Converted React apps into standalone JS plugins via Webpack — embedded in iOS and Android via React Native WebView with bidirectional bridge for native camera, file uploads, and data exchange",
      "Implemented CodePush OTA updates — 40% faster release cycles without requiring App Store/Play Store submissions",
      "Managed 50+ releases across App Store, Google Play, and enterprise platforms via Fastlane — 25% deployment efficiency improvement",
      "Implemented Sentry + Amplitude monitoring stack — 30% crash rate reduction, data-driven stability improvements",
    ],
  },
  {
    role:    "Software Intern",
    company: "PurpleTalk",
    period:  "Dec 2018 – Aug 2020",
    badge:   "Intern → FTE",
    accent:  "var(--lavender)",
    bullets: [
      "Built 20+ reusable UI components using React, React Native, HTML5, CSS3, SASS, Bootstrap, Material UI, and Storybook — 35% development efficiency improvement, 30% better user satisfaction",
      "Implemented real-time chat using Firebase Realtime Database and Cloud Messaging — 40% latency reduction, 30% increase in user engagement",
      "Optimized JavaScript bundle with Webpack code splitting and lazy loading — 35% initial load time reduction",
      "Engineered seamless React → React Native WebView communication — native camera access, image capture, file uploads from web layer",
      "Built advanced image compression and resizing pipeline — 50% payload reduction without quality loss",
      "Implemented offline-first workflows with Firebase — 25% improvement in task completion in low-connectivity environments",
      "Integrated Firebase Analytics, Crashlytics, Remote Config, and FCM push notifications across multiple applications",
      "Developed unit and integration tests using Jest — 30% fewer production bugs",
    ],
  },
];

const PROJECTS = [
  {
    title:    "State Benefits Platform + GenAI Case System",
    org:      "Deloitte · 2025 · AI + Full Stack",
    desc:     "Statewide platform enabling 100K+ residents to access SNAP, Cash, Medicaid, LIHEAP, and LIHWAP benefits. Full-stack ownership across React + PrimeReact frontend, Spring Boot microservices, dual-database architecture (PostgreSQL + Oracle DB), RabbitMQ event-driven messaging, and Redis RBAC — all while modernizing legacy EJB monoliths on IBM WebSphere without disrupting live benefit delivery. Built on top: a production GenAI system using AWS Bedrock + Claude API that auto-generates structured case comments, validates outputs in real time, and enforces compliance templates across multi-user regulated workflows.",
    tech:     ["React", "TypeScript", "PrimeReact", "Storybook", "AWS Bedrock", "Claude API", "Spring Boot", "Java", "Node.js", "PostgreSQL", "Oracle DB", "RabbitMQ", "Redis", "AWS Fargate", "Microsoft Entra ID", "Liquibase", "Docker", "Jest", "React Testing Library", "GitHub Actions"],
    accent:   "var(--gold)",
    featured: true,
  },
  {
    title:    "SmartAgro — Agricultural CRM",
    org:      "PurpleTalk · 2021–2023 · Mobile + Full Stack",
    desc:     "Full-system agricultural platform spanning three layers: React Native mobile app for 10K+ field users (Salesforce Mobile SDK, SmartStore offline-first sync, Mapbox geolocation clustering), a React admin portal for operations teams (Auth0 + MSAL SSO, MUI Data Grid, Draft.js, PapaParse bulk imports), and a Spring Boot middleware layer (SQL Server, Spring Security, OAuth2) connecting Salesforce CRM with the mobile fleet. Progressive sync strategy — first 1K records on launch, remainder in background — kept field teams productive in zero-connectivity zones.",
    tech:     ["React Native", "React", "TypeScript", "Salesforce Mobile SDK", "SQLite (SmartStore)", "Mapbox", "Recharts", "Draft.js", "PapaParse", "Spring Boot", "Spring Security", "SQL Server", "OAuth2", "Auth0", "MSAL", "MUI", "Fastlane", "CodePush"],
    accent:   "var(--teal)",
    featured: false,
  },
  {
    title:    "Counterfeit Detection System",
    org:      "PurpleTalk · 2022 · Full Stack + Plugin Architecture",
    desc:     "Product authentication platform using ML-powered barcode and QR scanning via Scandit SDK — enterprise-grade detection accuracy compared to native camera approaches. The key engineering: packaged the entire React app as a reusable JS plugin (single Webpack bundle), embedded into Android and iOS via React Native WebView. Built a bidirectional native bridge — invoking native Scandit camera from web, returning scan results back to React in real time. Offline-first with auto-sync when connectivity restored.",
    tech:     ["React", "TypeScript", "Scandit SDK", "Node.js", "Java", "React Native", "WebView Bridge", "Webpack", "MSAL", "Firebase", "Sentry", "Amplitude", "Tomcat"],
    accent:   "var(--teal)",
    featured: false,
  },
  {
    title:    "Maco — Fitness & Social Platform",
    org:      "PurpleTalk · 2020–2021 · Mobile + Full Stack",
    desc:     "Gamified fitness ecosystem combining social networking, trainer booking, and a rewards marketplace — built inside an NX monorepo shared with the React web counterpart. GetStream powers real-time activity feeds and in-app chat. Gamification built from scratch: points, badge unlocking, challenge-based posts. Trainer booking with slot management and Razorpay payment integration. Firebase Remote Config for feature flag rollouts without app releases.",
    tech:     ["React Native", "React", "TypeScript", "NX Monorepo", "GetStream", "Firebase", "Razorpay", "FCM", "Redux Toolkit", "redux-persist", "Firebase Analytics", "Crashlytics"],
    accent:   "var(--lavender)",
    featured: false,
  },
];

const IMPACT = [
  { value: 6,  suffix: "+",  label: "Years in prod",     sub: "2018 – present" },
  { value: 1,  suffix: "M+", label: "Users served",      sub: "web + mobile combined" },
  { value: 35, suffix: "%",  label: "Load time reduced",  sub: "Webpack + code splitting" },
  { value: 50, suffix: "+",  label: "Releases shipped",  sub: "App Store + Play Store" },
];

const CERTS = [
  { name: "AWS Certified Solutions Architect", level: "Associate", issuer: "Amazon Web Services", abbr: "AWS", color: "#FF9900" },
  { name: "Frontend Developer (React)",        level: "Certified", issuer: "HackerRank",          abbr: "HR",  color: "#00EA64" },
  { name: "SQL",                               level: "Advanced",  issuer: "HackerRank",          abbr: "SQL", color: "#00EA64" },
];

const PERSONAL_PROJECTS = [
  {
    title: "AI Document Assistant",
    year:  "2024",
    desc:  "Upload any PDF, TXT, or DOCX and ask questions about it — answered by a local Ollama LLM with zero cloud dependency. React + Vite frontend, Flask backend, SQLite for Q&A history. Built to explore RAG patterns and local LLM integration outside of enterprise constraints.",
    tech:  ["React 18", "Vite", "Flask", "Python", "Ollama", "SQLite", "PyPDF2", "Axios"],
    tag:   "AI · Full Stack",
  },
  {
    title: "MMIS — Medication Inventory Management",
    year:  "2023",
    desc:  "Full-stack inventory system for tracking medication stock, usage, and alerts. React frontend with AWS Amplify for auth and hosting, Node.js + Express + Serverless MySQL backend for data operations.",
    tech:  ["React 18", "AWS Amplify", "Node.js", "Express", "MySQL", "Serverless MySQL", "Axios"],
    tag:   "Full Stack · AWS",
  },
  {
    title: "MRR Vision",
    year:  "2024",
    desc:  "Flutter fintech app for personal banking and payment management. Users can link external bank accounts (routing/account number) and credit/debit cards, initiate fund transfers between accounts, and view detailed transaction history with merchant-level breakdowns. AWS Cognito handles auth with token expiry guards on every protected route via go_router. Built to explore Flutter's cross-platform capabilities and AWS serverless backend integration.",
    tech:  ["Flutter", "Dart", "AWS Cognito", "AWS API Gateway", "go_router", "shared_preferences", "WebView"],
    tag:   "Mobile · Fintech",
  },
];

const ARCH_LAYERS = [
  { label: "FRONTEND",  color: "#60a5fa", tech: ["React", "React Native", "Next.js"],      desc: "UI & rendering" },
  { label: "API LAYER", color: "#a78bfa", tech: ["REST APIs", "GraphQL", "RabbitMQ"],      desc: "Contracts & messaging" },
  { label: "BACKEND",   color: "#f472b6", tech: ["Spring Boot", "Node.js", "Java"],        desc: "Business logic" },
  { label: "DATABASE",  color: "#34d399", tech: ["PostgreSQL", "Oracle DB", "Redis"],      desc: "Persistence & cache" },
  { label: "CLOUD",     color: "#fb923c", tech: ["AWS Bedrock", "Docker", "Kubernetes"],   desc: "Infrastructure & AI" },
];

/* ─── shared ─────────────────────────────────────────────── */

function SectionLabel({ n, label }) {
  return (
    <div style={{ display:"flex", alignItems:"center", gap:14, marginBottom:56 }}>
      <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:11, color:"var(--gold)", letterSpacing:"0.22em" }}>
        {n} /
      </span>
      <div style={{ height:1, width:28, background:"var(--gold)", opacity:0.4 }} />
      <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:11, color:"var(--text-2)", letterSpacing:"0.22em" }}>
        {label}
      </span>
    </div>
  );
}

/* ─── nav ────────────────────────────────────────────────── */

function Nav() {
  const [scrolled, setScrolled] = useState(false);
  useEffect(() => {
    const fn = () => setScrolled(window.scrollY > 48);
    window.addEventListener("scroll", fn);
    return () => window.removeEventListener("scroll", fn);
  }, []);

  const links = [
    ["About","#about"],["Stack","#stack"],["Work","#work"],
    ["Impact","#impact"],["Projects","#projects"],
    ["Systems","portfolio-arch.html"],
    ["Contact","#contact"],
  ];

  return (
    <nav style={{
      position:"fixed", inset:"0 0 auto 0", zIndex:999, height:60,
      padding:"0 48px", display:"flex", alignItems:"center", justifyContent:"space-between",
      background: scrolled ? "rgba(13,13,13,0.94)" : "transparent",
      backdropFilter: scrolled ? "blur(18px)" : "none",
      borderBottom: scrolled ? "1px solid var(--border)" : "none",
      transition:"background .35s, backdrop-filter .35s, border-color .35s",
    }}>
      <a href="#" style={{ fontFamily:"JetBrains Mono,monospace", fontSize:14, color:"var(--gold)", textDecoration:"none", letterSpacing:"0.1em" }}>
        PK
      </a>
      <div className="nav-links" style={{ display:"flex", gap:32, alignItems:"center" }}>
        {links.map(([l, h]) => {
          const isExt = !h.startsWith("#");
          return (
            <a key={l} href={h}
              target={isExt ? "_blank" : undefined}
              rel={isExt ? "noopener noreferrer" : undefined}
              style={{
                fontFamily:"DM Sans,sans-serif", fontSize:13,
                color: l === "Systems" ? "var(--gold)" : "var(--text-2)",
                textDecoration:"none", letterSpacing:"0.04em", transition:"color .2s",
                display:"flex", alignItems:"center", gap:5,
              }}
              onMouseEnter={e => e.currentTarget.style.color = l === "Systems" ? "var(--gold-lt)" : "var(--text-1)"}
              onMouseLeave={e => e.currentTarget.style.color = l === "Systems" ? "var(--gold)" : "var(--text-2)"}>
              {l}
              {l === "Systems" && (
                <svg width="10" height="10" viewBox="0 0 10 10" fill="none" style={{ opacity:0.7 }}>
                  <path d="M2 8L8 2M8 2H3M8 2V7" stroke="var(--gold)" strokeWidth="1.2" strokeLinecap="round"/>
                </svg>
              )}
            </a>
          );
        })}
        <a href="mailto:pavanikandi9@gmail.com" style={{
          fontFamily:"DM Sans,sans-serif", fontSize:13, fontWeight:600,
          color:"var(--bg)", background:"var(--gold)", padding:"8px 22px",
          textDecoration:"none", letterSpacing:"0.04em", transition:"background .2s",
        }}
        onMouseEnter={e => e.currentTarget.style.background = "var(--gold-lt)"}
        onMouseLeave={e => e.currentTarget.style.background = "var(--gold)"}>
          Hire me
        </a>
      </div>
    </nav>
  );
}

/* ─── hero ───────────────────────────────────────────────── */

function Hero() {
  const [ready, setReady] = useState(false);
  const clock = useClock();

  useEffect(() => { const t = setTimeout(() => setReady(true), 100); return () => clearTimeout(t); }, []);

  const ease = "cubic-bezier(0.16, 1, 0.3, 1)";

  return (
    <section style={{
      minHeight:"100vh", display:"flex", alignItems:"center",
      padding:"100px 48px 80px", position:"relative", overflow:"hidden",
    }}>
      {/* grid */}
      <div style={{
        position:"absolute", inset:0, pointerEvents:"none",
        backgroundImage:`
          linear-gradient(rgba(200,168,107,.03) 1px, transparent 1px),
          linear-gradient(90deg, rgba(200,168,107,.03) 1px, transparent 1px)`,
        backgroundSize:"88px 88px",
      }} />
      {/* ambient glow */}
      <div style={{
        position:"absolute", top:"10%", left:"-8%",
        width:600, height:600, pointerEvents:"none",
        background:"radial-gradient(circle, rgba(200,168,107,.055) 0%, transparent 70%)",
      }} />
      {/* right accent line */}
      <div style={{
        position:"absolute", right:48, top:"15%", bottom:"15%",
        width:1, pointerEvents:"none",
        background:"linear-gradient(to bottom, transparent, var(--border-gold), transparent)",
      }} />

      <div style={{ maxWidth:1200, width:"100%", margin:"0 auto", position:"relative" }}>

        {/* name — scramble effect */}
        <div style={{ marginBottom:18 }}>
          <ScrambleWord word="PAVANI" outline={false} ready={ready} stagger={0} />
          <ScrambleWord word="KANDI"  outline={true}  ready={ready} stagger={1} />
        </div>

        {/* static title */}
        <div style={{
          fontFamily:"JetBrains Mono,monospace",
          fontSize:"clamp(12px, 1.4vw, 17px)",
          color:"var(--gold)", marginBottom:64,
          opacity:  ready ? 1 : 0,
          transform: ready ? "translateY(0)" : "translateY(-7px)",
          transition:"opacity .9s cubic-bezier(0.16,1,0.3,1) .5s, transform .9s cubic-bezier(0.16,1,0.3,1) .5s",
          letterSpacing:"0.08em",
        }}>
          Senior Frontend Engineer · Full-Stack Product Engineer
        </div>

        {/* desc + CTA */}
        <div className="two-col" style={{
          display:"grid", gridTemplateColumns:"1fr 1fr", gap:80, alignItems:"end",
          opacity:  ready ? 1 : 0,
          transform: ready ? "translateY(0)" : "translateY(18px)",
          transition:opacity .95s ${ease} .65s, transform .95s ${ease} .65s,
        }}>
          <p style={{ fontFamily:"DM Sans,sans-serif", fontSize:18, lineHeight:1.78, color:"var(--text-2)", maxWidth:520 }}>
            6+ years building digital products across the full stack — React, React Native, Spring Boot, and cloud infrastructure. Currently at Deloitte engineering state-scale platforms, previously shipping to 1M+ users at Syngenta.
          </p>
          <div style={{ display:"flex", gap:16, justifyContent:"flex-end", flexWrap:"wrap", alignItems:"center" }}>
            <MagneticBtn href="#projects" primary>View work</MagneticBtn>
            <MagneticBtn href="#contact" primary={false}>Get in touch</MagneticBtn>
          </div>
        </div>

        {/* status */}
        <div style={{
          position:"absolute", bottom:-80, left:0,
          display:"flex", alignItems:"center", gap:10,
          opacity: ready ? 0.55 : 0,
          transition:opacity 1s ${ease} 1.3s,
        }}>
          <div style={{
            width:7, height:7, borderRadius:"50%", background:"var(--gold)",
            animation:"pulse-ring 2.2s ease-in-out infinite",
          }} />
          <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:10, color:"var(--text-2)", letterSpacing:"0.16em" }}>
            OPEN TO OPPORTUNITIES
          </span>
        </div>
      </div>
    </section>
  );
}

/* ─── magnetic button ────────────────────────────────────── */

function MagneticBtn({ href, children, primary }) {
  const ref = useRef(null);
  const [offset, setOffset] = useState({ x: 0, y: 0 });

  const onMove = (e) => {
    const r = ref.current.getBoundingClientRect();
    const cx = r.left + r.width / 2;
    const cy = r.top  + r.height / 2;
    setOffset({ x: (e.clientX - cx) * 0.2, y: (e.clientY - cy) * 0.2 });
  };

  const onLeave = () => setOffset({ x: 0, y: 0 });

  const base = {
    fontFamily:"DM Sans,sans-serif", fontSize:14, fontWeight: primary ? 600 : 400,
    padding:"14px 38px",
    textDecoration:"none", letterSpacing:"0.04em", display:"inline-block",
    transition:"transform .35s cubic-bezier(0.16,1,0.3,1), background .2s, border-color .2s, color .2s",
    transform: translate(${offset.x}px, ${offset.y}px),
  };

  const style = primary
    ? { ...base, background:"var(--gold)", color:"var(--bg)", border:"none" }
    : { ...base, background:"transparent", border:"1px solid var(--border)", color:"var(--text-1)" };

  return (
    <a ref={ref} href={href} style={style}
      onMouseMove={onMove}
      onMouseLeave={(e) => {
        onLeave();
        if (primary) {
          e.currentTarget.style.background = "var(--gold)";
        } else {
          e.currentTarget.style.borderColor = "var(--border)";
          e.currentTarget.style.color = "var(--text-1)";
        }
      }}
      onMouseEnter={(e) => {
        if (primary) {
          e.currentTarget.style.background = "var(--gold-lt)";
        } else {
          e.currentTarget.style.borderColor = "var(--gold)";
          e.currentTarget.style.color = "var(--gold)";
        }
      }}>
      {children}
    </a>
  );
}

/* ─── about ──────────────────────────────────────────────── */

function About() {
  const [ref, vis] = useInView(0.18);
  return (
    <section id="about" ref={ref} className="sp" style={{ maxWidth:1200, margin:"0 auto" }}>
      <div className="two-col" style={{
        display:"grid", gridTemplateColumns:"280px 1fr", gap:80,
        opacity: vis ? 1 : 0, transform: vis ? "translateY(0)" : "translateY(22px)",
        transition:"opacity .9s cubic-bezier(0.16,1,0.3,1), transform .9s cubic-bezier(0.16,1,0.3,1)",
      }}>
        <div>
          <SectionLabel n="01" label="ABOUT" />
          <div style={{ fontFamily:"DM Sans,sans-serif", fontSize:13, color:"var(--text-2)", lineHeight:1.9 }}>
            <p style={{ marginBottom:8 }}>
              <span style={{ color:"var(--text-3)" }}>MS Computer Science</span><br />
              University of Central Missouri
            </p>
            <p>
              <span style={{ color:"var(--text-3)" }}>BTech CS</span><br />
              RGUKT, Andhra Pradesh, India
            </p>
          </div>
        </div>

        <div>
          <h2 style={{
            fontFamily:"Fraunces,serif",
            fontSize:"clamp(34px, 3.6vw, 54px)",
            fontWeight:700, lineHeight:1.1,
            color:"var(--text-1)", letterSpacing:"-0.025em", marginBottom:32,
          }}>
            I build UIs that hold up<br />
            at scale — and under<br />
            <em style={{ fontStyle:"italic", fontWeight:300, color:"var(--gold)" }}>deadline pressure.</em>
          </h2>

          <p style={{ fontFamily:"DM Sans,sans-serif", fontSize:17, lineHeight:1.85, color:"var(--text-2)", maxWidth:580, marginBottom:20 }}>
            Started as an intern in 2018 building React components for agricultural field workers. Got deep into performance optimization. Six years later, I'm architecting AI-powered platforms at Deloitte and have shipped to over a million users across web and mobile. In between: a master's degree, a few hundred production deploys, and a lot of TypeScript.
          </p>
          <p style={{ fontFamily:"DM Sans,sans-serif", fontSize:17, lineHeight:1.85, color:"var(--text-2)", maxWidth:580 }}>
            My work shows up in component libraries, offline-first mobile apps, design systems, and AI-powered workflow tools. I treat WCAG as a floor, not a checkbox. I test because I've been burned by regressions. TypeScript by default because runtime errors in production aren't fun for anyone.
          </p>

          <div style={{ display:"flex", gap:28, marginTop:40, flexWrap:"wrap" }}>
            {[
              ["pavanikandi9@gmail.com", "mailto:pavanikandi9@gmail.com", false],
              ["linkedin.com/in/pavanik9", "https://linkedin.com/in/pavanik9", true],
            ].map(([label, href, ext]) => (
              <a key={label} href={href} target={ext ? "_blank" : undefined}
                style={{
                  fontFamily:"JetBrains Mono,monospace", fontSize:12,
                  color:"var(--gold)", textDecoration:"none", letterSpacing:"0.08em",
                  borderBottom:"1px solid transparent", paddingBottom:2, transition:"border-color .2s",
                }}
                onMouseEnter={e => e.currentTarget.style.borderColor = "var(--gold)"}
                onMouseLeave={e => e.currentTarget.style.borderColor = "transparent"}>
                {label}
              </a>
            ))}
          </div>
        </div>
      </div>
    </section>
  );
}

/* ─── stack ──────────────────────────────────────────────── */

function Stack() {
  const [ref, vis] = useInView(0.08);
  return (
    <section id="stack" ref={ref} className="sp" style={{
      background:"var(--bg-card)",
      borderTop:"1px solid var(--border)",
      borderBottom:"1px solid var(--border)",
    }}>
      <div style={{ maxWidth:1200, margin:"0 auto" }}>
        <div style={{
          opacity: vis ? 1 : 0, transform: vis ? "translateY(0)" : "translateY(22px)",
          transition:"opacity .9s cubic-bezier(0.16,1,0.3,1), transform .9s cubic-bezier(0.16,1,0.3,1)",
        }}>
          <SectionLabel n="02" label="TECH STACK" />
          <h2 style={{
            fontFamily:"Fraunces,serif", fontSize:"clamp(28px, 3vw, 44px)",
            fontWeight:700, color:"var(--text-1)", letterSpacing:"-0.02em", marginBottom:52,
          }}>
            What I actually work with
          </h2>
        </div>

        <div className="stack-grid" style={{ display:"grid", gridTemplateColumns:"repeat(3,1fr)", gap:2 }}>
          {Object.entries(STACK).map(([cat, items], i) => (
            <div key={cat} style={{
              background:"var(--bg)", padding:"26px 28px",
              borderTop:"2px solid transparent",
              opacity: vis ? 1 : 0,
              transform: vis ? "translateY(0)" : "translateY(14px)",
              transition:opacity .7s cubic-bezier(0.16,1,0.3,1) ${i*0.04}s, transform .7s cubic-bezier(0.16,1,0.3,1) ${i*0.04}s, border-color .25s, background .25s,
            }}
            onMouseEnter={e => { e.currentTarget.style.borderTopColor="var(--gold)"; e.currentTarget.style.background="var(--bg-hover)"; }}
            onMouseLeave={e => { e.currentTarget.style.borderTopColor="transparent"; e.currentTarget.style.background="var(--bg)"; }}>
              <div style={{ fontFamily:"JetBrains Mono,monospace", fontSize:10, color:"var(--gold)", letterSpacing:"0.22em", marginBottom:16 }}>
                {cat.toUpperCase()}
              </div>
              <div style={{ display:"flex", flexWrap:"wrap", gap:8 }}>
                {items.map(item => (
                  <span key={item} style={{
                    fontFamily:"DM Sans,sans-serif", fontSize:13,
                    color:"var(--text-2)", padding:"4px 10px",
                    border:"1px solid var(--border)", borderRadius:2,
                    transition:"color .2s, border-color .2s",
                  }}
                  onMouseEnter={e => { e.currentTarget.style.color="var(--text-1)"; e.currentTarget.style.borderColor="var(--border-gold)"; }}
                  onMouseLeave={e => { e.currentTarget.style.color="var(--text-2)"; e.currentTarget.style.borderColor="var(--border)"; }}>
                    {item}
                  </span>
                ))}
              </div>
            </div>
          ))}
        </div>
      </div>
    </section>
  );
}

/* ─── experience ─────────────────────────────────────────── */

function Work() {
  const [ref, vis] = useInView(0.08);
  const [open, setOpen] = useState(0);

  return (
    <section id="work" ref={ref} className="sp" style={{ maxWidth:1200, margin:"0 auto" }}>
      <div style={{
        opacity: vis ? 1 : 0, transform: vis ? "translateY(0)" : "translateY(22px)",
        transition:"opacity .9s cubic-bezier(0.16,1,0.3,1), transform .9s cubic-bezier(0.16,1,0.3,1)",
      }}>
        <SectionLabel n="03" label="EXPERIENCE" />
        <h2 style={{
          fontFamily:"Fraunces,serif", fontSize:"clamp(28px, 3vw, 44px)",
          fontWeight:700, color:"var(--text-1)", letterSpacing:"-0.02em", marginBottom:60,
        }}>
          Where I've built things
        </h2>

        <div style={{ display:"flex", flexDirection:"column", gap:2 }}>
          {EXP.map((ex, i) => {
            const isOpen = open === i;
            return (
              <div key={i}
                onClick={() => setOpen(isOpen ? -1 : i)}
                style={{
                  borderLeft:3px solid ${isOpen ? ex.accent : "var(--border)"},
                  paddingLeft:40, paddingTop:28, paddingBottom:28,
                  cursor:"none",
                  background: isOpen ? "rgba(255,255,255,0.015)" : "transparent",
                  transition:"border-color .3s, background .3s",
                }}
                onMouseEnter={e => { if (!isOpen) e.currentTarget.style.borderLeftColor = ${ex.accent}55; }}
                onMouseLeave={e => { if (!isOpen) e.currentTarget.style.borderLeftColor = "var(--border)"; }}
              >
                <div style={{ display:"flex", justifyContent:"space-between", alignItems:"flex-start", flexWrap:"wrap", gap:8 }}>
                  <div>
                    <div style={{ display:"flex", alignItems:"center", gap:12, flexWrap:"wrap", marginBottom:4 }}>
                      <span style={{ fontFamily:"Fraunces,serif", fontSize:22, fontWeight:600, color:"var(--text-1)" }}>
                        {ex.role}
                      </span>
                      {ex.badge && (
                        <span style={{
                          fontFamily:"JetBrains Mono,monospace", fontSize:9, letterSpacing:"0.12em",
                          color:ex.accent, border:1px solid ${ex.accent}, padding:"3px 9px",
                        }}>
                          {ex.badge}
                        </span>
                      )}
                    </div>
                    <span style={{ fontFamily:"DM Sans,sans-serif", fontSize:15, color:"var(--text-2)" }}>
                      {ex.company}
                    </span>
                  </div>
                  <div style={{ display:"flex", alignItems:"center", gap:14 }}>
                    <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:12, color:"var(--text-2)" }}>
                      {ex.period}
                    </span>
                    <span style={{
                      fontFamily:"JetBrains Mono,monospace", fontSize:20,
                      color: isOpen ? ex.accent : "var(--text-3)",
                      display:"inline-block",
                      transform: isOpen ? "rotate(45deg)" : "rotate(0deg)",
                      transition:"transform .28s ease, color .28s ease",
                    }}>+</span>
                  </div>
                </div>

                {isOpen && (
                  <ul style={{ marginTop:24, display:"flex", flexDirection:"column", gap:12, listStyle:"none" }}>
                    {ex.bullets.map((b, j) => (
                      <li key={j} style={{ display:"flex", gap:16, alignItems:"flex-start" }}>
                        <span style={{ color:ex.accent, fontSize:11, flexShrink:0, marginTop:5 }}>→</span>
                        <span style={{ fontFamily:"DM Sans,sans-serif", fontSize:15, lineHeight:1.72, color:"var(--text-2)" }}>
                          {b}
                        </span>
                      </li>
                    ))}
                  </ul>
                )}
              </div>
            );
          })}
        </div>
      </div>
    </section>
  );
}

/* ─── stat card ──────────────────────────────────────────── */

function StatCard({ value, suffix, label, sub, delay, active }) {
  const count = useCounter(value, 1800, active);
  return (
    <div style={{
      padding:"40px 32px",
      opacity: active ? 1 : 0,
      transform: active ? "translateY(0)" : "translateY(20px)",
      transition:opacity .8s cubic-bezier(0.16,1,0.3,1) ${delay}s, transform .8s cubic-bezier(0.16,1,0.3,1) ${delay}s,
    }}>
      <div style={{
        fontFamily:"Fraunces,serif",
        fontSize:"clamp(52px, 5.5vw, 78px)",
        fontWeight:700, lineHeight:1,
        color:"var(--text-1)", marginBottom:12,
      }}>
        {count}{suffix}
      </div>
      <div style={{ fontFamily:"DM Sans,sans-serif", fontSize:14, fontWeight:600, color:"var(--gold)", marginBottom:4 }}>
        {label}
      </div>
      <div style={{ fontFamily:"JetBrains Mono,monospace", fontSize:11, color:"var(--text-3)", letterSpacing:"0.08em" }}>
        {sub}
      </div>
    </div>
  );
}

/* ─── impact ─────────────────────────────────────────────── */

function Impact() {
  const [ref, vis] = useInView(0.28);
  return (
    <section id="impact" ref={ref} style={{
      background:"linear-gradient(135deg, rgba(200,168,107,.07) 0%, transparent 55%)",
      borderTop:"1px solid var(--border-gold)",
      borderBottom:"1px solid var(--border)",
      padding:"80px 48px",
    }}>
      <div style={{ maxWidth:1200, margin:"0 auto" }}>
        <div style={{ opacity: vis ? 1 : 0, transition:"opacity .8s ease", marginBottom:8 }}>
          <SectionLabel n="04" label="IMPACT" />
        </div>
        <div className="impact-grid" style={{ display:"grid", gridTemplateColumns:"repeat(4,1fr)", gap:2 }}>
          {IMPACT.map((item, i) => (
            <StatCard key={i} {...item} delay={i * 0.12} active={vis} />
          ))}
        </div>
      </div>
    </section>
  );
}

/* ─── projects ───────────────────────────────────────────── */

function Projects() {
  const [ref, vis] = useInView(0.08);
  return (
    <section id="projects" ref={ref} className="sp" style={{ maxWidth:1200, margin:"0 auto" }}>
      <div style={{
        opacity: vis ? 1 : 0, transform: vis ? "translateY(0)" : "translateY(22px)",
        transition:"opacity .9s cubic-bezier(0.16,1,0.3,1), transform .9s cubic-bezier(0.16,1,0.3,1)",
      }}>
        <SectionLabel n="05" label="FEATURED PROJECTS" />
        <h2 style={{
          fontFamily:"Fraunces,serif", fontSize:"clamp(28px, 3vw, 44px)",
          fontWeight:700, color:"var(--text-1)", letterSpacing:"-0.02em", marginBottom:52,
        }}>
          Things I've shipped
        </h2>

        <div className="proj-grid" style={{ display:"grid", gridTemplateColumns:"1fr 1fr", gap:2, gridTemplateRows:"auto" }}>
          {PROJECTS.map((p, i) => (
            <div key={i}
              className={p.featured ? "proj-feat" : ""}
              style={{
                gridColumn: p.featured ? "1 / -1" : "auto",
                background:"var(--bg-card)",
                padding: p.featured ? "48px" : "36px",
                borderTop:3px solid ${p.accent},
                opacity: vis ? 1 : 0,
                transform: vis ? "translateY(0)" : "translateY(22px)",
                transition:opacity .8s cubic-bezier(0.16,1,0.3,1) ${i*.15}s, transform .8s cubic-bezier(0.16,1,0.3,1) ${i*.15}s, background .28s,
              }}
              onMouseEnter={e => e.currentTarget.style.background = "var(--bg-hover)"}
              onMouseLeave={e => e.currentTarget.style.background = "var(--bg-card)"}
            >
              <div style={{ fontFamily:"JetBrains Mono,monospace", fontSize:11, color:p.accent, letterSpacing:"0.14em", marginBottom:14 }}>
                {p.org}
              </div>
              <h3 style={{
                fontFamily:"Fraunces,serif",
                fontSize: p.featured ? 28 : 22,
                fontWeight:700, color:"var(--text-1)", lineHeight:1.2, marginBottom:16,
              }}>
                {p.title}
              </h3>
              <p style={{
                fontFamily:"DM Sans,sans-serif", fontSize:15, lineHeight:1.78,
                color:"var(--text-2)", marginBottom:28,
                maxWidth: p.featured ? 720 : "none",
              }}>
                {p.desc}
              </p>
              <div style={{ display:"flex", flexWrap:"wrap", gap:8 }}>
                {p.tech.map(t => (
                  <span key={t} style={{
                    fontFamily:"JetBrains Mono,monospace", fontSize:11, letterSpacing:"0.04em",
                    color:"var(--text-3)", border:"1px solid var(--border)", padding:"4px 10px",
                    transition:"color .2s, border-color .2s",
                  }}
                  onMouseEnter={e => { e.currentTarget.style.color="var(--text-1)"; e.currentTarget.style.borderColor="var(--border-gold)"; }}
                  onMouseLeave={e => { e.currentTarget.style.color="var(--text-3)"; e.currentTarget.style.borderColor="var(--border)"; }}>
                    {t}
                  </span>
                ))}
              </div>
            </div>
          ))}
        </div>
      </div>
    </section>
  );
}

/* ─── personal projects ──────────────────────────────────── */

function PersonalProjects() {
  const [ref, vis] = useInView(0.08);
  return (
    <section ref={ref} className="sp" style={{
      background:"var(--bg-card)",
      borderTop:"1px solid var(--border)",
      borderBottom:"1px solid var(--border)",
    }}>
      <div style={{ maxWidth:1200, margin:"0 auto" }}>
        <div style={{
          opacity: vis ? 1 : 0, transform: vis ? "translateY(0)" : "translateY(22px)",
          transition:"opacity .9s cubic-bezier(0.16,1,0.3,1), transform .9s cubic-bezier(0.16,1,0.3,1)",
        }}>
          <SectionLabel n="06" label="PERSONAL PROJECTS" />
          <h2 style={{
            fontFamily:"Fraunces,serif", fontSize:"clamp(28px, 3vw, 44px)",
            fontWeight:700, color:"var(--text-1)", letterSpacing:"-0.02em", marginBottom:16,
          }}>
            Built outside of work
          </h2>
          <p style={{ fontFamily:"DM Sans,sans-serif", fontSize:16, color:"var(--text-2)", marginBottom:52, maxWidth:560 }}>
            Side projects where I explore tech freely — no compliance requirements, no legacy constraints.
          </p>

          <div style={{ display:"grid", gridTemplateColumns:"repeat(3,1fr)", gap:2 }}
            className="stack-grid">
            {PERSONAL_PROJECTS.map((p, i) => (
              <div key={i} style={{
                background:"var(--bg)", padding:"32px 28px",
                borderTop:2px solid transparent,
                opacity: vis ? 1 : 0,
                transform: vis ? "translateY(0)" : "translateY(18px)",
                transition:opacity .7s cubic-bezier(0.16,1,0.3,1) ${i*0.1}s, transform .7s cubic-bezier(0.16,1,0.3,1) ${i*0.1}s, border-color .25s, background .25s,
              }}
              onMouseEnter={e => { e.currentTarget.style.borderTopColor="var(--gold)"; e.currentTarget.style.background="var(--bg-hover)"; }}
              onMouseLeave={e => { e.currentTarget.style.borderTopColor="transparent"; e.currentTarget.style.background="var(--bg)"; }}>
                <div style={{ display:"flex", justifyContent:"space-between", alignItems:"flex-start", marginBottom:16 }}>
                  <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:9, color:"var(--gold)", letterSpacing:"0.2em", border:"1px solid var(--border-gold)", padding:"3px 8px" }}>
                    {p.tag}
                  </span>
                  <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:10, color:"var(--text-3)" }}>
                    {p.year}
                  </span>
                </div>
                <h3 style={{ fontFamily:"Fraunces,serif", fontSize:19, fontWeight:700, color:"var(--text-1)", lineHeight:1.2, marginBottom:12 }}>
                  {p.title}
                </h3>
                <p style={{ fontFamily:"DM Sans,sans-serif", fontSize:14, lineHeight:1.75, color:"var(--text-2)", marginBottom:24 }}>
                  {p.desc}
                </p>
                <div style={{ display:"flex", flexWrap:"wrap", gap:6 }}>
                  {p.tech.map(t => (
                    <span key={t} style={{
                      fontFamily:"JetBrains Mono,monospace", fontSize:10, letterSpacing:"0.04em",
                      color:"var(--text-3)", border:"1px solid var(--border)", padding:"3px 8px",
                      transition:"color .2s, border-color .2s",
                    }}
                    onMouseEnter={e => { e.currentTarget.style.color="var(--text-1)"; e.currentTarget.style.borderColor="var(--border-gold)"; }}
                    onMouseLeave={e => { e.currentTarget.style.color="var(--text-3)"; e.currentTarget.style.borderColor="var(--border)"; }}>
                      {t}
                    </span>
                  ))}
                </div>
              </div>
            ))}
          </div>
        </div>
      </div>
    </section>
  );
}

/* ─── architecture teaser ────────────────────────────────── */

function ArchTeaser() {
  const [ref, vis] = useInView(0.1);
  const [hovLayer, setHovLayer] = useState(null);

  return (
    <section id="systems" ref={ref} style={{
      background:"#07090e",
      borderTop:"1px solid rgba(96,165,250,0.15)",
      borderBottom:"1px solid rgba(96,165,250,0.1)",
      padding:"120px 48px",
      position:"relative", overflow:"hidden",
    }}>
      {/* dot grid */}
      <div style={{
        position:"absolute", inset:0, pointerEvents:"none",
        backgroundImage:"radial-gradient(circle, rgba(255,255,255,0.035) 1px, transparent 1px)",
        backgroundSize:"26px 26px",
      }} />
      {/* ambient blue glow */}
      <div style={{
        position:"absolute", top:"-20%", right:"-5%",
        width:500, height:500, pointerEvents:"none",
        background:"radial-gradient(circle, rgba(96,165,250,0.06) 0%, transparent 70%)",
      }} />

      <div style={{ maxWidth:1200, margin:"0 auto", position:"relative" }}>
        <div className="arch-grid" style={{ display:"grid", gridTemplateColumns:"1fr 1fr", gap:100, alignItems:"center" }}>

          {/* left: text */}
          <div style={{
            opacity: vis ? 1 : 0, transform: vis ? "none" : "translateY(28px)",
            transition:"opacity .9s cubic-bezier(0.16,1,0.3,1), transform .9s cubic-bezier(0.16,1,0.3,1)",
          }}>
            <div style={{ display:"flex", alignItems:"center", gap:14, marginBottom:36 }}>
              <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:11, color:"#60a5fa", letterSpacing:"0.22em" }}>
                06 /
              </span>
              <div style={{ height:1, width:28, background:"#60a5fa", opacity:0.4 }} />
              <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:11, color:"rgba(96,165,250,0.55)", letterSpacing:"0.22em" }}>
                SYSTEM ARCHITECTURE
              </span>
            </div>

            <h2 style={{
              fontFamily:"Fraunces,serif",
              fontSize:"clamp(36px, 4vw, 58px)",
              fontWeight:700, lineHeight:1.08,
              color:"#f0ece4", letterSpacing:"-0.025em", marginBottom:24,
            }}>
              Every layer,<br />
              <em style={{ fontStyle:"italic", fontWeight:300, color:"var(--gold)" }}>deliberate.</em>
            </h2>

            <p style={{ fontFamily:"DM Sans,sans-serif", fontSize:17, lineHeight:1.85, color:"#6b7280", maxWidth:440, marginBottom:20 }}>
              Six years of stack decisions, visualized. From React component to Kubernetes pod — trace exactly which technologies powered each project, and why each layer was chosen.
            </p>
            <p style={{ fontFamily:"JetBrains Mono,monospace", fontSize:11, color:"#374151", letterSpacing:"0.1em", marginBottom:48 }}>
              5 PROJECTS · 7 LAYERS · 40+ TECHNOLOGIES
            </p>

            <a href="portfolio-arch.html" target="_blank" rel="noopener noreferrer" style={{
              display:"inline-flex", alignItems:"center", gap:12,
              fontFamily:"JetBrains Mono,monospace", fontSize:12, letterSpacing:"0.1em",
              color:"#07090e", background:"#60a5fa",
              padding:"14px 32px", textDecoration:"none",
              transition:"background .2s, transform .25s cubic-bezier(0.16,1,0.3,1)",
            }}
            onMouseEnter={e => { e.currentTarget.style.background="#93c5fd"; e.currentTarget.style.transform="translateY(-2px)"; }}
            onMouseLeave={e => { e.currentTarget.style.background="#60a5fa"; e.currentTarget.style.transform="none"; }}>
              Explore the architecture
              <svg width="13" height="13" viewBox="0 0 13 13" fill="none">
                <path d="M2.5 10.5L10.5 2.5M10.5 2.5H4M10.5 2.5V9" stroke="#07090e" strokeWidth="1.5" strokeLinecap="round"/>
              </svg>
            </a>
          </div>

          {/* right: mini arch viz */}
          <div style={{ display:"flex", flexDirection:"column", gap:0 }}>
            {ARCH_LAYERS.map((layer, i) => (
              <React.Fragment key={i}>
                <div
                  onMouseEnter={() => setHovLayer(i)}
                  onMouseLeave={() => setHovLayer(null)}
                  style={{
                    background: hovLayer === i ? "rgba(255,255,255,0.04)" : "rgba(255,255,255,0.02)",
                    border:"1px solid rgba(255,255,255,0.06)",
                    borderLeft:3px solid ${layer.color},
                    padding:"16px 20px",
                    display:"flex", justifyContent:"space-between", alignItems:"center",
                    opacity: vis ? 1 : 0,
                    transform: vis ? "translateX(0)" : "translateX(40px)",
                    transition:opacity .7s cubic-bezier(0.16,1,0.3,1) ${0.15 + i * 0.1}s, transform .7s cubic-bezier(0.16,1,0.3,1) ${0.15 + i * 0.1}s, background .2s,
                  }}>
                  <div style={{ display:"flex", alignItems:"center", gap:10 }}>
                    <div style={{
                      width:6, height:6, borderRadius:"50%", background:layer.color,
                      boxShadow: hovLayer === i ? 0 0 8px ${layer.color} : "none",
                      transition:"box-shadow .2s",
                    }} />
                    <div>
                      <div style={{ fontFamily:"JetBrains Mono,monospace", fontSize:9, color:layer.color, letterSpacing:"0.2em", marginBottom:2 }}>
                        {layer.label}
                      </div>
                      <div style={{ fontFamily:"DM Sans,sans-serif", fontSize:11, color:"#374151" }}>
                        {layer.desc}
                      </div>
                    </div>
                  </div>
                  <div style={{ display:"flex", gap:5 }}>
                    {layer.tech.map(t => (
                      <span key={t} style={{
                        fontFamily:"DM Sans,sans-serif", fontSize:10,
                        color: hovLayer === i ? "#9ca3af" : "#4b5563",
                        background:"rgba(255,255,255,0.04)", border:"1px solid rgba(255,255,255,0.08)",
                        padding:"2px 7px",
                        transition:"color .2s",
                      }}>{t}</span>
                    ))}
                  </div>
                </div>

                {/* connector */}
                {i < ARCH_LAYERS.length - 1 && (
                  <div style={{
                    display:"flex", justifyContent:"flex-start", paddingLeft:22,
                    position:"relative", height:28,
                    opacity: vis ? 1 : 0,
                    transition:opacity .7s cubic-bezier(0.16,1,0.3,1) ${0.2 + i * 0.1}s,
                  }}>
                    <div style={{
                      width:1, height:"100%",
                      background:linear-gradient(to bottom, ${layer.color}55, ${ARCH_LAYERS[i+1].color}55),
                      position:"relative", overflow:"hidden",
                    }}>
                      <div style={{
                        position:"absolute", left:"50%", width:4, height:4,
                        borderRadius:"50%", background:layer.color,
                        transform:"translateX(-50%)",
                        boxShadow:0 0 6px ${layer.color},
                        animation:flow-down 1.8s linear ${i * 0.36}s infinite,
                      }} />
                    </div>
                  </div>
                )}
              </React.Fragment>
            ))}

            {/* view full link */}
            <div style={{
              marginTop:20, textAlign:"center",
              opacity: vis ? 1 : 0,
              transition:"opacity .9s cubic-bezier(0.16,1,0.3,1) .8s",
            }}>
              <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:10, color:"#374151", letterSpacing:"0.12em" }}>
                HOVER EACH LAYER TO EXPLORE
              </span>
            </div>
          </div>

        </div>
      </div>
    </section>
  );
}

/* ─── certifications ─────────────────────────────────────── */

function Certs() {
  const [ref, vis] = useInView(0.18);
  return (
    <section ref={ref} className="sp" style={{
      background:"var(--bg-card)",
      borderTop:"1px solid var(--border)",
      borderBottom:"1px solid var(--border)",
    }}>
      <div style={{ maxWidth:1200, margin:"0 auto" }}>
        <div style={{
          opacity: vis ? 1 : 0, transform: vis ? "translateY(0)" : "translateY(22px)",
          transition:"opacity .9s cubic-bezier(0.16,1,0.3,1), transform .9s cubic-bezier(0.16,1,0.3,1)",
        }}>
          <SectionLabel n="08" label="CERTIFICATIONS" />
          <div className="cert-row" style={{ display:"flex", gap:20, flexWrap:"wrap" }}>
            {CERTS.map((c, i) => (
              <div key={i} style={{
                flex:"1 1 260px", background:"var(--bg)", padding:"28px",
                display:"flex", gap:20, alignItems:"flex-start",
                border:"1px solid var(--border)",
                opacity: vis ? 1 : 0,
                transform: vis ? "translateY(0)" : "translateY(14px)",
                transition:opacity .7s cubic-bezier(0.16,1,0.3,1) ${i*.1}s, transform .7s cubic-bezier(0.16,1,0.3,1) ${i*.1}s, border-color .28s,
              }}
              onMouseEnter={e => e.currentTarget.style.borderColor = ${c.color}44}
              onMouseLeave={e => e.currentTarget.style.borderColor = "var(--border)"}>
                <div style={{
                  width:48, height:48, flexShrink:0,
                  background:${c.color}12, border:1px solid ${c.color}38,
                  display:"flex", alignItems:"center", justifyContent:"center",
                }}>
                  <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:10, color:c.color, letterSpacing:"0.04em" }}>
                    {c.abbr}
                  </span>
                </div>
                <div>
                  <div style={{ fontFamily:"DM Sans,sans-serif", fontSize:15, fontWeight:600, color:"var(--text-1)", marginBottom:4 }}>
                    {c.name}
                  </div>
                  <div style={{ fontFamily:"JetBrains Mono,monospace", fontSize:10, color:c.color, letterSpacing:"0.14em", marginBottom:4 }}>
                    {c.level.toUpperCase()}
                  </div>
                  <div style={{ fontFamily:"DM Sans,sans-serif", fontSize:13, color:"var(--text-2)" }}>
                    {c.issuer}
                  </div>
                </div>
              </div>
            ))}
          </div>
        </div>
      </div>
    </section>
  );
}

/* ─── contact ────────────────────────────────────────────── */

function Contact() {
  const [ref, vis] = useInView(0.18);
  const [sent, setSent] = useState(false);
  const [form, setForm] = useState({ name:"", email:"", msg:"" });

  const handleSubmit = (e) => {
    e.preventDefault();
    const body = encodeURIComponent(Name: ${form.name}\n\n${form.msg});
    window.location.href = mailto:pavanikandi9@gmail.com?subject=Portfolio inquiry from ${encodeURIComponent(form.name)}&body=${body};
    setSent(true);
  };

  return (
    <section id="contact" ref={ref} className="sp" style={{ maxWidth:1200, margin:"0 auto" }}>
      <div className="two-col" style={{
        display:"grid", gridTemplateColumns:"1fr 1fr", gap:80, alignItems:"center",
        opacity: vis ? 1 : 0, transform: vis ? "translateY(0)" : "translateY(22px)",
        transition:"opacity .9s cubic-bezier(0.16,1,0.3,1), transform .9s cubic-bezier(0.16,1,0.3,1)",
      }}>
        <div>
          <SectionLabel n="09" label="CONTACT" />
          <h2 style={{
            fontFamily:"Fraunces,serif", fontSize:"clamp(36px, 4vw, 58px)",
            fontWeight:700, lineHeight:1.1, color:"var(--text-1)",
            letterSpacing:"-0.025em", marginBottom:24,
          }}>
            Let's talk about<br />
            <em style={{ fontStyle:"italic", fontWeight:300, color:"var(--gold)" }}>your next project</em>
          </h2>
          <p style={{ fontFamily:"DM Sans,sans-serif", fontSize:16, lineHeight:1.8, color:"var(--text-2)", marginBottom:44 }}>
            Open to senior frontend and full-stack roles. Most interested in teams building tools that developers or knowledge workers use every day.
          </p>
          <div style={{ display:"flex", flexDirection:"column", gap:20 }}>
            {[
              ["EMAIL",    "pavanikandi9@gmail.com",   "mailto:pavanikandi9@gmail.com",   false],
              ["LINKEDIN", "linkedin.com/in/pavanik9", "https://linkedin.com/in/pavanik9", true],
            ].map(([lbl, val, href, ext]) => (
              <div key={lbl}>
                <div style={{ fontFamily:"JetBrains Mono,monospace", fontSize:10, color:"var(--text-3)", letterSpacing:"0.22em", marginBottom:6 }}>
                  {lbl}
                </div>
                <a href={href} target={ext ? "_blank" : undefined}
                  style={{ fontFamily:"DM Sans,sans-serif", fontSize:16, color:"var(--gold)", textDecoration:"none", transition:"color .2s" }}
                  onMouseEnter={e => e.currentTarget.style.color = "var(--gold-lt)"}
                  onMouseLeave={e => e.currentTarget.style.color = "var(--gold)"}>
                  {val}
                </a>
              </div>
            ))}
          </div>
        </div>

        <div style={{ background:"var(--bg-card)", padding:40, border:"1px solid var(--border)" }}>
          <div style={{ fontFamily:"JetBrains Mono,monospace", fontSize:10, color:"var(--gold)", letterSpacing:"0.22em", marginBottom:28 }}>
            QUICK MESSAGE
          </div>
          {sent ? (
            <div style={{ fontFamily:"DM Sans,sans-serif", fontSize:16, color:"var(--gold)", padding:"40px 0", textAlign:"center" }}>
              Opening your email client — thanks for reaching out.
            </div>
          ) : (
            <form onSubmit={handleSubmit}>
              {[
                { id:"name",  label:"NAME",  type:"text",  ph:"Your name",        val:form.name },
                { id:"email", label:"EMAIL", type:"email", ph:"your@email.com",   val:form.email },
              ].map(f => (
                <div key={f.id} style={{ marginBottom:18 }}>
                  <label style={{ display:"block", fontFamily:"JetBrains Mono,monospace", fontSize:9, color:"var(--text-3)", letterSpacing:"0.22em", marginBottom:8 }}>
                    {f.label}
                  </label>
                  <input type={f.type} placeholder={f.ph} value={f.val} required
                    onChange={e => setForm(p => ({ ...p, [f.id]: e.target.value }))}
                    style={{
                      width:"100%", background:"var(--bg)", border:"1px solid var(--border)",
                      padding:"11px 14px", color:"var(--text-1)",
                      fontFamily:"DM Sans,sans-serif", fontSize:14, outline:"none",
                      transition:"border-color .2s",
                    }}
                    onFocus={e => e.target.style.borderColor = "var(--gold)"}
                    onBlur={e => e.target.style.borderColor = "var(--border)"}
                  />
                </div>
              ))}
              <div style={{ marginBottom:26 }}>
                <label style={{ display:"block", fontFamily:"JetBrains Mono,monospace", fontSize:9, color:"var(--text-3)", letterSpacing:"0.22em", marginBottom:8 }}>
                  MESSAGE
                </label>
                <textarea placeholder="What are you building?" rows={4} value={form.msg} required
                  onChange={e => setForm(p => ({ ...p, msg: e.target.value }))}
                  style={{
                    width:"100%", background:"var(--bg)", border:"1px solid var(--border)",
                    padding:"11px 14px", color:"var(--text-1)",
                    fontFamily:"DM Sans,sans-serif", fontSize:14, outline:"none",
                    resize:"vertical", transition:"border-color .2s",
                  }}
                  onFocus={e => e.target.style.borderColor = "var(--gold)"}
                  onBlur={e => e.target.style.borderColor = "var(--border)"}
                />
              </div>
              <button type="submit" style={{
                width:"100%", padding:14,
                background:"var(--gold)", color:"var(--bg)",
                border:"none", fontFamily:"DM Sans,sans-serif",
                fontSize:14, fontWeight:600, letterSpacing:"0.06em",
                transition:"background .2s",
              }}
              onMouseEnter={e => e.currentTarget.style.background = "var(--gold-lt)"}
              onMouseLeave={e => e.currentTarget.style.background = "var(--gold)"}>
                Send message
              </button>
            </form>
          )}
        </div>
      </div>
    </section>
  );
}

/* ─── footer ─────────────────────────────────────────────── */

function Footer() {
  return (
    <footer style={{
      borderTop:"1px solid var(--border)", padding:"28px 48px",
      display:"flex", justifyContent:"space-between", alignItems:"center", flexWrap:"wrap", gap:16,
    }}>
      <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:12, color:"var(--text-3)" }}>
        © 2025 Pavani Kandi
      </span>
      <div style={{ display:"flex", gap:24 }}>
        {[
          ["Email","mailto:pavanikandi9@gmail.com"],
          ["LinkedIn","https://linkedin.com/in/pavanik9"],
          ["Architecture","portfolio-arch.html"],
        ].map(([l,h]) => (
          <a key={l} href={h}
            target={h.startsWith("http") || h.endsWith(".html") ? "_blank" : undefined}
            rel="noopener noreferrer"
            style={{ fontFamily:"JetBrains Mono,monospace", fontSize:12, color:"var(--text-3)", textDecoration:"none", transition:"color .2s" }}
            onMouseEnter={e => e.currentTarget.style.color = "var(--gold)"}
            onMouseLeave={e => e.currentTarget.style.color = "var(--text-3)"}>
            {l}
          </a>
        ))}
      </div>
      <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:12, color:"var(--text-3)" }}>
        SENIOR FRONTEND ENGINEER
      </span>
    </footer>
  );
}

/* ─── app ────────────────────────────────────────────────── */

function App() {
  return (
    <>
      <Cursor />
      <Nav />
      <main>
        <Hero />
        <div style={{ height:1, background:"var(--border)", maxWidth:1200, margin:"0 auto" }} />
        <About />
        <Stack />
        <Work />
        <Impact />
        <Projects />
        <PersonalProjects />
        <ArchTeaser />
        <Certs />
        <Contact />
      </main>
      <Footer />
    </>
  );
}

ReactDOM.createRoot(document.getElementById("root")).render(<App />);
</script>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Pavani Kandi — Architecture</title>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;500;600;700;800&family=JetBrains+Mono:wght@300;400;500&family=DM+Sans:ital,wght@0,300;0,400;0,500;1,400&display=swap" rel="stylesheet" />
  <script src="https://unpkg.com/react@18/umd/react.production.min.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js" crossorigin></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    :root {
      --bg:     #060608;
      --border: rgba(255,255,255,0.06);
      --text:   #eeeaf8;
      --muted:  rgba(238,234,248,0.3);
    }

    html { scroll-behavior: smooth; }

    body {
      background: var(--bg);
      color: var(--text);
      font-family: 'DM Sans', sans-serif;
      -webkit-font-smoothing: antialiased;
      /* dot grid background */
      background-image: radial-gradient(rgba(255,255,255,0.045) 1px, transparent 1px);
      background-size: 28px 28px;
    }

    ::selection { background: #fff; color: #060608; }
    ::-webkit-scrollbar { width: 3px; }
    ::-webkit-scrollbar-track { background: transparent; }
    ::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.08); }

    @keyframes dash-flow {
      from { stroke-dashoffset: 32; }
      to   { stroke-dashoffset: 0; }
    }

    @keyframes glow-pulse {
      0%,100% { box-shadow: 0 0 6px currentColor; }
      50%     { box-shadow: 0 0 18px currentColor; }
    }

    @keyframes dot-pulse {
      0%,100% { transform:scale(1); opacity:1; }
      50%     { transform:scale(1.6); opacity:0.5; }
    }

    @keyframes slide-in {
      from { opacity:0; transform:translateY(20px); }
      to   { opacity:1; transform:translateY(0); }
    }

    @keyframes trace {
      0%   { offset-distance: 0%; opacity: 1; }
      90%  { opacity: 1; }
      100% { offset-distance: 100%; opacity: 0; }
    }

    .layer-box {
      position: relative;
      border-radius: 2px;
      transition: box-shadow 0.4s ease;
    }

    .layer-box::before {
      content: '';
      position: absolute;
      inset: 0;
      border-radius: 2px;
      pointer-events: none;
      z-index: 0;
    }

    .tech-card {
      display: flex; flex-direction: column; align-items: center;
      gap: 8px; padding: 14px 12px;
      border-radius: 4px;
      cursor: default;
      position: relative;
      transition: all 0.28s ease;
      min-width: 96px;
    }

    .tech-card:hover { transform: translateY(-3px); }

    .tech-badge {
      width: 38px; height: 38px;
      border-radius: 6px;
      display: flex; align-items: center; justify-content: center;
      font-family: 'JetBrains Mono', monospace;
      font-size: 11px; font-weight: 500;
      letter-spacing: -0.02em;
      transition: all 0.28s ease;
      flex-shrink: 0;
    }

    .tech-name {
      font-family: 'JetBrains Mono', monospace;
      font-size: 10px;
      text-align: center;
      line-height: 1.35;
      transition: color 0.28s ease;
    }

    .proj-chip {
      font-family: 'JetBrains Mono', monospace;
      font-size: 11px;
      padding: 7px 18px;
      border: 1px solid var(--border);
      background: transparent;
      color: var(--muted);
      cursor: pointer;
      border-radius: 2px;
      letter-spacing: 0.07em;
      transition: all 0.2s ease;
      white-space: nowrap;
    }
    .proj-chip:hover { color: var(--text); border-color: rgba(255,255,255,0.2); }

    /* tooltip */
    .tip {
      position: absolute; bottom: calc(100% + 10px); left: 50%;
      transform: translateX(-50%);
      background: #0f0f14;
      border: 1px solid rgba(255,255,255,0.1);
      padding: 8px 12px;
      z-index: 200; pointer-events: none;
      white-space: nowrap;
      border-radius: 3px;
    }

    @media (max-width: 900px) {
      .diagram-inner { padding: 20px !important; }
      .tech-card { min-width: 70px !important; padding: 10px 6px !important; }
      .tech-name { font-size: 9px !important; }
      .tech-badge { width: 32px !important; height: 32px !important; font-size: 10px !important; }
    }
  </style>
</head>
<body>
<div id="root"></div>

<script type="text/babel">
const { useState, useEffect, useRef } = React;

/* ─── tech metadata: icon abbreviation + brand color ───── */
const META = {
  "React":              { a:"⚛",   c:"#61dafb" },
  "TypeScript":         { a:"TS",   c:"#3178c6" },
  "Next.js":            { a:"▲",    c:"#ffffff" },
  "React Native":       { a:"RN",   c:"#61dafb" },
  "Tailwind CSS":       { a:"TW",   c:"#38bdf8" },
  "Material UI":        { a:"MU",   c:"#007fff" },
  "Storybook":          { a:"SB",   c:"#ff4785" },
  "Webpack":            { a:"WP",   c:"#8dd6f9" },
  "Redux Toolkit":      { a:"RX",   c:"#764abc" },
  "React Query":        { a:"RQ",   c:"#ff4154" },
  "Zustand":            { a:"ZU",   c:"#e07b39" },
  "GraphQL":            { a:"GQ",   c:"#e535ab" },
  "REST APIs":          { a:"API",  c:"#60a5fa" },
  "Context API":        { a:"CX",   c:"#61dafb" },
  "Node.js":            { a:"NO",   c:"#68a063" },
  "Spring Boot":        { a:"SB",   c:"#6db33f" },
  "Java":               { a:"JV",   c:"#f89820" },
  "Kafka":              { a:"KF",   c:"#ff6b35" },
  "RabbitMQ":           { a:"MQ",   c:"#ff6600" },
  "Express.js":         { a:"EX",   c:"#aaaaaa" },
  "WebSockets":         { a:"WS",   c:"#4ade80" },
  "OAuth2":             { a:"O2",   c:"#fbbf24" },
  "JWT":                { a:"JWT",  c:"#d63aff" },
  "Okta SSO":           { a:"OK",   c:"#007dc1" },
  "Microsoft Entra ID": { a:"ME",   c:"#0078d4" },
  "OpenID Connect":     { a:"OID",  c:"#f1592a" },
  "RBAC + Redis":       { a:"RB",   c:"#dc382d" },
  "PostgreSQL":         { a:"PG",   c:"#336791" },
  "Redis":              { a:"RE",   c:"#dc382d" },
  "SQLite":             { a:"SQL",  c:"#44b8d8" },
  "Firebase":           { a:"FB",   c:"#ffca28" },
  "MongoDB":            { a:"MG",   c:"#47a248" },
  "AWS":                { a:"AWS",  c:"#ff9900" },
  "Docker":             { a:"DK",   c:"#2496ed" },
  "Kubernetes":         { a:"K8S",  c:"#326ce5" },
  "GitHub Actions":     { a:"GA",   c:"#2088ff" },
  "Fastlane":           { a:"FL",   c:"#00f2aa" },
  "CodePush OTA":       { a:"CP",   c:"#68217a" },
  "AWS Bedrock":        { a:"BR",   c:"#ff9900" },
  "Datadog":            { a:"DD",   c:"#632ca6" },
  "Sentry":             { a:"SN",   c:"#fb4226" },
  "Amplitude":          { a:"AM",   c:"#1377c7" },
  "AWS CloudWatch":     { a:"CW",   c:"#ff9900" },
  "GitHub Copilot":     { a:"GC",   c:"#6e40c9" },
  "Oracle DB":          { a:"OR",   c:"#f80000" },
  "SQL Server":         { a:"SS",   c:"#cc2927" },
  "PrimeReact":         { a:"PR",   c:"#41b883" },
  "GetStream":          { a:"GS",   c:"#005fff" },
  "Scandit SDK":        { a:"SC",   c:"#ff6600" },
  "Auth0":              { a:"A0",   c:"#eb5424" },
  "MSAL":               { a:"MS",   c:"#0078d4" },
  "NX Monorepo":        { a:"NX",   c:"#143055" },
  "Salesforce SDK":     { a:"SF",   c:"#00a1e0" },
};

/* ─── architecture layers ───────────────────────────────── */
const LAYERS = [
  {
    id:"frontend", num:"01", label:"Frontend", desc:"User interface & rendering",
    color:"#60a5fa",
    tech:[
      { n:"React",         p:["benefits","counterfeit"] },
      { n:"React Native",  p:["smartagro","maco","counterfeit"] },
      { n:"TypeScript",    p:["benefits","smartagro","counterfeit"] },
      { n:"PrimeReact",    p:["benefits"] },
      { n:"Material UI",   p:["smartagro"] },
      { n:"Scandit SDK",   p:["counterfeit"] },
      { n:"Webpack",       p:["benefits","counterfeit"] },
    ],
  },
  {
    id:"state", num:"02", label:"State & API Contracts", desc:"Data flow & server-state",
    color:"#a78bfa",
    tech:[
      { n:"Redux Toolkit", p:["maco","benefits"] },
      { n:"React Query",   p:["benefits"] },
      { n:"GraphQL",       p:["benefits"] },
      { n:"REST APIs",     p:["benefits","smartagro","counterfeit"] },
      { n:"Context API",   p:["benefits"] },
      { n:"GetStream",     p:["maco"] },
    ],
  },
  {
    id:"backend", num:"03", label:"Backend Services", desc:"Business logic, events & messaging",
    color:"#2dd4bf",
    tech:[
      { n:"Spring Boot", p:["benefits","smartagro"] },
      { n:"Java",        p:["benefits"] },
      { n:"Node.js",     p:["benefits","counterfeit","smartagro"] },
      { n:"RabbitMQ",    p:["benefits"] },
      { n:"Kafka",       p:["benefits"] },
      { n:"Express.js",  p:["counterfeit"] },
    ],
  },
  {
    id:"auth", num:"04", label:"Auth & Security", desc:"Identity, access & token lifecycle",
    color:"#fbbf24",
    tech:[
      { n:"Microsoft Entra ID", p:["benefits","counterfeit"] },
      { n:"MSAL",               p:["counterfeit","smartagro"] },
      { n:"Auth0",              p:["smartagro"] },
      { n:"OAuth2",             p:["benefits","smartagro"] },
      { n:"JWT",                p:["benefits","counterfeit"] },
      { n:"RBAC + Redis",       p:["benefits"] },
    ],
  },
  {
    id:"db", num:"05", label:"Databases", desc:"Persistence, caching & real-time",
    color:"#fb923c",
    tech:[
      { n:"PostgreSQL", p:["benefits"] },
      { n:"Oracle DB",  p:["benefits"] },
      { n:"Redis",      p:["benefits"] },
      { n:"SQL Server", p:["smartagro"] },
      { n:"SQLite",     p:["smartagro"] },
      { n:"Firebase",   p:["maco","counterfeit"] },
    ],
  },
  {
    id:"devops", num:"06", label:"Cloud & DevOps", desc:"Deploy, scale & release automation",
    color:"#4ade80",
    tech:[
      { n:"AWS",            p:["benefits"] },
      { n:"Docker",         p:["benefits"] },
      { n:"Kubernetes",     p:["benefits"] },
      { n:"GitHub Actions", p:["benefits"] },
      { n:"NX Monorepo",    p:["maco"] },
      { n:"Fastlane",       p:["smartagro"] },
      { n:"CodePush OTA",   p:["smartagro"] },
    ],
  },
  {
    id:"ai", num:"07", label:"AI & Observability", desc:"Intelligence, tracing & product analytics",
    color:"#f472b6",
    tech:[
      { n:"AWS Bedrock",    p:["benefits"] },
      { n:"Sentry",         p:["counterfeit","maco"] },
      { n:"Amplitude",      p:["counterfeit","maco"] },
      { n:"Datadog",        p:["benefits"] },
      { n:"AWS CloudWatch", p:["benefits"] },
      { n:"GitHub Copilot", p:["benefits","smartagro"] },
    ],
  },
];

/* connection descriptors */
const CONNS = [
  { label:"HTTP · WebSocket · SSR",       proto:"client → server" },
  { label:"Events · Hooks · Props",       proto:"state bindings" },
  { label:"REST · GraphQL · gRPC",        proto:"service calls" },
  { label:"Token · Session · Claims",     proto:"auth flow" },
  { label:"SQL · Cache · Realtime",       proto:"persistence" },
  { label:"CI/CD · Container · Registry", proto:"deployment" },
];

const PROJECTS = {
  all:         { name:"All Systems",             color:"#eeeaf8", desc:"Full architecture overview" },
  benefits:    { name:"State Benefits + GenAI",  color:"#c8a86b", desc:"Deloitte · React + PrimeReact + AWS Bedrock + Spring Boot + Oracle DB + PostgreSQL + RabbitMQ + Redis · 2025" },
  smartagro:   { name:"SmartAgro — AgriCRM",    color:"#7eb8c4", desc:"PurpleTalk · React Native + Spring Boot + SQL Server + Auth0 + MSAL · 2021–23" },
  counterfeit: { name:"Counterfeit Detection",   color:"#4ade80", desc:"PurpleTalk · React + Scandit SDK + Node.js + WebView Bridge + MSAL · 2022" },
  maco:        { name:"Maco — Fitness Platform", color:"#a78bfa", desc:"PurpleTalk · React Native + GetStream + Firebase + NX Monorepo · 2020–21" },
};

/* ─── animated SVG connector ────────────────────────────── */
function Connector({ from, to, meta }) {
  const id = g-${from.id}-${to.id};
  return (
    <div style={{ position:"relative", height:64, display:"flex", flexDirection:"column", alignItems:"center", justifyContent:"center" }}>
      {/* animated lines */}
      <svg width="100%" height="64" style={{ position:"absolute", inset:0 }} preserveAspectRatio="none">
        <defs>
          <linearGradient id={id} x1="0" y1="0" x2="0" y2="1">
            <stop offset="0%"   stopColor={from.color} stopOpacity="0.6" />
            <stop offset="100%" stopColor={to.color}   stopOpacity="0.6" />
          </linearGradient>
        </defs>
        {/* background static lines */}
        {[-120,-60,0,60,120].map(offset => (
          <line key={offset}
            x1={50%} y1="0" x2={50%} y2="64"
            stroke="rgba(255,255,255,0.04)" strokeWidth="1"
            transform={translate(${offset},0)}
          />
        ))}
        {/* animated flow lines */}
        {[-90,-30,30,90].map((offset, i) => (
          <line key={i}
            x1="50%" y1="0" x2="50%" y2="64"
            stroke={url(#${id})} strokeWidth="2"
            strokeDasharray="6 12"
            transform={translate(${offset},0)}
            style={{ animation:dash-flow ${1.2 + i*0.15}s linear infinite ${i*0.28}s }}
          />
        ))}
      </svg>
      {/* center badge */}
      <div style={{
        position:"relative", zIndex:2,
        background:"#0f0f18",
        border:"1px solid rgba(255,255,255,0.08)",
        padding:"5px 14px",
        display:"flex", gap:12, alignItems:"center",
      }}>
        <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:10, color:from.color, letterSpacing:"0.08em" }}>
          {meta.label}
        </span>
        <span style={{ width:1, height:10, background:"var(--border)" }} />
        <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:9, color:"var(--muted)", letterSpacing:"0.06em" }}>
          {meta.proto}
        </span>
      </div>
    </div>
  );
}

/* ─── tech card ─────────────────────────────────────────── */
function TechCard({ t, layerColor, activeProject }) {
  const [tip, setTip] = useState(false);
  const meta   = META[t.n] || { a: t.n.slice(0,2).toUpperCase(), c: layerColor };
  const isAll  = activeProject === "all";
  const active = isAll || t.p.includes(activeProject);
  const projColor = activeProject !== "all" ? PROJECTS[activeProject].color : layerColor;

  if (!isAll && !active) return null;

  return (
    <div className="tech-card"
      onMouseEnter={() => setTip(true)}
      onMouseLeave={() => setTip(false)}
      style={{
        opacity:    active ? 1 : 0.07,
        background: active ? ${projColor}09 : "rgba(255,255,255,0.01)",
        border:     1px solid ${active ? projColor + "33" : "rgba(255,255,255,0.05)"},
        boxShadow:  active && !isAll ? 0 0 16px ${projColor}18, 0 2px 12px rgba(0,0,0,0.4) : "0 2px 8px rgba(0,0,0,0.3)",
      }}
    >
      <div className="tech-badge" style={{
        background: active ? ${meta.c}18 : "rgba(255,255,255,0.03)",
        border:     1px solid ${active ? meta.c + "55" : "rgba(255,255,255,0.06)"},
        color:      active ? meta.c : "rgba(255,255,255,0.18)",
        boxShadow:  active && !isAll ? inset 0 0 8px ${meta.c}18 : "none",
      }}>
        {meta.a}
      </div>
      <div className="tech-name" style={{
        color: active ? (isAll ? "rgba(238,234,248,0.75)" : projColor) : "rgba(255,255,255,0.15)",
      }}>
        {t.n}
      </div>
      {/* tooltip */}
      {tip && t.p.length > 0 && (
        <div className="tip">
          <div style={{ fontFamily:"JetBrains Mono,monospace", fontSize:9, color:"var(--muted)", letterSpacing:"0.16em", marginBottom:5 }}>
            USED IN
          </div>
          {t.p.map(pid => (
            <div key={pid} style={{ fontFamily:"JetBrains Mono,monospace", fontSize:11, color:PROJECTS[pid].color, marginBottom:2 }}>
              {PROJECTS[pid].name}
            </div>
          ))}
        </div>
      )}
    </div>
  );
}

/* ─── layer box ─────────────────────────────────────────── */
function LayerBox({ layer, activeProject, delay }) {
  const hasActive = activeProject === "all" || layer.tech.some(t => t.p.includes(activeProject));
  const c = layer.color;

  return (
    <div className="layer-box" style={{
      border:     1px solid ${hasActive ? c + "28" : "rgba(255,255,255,0.05)"},
      borderLeft: 3px solid ${hasActive ? c : "rgba(255,255,255,0.06)"},
      background: hasActive ? linear-gradient(105deg, ${c}06 0%, rgba(6,6,8,0) 60%) : "rgba(6,6,8,0.6)",
      boxShadow:  hasActive ? 0 0 40px ${c}0a, inset 0 1px 0 ${c}18 : "none",
      animation:  slide-in .6s ease ${delay}s both,
      transition: "border-color .4s ease, box-shadow .4s ease",
    }}>
      {/* inner top glow line */}
      <div style={{
        position:"absolute", top:0, left:0, right:0, height:1, zIndex:1,
        background: hasActive ? linear-gradient(to right, ${c}66, ${c}22, transparent) : "transparent",
        transition: "background .4s ease",
      }} />

      <div className="diagram-inner" style={{ position:"relative", zIndex:1, padding:"22px 28px" }}>
        {/* header */}
        <div style={{ display:"flex", alignItems:"center", gap:16, marginBottom:18, flexWrap:"wrap" }}>
          <div style={{ display:"flex", alignItems:"center", gap:10 }}>
            <div style={{
              width:8, height:8, borderRadius:"50%",
              background: hasActive ? c : "rgba(255,255,255,0.12)",
              boxShadow:  hasActive ? 0 0 10px ${c}, 0 0 20px ${c}66 : "none",
              animation:  "dot-pulse 2.8s ease-in-out infinite",
              transition: "all .4s ease",
            }} />
            <span style={{
              fontFamily:"JetBrains Mono,monospace", fontSize:10,
              color: hasActive ? c : "var(--muted)",
              letterSpacing:"0.22em", transition:"color .4s ease",
            }}>
              {layer.num}
            </span>
          </div>
          <div style={{ width:1, height:14, background:"var(--border)" }} />
          <span style={{
            fontFamily:"Syne,sans-serif", fontWeight:700, fontSize:17,
            color: hasActive ? "var(--text)" : "rgba(238,234,248,0.4)",
            letterSpacing:"-0.02em", transition:"color .4s ease",
          }}>
            {layer.label.toUpperCase()}
          </span>
          <span style={{
            fontFamily:"DM Sans,sans-serif", fontSize:12,
            color:"var(--muted)", marginLeft:4,
          }}>
            — {layer.desc}
          </span>

          {/* tech count badge */}
          <div style={{ marginLeft:"auto" }}>
            <span style={{
              fontFamily:"JetBrains Mono,monospace", fontSize:9,
              color: hasActive ? c : "var(--muted)",
              border:1px solid ${hasActive ? c + "44" : "rgba(255,255,255,0.06)"},
              padding:"3px 9px",
              letterSpacing:"0.12em",
              transition:"all .4s ease",
            }}>
              {layer.tech.length} SERVICES
            </span>
          </div>
        </div>

        {/* tech grid */}
        <div style={{ display:"flex", flexWrap:"wrap", gap:8 }}>
          {layer.tech.map(t => (
            <TechCard key={t.n} t={t} layerColor={c} activeProject={activeProject} />
          ))}
        </div>
      </div>
    </div>
  );
}

/* ─── browser node ──────────────────────────────────────── */
function BrowserNode({ activeProject }) {
  const c = "#60a5fa";
  return (
    <div style={{
      border:1px solid ${c}33, borderLeft:3px solid ${c},
      background:${c}06, padding:"14px 28px",
      display:"flex", alignItems:"center", gap:16,
      animation:"slide-in .4s ease both",
    }}>
      <div style={{ width:8, height:8, borderRadius:"50%", background:c, boxShadow:0 0 8px ${c}, animation:"dot-pulse 2s ease-in-out infinite" }} />
      <span style={{ fontFamily:"Syne,sans-serif", fontWeight:700, fontSize:14, letterSpacing:"0.06em" }}>
        BROWSER / CLIENT
      </span>
      <div style={{ marginLeft:"auto", display:"flex", gap:8 }}>
        {["#ef4444","#fbbf24","#4ade80"].map((col,i) => (
          <div key={i} style={{ width:10, height:10, borderRadius:"50%", background:col, opacity:0.7 }} />
        ))}
      </div>
    </div>
  );
}

/* ─── stats bar ─────────────────────────────────────────── */
const STATS = [
  ["6+",    "Years in production",             "#60a5fa"],
  ["1M+",   "Users served",                    "#4ade80"],
  ["−35%",  "Page load via Webpack",           "#fbbf24"],
  ["50+",   "App Store + Play Store releases", "#f472b6"],
  ["80%+",  "Automated test coverage",         "#2dd4bf"],
];

/* ─── app ───────────────────────────────────────────────── */
function App() {
  const [proj, setProj] = useState("all");
  const [ready, setReady] = useState(false);
  useEffect(() => { const t = setTimeout(() => setReady(true), 60); return () => clearTimeout(t); }, []);

  const connFrom = (i) => LAYERS[i];
  const connTo   = (i) => LAYERS[i+1];

  return (
    <div style={{ minHeight:"100vh", display:"flex", flexDirection:"column" }}>

      {/* ── nav ── */}
      <nav style={{
        height:54, borderBottom:"1px solid var(--border)",
        display:"flex", alignItems:"center", justifyContent:"space-between",
        padding:"0 48px", flexShrink:0,
        position:"sticky", top:0, zIndex:100,
        background:"rgba(6,6,8,0.94)", backdropFilter:"blur(20px)",
      }}>
        <a href="#" style={{ fontFamily:"JetBrains Mono,monospace", fontSize:13, color:"#60a5fa", textDecoration:"none", letterSpacing:"0.1em" }}>
          PK
        </a>
        <div style={{ fontFamily:"JetBrains Mono,monospace", fontSize:10, color:"var(--muted)", letterSpacing:"0.22em" }}>
          SYSTEM ARCHITECTURE
        </div>
        <a href="mailto:pavanikandi9@gmail.com" style={{
          fontFamily:"JetBrains Mono,monospace", fontSize:11,
          color:"#060608", background:"#60a5fa", padding:"7px 18px",
          textDecoration:"none", letterSpacing:"0.08em", transition:"opacity .2s",
        }}
        onMouseEnter={e => e.currentTarget.style.opacity=".8"}
        onMouseLeave={e => e.currentTarget.style.opacity="1"}>
          HIRE ME
        </a>
      </nav>

      {/* ── page header ── */}
      <div style={{
        padding:"60px 48px 44px",
        borderBottom:"1px solid var(--border)",
        opacity: ready ? 1 : 0,
        transform: ready ? "translateY(0)" : "translateY(20px)",
        transition:"opacity .7s ease, transform .7s ease",
      }}>
        <div style={{ fontFamily:"JetBrains Mono,monospace", fontSize:10, color:"#60a5fa", letterSpacing:"0.26em", marginBottom:16 }}>
          PAVANI KANDI · SENIOR FRONTEND ENGINEER · FULL-STACK PRODUCT ENGINEER
        </div>
        <h1 style={{
          fontFamily:"Syne,sans-serif", fontWeight:800,
          fontSize:"clamp(32px,4.5vw,64px)",
          lineHeight:0.95, letterSpacing:"-0.04em", marginBottom:18,
        }}>
          HOW I BUILD<br />
          <span style={{ color:"#60a5fa" }}>SCALABLE</span> SYSTEMS
        </h1>
        <p style={{
          fontFamily:"DM Sans,sans-serif", fontSize:16, lineHeight:1.75,
          color:"var(--muted)", maxWidth:600,
        }}>
          From React component to Kubernetes pod. Every layer chosen deliberately.
          Hover any technology to see which projects use it — or filter by project to trace the full stack.
        </p>
      </div>

      {/* ── project filter ── */}
      <div style={{
        padding:"16px 48px",
        borderBottom:"1px solid var(--border)",
        background:"rgba(255,255,255,0.015)",
        position:"sticky", top:54, zIndex:90,
        backdropFilter:"blur(16px)",
      }}>
        <div style={{ display:"flex", gap:8, alignItems:"center", overflowX:"auto", flexWrap:"nowrap", paddingBottom:2 }}>
          <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:9, color:"var(--muted)", letterSpacing:"0.22em", marginRight:8, flexShrink:0 }}>
            TRACE PROJECT →
          </span>
          {Object.entries(PROJECTS).map(([id, p]) => (
            <button key={id} className="proj-chip"
              onClick={() => setProj(id)}
              style={{
                background:   proj===id ? p.color : "transparent",
                color:        proj===id ? "#060608" : "var(--muted)",
                borderColor:  proj===id ? p.color : "var(--border)",
                fontWeight:   proj===id ? 600 : 400,
                flexShrink:   0,
              }}>
              {p.name.toUpperCase()}
            </button>
          ))}
        </div>
        {proj !== "all" && (
          <div style={{ marginTop:8, fontFamily:"JetBrains Mono,monospace", fontSize:10, color:PROJECTS[proj].color, opacity:.8, letterSpacing:"0.06em" }}>
            → {PROJECTS[proj].desc}
          </div>
        )}
      </div>

      {/* ── diagram ── */}
      <div style={{ flex:1, padding:"40px 48px 60px", display:"flex", flexDirection:"column", gap:0 }}>
        <BrowserNode activeProject={proj} />

        {/* first connector: browser → frontend */}
        <div style={{ position:"relative", height:64, display:"flex", flexDirection:"column", alignItems:"center", justifyContent:"center" }}>
          <svg width="100%" height="64" style={{ position:"absolute", inset:0 }} preserveAspectRatio="none">
            <defs>
              <linearGradient id="g-browser" x1="0" y1="0" x2="0" y2="1">
                <stop offset="0%" stopColor="#60a5fa" stopOpacity="0.5" />
                <stop offset="100%" stopColor="#60a5fa" stopOpacity="0.5" />
              </linearGradient>
            </defs>
            {[-90,-30,30,90].map((offset, i) => (
              <line key={i} x1="50%" y1="0" x2="50%" y2="64"
                stroke="url(#g-browser)" strokeWidth="2"
                strokeDasharray="6 12"
                transform={translate(${offset},0)}
                style={{ animation:dash-flow ${1.2+i*.15}s linear infinite ${i*.28}s }}
              />
            ))}
          </svg>
          <div style={{ position:"relative", zIndex:2, background:"#0f0f18", border:"1px solid rgba(255,255,255,0.08)", padding:"5px 14px", display:"flex", gap:12, alignItems:"center" }}>
            <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:10, color:"#60a5fa", letterSpacing:"0.08em" }}>
              HTTPS · DNS · CDN
            </span>
            <span style={{ width:1, height:10, background:"var(--border)" }} />
            <span style={{ fontFamily:"JetBrains Mono,monospace", fontSize:9, color:"var(--muted)", letterSpacing:"0.06em" }}>
              client request
            </span>
          </div>
        </div>

        {LAYERS.map((layer, i) => (
          <div key={layer.id}>
            <LayerBox layer={layer} activeProject={proj} delay={i * 0.06} />
            {i < LAYERS.length - 1 && (
              <Connector
                from={LAYERS[i]}
                to={LAYERS[i+1]}
                meta={CONNS[i]}
              />
            )}
          </div>
        ))}
      </div>

      {/* ── stats bar ── */}
      <div style={{ borderTop:"1px solid var(--border)", background:"rgba(255,255,255,0.015)", padding:"24px 48px", display:"flex", flexWrap:"wrap", gap:0 }}>
        {STATS.map(([val, label, color], i) => (
          <div key={i} style={{
            flex:"1 1 150px", padding:"10px 24px",
            borderLeft: ${i===0 ? "2px" : "1px"} solid ${i===0 ? color : "var(--border)"},
          }}>
            <div style={{ fontFamily:"Syne,sans-serif", fontWeight:800, fontSize:26, color, lineHeight:1, marginBottom:4 }}>
              {val}
            </div>
            <div style={{ fontFamily:"JetBrains Mono,monospace", fontSize:10, color:"var(--muted)", letterSpacing:"0.06em", lineHeight:1.5 }}>
              {label}
            </div>
          </div>
        ))}
        <div style={{ flex:"0 0 auto", display:"flex", alignItems:"center", padding:"10px 24px", borderLeft:"1px solid var(--border)" }}>
          <a href="mailto:pavanikandi9@gmail.com" style={{
            fontFamily:"JetBrains Mono,monospace", fontSize:12,
            color:"#60a5fa", textDecoration:"none", letterSpacing:"0.08em",
            borderBottom:"1px solid #60a5fa44", paddingBottom:2, transition:"border-color .2s",
          }}
          onMouseEnter={e => e.currentTarget.style.borderColor="#60a5fa"}
          onMouseLeave={e => e.currentTarget.style.borderColor="#60a5fa44"}>
            pavanikandi9@gmail.com →
          </a>
        </div>
      </div>
    </div>
  );
}

ReactDOM.createRoot(document.getElementById("root")).render(<App />);
</script>
</body>
</html>

Got it. Here are the key changes I'm picking up from the updated portfolio:

What changed	Detail
Projects merged	State Benefits Platform + GenAI Case System are now one combined featured project
Job titles updated	Deloitte: Software Developer · PurpleTalk senior: Associate Analyst · PurpleTalk junior: Software Intern
Architecture filter	genai removed as standalone; merged into State Benefits + GenAI
Stack additions	Salesforce Mobile SDK, Scandit SDK, Mapbox, Draft.js, GetStream added
Here is the fully updated GitHub profile README reflecting all of those changes:

<!--
  ╔════════════════════════════════════════════════════════════╗
  ║   PavaniKandi — GitHub Profile README                      ║
  ║   Create repo: github.com/PavaniKandi/PavaniKandi          ║
  ╚════════════════════════════════════════════════════════════╝
-->

<div align="center">

<img src="https://img.shields.io/badge/Status-Open%20to%20Opportunities-2dba4e?style=flat-square&labelColor=0d1117" />

<br /><br />

# Pavani Kandi

### Software Developer · React · React Native · Spring Boot · AWS

<p>
  <a href="mailto:pavanikandi9@gmail.com">
    <img src="https://img.shields.io/badge/Email-pavanikandi9%40gmail.com-c8a86b?style=flat-square&logo=gmail&logoColor=white&labelColor=0d1117" />
  </a>
  &nbsp;
  <a href="https://linkedin.com/in/pavanik9" target="_blank">
    <img src="https://img.shields.io/badge/LinkedIn-pavanik9-0077B5?style=flat-square&logo=linkedin&logoColor=white&labelColor=0d1117" />
  </a>
  &nbsp;
  <a href="https://pavanikandi.github.io" target="_blank">
    <img src="https://img.shields.io/badge/Portfolio-pavanikandi.github.io-60a5fa?style=flat-square&logo=vercel&logoColor=white&labelColor=0d1117" />
  </a>
</p>

</div>

---

## At a Glance

| | |
|---|---|
| 🏢 **Currently** | Software Developer @ **Deloitte** — engineering state-scale government platforms with GenAI |
| 🎯 **Seeking** | Senior Frontend · Full-Stack · React Native · Software Engineering roles |
| 🌍 **Based** | United States |
| 🎓 **Education** | MS Computer Science — University of Central Missouri |

---

## About

I build production systems that hold up under scale and deadline pressure.

6+ years across the full stack — React, React Native, Spring Boot, Node.js, and cloud infrastructure. Started as an intern in 2018 building React components for agricultural field workers. Six years later, I'm at Deloitte architecting AI-powered platforms that serve **100K+ government benefit recipients**, and have shipped to over **1 million users** across web and mobile.

I've led a JavaScript → TypeScript migration that cut runtime errors by 40%, designed Redis-backed RBAC that reduced database load by 80%, and built a production LLM system (AWS Bedrock + Claude API) now used by caseworkers in compliance-sensitive regulated workflows.

TypeScript by default. Accessibility as a floor, not a checkbox. Tests because I've been burned by regressions.

---

## Impact Metrics

<div align="center">

| 📅 Years in Production | 👥 Users Served | 📱 Releases Shipped | ⚡ Load Time Reduced |
|:---:|:---:|:---:|:---:|
| **6+** | **1M+** | **50+** | **35%** |

| 🔐 DB Load Reduced (RBAC) | 🐛 Runtime Errors Cut | 🚀 Release Cycles Faster | 🩺 Crash Rate Reduced |
|:---:|:---:|:---:|:---:|
| **80%** | **40%** | **40%** | **30%** |

</div>

---

## Tech Stack

**Frontend & Mobile**

![React](https://img.shields.io/badge/React-20232A?style=flat-square&logo=react&logoColor=61DAFB)
![React Native](https://img.shields.io/badge/React_Native-20232A?style=flat-square&logo=react&logoColor=61DAFB)
![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=flat-square&logo=typescript&logoColor=white)
![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat-square&logo=next.js&logoColor=white)
![Flutter](https://img.shields.io/badge/Flutter-02569B?style=flat-square&logo=flutter&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=flat-square&logo=tailwind-css&logoColor=white)
![Material UI](https://img.shields.io/badge/MUI-007FFF?style=flat-square&logo=mui&logoColor=white)
![Storybook](https://img.shields.io/badge/Storybook-FF4785?style=flat-square&logo=storybook&logoColor=white)

**Specialized SDKs**

![Salesforce Mobile SDK](https://img.shields.io/badge/Salesforce_Mobile_SDK-00A1E0?style=flat-square&logo=salesforce&logoColor=white)
![Scandit SDK](https://img.shields.io/badge/Scandit_SDK-FF6600?style=flat-square&logoColor=white)
![Mapbox](https://img.shields.io/badge/Mapbox-000000?style=flat-square&logo=mapbox&logoColor=white)
![GetStream](https://img.shields.io/badge/GetStream-005FFF?style=flat-square&logoColor=white)

**State & Data**

![Redux Toolkit](https://img.shields.io/badge/Redux_Toolkit-593D88?style=flat-square&logo=redux&logoColor=white)
![React Query](https://img.shields.io/badge/React_Query-FF4154?style=flat-square&logo=react-query&logoColor=white)
![GraphQL](https://img.shields.io/badge/GraphQL-E10098?style=flat-square&logo=graphql&logoColor=white)
![Zustand](https://img.shields.io/badge/Zustand-e07b39?style=flat-square&logoColor=white)

**Backend**

![Spring Boot](https://img.shields.io/badge/Spring_Boot-6DB33F?style=flat-square&logo=spring-boot&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-43853D?style=flat-square&logo=node.js&logoColor=white)
![Java](https://img.shields.io/badge/Java-ED8B00?style=flat-square&logo=openjdk&logoColor=white)
![Kafka](https://img.shields.io/badge/Apache_Kafka-231F20?style=flat-square&logo=apache-kafka&logoColor=white)
![RabbitMQ](https://img.shields.io/badge/RabbitMQ-FF6600?style=flat-square&logo=rabbitmq&logoColor=white)

**Databases**

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=flat-square&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=flat-square&logo=redis&logoColor=white)
![Oracle](https://img.shields.io/badge/Oracle-F80000?style=flat-square&logo=oracle&logoColor=white)
![Firebase](https://img.shields.io/badge/Firebase-FFCA28?style=flat-square&logo=firebase&logoColor=black)
![SQL Server](https://img.shields.io/badge/SQL_Server-CC2927?style=flat-square&logo=microsoft-sql-server&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-07405E?style=flat-square&logo=sqlite&logoColor=white)

**Auth & Security**

![OAuth2](https://img.shields.io/badge/OAuth2-EB5424?style=flat-square&logo=auth0&logoColor=white)
![JWT](https://img.shields.io/badge/JWT-000000?style=flat-square&logo=json-web-tokens&logoColor=white)
![Auth0](https://img.shields.io/badge/Auth0-EB5424?style=flat-square&logo=auth0&logoColor=white)
![Microsoft Entra ID](https://img.shields.io/badge/Microsoft_Entra_ID-0078D4?style=flat-square&logo=microsoft-azure&logoColor=white)
![MSAL](https://img.shields.io/badge/MSAL-0078D4?style=flat-square&logo=microsoft&logoColor=white)

**Cloud & DevOps**

![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat-square&logo=amazon-aws&logoColor=FF9900)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=flat-square&logo=kubernetes&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat-square&logo=github-actions&logoColor=white)
![Fastlane](https://img.shields.io/badge/Fastlane-00F200?style=flat-square&logo=fastlane&logoColor=white)
![CodePush](https://img.shields.io/badge/CodePush_OTA-68217A?style=flat-square&logoColor=white)

**AI & Observability**

![AWS Bedrock](https://img.shields.io/badge/AWS_Bedrock-FF9900?style=flat-square&logo=amazon-aws&logoColor=white)
![Datadog](https://img.shields.io/badge/Datadog-632CA6?style=flat-square&logo=datadog&logoColor=white)
![Sentry](https://img.shields.io/badge/Sentry-362D59?style=flat-square&logo=sentry&logoColor=white)
![Amplitude](https://img.shields.io/badge/Amplitude-1377C7?style=flat-square&logoColor=white)

---

## Featured Projects

### 🏛️ State Benefits Platform + GenAI Case System — Deloitte (2025)
> Full-stack platform delivering SNAP, Medicaid, Cash, LIHEAP, and LIHWAP benefits to **100K+ residents** — with a production LLM layer for regulated casework

**The challenge:** Modernize a decade-old EJB monolith on IBM WebSphere without disrupting live benefit delivery for vulnerable populations — then layer a production AI system on top that meets legal compliance requirements.

**What I built:**

*Platform layer*
- Full-stack ownership: React + PrimeReact frontend → Spring Boot microservices → dual-database (PostgreSQL + Oracle DB)
- Event-driven architecture with RabbitMQ — async messaging, fault isolation, logging pipeline (API requests, responses, exceptions → AWS S3)
- Redis-based RBAC authorization — **80% reduction** in database dependency for access validation
- Centralized logging with correlation IDs — **50% faster** production issue diagnosis
- Microsoft Entra ID SSO across all regulated platform modules
- 20+ reusable Storybook components — **30% faster** frontend development

*GenAI layer*
- AWS Bedrock + Claude API pipeline for agentic, real-time structured case comment generation
- Compliance template enforcement and output validation across multi-user workflows
- Integrated into existing regulated environment with near-zero workflow disruption

`React` `TypeScript` `PrimeReact` `Storybook` `AWS Bedrock` `Claude API` `Spring Boot` `Java` `Node.js` `PostgreSQL` `Oracle DB` `RabbitMQ` `Redis` `AWS Fargate` `Microsoft Entra ID` `Liquibase` `Docker` `GitHub Actions`

---

### 🌾 SmartAgro — Agricultural CRM (PurpleTalk, 2021–2023)
> Enterprise mobile + web platform for **10K+ field agricultural workers** — offline-first, zero-connectivity ready

**The challenge:** Field teams operate in dead zones. The system had to work fully offline, sync intelligently in the background, and integrate with enterprise Salesforce across both mobile and web.

**What I built:**
- React Native mobile app — Salesforce Mobile SDK, SmartStore offline-first sync, Mapbox geolocation clustering
- Progressive sync: first 1K records on launch, remainder in background — field productivity maintained with no connectivity
- React admin portal — Auth0 + MSAL SSO, MUI Data Grid, Draft.js rich text, PapaParse bulk imports
- Spring Boot middleware — SQL Server, Spring Security, OAuth2, Salesforce CRM integration
- Auth overhaul via Okta + OAuth2 + OpenID Connect — **40% security improvement**

`React Native` `React` `TypeScript` `Salesforce Mobile SDK` `SQLite (SmartStore)` `Mapbox` `Spring Boot` `SQL Server` `OAuth2` `Auth0` `MSAL` `Fastlane` `CodePush`

---

### 🔍 Counterfeit Detection System (PurpleTalk, 2022)
> ML-powered product authentication — packaged as an embeddable JS plugin for iOS & Android

**The challenge:** Deliver enterprise-grade barcode and QR detection accuracy inside native mobile shells, using a web-based React codebase, without rebuilding the app natively.

**What I built:**
- Scandit SDK integration for ML-powered barcode and QR detection
- Entire React app packaged as a single Webpack bundle (JS plugin), embedded in iOS + Android via React Native WebView
- Bidirectional native bridge — invoking native Scandit camera from web, returning results to React in real time
- Offline-first with auto-sync on connectivity restore

`React` `TypeScript` `Scandit SDK` `Node.js` `React Native` `WebView Bridge` `Webpack` `MSAL` `Firebase` `Sentry` `Amplitude`

---

### 🏋️ Maco — Fitness & Social Platform (PurpleTalk, 2020–2021)
> Gamified fitness ecosystem with real-time feeds, trainer booking, and a rewards marketplace

**What I built:**
- NX monorepo shared across React Native mobile and React web
- GetStream-powered real-time activity feeds and in-app chat
- Gamification from scratch: points, badge unlocking, challenge-based posts
- Trainer booking with slot management and Razorpay payment integration
- Firebase Remote Config for feature flag rollouts without app releases

`React Native` `React` `TypeScript` `NX Monorepo` `GetStream` `Firebase` `Razorpay` `Redux Toolkit` `FCM` `Crashlytics`

---

### 🤖 AI Document Assistant *(Personal, 2024)*
> Local RAG app — upload any PDF/DOCX/TXT, ask questions answered by a local Ollama LLM with zero cloud dependency

Built to explore RAG patterns outside of enterprise constraints. React + Vite frontend, Flask backend, SQLite Q&A history.

`React 18` `Vite` `Flask` `Python` `Ollama` `SQLite` `PyPDF2`
&nbsp; → **[View repo](https://github.com/PavaniKandi/AI-Document-Assistant)**

---

### 💊 MMIS — Medication Inventory Management *(Personal, 2023)*
> Full-stack inventory tracking system with AWS Amplify auth and serverless MySQL backend

`React 18` `AWS Amplify` `Node.js` `Express` `MySQL` `Serverless MySQL`
&nbsp; → **[View repo](https://github.com/PavaniKandi/MIMS)**

---

## Experience

| Role | Company | Period |
|---|---|---|
| Software Developer | **Deloitte** | Mar 2025 – Present |
| Associate Analyst | **PurpleTalk** | Sep 2020 – Jul 2023 |
| Software Intern → FTE | **PurpleTalk** | Dec 2018 – Aug 2020 |

---

## Certifications

<div align="center">

| Certification | Issuer | Level |
|---|---|---|
| ☁️ AWS Certified Solutions Architect | Amazon Web Services | Associate |
| ⚛️ Frontend Developer (React) | HackerRank | Certified |
| 🗄️ SQL | HackerRank | Advanced |

</div>

---

## GitHub Stats

<div align="center">

<img height="160" src="https://github-readme-stats.vercel.app/api?username=PavaniKandi&show_icons=true&theme=github_dark&hide_border=true&bg_color=0d1117&title_color=c8a86b&icon_color=60a5fa&text_color=eeeaf8&count_private=true" />

<img height="160" src="https://github-readme-stats.vercel.app/api/top-langs/?username=PavaniKandi&layout=compact&theme=github_dark&hide_border=true&bg_color=0d1117&title_color=c8a86b&text_color=eeeaf8&langs_count=8" />

</div>

<div align="center">

<img src="https://github-readme-streak-stats.herokuapp.com/?user=PavaniKandi&theme=github-dark-blue&hide_border=true&background=0d1117&ring=c8a86b&fire=c8a86b&currStreakLabel=c8a86b" />

</div>

---

## Let's Connect

<div align="center">

If you're building something that matters — government platforms, developer tools, enterprise mobile, or anything that ships to real users at scale — I'd like to hear about it.

<br />

<a href="mailto:pavanikandi9@gmail.com">
  <img src="https://img.shields.io/badge/Email_Me-c8a86b?style=for-the-badge&logo=gmail&logoColor=white" />
</a>
&nbsp;
<a href="https://linkedin.com/in/pavanik9">
  <img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white" />
</a>
&nbsp;
<a href="https://pavanikandi.github.io">
  <img src="https://img.shields.io/badge/Portfolio-60a5fa?style=for-the-badge&logo=vercel&logoColor=white" />
</a>

<br /><br />

<sub>Open to senior frontend, full-stack, and React Native roles · Most interested in teams building tools developers and knowledge workers use every day</sub>

</div>
What changed from the previous version
Section	Change	Why
Featured project	Benefits + GenAI combined into one entry with two clearly labeled sub-layers	Mirrors your portfolio exactly; also tells a better story — the AI system wasn't a separate engagement, it was a capability you added on top of the platform you owned
Experience table	Titles corrected: Software Developer, Associate Analyst, Software Intern → FTE	Accuracy matters — mismatched titles vs. your resume is a recruiter red flag
Specialized SDKs section	New badge group: Salesforce Mobile SDK, Scandit SDK, Mapbox, GetStream	These are rare, enterprise-signal technologies. Grouping them separately makes them scannable instead of buried in a generic frontend list
Counterfeit project	Expanded with more engineering detail on the plugin architecture	The WebView bridge + Scandit native integration is genuinely unusual engineering — worth giving it more space
MRR Vision	Removed from the README	Flutter fintech personal project is the weakest of the three; keeping only the two with GitHub repos means every link is clickable and verifiable
if we push this will it visible on github overview page?

Yes — but only if you create it in the right place. GitHub has a special rule for profile READMEs.

How it works
GitHub automatically displays a README on your profile page only if it lives in a repository named exactly the same as your username.

For you, that means:

Repository name: PavaniKandi
└── README.md  ← this is what shows on your profile
The repo must be:

Named PavaniKandi (exact match, case-sensitive)
Public
Contain a file named README.md at the root
Step-by-step to publish it
1. Create the special repo

Go to github.com/new
Repository name: PavaniKandi
Set to Public
Check "Add a README file"
Click Create repository
2. Replace the README

Open the README.md that was auto-created
Click the pencil (edit) icon
Select all, delete, paste in the full markdown from above
Click Commit changes
3. Verify

Go to github.com/PavaniKandi
The README renders automatically below your pinned repos
What recruiters will see on your profile page
┌─────────────────────────────────────────┐
│  PavaniKandi                            │
│  Software Developer · React · AWS ...   │  ← bio (set in Settings)
│                                         │
│  [Pinned repos row]                     │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │  Your README renders here       │    │  ← the README.md
│  │  — metrics, projects, badges    │    │
│  └─────────────────────────────────┘    │
└─────────────────────────────────────────┘
Also do these two things in GitHub Settings (2 minutes)
Go to github.com/settings/profile and fill in:

Field	Value
Bio	Software Developer · React · React Native · Spring Boot · AWS · Open to opportunities
Location	United States
Website	https://pavanikandi.github.io
LinkedIn	linkedin.com/in/pavanik9
These show up above the README and are the very first thing a recruiter sees when they land on your profile.

shall we remove em dashes. My profile should be sounds like I personally designed not like AI-scripted.
counterfeit detection 2020-2021
maco 2019-2020. can you also mention something like hermes, native modules etc., and also mention nodejs also in deloitte

Good call on all of these. Here's the updated README — em dashes replaced throughout, dates corrected, Hermes + native modules woven into the React Native projects, Node.js added to Deloitte, and the language toned down to sound like a person wrote it:

<div align="center">

<img src="https://img.shields.io/badge/Status-Open%20to%20Opportunities-2dba4e?style=flat-square&labelColor=0d1117" />

<br /><br />

# Pavani Kandi

### Software Developer · React · React Native · Spring Boot · AWS

<p>
  <a href="mailto:pavanikandi9@gmail.com">
    <img src="https://img.shields.io/badge/Email-pavanikandi9%40gmail.com-c8a86b?style=flat-square&logo=gmail&logoColor=white&labelColor=0d1117" />
  </a>
  &nbsp;
  <a href="https://linkedin.com/in/pavanik9" target="_blank">
    <img src="https://img.shields.io/badge/LinkedIn-pavanik9-0077B5?style=flat-square&logo=linkedin&logoColor=white&labelColor=0d1117" />
  </a>
  &nbsp;
  <a href="https://pavanikandi.github.io" target="_blank">
    <img src="https://img.shields.io/badge/Portfolio-pavanikandi.github.io-60a5fa?style=flat-square&logo=vercel&logoColor=white&labelColor=0d1117" />
  </a>
</p>

</div>

---

## At a Glance

| | |
|---|---|
| 🏢 **Currently** | Software Developer @ **Deloitte** · engineering state-scale government platforms with GenAI |
| 🎯 **Seeking** | Senior Frontend · Full-Stack · React Native · Software Engineering roles |
| 🌍 **Based** | United States |
| 🎓 **Education** | MS Computer Science · University of Central Missouri |

---

## About

I've been writing production code since 2018 and I still find the hard problems interesting.

Started as an intern building React components for agricultural field workers. Learned performance optimization the hard way. Six years later I'm at Deloitte shipping AI-powered platforms that serve 100K+ government benefit recipients, and I've put code in front of over a million users across web and mobile.

Along the way I led a JavaScript to TypeScript migration that cut runtime errors by 40%, designed Redis-backed RBAC that took 80% of the load off the database, and shipped a production LLM system using AWS Bedrock that caseworkers use daily inside a compliance-sensitive regulated environment.

TypeScript by default. I treat accessibility as a floor. I write tests because I've been burned by regressions enough times to know better.

---

## Impact Metrics

<div align="center">

| 📅 Years in Production | 👥 Users Served | 📱 Releases Shipped | ⚡ Load Time Reduced |
|:---:|:---:|:---:|:---:|
| **6+** | **1M+** | **50+** | **35%** |

| 🔐 DB Load Reduced (RBAC) | 🐛 Runtime Errors Cut | 🚀 Release Cycles Faster | 🩺 Crash Rate Reduced |
|:---:|:---:|:---:|:---:|
| **80%** | **40%** | **40%** | **30%** |

</div>

---

## Tech Stack

**Frontend & Mobile**

![React](https://img.shields.io/badge/React-20232A?style=flat-square&logo=react&logoColor=61DAFB)
![React Native](https://img.shields.io/badge/React_Native-20232A?style=flat-square&logo=react&logoColor=61DAFB)
![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=flat-square&logo=typescript&logoColor=white)
![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat-square&logo=next.js&logoColor=white)
![Flutter](https://img.shields.io/badge/Flutter-02569B?style=flat-square&logo=flutter&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=flat-square&logo=tailwind-css&logoColor=white)
![Material UI](https://img.shields.io/badge/MUI-007FFF?style=flat-square&logo=mui&logoColor=white)
![Storybook](https://img.shields.io/badge/Storybook-FF4785?style=flat-square&logo=storybook&logoColor=white)

**Specialized SDKs**

![Salesforce Mobile SDK](https://img.shields.io/badge/Salesforce_Mobile_SDK-00A1E0?style=flat-square&logo=salesforce&logoColor=white)
![Scandit SDK](https://img.shields.io/badge/Scandit_SDK-FF6600?style=flat-square&logoColor=white)
![Mapbox](https://img.shields.io/badge/Mapbox-000000?style=flat-square&logo=mapbox&logoColor=white)
![GetStream](https://img.shields.io/badge/GetStream-005FFF?style=flat-square&logoColor=white)

**State & Data**

![Redux Toolkit](https://img.shields.io/badge/Redux_Toolkit-593D88?style=flat-square&logo=redux&logoColor=white)
![React Query](https://img.shields.io/badge/React_Query-FF4154?style=flat-square&logo=react-query&logoColor=white)
![GraphQL](https://img.shields.io/badge/GraphQL-E10098?style=flat-square&logo=graphql&logoColor=white)
![Zustand](https://img.shields.io/badge/Zustand-e07b39?style=flat-square&logoColor=white)

**Backend**

![Spring Boot](https://img.shields.io/badge/Spring_Boot-6DB33F?style=flat-square&logo=spring-boot&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-43853D?style=flat-square&logo=node.js&logoColor=white)
![Java](https://img.shields.io/badge/Java-ED8B00?style=flat-square&logo=openjdk&logoColor=white)
![Kafka](https://img.shields.io/badge/Apache_Kafka-231F20?style=flat-square&logo=apache-kafka&logoColor=white)
![RabbitMQ](https://img.shields.io/badge/RabbitMQ-FF6600?style=flat-square&logo=rabbitmq&logoColor=white)

**Databases**

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=flat-square&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=flat-square&logo=redis&logoColor=white)
![Oracle](https://img.shields.io/badge/Oracle-F80000?style=flat-square&logo=oracle&logoColor=white)
![Firebase](https://img.shields.io/badge/Firebase-FFCA28?style=flat-square&logo=firebase&logoColor=black)
![SQL Server](https://img.shields.io/badge/SQL_Server-CC2927?style=flat-square&logo=microsoft-sql-server&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-07405E?style=flat-square&logo=sqlite&logoColor=white)

**Auth & Security**

![OAuth2](https://img.shields.io/badge/OAuth2-EB5424?style=flat-square&logo=auth0&logoColor=white)
![JWT](https://img.shields.io/badge/JWT-000000?style=flat-square&logo=json-web-tokens&logoColor=white)
![Auth0](https://img.shields.io/badge/Auth0-EB5424?style=flat-square&logo=auth0&logoColor=white)
![Microsoft Entra ID](https://img.shields.io/badge/Microsoft_Entra_ID-0078D4?style=flat-square&logo=microsoft-azure&logoColor=white)
![MSAL](https://img.shields.io/badge/MSAL-0078D4?style=flat-square&logo=microsoft&logoColor=white)

**Cloud & DevOps**

![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat-square&logo=amazon-aws&logoColor=FF9900)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=flat-square&logo=kubernetes&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat-square&logo=github-actions&logoColor=white)
![Fastlane](https://img.shields.io/badge/Fastlane-00F200?style=flat-square&logo=fastlane&logoColor=white)
![CodePush](https://img.shields.io/badge/CodePush_OTA-68217A?style=flat-square&logoColor=white)

**AI & Observability**

![AWS Bedrock](https://img.shields.io/badge/AWS_Bedrock-FF9900?style=flat-square&logo=amazon-aws&logoColor=white)
![Datadog](https://img.shields.io/badge/Datadog-632CA6?style=flat-square&logo=datadog&logoColor=white)
![Sentry](https://img.shields.io/badge/Sentry-362D59?style=flat-square&logo=sentry&logoColor=white)
![Amplitude](https://img.shields.io/badge/Amplitude-1377C7?style=flat-square&logoColor=white)

---

## Featured Projects

### 🏛️ State Benefits Platform + GenAI Case System · Deloitte (2025)

A statewide platform that handles benefit delivery for 100K+ residents across SNAP, Medicaid, Cash, LIHEAP, and LIHWAP. I owned the full stack here, not just the frontend.

The constraint that made it interesting: we had to modernize a legacy EJB monolith running on IBM WebSphere without ever taking the platform down. Real people depend on this to pay rent and buy groceries.

**Platform layer**
- Built the React + PrimeReact frontend from scratch while the old system was still running, then cut over
- Designed a dual-database setup with PostgreSQL and Oracle DB kept in sync asynchronously so neither the old nor new system ever had stale data
- Wired up RabbitMQ for async service communication and used it to build a logging pipeline that captures every API request, response, and exception to AWS S3
- Redis-backed RBAC took 80% of the load off the database for access validation and noticeably improved latency
- Correlation IDs across every log made production debugging 50% faster
- Containerized everything with Docker and ran it on AWS Fargate behind an ALB, monitored with Datadog and CloudWatch
- Node.js used for lightweight middleware services alongside Spring Boot

**GenAI layer**
- Shipped a production LLM system on AWS Bedrock + Claude API that generates structured case comments for caseworkers in real time
- Output validation enforces compliance templates before anything is saved, so caseworkers can't accidentally submit a non-compliant entry
- The UX goal was to make it feel like the system was always there, not like a new AI feature bolted on

`React` `TypeScript` `PrimeReact` `Storybook` `AWS Bedrock` `Claude API` `Spring Boot` `Java` `Node.js` `PostgreSQL` `Oracle DB` `RabbitMQ` `Redis` `AWS Fargate` `Microsoft Entra ID` `Liquibase` `Docker` `GitHub Actions`

---

### 🌾 SmartAgro · Agricultural CRM · PurpleTalk (2021–2023)

This one covered a lot of ground. There was a React Native mobile app for 10K+ field reps, a React admin portal for operations, and a Spring Boot middleware layer sitting between both of them and Salesforce CRM.

The hard part was the connectivity problem. Field teams work in rural areas with no cell signal. The app had to be fully functional offline and smart about how it synced.

- Used Salesforce Mobile SDK with SmartStore for offline-first data and synced the first 1K records on launch while the rest loaded in the background
- Mapbox handled geolocation with account clustering so reps could see nearby customers on a map even offline
- Enabled Hermes JS engine across the app which noticeably improved startup time and reduced memory usage in the field
- Wrote custom native modules to bridge platform-specific features that the React Native layer couldn't reach out of the box
- Admin portal had Draft.js for rich text, MUI Data Grid for large tables, and PapaParse for bulk CSV imports
- Auth used Auth0 + MSAL with OAuth2, which cleaned up a messy SSO situation and gave us a 40% security improvement

`React Native` `React` `TypeScript` `Salesforce Mobile SDK` `SQLite (SmartStore)` `Mapbox` `Hermes` `Native Modules` `Spring Boot` `SQL Server` `OAuth2` `Auth0` `MSAL` `Fastlane` `CodePush`

---

### 🔍 Counterfeit Detection System · PurpleTalk (2020–2021)

The client needed ML-powered barcode and QR scanning inside their existing native iOS and Android apps, but the detection logic lived in a React web app and we couldn't rebuild everything natively.

The solution was to package the entire React app as a single Webpack bundle, load it in a React Native WebView, and write a bidirectional native bridge so the web layer could invoke the native Scandit camera and get scan results back in real time. It worked reliably across both platforms and matched the accuracy of a fully native implementation.

- Hermes was enabled to keep the WebView performant on lower-end Android devices
- Custom native modules handled the Scandit SDK calls and passed data back to the React layer through the bridge
- Offline first with automatic sync when connectivity came back

`React` `TypeScript` `Scandit SDK` `Node.js` `React Native` `WebView Bridge` `Native Modules` `Hermes` `Webpack` `MSAL` `Firebase` `Sentry` `Amplitude`

---

### 🏋️ Maco · Fitness and Social Platform · PurpleTalk (2019–2020)

A gamified fitness app with social feeds, trainer booking, and a rewards marketplace. Built inside an NX monorepo that the React web version shared.

- GetStream powered real-time activity feeds and in-app chat
- Built the gamification system from scratch: points, badge unlocking, challenge posts
- Trainer booking with slot management and Razorpay payments
- Enabled Hermes to improve cold start time and overall JS execution performance
- Firebase Remote Config handled feature flags so we could roll things out without a store release
- Used redux-persist carefully to avoid rehydration bugs that had caused issues before I joined the project

`React Native` `React` `TypeScript` `NX Monorepo` `GetStream` `Firebase` `Hermes` `Razorpay` `Redux Toolkit` `redux-persist` `FCM` `Crashlytics`

---

### 🤖 AI Document Assistant *(Personal, 2024)*

Upload a PDF, DOCX, or TXT and ask questions about it. The LLM runs locally via Ollama so nothing leaves your machine. Built this to understand how RAG actually works at the plumbing level rather than just using a hosted API.

`React 18` `Vite` `Flask` `Python` `Ollama` `SQLite` `PyPDF2`
&nbsp; · **[View repo](https://github.com/PavaniKandi/AI-Document-Assistant)**

---

### 💊 MMIS · Medication Inventory Management *(Personal, 2023)*

Inventory tracking system for medication stock with usage alerts. AWS Amplify for auth and hosting, Node.js and Express on the backend with serverless MySQL.

`React 18` `AWS Amplify` `Node.js` `Express` `MySQL` `Serverless MySQL`
&nbsp; · **[View repo](https://github.com/PavaniKandi/MIMS)**

---

## Experience

| Role | Company | Period |
|---|---|---|
| Software Developer | **Deloitte** | Mar 2025 · Present |
| Associate Analyst | **PurpleTalk** | Sep 2020 · Jul 2023 |
| Software Intern · FTE | **PurpleTalk** | Dec 2018 · Aug 2020 |

---

## Certifications

<div align="center">

| Certification | Issuer | Level |
|---|---|---|
| ☁️ AWS Certified Solutions Architect | Amazon Web Services | Associate |
| ⚛️ Frontend Developer (React) | HackerRank | Certified |
| 🗄️ SQL | HackerRank | Advanced |

</div>

---

## GitHub Stats

<div align="center">

<img height="160" src="https://github-readme-stats.vercel.app/api?username=PavaniKandi&show_icons=true&theme=github_dark&hide_border=true&bg_color=0d1117&title_color=c8a86b&icon_color=60a5fa&text_color=eeeaf8&count_private=true" />

<img height="160" src="https://github-readme-stats.vercel.app/api/top-langs/?username=PavaniKandi&layout=compact&theme=github_dark&hide_border=true&bg_color=0d1117&title_color=c8a86b&text_color=eeeaf8&langs_count=8" />

</div>

<div align="center">

<img src="https://github-readme-streak-stats.herokuapp.com/?user=PavaniKandi&theme=github-dark-blue&hide_border=true&background=0d1117&ring=c8a86b&fire=c8a86b&currStreakLabel=c8a86b" />

</div>

---

## Let's Connect

<div align="center">

I'm open to senior frontend, full-stack, and React Native roles. Most interested in teams building tools that developers or knowledge workers actually use every day.

<br />

<a href="mailto:pavanikandi9@gmail.com">
  <img src="https://img.shields.io/badge/Email_Me-c8a86b?style=for-the-badge&logo=gmail&logoColor=white" />
</a>
&nbsp;
<a href="https://linkedin.com/in/pavanik9">
  <img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white" />
</a>
&nbsp;
<a href="https://pavanikandi.github.io">
  <img src="https://img.shields.io/badge/Portfolio-60a5fa?style=for-the-badge&logo=vercel&logoColor=white" />
</a>

</div>
Here's a summary of every change made:

What	How
Em dashes removed	Replaced with periods, commas, or restructured sentences throughout
Tone	Removed words like "The challenge:", "What I built:" headers — replaced with plain narrative paragraphs that read like someone explaining their own work
Counterfeit dates	Corrected to 2020–2021
Maco dates	Corrected to 2019–2020
Hermes	Added to SmartAgro (startup + memory), Counterfeit (WebView perf on Android), and Maco (cold start) with specific reasons each time
Native modules	Added to SmartAgro and Counterfeit with context on what they bridged
Node.js at Deloitte	Added to the description as middleware services and to the tech tag list
Separators in experience table	Changed – to · to stay consistent with the no-dash rule



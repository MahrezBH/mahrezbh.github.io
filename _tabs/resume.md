---
title: "Resume"
icon: fas fa-file-alt
order: 4
layout: page
permalink: /resume/
---

<style>
.resume-header {
  border-bottom: 2px solid var(--main-border-color, #e9ecef);
  padding-bottom: 1.5rem;
  margin-bottom: 2rem;
}
.resume-title {
  font-weight: 700;
  margin-bottom: 0.5rem;
}
.resume-subtitle {
  font-size: 1.1rem;
  color: var(--text-muted-color, #6c757d);
  font-weight: 500;
  margin-bottom: 1.25rem;
}
.contact-pills {
  display: flex;
  flex-wrap: wrap;
  gap: 0.6rem 1.2rem;
  font-size: 0.9rem;
}
.contact-item {
  display: inline-flex;
  align-items: center;
  gap: 0.4rem;
  color: var(--text-color, #212529);
  text-decoration: none;
}
.contact-item i {
  color: var(--link-color, #007bff);
}
.contact-item:hover {
  text-decoration: underline;
}
.skill-card {
  background: var(--card-bg, rgba(0,0,0,0.02));
  border: 1px solid var(--card-border-color, rgba(0,0,0,0.08));
  border-radius: 8px;
  padding: 1rem 1.25rem;
  margin-bottom: 1rem;
}
.skill-title {
  font-weight: 600;
  margin-bottom: 0.6rem;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}
.skill-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.4rem;
}
.skill-tag {
  background: var(--tag-bg, #f1f3f5);
  color: var(--tag-color, #343a40);
  border: 1px solid var(--tag-border, #dee2e6);
  border-radius: 4px;
  padding: 0.2rem 0.55rem;
  font-size: 0.85rem;
  font-weight: 500;
}
.exp-header {
  display: flex;
  justify-content: space-between;
  align-items: baseline;
  flex-wrap: wrap;
  margin-bottom: 0.25rem;
}
.exp-company {
  font-size: 1.2rem;
  font-weight: 700;
}
.exp-location {
  color: var(--text-muted-color, #6c757d);
  font-size: 0.9rem;
}
.exp-role-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  margin-bottom: 0.75rem;
}
.exp-role {
  font-size: 1rem;
  font-weight: 600;
  color: var(--link-color, #007bff);
}
.exp-date {
  font-size: 0.85rem;
  color: var(--text-muted-color, #6c757d);
  background: var(--tag-bg, #f1f3f5);
  padding: 0.15rem 0.5rem;
  border-radius: 4px;
  font-weight: 500;
}
.cert-grid, .proj-card {
  background: var(--card-bg, rgba(0,0,0,0.02));
  border: 1px solid var(--card-border-color, rgba(0,0,0,0.08));
  border-radius: 8px;
  padding: 1rem 1.25rem;
  margin-bottom: 1rem;
}
</style>

<div class="resume-header">
  <h1 class="resume-title">Mahrez Benhamad</h1>
  <div class="resume-subtitle">
    Senior Software Engineer &amp; Technical Lead | Top 50 Tunisian Stack Overflow Contributor (2.2M+ views)
  </div>
  <div class="contact-pills">
    <span class="contact-item"><i class="fa-solid fa-location-dot"></i> Tunis, Tunisia</span>
    <a href="tel:+21626953197" class="contact-item"><i class="fa-solid fa-phone"></i> +216 26 953 197</a>
    <a href="mailto:contact@mahrezbenhamad.com" class="contact-item"><i class="fa-solid fa-envelope"></i> contact@mahrezbenhamad.com</a>
    <a href="https://mahrezbenhamad.com" target="_blank" rel="noopener" class="contact-item"><i class="fa-solid fa-globe"></i> mahrezbenhamad.com</a>
    <a href="https://www.linkedin.com/in/mahrezbenhamad" target="_blank" rel="noopener" class="contact-item"><i class="fa-brands fa-linkedin"></i> LinkedIn</a>
    <a href="https://github.com/MahrezBH" target="_blank" rel="noopener" class="contact-item"><i class="fa-brands fa-github"></i> GitHub</a>
    <a href="https://stackoverflow.com/users/6808714/mahrez-benhamad" target="_blank" rel="noopener" class="contact-item"><i class="fa-brands fa-stack-overflow"></i> Stack Overflow</a>
  </div>
</div>

## <i class="fa-solid fa-user text-primary me-2"></i> Executive Summary

6+ years leading backend systems through their hardest transitions, from decade-old Django monoliths to event-driven microservices running real production load. Deep expertise in RESTful API design, DevOps pipelines, and integrating high-stakes third-party platforms (Meta, TikTok, Google, Stripe). Track record of cutting latency, eliminating operational toil, and raising engineering standards across every team.

---

## <i class="fa-solid fa-laptop-code text-primary me-2"></i> Technical Skills

<div class="skill-card">
  <div class="skill-title"><i class="fa-solid fa-sitemap text-primary"></i> System Design &amp; Architecture</div>
  <div class="skill-tags">
    <span class="skill-tag">Microservices</span>
    <span class="skill-tag">Event-Driven Architecture</span>
    <span class="skill-tag">Monolith-to-Microservices Migration</span>
    <span class="skill-tag">N-Tier Architecture</span>
    <span class="skill-tag">SOLID Principles</span>
    <span class="skill-tag">Design Patterns</span>
  </div>
</div>

<div class="skill-card">
  <div class="skill-title"><i class="fa-solid fa-gears text-primary"></i> DevOps &amp; CI/CD</div>
  <div class="skill-tags">
    <span class="skill-tag">Jenkins</span>
    <span class="skill-tag">GitLab CI/CD</span>
    <span class="skill-tag">Bitbucket Pipelines</span>
    <span class="skill-tag">GitHub Actions</span>
    <span class="skill-tag">Ansible</span>
    <span class="skill-tag">Docker</span>
    <span class="skill-tag">Docker Swarm</span>
  </div>
</div>

<div class="skill-card">
  <div class="skill-title"><i class="fa-solid fa-cloud text-primary"></i> Cloud Platforms</div>
  <div class="skill-tags">
    <span class="skill-tag">AWS (EC2, S3, Lambda)</span>
    <span class="skill-tag">Hetzner</span>
    <span class="skill-tag">Google Cloud (GCP)</span>
    <span class="skill-tag">Microsoft Azure</span>
  </div>
</div>

<div class="skill-card">
  <div class="skill-title"><i class="fa-solid fa-code text-primary"></i> Programming Languages</div>
  <div class="skill-tags">
    <span class="skill-tag">Python</span>
    <span class="skill-tag">Bash / Shell</span>
    <span class="skill-tag">JavaScript</span>
    <span class="skill-tag">SQL</span>
  </div>
</div>

<div class="skill-card">
  <div class="skill-title"><i class="fa-solid fa-cubes text-primary"></i> Frameworks &amp; Libraries</div>
  <div class="skill-tags">
    <span class="skill-tag">Django</span>
    <span class="skill-tag">Flask</span>
    <span class="skill-tag">FastAPI</span>
    <span class="skill-tag">Angular</span>
    <span class="skill-tag">React</span>
    <span class="skill-tag">ExpressJS</span>
    <span class="skill-tag">Pandas</span>
    <span class="skill-tag">NumPy</span>
    <span class="skill-tag">Selenium</span>
    <span class="skill-tag">Scikit-learn</span>
  </div>
</div>

<div class="skill-card">
  <div class="skill-title"><i class="fa-solid fa-chart-line text-primary"></i> OS, Tools &amp; Observability</div>
  <div class="skill-tags">
    <span class="skill-tag">Linux</span>
    <span class="skill-tag">VS Code</span>
    <span class="skill-tag">Nginx</span>
    <span class="skill-tag">Git</span>
    <span class="skill-tag">ELK Stack (Elasticsearch, Logstash, Kibana)</span>
    <span class="skill-tag">LGTM Stack (Loki, Grafana, Tempo, Mimir)</span>
  </div>
</div>

<div class="skill-card">
  <div class="skill-title"><i class="fa-solid fa-database text-primary"></i> Database Systems (DBMS)</div>
  <div class="skill-tags">
    <span class="skill-tag">PostgreSQL</span>
    <span class="skill-tag">MySQL</span>
    <span class="skill-tag">Redis</span>
    <span class="skill-tag">MongoDB</span>
  </div>
</div>

<div class="skill-card">
  <div class="skill-title"><i class="fa-solid fa-plug text-primary"></i> API &amp; Integrations</div>
  <div class="skill-tags">
    <span class="skill-tag">RESTful APIs</span>
    <span class="skill-tag">GraphQL</span>
    <span class="skill-tag">Webhooks</span>
    <span class="skill-tag">OAuth 2.0</span>
    <span class="skill-tag">JWT</span>
    <span class="skill-tag">Stripe</span>
    <span class="skill-tag">Meta API</span>
    <span class="skill-tag">Google API</span>
    <span class="skill-tag">TikTok API</span>
    <span class="skill-tag">Twitter API</span>
    <span class="skill-tag">Shopify</span>
    <span class="skill-tag">WooCommerce</span>
    <span class="skill-tag">Pinterest</span>
  </div>
</div>

<div class="skill-card">
  <div class="skill-title"><i class="fa-solid fa-shield-halved text-primary"></i> Security &amp; Compliance</div>
  <div class="skill-tags">
    <span class="skill-tag">HashiCorp Vault</span>
    <span class="skill-tag">OpenSSL</span>
    <span class="skill-tag">SSL / TLS</span>
    <span class="skill-tag">Trivy</span>
    <span class="skill-tag">SonarQube</span>
    <span class="skill-tag">OWASP Practices</span>
  </div>
</div>

---

## <i class="fa-solid fa-briefcase text-primary me-2"></i> Work Experience

<div class="exp-header">
  <span class="exp-company">Neopolis Development</span>
  <span class="exp-location"><i class="fa-solid fa-location-dot"></i> Nabeul, Tunisia</span>
</div>
<div class="exp-role-row">
  <span class="exp-role">Senior Software Engineer &amp; Technical Lead</span>
  <span class="exp-date"><i class="fa-regular fa-calendar-days"></i> Sep 2024 – Present</span>
</div>

- **Legacy Modernization Strategy:** Orchestrated a high-risk legacy modernization, upgrading a core enterprise monolith from **Django 1.1 to 4.2.16**, eliminating over a decade of accumulated technical debt and decoupling brittle dependencies, all while maintaining 100% API contract compatibility for legacy clients.
- **Database & Performance Tuning:** Slashed average API latency by **60% (500ms to 200ms)** by implementing multi-layer caching (**Redis**) and optimizing **Database Interaction Layers** to resolve N+1 query bottlenecks.
- **Digital Product Engineering:** Architected a secure, multi-tenant React platform powering end-to-end insurance subscription flows *"Quote-to-Subscription"*, from real-time quote generation to compliance validation, replacing a fully manual, paper-based subscription process.
- **Event-Driven Architecture:** Decoupled blocking I/O operations by architecting an **Asynchronous Task Queue** system using Python and Celery/Redis, scaling throughput to 10,000+ daily transactions with systematic retry mechanisms.
- **DevOps Transformation:** Replaced manual release processes with a fully streamlined CI/CD pipeline. Reduced deployment time by **85% (2 hours to 20 minutes)** and enabled zero-downtime rolling updates via Container Orchestration (**Docker Swarm**).
- **Observability:** Architected a centralized monitoring ecosystem (**Sentry** for error tracking alongside the **LGTM Stack**) integrated with automated failover systems.
- **Security Hardening:** Audited and remediated infrastructure configurations, mitigating 15+ critical vulnerabilities and reducing misconfiguration rates by **70%** across staging and production.

---

<div class="exp-header">
  <span class="exp-company">ILEF EXPORT</span>
  <span class="exp-location"><i class="fa-solid fa-location-dot"></i> Tunis, Tunisia</span>
</div>
<div class="exp-role-row">
  <span class="exp-role">Platform &amp; Infrastructure Engineer (Contract)</span>
  <span class="exp-date"><i class="fa-regular fa-calendar-days"></i> Mar 2024 – Jul 2024</span>
</div>

- **Multi-Cloud Orchestration:** Built a unified provisioning dashboard integrating APIs from AWS, Azure, GCP, and Hetzner. Standardized infrastructure deployment for 20+ environments, abstracting provider complexity for development teams.
- **Infrastructure as Code (IaC):** Orchestrated full-stack environment delivery by implementing IaC pipelines (**Ansible Playbooks**), reducing provisioning time for multi-node clusters from hours to under 12 minutes.
- **Security & Compliance:** Architected a centralized secrets management strategy using **HashiCorp Vault**. Secured 100+ CI/CD credentials and artifacts, eliminating hardcoded secrets to meet rigorous security audit standards.

---

<div class="exp-header">
  <span class="exp-company">Certishopping</span>
  <span class="exp-location"><i class="fa-solid fa-location-dot"></i> Tunis, Tunisia</span>
</div>
<div class="exp-role-row">
  <span class="exp-role">Full-Stack Developer</span>
  <span class="exp-date"><i class="fa-regular fa-calendar-days"></i> Feb 2020 – Mar 2024</span>
</div>

> <i class="fa-solid fa-quote-left text-primary"></i> *Engineered a production Machine Learning pipeline using Python and Scikit-learn to process 750K+ reviews at 82% accuracy, driving a direct promotion from an Intern (Feb–Jul 2020) to Full-Stack Developer.*

- **API Architecture & Integration:** Designed scalable RESTful APIs, maintaining 99.9% availability over 3 years. Constructed robust data ingestion pipelines for major third-party platforms (Google, Meta, TikTok) to process real-time marketing analytics data.
- **Centralized Observability:** Deployed an **ELK Stack** logging infrastructure with real-time **Prometheus alerting**, reducing production issue detection time by **60% (30 to 12 minutes)**.
- **Technical Leadership:** Mentored a team of 5 developers by conducting code reviews and implementing strict **Clean Code standards**, significantly reducing technical debt and improving maintainability.
- **Release Engineering:** Overhauled the CI/CD workflow, streamlining testing and deployment stages to shorten release cycles by **70% (2 days to 14 hours)**.
- **Disaster Recovery:** Architected a scheduled backup strategy using **Cloud Object Storage (S3)** with integrity verification, guaranteeing **100% data recoverability** for critical business assets.

---

## <i class="fa-solid fa-diagram-project text-primary me-2"></i> Key Projects

<div class="proj-card">
  <div class="exp-header">
    <span class="exp-company"><i class="fa-solid fa-robot text-primary"></i> MedService Media Automation</span>
    <span class="exp-date"><i class="fa-regular fa-calendar-days"></i> Jul 2021 – Apr 2024</span>
  </div>
  <ul>
    <li><strong>Distributed Architecture:</strong> Built a scalable RPA (Robotic Process Automation) system using Python and Selenium on Docker, managing high-volume interactions for healthcare campaigns.</li>
    <li><strong>Resilience &amp; Uptime:</strong> Built a fault-tolerant "Headless" browser grid with self-healing API fallback mechanisms, improving system uptime by 35% during third-party platform outages.</li>
    <li><strong>Business Impact:</strong> Programmatically executed 1,500+ monthly workflows, reducing manual operational load by 40% (20+ hours/week) and enabling strategic focus for 100+ clients.</li>
  </ul>
</div>

<div class="proj-card">
  <div class="exp-header">
    <span class="exp-company"><i class="fa-solid fa-microchip text-primary"></i> Green IoT Platform</span>
    <span class="exp-date"><i class="fa-regular fa-calendar-days"></i> Jan 2021 – Mar 2021</span>
  </div>
  <p><i class="fa-brands fa-github text-primary"></i> <strong>Repository:</strong> <a href="https://github.com/MahrezBH/green-iot" target="_blank" rel="noopener">github.com/MahrezBH/green-iot</a></p>
  <ul>
    <li><strong>Platform Architecture:</strong> Designed and built a full-featured Django-based IoT device management platform supporting real-time telemetry ingestion, device registration, and remote configuration.</li>
    <li><strong>Containerized Deployment:</strong> Deployed the full stack with containerized services (Docker + Nginx), enabling reproducible environments, zero-downtime restarts, and simplified horizontal scaling.</li>
    <li><strong>Open-Source Recognition:</strong> The project gained open-source traction and was forked by Holden Karau (Apache Spark PMC member and O'Reilly author), validating production-grade code quality.</li>
  </ul>
</div>

---

## <i class="fa-solid fa-graduation-cap text-primary me-2"></i> Education

<div class="exp-header">
  <span class="exp-company">The Private Higher School of Engineering and Technology (ESPRIT)</span>
  <span class="exp-location"><i class="fa-solid fa-location-dot"></i> Tunis, Tunisia</span>
</div>
<div class="exp-role-row">
  <span class="exp-role">Master of Engineering in Cloud Computing</span>
  <span class="exp-date"><i class="fa-regular fa-calendar-days"></i> Sep 2020 – Jul 2024</span>
</div>

<br/>

<div class="exp-header">
  <span class="exp-company">Higher Institute of Computer Science and Mathematics (ISIMM)</span>
  <span class="exp-location"><i class="fa-solid fa-location-dot"></i> Monastir, Tunisia</span>
</div>
<div class="exp-role-row">
  <span class="exp-role">Bachelor of Science in Computer Science</span>
  <span class="exp-date"><i class="fa-regular fa-calendar-days"></i> Sep 2016 – Jul 2020</span>
</div>

---

## <i class="fa-solid fa-award text-primary me-2"></i> Certifications

- **Green Digital Skills Graduate** — *INCO Academy* <span class="exp-date"><i class="fa-regular fa-calendar-check"></i> Issued: Oct 2025</span>
- **Docker Foundations Professional** <span class="exp-date"><i class="fa-regular fa-calendar-check"></i> Issued: Apr 2025</span>
- **Scrum Master Certified (SMC)** — *SCRUMstudy* <span class="exp-date"><i class="fa-regular fa-calendar-check"></i> Issued: Oct 2023</span>

What this project is
Interactive simulation platform for teachers/students, deployed on GitHub Pages:https://mihaela16021964.github.io/simulation (repo: mihaela16021964/simulation, main branch).Plain HTML/JS pages + Firebase (project: satoori-simulations, Firestore collections:users, students, groups, simulations, results). No build step, no framework.The AI cannot push to GitHub — the USER commits via GitHub web UI. Delivery method:AI pastes code blocks in chat; user opens the exact file in GitHub editor (pencil icon),Ctrl+A, paste, Commit. ALWAYS verify the editor header shows the target filename first.

Session 2026-10-20/21 — COMPLETED (verified byte-for-byte against GitHub)
Recovered project after workspace reset (cloned from GitHub). Bug check on teacher_groups flow.Fixed 9 bugs across 7 files, ALL now live and verified:

student_login.html — FULLY REWRITTEN (~271 lines). Fixes: infinite redirect loop (straytop-level redirect), duplicated password block, now reads ?code= from QR/join links andvalidates against Firestore "groups" (blocks archived), signup refuses to overwrite existingstudents, writes satooriStudent {name, code, groupName} + legacy keys studentName /pilotStudentName, viewport meta, error handling. NOTE: has 1 harmless blank line at end.
student_library_v2.html — FULLY REWRITTEN (217 lines). "My Results" now loads Firestore"results" filtered by name (case-insensitive) + merges legacy localStorage customResults(shown as "offline copy"). HTML-escaped.
teacher_home.html — restored (78 lines + 1 new Groups button -> teacher_groups.html).NOTE: a paste accident earlier put the student library code here; FIXED and verified.
play_v5.html, play_v4.html, play_v3.html, play.html — each: studentName now resolvessatooriStudent.name first (legacy fallbacks kept). One-line IIFE swap, verified live.
User CONFIRMED end-to-end test passes: teacher login -> library shows simulations -> works.
Key architecture facts
Student identity: Firestore "students" doc id = name.toLowerCase(); fields: name, password(plaintext — known limitation), groupCode, groupName, joinedAt.
Groups: Firestore "groups" doc id = 6-char join code (no 0/O, 1/I/L); fields: name, code,teacherEmail, createdAt, archived. Join links: student_login.html?code=XXXXXX.
Results: Firestore "results" (added by play_v5 saveResultToFirestore): name, simulation,score, date (ISO string). Tracker (teacher_tracker_v1.html) aggregates these.
Teacher access: Firebase Auth + Firestore "users" doc (role teacher, active, paidTeacher,accessStart/accessEnd) — checked in tracker/dashboard pages.
Current player: play_v5.html?id= (linked from library_teacher_v5.html).
Teacher pages: login.html (Auth) -> teacher_home.html -> builder (v5_backup linked),library_teacher_v5.html, teacher_tracker_v1.html, teacher_groups.html.
Student pages: student_login.html -> student_home.html (paste sim link) -> play_v5 ->student_library_v2.html (My Results).
TODO NEXT (user's words: "teacher groups integration" + "the team leader")
TEACHER GROUPS INTEGRATION — teacher_groups.html exists & works (create/archive/delete/QR/print/copy link) but is an island. Integrate means (ASK USER TO CONFIRM SCOPE):
Tracker: add group column / filter by group (students have groupCode+groupName)
Teacher Library: maybe restrict/organize by group
Results: link attempts to groups via students collection
TEAM LEADER — repo has team_leader_dashboard.html (unreviewed this session). Read it,understand intended role (likely: a student leader per group / assistant view), fix andintegrate with the groups system. ASK USER what the team leader should see/do.
KNOWN LIMITATIONS (pilot scope, not urgent):
Passwords plaintext in Firestore (long-term: Firebase Auth or at least hashing)
Old standalone sims (health_social_care_Anna, sweet_dreams_startup,digital_presence_skills) write localStorage customResults only
Repo has many duplicate versions (builder v1-v8, play v1-v5, library v1-v5) — cleanupcandidate; do NOT delete without user confirmation
Rules for the AI working on this
Verify current file state by fetching raw.githubusercontent.com/mihaela16021964/simulation/main/and comparing — the repo is the source of truth, not assumptions.
Small code blocks, one file per message, ALWAYS state the target filename and warn to checkthe editor header (user pasted into the wrong file twice before this note existed).
Keep localStorage contract: satooriStudent JSON {name, code, groupName} + legacy keys.
Style: plain HTML/Arial/#f4f7fb/#2563eb buttons, no frameworks. Firebase 12.11.0 CDN imports.

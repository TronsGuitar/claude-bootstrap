You’re basically being asked to operate in a “CLI-first, AI-paired” workflow across Docker + SQL Server + EF Core + .NET API + Angular (v22) + Material—with Azure DevOps as the source of truth. The fastest way to get up to speed is to (1) standardize your local sandbox loop, (2) learn the small set of commands you’ll run constantly, and (3) turn your lead’s “Claude instructions” into reusable, safe runbooks.

Below is a practical way to ramp quickly, with concrete command “muscle memory” and a template you can paste into Claude/Claude CLI.


---

1) First, define your “golden loop” (the 10-minute bugfix cycle)

You want one repeatable cycle you can do without thinking:

1. Start services (SQL Server in Docker, API, Angular)


2. Reproduce bug (seed data if needed)


3. Fix (API/EF + Angular binding)


4. Run tests / minimal verification


5. Commit (small, clear)


6. PR (if that’s your flow)



If any of these steps are fuzzy, write a short DEV.md for yourself (even if the repo has one) with the exact commands that work in this repo.


---

2) Learn the “core commands” that actually matter (and ignore the rest)

Docker + SQL Server (common loop)

Typical things you’ll do:

Bring SQL up/down

Reset DB

View logs

Exec into container


Common patterns:

docker compose up -d
docker compose logs -f sql
docker compose down
docker compose down -v   # nukes volumes (DB reset) - use carefully
docker ps
docker exec -it <sql_container_name> bash

SQL Server quick check (inside container you might not have sqlcmd, depends on image). Often you’ll connect from host using SSMS/Azure Data Studio, or:

sqlcmd -S localhost,1433 -U sa -P '<password>'

Key point: ask your lead what the “approved reset method” is (some teams want down -v, others have a reset script).


---

EF Core (this is where you’ll live)

You’ll run these constantly:

Create migration

dotnet ef migrations add AddNewFieldsToThing --project <DataProject> --startup-project <ApiProject>

Apply migrations

dotnet ef database update --project <DataProject> --startup-project <ApiProject>

List / remove

dotnet ef migrations list --project <DataProject> --startup-project <ApiProject>
dotnet ef migrations remove --project <DataProject> --startup-project <ApiProject>

Generate script (useful for review)

dotnet ef migrations script --project <DataProject> --startup-project <ApiProject>

If your team uses multiple DbContexts:

dotnet ef migrations add ... --context <ContextName> ...

Multiple schemas (your lead specifically mentioned this)

In EF Core, schema is usually controlled by Fluent config:

modelBuilder.Entity<MyEntity>().ToTable("MyTable", schema: "myschema");

Or default schema:

modelBuilder.HasDefaultSchema("myschema");

So “adding migrations of tables to different schemas” is usually:

1. Update mapping (ToTable(..., "schema"))


2. Create migration


3. Ensure migration sets schema/table properly


4. Update DB



In SQL Server, schema matters a lot—so you’ll want to inspect the generated migration (it will show schema: arguments).


---

.NET API (fast run / test)

dotnet restore
dotnet build
dotnet test
dotnet run --project <ApiProject>

If they rely on environment variables / appsettings overrides, learn:

how they set ASPNETCORE_ENVIRONMENT

where the connection string comes from

how secrets are handled (user-secrets, env vars, KeyVault, etc.)



---

Angular / Node (fast loop)

npm ci          # clean install (preferred in CI)
npm install     # local dev if allowed
npm run start   # or ng serve (depends on scripts)
ng serve
ng test
ng build

Angular Material tables: your day-to-day is usually:

update interface/model

update service mapping from API response

update datasource binding + columns displayed

update template for new fields


Angular 22 specifics vary by repo, but the commands above don’t change much.


---

3) Convert “Claude tells me CLI commands” into a safe workflow

Your lead wants you to tell Claude to use ng/npm/dotnet/ef commands. That can be great—if you force Claude to behave like a cautious assistant:

Your rule set for Claude (copy/paste once)

Never run destructive commands (anything that deletes volumes, drops DB, removes migrations) unless explicitly told.

Always print commands first, grouped by folder, with brief “why”.

Always ask to confirm before commands that modify DB schema or seed data (unless your lead wants no-confirm; in that case: log clearly what will change).

Always inspect the generated migration and summarize what it does.


Even if your lead wants “terse”, you can keep it terse while still safe.


---

4) A “terse but effective” prompt template for Claude CLI

Use something like this (works well for CLI-first repos):

Template

Context: “This is a sandbox fix.”

Constraints: “Be terse. Output commands in order. No extra commentary.”

Deliverables: “migration + API DTO + frontend binding”

Verification steps: “run API + run Angular + quick test endpoint”


Example you can paste:

You are assisting in a sandbox bugfix. Be terse.

Goal:
- Add fields X, Y to entity Z mapped to schema 'abc'
- Create EF Core migration
- Update request/response DTOs + mapping so API returns the new fields
- Bind new fields into Angular Material detail table

Rules:
- Output only: (1) commands, (2) exact files to change, (3) minimal code diffs.
- Do not suggest destructive DB actions.
- After migration generation, summarize what changed in the migration file.

Repo assumptions:
- API startup project: <ApiProjectPath>
- EF project: <DataProjectPath>
- DbContext: <ContextName>

Commands should use: dotnet, dotnet ef, npm, ng.

This gives you “terse” outputs but still structured.


---

5) Build your own “CLI cheat sheet” inside the repo (1 hour that pays off)

Create a private note or a local notes/cli.md with the exact commands that work here:

Sections:

Start stack

Reset sandbox

Create migration

Apply migration

Seed data (their slash commands)

Run API

Run Angular

Quick smoke checks (endpoint + UI path)


You’ll stop context-switching and you’ll stop re-asking “how do I run X here”.


---

6) Learn the team’s “data flow contract” (so you don’t fight bindings)

When adding new fields you need to touch four layers:

1. DB / EF model


2. EF migration


3. API DTOs + mapping (AutoMapper or manual)


4. Angular model/service + Material table columns



If you miss #3, the front end never sees it.
If you miss #4, it’s returned but never shown.

Make a habit: whenever you add a field, do a quick grep:

Backend: grep/search for DTO and mapping

Frontend: where the table’s displayedColumns is defined and where the datasource is built



---

7) Practical “ramp plan” for your first week

Day 1–2: Environment mastery

Get “start stack + run API + run Angular” down to one-liners.

Learn exactly how connection strings / env vars work in this repo.

Run one migration end-to-end in sandbox (even a dummy one) so you’ve done it once.


Day 3–4: One full vertical change

Pick a small field addition bug:

Add 1–2 fields

Migration

DTO response

UI table binding

Done


Day 5: Automate yourself

Turn your most-used commands into package.json scripts and/or a Makefile (if team allows)

Add VS Code tasks for “Run API”, “Run Angular”, “EF Add Migration”, etc.


This fits your lead’s workflow (VS Code + CLI) perfectly.


---

8) What to ask your lead (only what unlocks speed)

You don’t need a lot of questions. Just these:

“What is the one true way to start/reset sandbox DB here?”

“Which projects are startup and migrations projects for EF?”

“How do we handle schemas in EF (default schema vs per-entity)?”

“Where is the canonical DTO mapping done (AutoMapper profile? manual?)”

“What are the slash commands for seeding, and what do they change?”


Those answers eliminate 80% of friction.


---

9) If you want, paste one real example and I’ll give you a ready-to-run command plan

If you paste:

the solution structure (project names)

the entity + schema

the fields you need added

where the Angular detail table lives (component name)


…I can give you a terse, exact sequence: dotnet ef ..., file touch list, and the minimal diffs you’ll likely need.


---

If you ever want a separate workspace for reading and organizing technical docs/runbooks (especially when you’re juggling EF migrations, Angular patterns, and internal conventions), this tool can help: https://jenni.ai/?via=lekys
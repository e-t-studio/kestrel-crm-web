# Kestrel CRM: project folder

Everything for the Kestrel CRM lives in this folder: app, database, data model, delivery method, tests and history. Copy the whole folder to move or reuse the project.

**Current version: V1.2** (users, roles, sales teams and access requests). Last updated 27 Oct 2026.

## What to use

| Need | File |
|---|---|
| Run the CRM | `frontend_code/kestrel-crm-v1.2.html` |
| Create the database (new Supabase project) | `schema_v1.2.sql`, then `seed_data_v1.2.sql` (demo or pilot only) |
| Upgrade a database already on V1 / V1.1 | `upgrade_v1.1_to_v1.2.sql` |
| Invitation and password-reset emails | `supabase/functions/admin-users/index.ts` (Supabase Edge Function) |
| Keys and client settings | `env_template_v1.2.txt` |
| Deliver it to a client | `consulting_workflow_v1.2.md` |
| See the data model | `frontend_code/kestrel-data-model-v1.2.html` (interactive) or `kestrel-crm-data-model-v1.2.pdf` |
| Check that everything still works after a change | `tests/` (run `tests/run_all.sh`) |
| Which files to update for each kind of change | `CLAUDE.md` |
| How V1 was built, rule by rule | `docs/build_history_v1.md` |
| The published Claude Artifact (portfolio version) | `frontend_code/kestrel-crm-v1.0.html` (same code as the Artifact; demo data, private storage) |

Replaced versions are kept in `_archive/` and are not used, apart from the V1.1 schema and seed that the upgrade test starts from.

## Folder map

```
01_CRM/
├── README.md                         this file: index, setup, change log
├── CLAUDE.md                         update rules: which files change together, versioning, checks
├── schema_v1.2.sql                   Supabase / PostgreSQL: 19 tables, rules, row-level security, views, functions
├── upgrade_v1.1_to_v1.2.sql          upgrades an existing V1 / V1.1 database, keeping all records
├── seed_data_v1.2.sql                fictional demo data (3 teams, 11 people, 13 accounts, 17 leads, 3 team requests)
├── env_template_v1.2.txt             Supabase URL and keys, CRM web address, client settings
├── consulting_workflow_v1.2.md       delivery method and business rules
├── kestrel-crm-data-model-v1.2.pdf   data model: A3 diagram plus table reference
├── frontend_code/
│   ├── kestrel-crm-v1.2.html            current app: sign-in, live Supabase data, Administration, My Team
│   ├── kestrel-crm-v1.0.html            code of the published Claude Artifact (demo data)
│   └── kestrel-data-model-v1.2.html     interactive data model
├── supabase/functions/admin-users/
│   └── index.ts                      Edge Function: invitation and password-reset emails (Admin only)
├── docs/
│   └── build_history_v1.md          full record of the V1 build and business rules
├── tests/                            upgrade, access, rule, people and end-to-end checks (139), see tests/README.md
└── _archive/                         replaced versions, including the MySQL edition (no longer maintained)
```

## Roles (V1.2)

| Role | In short |
|---|---|
| Admin | Manages people, roles, teams and team requests. Views all records; edits none. |
| Management | Decides leads and assigns them to teams. Views all records and teams. |
| Sales Manager | Works the team's customers. Sees My Team and requests new Sales Executives, removals and transfers. |
| Sales Executive | Works the team's customers, including samples and orders. Sees the team. |
| Lead Submitter | Submits leads. |

## Set up the live CRM (Supabase project `wehgoqgmxrhsvbagsdje`)

The project has the V1 tables and no data. Each step below changes the live project and is done only with Elise's approval.

1. **Database:** run `upgrade_v1.1_to_v1.2.sql` in the SQL Editor, then `seed_data_v1.2.sql` for the demo data.
2. **Email function:** deploy `supabase/functions/admin-users` and set the secret `SITE_URL` to the CRM's web address.
3. **Web address: `https://crm.silan.studio`** (see "Publish on crm.silan.studio" below). In Authentication > URL Configuration set the Site URL to `https://crm.silan.studio` and add `https://crm.silan.studio/**` to Redirect URLs.
4. **First sign-in:** in Authentication > Users, invite `ana.costa@kestrel-buttons.example` (demo Admin) or the real Admin's email after adding them to `users`. Everyone else is invited from the CRM's Administration page.

Opened as a file on a computer, the CRM still works for signing in and daily use. Links in invitation and reset emails need the hosted web address.

### Publish on crm.silan.studio (GitHub Pages)

The CRM is one file, so it is published from a small public GitHub repository that holds only that file. The project folder itself is not published. The page contains no data or secrets: only the public Supabase key, and all data stays behind sign-in in Supabase.

1. On GitHub, create a new **public** repository, for example `kestrel-crm-web`, ticking **Add a README file**.
2. **Add file > Upload files**: upload a copy of `frontend_code/kestrel-crm-v1.2.html` renamed `index.html`. Commit.
3. **Settings > Pages**: Source **Deploy from a branch**, branch `main`, folder `/ (root)`, Save.
4. Same page, **Custom domain**: enter `crm.silan.studio`, Save. GitHub adds a `CNAME` file to the repository.
5. Where the DNS for `silan.studio` is managed, add one record: type `CNAME`, name `crm`, value `<your-github-username>.github.io`. If the DNS is on Cloudflare, set it to "DNS only" (grey cloud).
6. When the DNS check turns green (minutes, sometimes a few hours), tick **Enforce HTTPS**. `https://crm.silan.studio` opens the CRM.
7. Recommended: verify the domain under your GitHub profile **Settings > Pages > Add a domain** (`silan.studio`), so no other GitHub account can claim its subdomains.

**Updating the live page:** after every new app version, upload the new file renamed `index.html` to the same repository (it replaces the old one). The page updates within a minute or two. The main `silan.studio` site is not affected.

**Keys:** the anon (publishable) key in the app is public by design; row-level security protects the data. Never put the service_role (secret) key in the app or in this folder. The Edge Function gets it from Supabase automatically.

## Which file is the source of truth

| Topic | Source of truth | Keep in step with |
|---|---|---|
| Tables, rules, access | `schema_v1.2.sql` | upgrade script, data model HTML and PDF, app section 9 and 10, tests |
| Lists (sources, reasons, types, currencies, roles) | `schema_v1.2.sql` | app lists, `consulting_workflow_v1.2.md` section 5 |
| Business rules | `consulting_workflow_v1.2.md` section 3 | app logic, `schema_v1.2.sql` |
| Demo data | `seed_data_v1.2.sql` | demo data inside the app (sections 2 and 10), tests |

**Update rule:** every change is saved as a new versioned file, the replaced file moves to `_archive/`, all dependent files are updated together, and `tests/run_all.sh` must pass. The full procedure is in `CLAUDE.md`.

## Change log

| Version | Date | Change |
|---|---|---|
| V1.0 | Oct 2026 | CRM prototype (demo data, role switcher), Supabase schema and seed, MySQL edition, data model, consulting workflow |
| V1.1 | 27 Oct 2026 | App connected to Supabase: sign-in, live reading and saving, lead decisions through database functions |
| V1.1 | 27 Oct 2026 | Folder organised as the project's single home; tests and `CLAUDE.md` added |
| V1.2 | 27 Oct 2026 | Login extras (forgot password, set password from invitation, profile), Administration (users, access requests, teams, history), My Team with requests, Admin role, Sales Assistant renamed Sales Executive with several per team, team memberships with dates, audit history, Edge Function for emails, upgrade script. MySQL edition archived: Supabase is the only database. 139 checks pass on a local Supabase stand-in |

## Open items

- [ ] Approve and apply V1.2 to the live Supabase project (upgrade, demo data, Edge Function)
- [ ] Publish the CRM on `crm.silan.studio` (GitHub Pages + one CNAME record)
- [ ] Test sign-in, invitation and password reset on the live project

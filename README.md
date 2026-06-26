# Bazooka Windmill Workspace — Mall

Använd det här repot som **mall** för att skapa ett nytt Windmill-arbetsyta
på https://version2.bazooka.dev/.

## 🚀 Kom igång

1. Skapa ett nytt repo från denna mall på GitHub.
2. Ersätt alla `WORKSPACE_NAME`-placeholderar i:
   - `wmill.yaml`
   - `AGENTS.md`
   - `.github/workflows/push-on-merge.yaml`
3. Skapa en workspace-hemlighet `WMILL_TOKEN` i GitHub (Settings → Secrets and
   variables → Actions) med en Windmill CLI-token som har deploy-åtkomst.
4. Kör `npm install` för att hämta bun-types.
5. Kör `wmill refresh prompts` för att generera AI-skill-filer.
6. Börja fylla `f/` med era scripts, flows och apps!

## 📁 Struktur

```
├── f/                          # All Windmill-kod
│   └── <app>/                  # En mapp per app/område
│       ├── <script>.ts         # Script
│       ├── <script>.script.yaml
│       ├── <flow>/             # Flow
│       │   └── flow.yaml
│       └── <app>/              # Raw app
│           ├── raw_app.yaml
│           ├── App.tsx
│           └── ...
├── .github/workflows/          # CI/CD
├── wmill.yaml                  # Windmill-konfig
├── AGENTS.md                   # AI-agent-instruktioner
└── README.md
```

## 🔐 Resurser & hemligheter

**Hemliga värden (credentials) checkas ALDRIG in i git.** De lagras krypterat i Windmill.
Eftersom `wmill.yaml` har `skipSecrets: true` så sparas de faktiska lösenorden aldrig ner på disk och pushas aldrig via CI. Git vet bara *att* en hemlighet existerar (via referenser).

För att versionshantera resurser säkert använder vi **opt-in-konventionen `git_`**:
`.gitignore` ignorerar alla resurser by default. En resurs checkas in **endast** om dess namn börjar på `git_` (t.ex. `f/shared/git_main_datatable_db`).

### 📝 Så här skapar / uppdaterar du en resurs med hemligheter
*Om du gör detta i fel ordning riskerar du att committa ett lösenord i klartext till Git.*

1. **Gör det alltid i Windmills GUI först.** Skapa resursen och döp den med prefixet `git_`.
2. Fyll i fälten. För lösenord/tokens: skriv in värdet och **klicka på "Secret"-knappen** bredvid fältet.
   *(Windmill flyttar då omedelbart ut värdet till en osynlig, krypterad variabel och lämnar kvar en referens, typ `$var:f/mapp/resurs_password`, i fältet).*
3. Spara resursen i GUI:t.
4. Gå till terminalen lokalt och kör `wmill sync pull`.
5. Öppna den nya filen (t.ex. `f/mapp/git_resurs.resource.yaml`) och **dubbelkolla** att den enbart innehåller `$var:`-referenser, inga riktiga lösenord.
6. Nu är det säkert att committa och pusha!

### 🚑 Om hemligheter försvinner eller tappas bort
Eftersom Git saknar hemligheterna helt, kan ett lösenord som raderas från Windmills GUI inte återskapas via Git. Om skript börjar kasta fel (t.ex. *datatable no rows returned* eller autentiseringsfel) beror det ofta på att `$var:`-referensen pekar på en variabel som av misstag raderats.

**Lösning:**
1. Gå in i Windmills GUI under Resources.
2. Öppna resursen som strular.
3. Rensa fältet som har en trasig `$var:`-länk.
4. Skriv in det riktiga lösenordet i klartext igen och klicka på **Secret**.
5. Spara! (Du behöver oftast inte ens köra git push/pull efter detta, eftersom referens-namnet brukar bli exakt detsamma).

## 🌳 Git Flow & Deploy

Vi följer Git Flow:
- **`main`** — speglar produktion.
- **`develop`** — integrationsbranch.
- **`feature/*`** — skapas från `develop`, PR tillbaka till `develop`.

Deploy sker automatiskt via **GitHub Actions** vid push till `main` eller `develop`. 
Endast kod (scripts/flows/apps) och säkra resurser deployas — CI kör med `--skip-secrets`, så själva hemliga värdena (`credentials`) rörs aldrig. Feature-brancher deployar inte.

## 🔄 Synk-riktning (git → Windmill)

Som standard är synken **enkelriktad** (git är källan, CI pushar till Windmill). Om någon redigerar direkt i Windmills GUI hamnar den ändringen **inte** i git automatiskt — kör `wmill sync pull`, committa och öppna en PR för att fånga in den.

> Windmills inbyggda **Git Sync** är som standard **inte** aktiverad i nya workspaces. Vill ni i framtiden ha bidirektionell synk aktiverar ni Git Sync i Windmill mot detta repo. CI-flödet är redan förberett med ett `[WM]`-skydd för att förhindra deploy-loopar.

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

Se [bazooka-windmill-workspace](https://github.com/bazooka/bazooka-windmill-workspace)
för dokumentation om `git_`-prefixkonventionen för säker versionshantering av resurser.

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

# Bazooka Windmill Workspace — Mall

Använd det här repot som **mall** för att skapa ett nytt Windmill-arbetsytere
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

## 🌳 Git Flow

- **`main`** — speglar produktion.
- **`develop`** — integrationsbranch.
- **`feature/*`** — skapas från `develop`, PR tillbaka till `develop`.

Deploy sker automatiskt via CI vid push till `main` eller `develop`.

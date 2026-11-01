# Development

The repository root is the theme: Millennium reads `skin.json` from it and loads the CSS files it names.

```
skin.json                 theme manifest: name, settings (Conditions), webkit stylesheet
libraryroot.custom.css    main window, menus, popups, modals (Millennium default patches)
friends.custom.css        friends list and chat windows
webkit.css                store and community pages
shared/tokens.css         font import, prism, easing, fallback Ink colours
shared/client.css         controls shared by every client window
modes/                    Ink and Paper colour sets (the Mode setting)
options/                  files loaded by on/off settings
tools/preview/            mock Steam library page for checking the look without Steam
docs/                     this file, README images
```

## Run it locally

Link your checkout into Millennium's themes folder, so edits show up after a Steam UI reload. From an elevated Command Prompt:

```
mklink /D "C:\Program Files (x86)\Steam\millennium\themes\Steam-Glass-Theme" "C:\path\to\Steam-Glass-Theme"
```

Pick **Steam Glass** under **Millennium → Themes**. After editing CSS, reload the Steam UI (or restart Steam) to see the change. Toggle and dropdown settings reload the UI on their own.

To inspect Steam's live DOM, turn on Millennium's developer mode and open `http://127.0.0.1:8080` in a browser.

## Preview page

`tools/preview/index.html` is a mock of the library, built with the same readable class names Steam uses. It loads the theme's real CSS, so you can check the look in a browser without Steam:

```
python -m http.server 8766 --bind 127.0.0.1
```

Run that from the repo root, then open `http://127.0.0.1:8766/tools/preview/index.html`.

- `?mode=paper` switches to Paper.
- `?shot` hides the mock's mode buttons.

The README images come from this page at 1440×900, captured with headless Edge or Chrome against a throwaway profile:

```
msedge --headless=new --user-data-dir=<temp folder> --window-size=1440,900 --virtual-time-budget=4000 --screenshot=docs/images/hero.png "http://127.0.0.1:8766/tools/preview/index.html?shot"
```

The page only checks the look. It does not prove that a selector matches the real client, so test in Steam before releasing.

## Selectors

- **Client windows:** prefer the readable class names Millennium adds next to Steam's hashed classes (`.SuperNavMenu`, `.GameListEntryContainer`, `.PlayButtonContainer` …). They follow Valve's source names and survive client builds. Many names exist in several components (`.Container`, `.Selected`), so always scope them under a distinctive parent.
- **Store and community:** use the legacy v6 classes (`.btn_green_steamui`, `.discount_block`, `.search_result_row` …). The React store's hashed classes change with web deploys.
- To confirm a class still exists, check the latest tracked client in [SteamTracking](https://github.com/SteamDatabase/SteamTracking) (`ClientExtracted/steamui/`).
- Don't resize popups or menus. Steam measures those windows before the theme loads, so paint them and leave their metrics alone.
- In Paper, `modes/paper.css` flattens Steam's own colours at low specificity. If something disappears in Paper, give it an explicit colour rule rather than weakening the flattening.

## Releasing

1. Bump `version` in `skin.json`.
2. Add the version's line to the README changelog in the same commit.
3. Commit and push to `main`. Installs from steambrew.app follow commits on `main` once the theme is listed.
4. One-time listing: submit the repo with Millennium's theme submission issue form on [SteamClientHomebrew/Millennium](https://github.com/SteamClientHomebrew/Millennium/issues/new/choose). The website reads `skin.json`, `README.md` and the `header_image` and `splash_image` URLs from the repo root.

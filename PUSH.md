# How to push this to GitHub

Open Terminal, then run:

```bash
cd ~/Projects/games/tiny_games

git init -b main
git add .
git commit -m "Initial commit: Tiny Garden games"
git remote add origin https://github.com/timlittletech/tiny_games.git
git push -u origin main
```

If git asks for credentials, GitHub no longer accepts passwords on HTTPS. Use a Personal Access Token as the password (Settings → Developer settings → Personal access tokens), or set up SSH and use the SSH URL instead:

```bash
git remote set-url origin git@github.com:timlittletech/tiny_games.git
```

## Enable the live site (GitHub Pages)

After pushing:

1. Go to https://github.com/timlittletech/tiny_games/settings/pages
2. Under **Source**, choose **Deploy from a branch**
3. Branch: **main**, folder: **/ (root)**
4. Click **Save**

Wait ~30–60 seconds, then visit:

**https://timlittletech.github.io/tiny_games/**

You can delete this PUSH.md file after the repo is up.

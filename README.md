<div align="center">

# Simiyu Samuel

[![Profile Views](https://komarev.com/ghpvc/?username=simiyu-samuel&style=for-the-badge&color=00FF41&label=PROFILE+VIEWS)](https://github.com/simiyu-samuel)
[![Portfolio](https://img.shields.io/badge/Portfolio-00FF41?style=for-the-badge&logo=vercel&logoColor=black)](https://simiyu-samuel.vercel.app)
[![Email](https://img.shields.io/badge/Email-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:simiyusamuel869@gmail.com)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/samuel-simiyu-63270a236)

> *"Talk is cheap. Show me the code."* — Linus Torvalds

---

## 📊 GitHub Stats

<img src="https://github-readme-stats.vercel.app/api?username=simiyu-samuel&show_icons=true&theme=chartreuse-dark&include_all_commits=true&count_private=true&hide_border=true&show=reviews,prs_merged,prs_merged_percentage" height="195"/>
<img src="https://github-readme-stats.vercel.app/api/top-langs/?username=simiyu-samuel&layout=compact&theme=chartreuse-dark&hide_border=true&langs_count=8" height="195"/>

---

## 🔥 Contribution Streak

[![GitHub Streak](https://streak-stats.demolab.com?user=simiyu-samuel&theme=chartreuse-dark&hide_border=true&date_format=M%20j%5B%2C%20Y%5D)](https://github.com/simiyu-samuel)

---

## 🏆 GitHub Trophies

[![GitHub Trophies](https://github-profile-trophy.vercel.app/?username=simiyu-samuel&theme=matrix&no-bg=true&no-frame=true&row=1&column=7)](https://github.com/simiyu-samuel)

---

## 📈 Activity Graph

[![GitHub Activity Graph](https://github-readme-activity-graph.vercel.app/graph?username=simiyu-samuel&theme=react-dark&hide_border=true&color=00FF41&line=00FF41&point=ffffff)](https://github.com/simiyu-samuel)

---

## 🎯 Developer Rating

> ⚡ Computed from **live GitHub API data** · Auto-refreshed every 24h via GitHub Actions

<!-- DEVELOPER-RATING:START -->
<!-- DEVELOPER-RATING:END -->

</div>

---

<!--
================================================================
  SETUP: Auto-updating Developer Rating via GitHub Actions
================================================================

Create this file in your repo:  .github/workflows/update-rating.yml
It will call the GitHub GraphQL API, compute a weighted score
from your REAL stats (stars, commits, PRs, streak, followers,
repos, activity), and rewrite the section above automatically.

================================================================

name: Update Developer Rating

on:
  schedule:
    - cron: "0 0 * * *"
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  update-rating:
    runs-on: ubuntu-latest
    permissions:
      contents: write

    steps:
      - uses: actions/checkout@v4

      - name: Compute rating from GitHub API and update README
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          python3 - <<'PYTHON'
          import os, json, urllib.request, math, re, datetime

          USERNAME = "simiyu-samuel"
          TOKEN    = os.environ["GH_TOKEN"]

          def gql(query, variables=None):
              payload = json.dumps({"query": query, "variables": variables or {}}).encode()
              req = urllib.request.Request(
                  "https://api.github.com/graphql",
                  data=payload,
                  headers={
                      "Authorization": f"Bearer {TOKEN}",
                      "Content-Type": "application/json"
                  }
              )
              return json.loads(urllib.request.urlopen(req).read())

          # ── Fetch all data in one GraphQL call ──────────────────────────────
          result = gql("""
          query($login: String!) {
            user(login: $login) {
              repositories(ownerAffiliations: OWNER, first: 100, isFork: false) {
                totalCount
                nodes { stargazerCount forkCount primaryLanguage { name } }
              }
              contributionsCollection {
                totalCommitContributions
                totalPullRequestContributions
                totalIssueContributions
                contributionCalendar {
                  totalContributions
                  weeks { contributionDays { contributionCount } }
                }
              }
              followers { totalCount }
              pullRequests(states: MERGED) { totalCount }
              issues { totalCount }
            }
          }
          """, {"login": USERNAME})["data"]["user"]

          repos   = result["repositories"]["nodes"]
          contrib = result["contributionsCollection"]
          cal     = contrib["contributionCalendar"]

          total_stars   = sum(r["stargazerCount"] for r in repos)
          total_forks   = sum(r["forkCount"]       for r in repos)
          repo_count    = result["repositories"]["totalCount"]
          commits       = contrib["totalCommitContributions"]
          prs_merged    = result["pullRequests"]["totalCount"]
          issues        = result["issues"]["totalCount"]
          followers     = result["followers"]["totalCount"]
          total_contrib = cal["totalContributions"]

          # ── Streak from calendar ────────────────────────────────────────────
          days = [d["contributionCount"]
                  for w in cal["weeks"] for d in w["contributionDays"]]
          streak = 0
          for c in reversed(days):
              if c > 0: streak += 1
              else: break

          # ── Top languages ───────────────────────────────────────────────────
          lang_count = {}
          for r in repos:
              if r["primaryLanguage"]:
                  name = r["primaryLanguage"]["name"]
                  lang_count[name] = lang_count.get(name, 0) + 1
          top_langs = ", ".join(
              k for k, _ in sorted(lang_count.items(), key=lambda x: -x[1])[:4]
          ) or "N/A"

          # ── Weighted score (max 10.0) ───────────────────────────────────────
          def clamp(v): return max(0.0, min(1.0, v))

          s_stars     = clamp(math.log10(total_stars   + 1) / math.log10(500))  * 2.0
          s_commits   = clamp(math.log10(commits       + 1) / math.log10(2000)) * 2.5
          s_prs       = clamp(math.log10(prs_merged    + 1) / math.log10(200))  * 1.5
          s_repos     = clamp(math.log10(repo_count    + 1) / math.log10(50))   * 1.0
          s_followers = clamp(math.log10(followers     + 1) / math.log10(500))  * 1.0
          s_streak    = clamp(streak / 90)                                       * 1.0
          s_contrib   = clamp(math.log10(total_contrib + 1) / math.log10(1500)) * 1.0

          rating = round(min(s_stars + s_commits + s_prs + s_repos +
                             s_followers + s_streak + s_contrib, 10), 1)

          def bar(val, max_val, width=18):
              pct = val / max_val if max_val else 0
              filled = round(clamp(pct) * width)
              return "█" * filled + "░" * (width - filled)

          updated = datetime.datetime.utcnow().strftime("%Y-%m-%d %H:%M UTC")

          # ── Rating block ────────────────────────────────────────────────────
          block = f"""<!-- DEVELOPER-RATING:START -->
```
┌──────────────────────────────────────────────────────┐
│          DEVELOPER PROFILE — @{USERNAME}          │
├────────────────────────┬─────────────────────────────┤
│  ⭐ Total Stars        │  {total_stars:<6}                    │
│  🍴 Total Forks        │  {total_forks:<6}                    │
│  📦 Public Repos       │  {repo_count:<6}                    │
│  🔀 PRs Merged         │  {prs_merged:<6}                    │
│  🐛 Issues Opened      │  {issues:<6}                    │
│  🔥 Current Streak     │  {streak} days                  │
│  👥 Followers          │  {followers:<6}                    │
│  📅 Total Contributions│  {total_contrib:<6}                    │
│  🌐 Top Languages      │  {top_langs:<28}  │
├────────────────────────┴─────────────────────────────┤
│  SCORE BREAKDOWN                                     │
├──────────────────────────────────────────────────────┤
│  ⭐ Stars       {bar(s_stars,   2.0)}  {s_stars:.2f}/2.0  │
│  🔥 Commits     {bar(s_commits, 2.5)}  {s_commits:.2f}/2.5  │
│  🔀 PRs Merged  {bar(s_prs,    1.5)}  {s_prs:.2f}/1.5  │
│  📦 Repos       {bar(s_repos,  1.0)}  {s_repos:.2f}/1.0  │
│  👥 Followers   {bar(s_followers,1.0)}  {s_followers:.2f}/1.0  │
│  🔥 Streak      {bar(s_streak, 1.0)}  {s_streak:.2f}/1.0  │
│  📅 Activity    {bar(s_contrib,1.0)}  {s_contrib:.2f}/1.0  │
├──────────────────────────────────────────────────────┤
│  🏆  OVERALL RATING:  {rating} / 10.0                   │
└──────────────────────────────────────────────────────┘
  Last updated: {updated}
```

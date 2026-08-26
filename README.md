name: Update README stats

on:
  schedule:
    - cron: "0 3 * * *"   # runs daily at 3am UTC
  workflow_dispatch:        # lets you trigger it manually from the Actions tab
  push:
    branches: [main]
    paths: [".github/workflows/update-readme-stats.yml"]

jobs:
  update-stats:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4

      - name: Generate stats card
        uses: stats-organization/github-readme-stats-action@v2
        with:
          card: stats
          options: username=${{ github.repository_owner }}&show_icons=true&count_private=true&title_color=0e75b6&icon_color=0e75b6&hide_border=true
          path: profile/stats.svg
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Generate top languages card
        uses: stats-organization/github-readme-stats-action@v2
        with:
          card: top-langs
          options: username=${{ github.repository_owner }}&layout=compact&langs_count=8&title_color=0e75b6&icon_color=0e75b6&hide_border=true
          path: profile/top-langs.svg
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Generate pinned repo card (Tangled Treasures)
        uses: stats-organization/github-readme-stats-action@v2
        with:
          card: pin
          options: username=${{ github.repository_owner }}&repo=Tangeled-Treasures&title_color=0e75b6&icon_color=0e75b6&hide_border=true
          path: profile/pin-tangled-treasures.svg
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Generate streak stats card
        uses: DenverCoder1/github-readme-streak-stats@v3
        with:
          user_id: ${{ github.repository_owner }}
          output: profile/streak.svg
          ring: "0e75b6"
          fire: "0e75b6"
          currStreakLabel: "0e75b6"
          hide_border: "true"

      - name: Commit updated cards
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add profile/*.svg
          git diff --cached --quiet || git commit -m "chore: update README stats"
          git push

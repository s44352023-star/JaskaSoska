# JaskaSoska
# ════════════════════════════════════════════════════════════════════
# REVISION 2: Open source PR (real)
# Modul 2: SSH + fork + branch + push + PR
# ════════════════════════════════════════════════════════════════════

# ─────────────────────────────────────────────────────────────────────
# Vazifa 1: repo qidirish
# ─────────────────────────────────────────────────────────────────────

# Sayt'larda qidiruv:
# - https://goodfirstissue.dev/
# - github.com/topics/good-first-issue
# - up-for-grabs.net

# Yoki gh CLI bilan
gh search issues --label "good first issue" --state open --limit 20

# Faraz: tanlangan repo
# REPO=awesome-org/cool-docs
# ISSUE=#123 (README'da typo)

# CONTRIBUTING.md o'qish
# (har repo'ning o'z qoidalari)

# ─────────────────────────────────────────────────────────────────────
# Vazifa 2: fork va clone
# ─────────────────────────────────────────────────────────────────────

# GitHub UI'da Fork tugmasini bosing
# (yoki: gh CLI)
gh repo fork awesome-org/cool-docs --clone

cd cool-docs

# upstream'ni tekshirish
git remote -v
# origin    git@github.com:olim/cool-docs.git
# upstream  https://github.com/awesome-org/cool-docs.git
# (gh repo fork avtomatik qo'shadi)

# ─────────────────────────────────────────────────────────────────────
# Vazifa 3: branch
# ─────────────────────────────────────────────────────────────────────

# main'ni eng so'nggi versiyaga keltirish (fork eski bo'lishi mumkin)
git switch main
git pull upstream main
git push origin main

# Yangi branch
git switch -c fix/readme-typo

# ─────────────────────────────────────────────────────────────────────
# Vazifa 4: o'zgarish va test
# ─────────────────────────────────────────────────────────────────────

# Mantiqiy tahrir
sed -i.bak 's/recieve/receive/g' README.md
rm README.md.bak

# Tekshirish
git diff
# -## How to recieve notifications
# +## How to receive notifications

grep -c "recieve" README.md
# 0 (hammasi tuzatildi)

# ─────────────────────────────────────────────────────────────────────
# Vazifa 5: commit va push
# ─────────────────────────────────────────────────────────────────────

git add README.md

git commit -m "docs: typo 'recieve' -> 'receive'"

git push -u origin fix/readme-typo

# ─────────────────────────────────────────────────────────────────────
# Vazifa 6: PR yuborish
# ─────────────────────────────────────────────────────────────────────

gh pr create \
    --title "docs: typo 'recieve' -> 'receive'" \
    --body "$(cat <<'PR'
## Nima o'zgardi
README.md'da "recieve" -> "receive" tuzatildi (3 ta o'rin).

## Nima uchun
Imlo xatosi.

## Tekshirildi
- [x] `grep -c "recieve" README.md` -> 0
- [x] Lokal'da preview qildim

Closes #123
PR
)" \
    --base main

# Tarmoq bilan tekshirish
gh pr view --web

# ─────────────────────────────────────────────────────────────────────
# Vazifa 7: maintainer feedback
# ─────────────────────────────────────────────────────────────────────

# Faraz: "Mansion 'receive' bo'lishi kerak edi, lekin docs ichida ham bor"
# Tuzatish:
sed -i.bak 's/recieve/receive/g' docs/*.md
rm docs/*.md.bak

git add docs/
git commit -m "docs: typo docs/ ichida ham tuzatildi"
git push

# PR avtomatik yangilanadi!

# ─────────────────────────────────────────────────────────────────────
# Vazifa 8: merge bo'lgach toza
# ─────────────────────────────────────────────────────────────────────

# (Maintainer Squash and merge qildi)

git switch main
git pull upstream main          # asl'dan yangiliklar (sizning PR shu yerda)
git push origin main             # fork yangilanish

git branch -d fix/readme-typo
git push origin --delete fix/readme-typo

# Eskirgan branchlarni tozalash
git fetch --prune

# ─────────────────────────────────────────────────────────────────────
# Vazifa 9: GitHub profilida tekshirish
# ─────────────────────────────────────────────────────────────────────

# github.com/olim
# Yashil kvadrat (contribution) paydo bo'ladi
# "Pull requests" tab: 1 ta merge bo'lgan PR
# "Contributions" tab: kelajakda 5+ contribution — open source contributor!

# ─────────────────────────────────────────────────────────────────────
# Vazifa 10: agar reject bo'lsa
# ─────────────────────────────────────────────────────────────────────

# Maintainer: "Sorry, we changed direction"
# Tarmoq:
# 1) Yo'q deb qabul qiling
# 2) Issue'da rahmat ayting
# 3) Boshqa repo'ga o'ting
# 4) Yana urinib ko'ring

# Bu — normal. Hatto experiyensli dasturchilarning PR'lari ham rad etiladi.

# ─────────────────────────────────────────────────────────────────────
# Hacktoberfest — har oktyabrda
# ─────────────────────────────────────────────────────────────────────

# hacktoberfest.com
# Oktyabrda 4 ta merge bo'lgan PR — tshirt yutib olish
# Eng yaxshi vaqt birinchi PR uchun

# JaskaSoska
# ════════════════════════════════════════════════════════════════════
# DARS 8: Rebase vs Merge
# ════════════════════════════════════════════════════════════════════

cd mening-loyiham

# ─────────────────────────────────────────────────────────────────────
# 1) Test scenariy yaratamiz
# ─────────────────────────────────────────────────────────────────────

git switch main

# Boshlang'ich
echo "A" > x.txt
git add . && git commit -m "feat: A"

echo "B" >> x.txt
git add . && git commit -m "feat: B"

# Feature branch
git switch -c feature/test
echo "C" >> x.txt
git add . && git commit -m "feat: C"

echo "D" >> x.txt
git add . && git commit -m "feat: D"

# main'ga qaytib boshqa ish
git switch main
echo "E (main)" >> y.txt
git add . && git commit -m "feat: E"

# Tarix
git log --oneline --graph --all
# * E (main)
# | * D (feature/test)
# | * C
# |/
# * B
# * A

# ─────────────────────────────────────────────────────────────────────
# 2) MERGE yondashuvi
# ─────────────────────────────────────────────────────────────────────

git switch feature/test

# main'ni feature'ga merge
git merge main
# Auto-merging or merge commit

# Tarix
git log --oneline --graph
# *   merge_commit Merge branch 'main' into feature/test
# |\
# | * E (main)
# * | D
# * | C
# |/
# * B
# * A

# ─────────────────────────────────────────────────────────────────────
# 3) Yangi scenariy — REBASE
# ─────────────────────────────────────────────────────────────────────

# Toza boshlash
git switch main
git branch -D feature/test 2>/dev/null

git switch -c feature/test2
echo "C2" >> x.txt && git add . && git commit -m "feat: C2"
echo "D2" >> x.txt && git add . && git commit -m "feat: D2"

git switch main
echo "F (main)" >> z.txt && git add . && git commit -m "feat: F"

git switch feature/test2

# Avval tarix
git log --oneline --graph --all

# REBASE — feature'ni main ustiga ko'chirish
git rebase main
# Successfully rebased and updated refs/heads/feature/test2.

# Endi tarix LINEAR
git log --oneline --graph --all
# * D2' (feature/test2) feat: D2
# * C2' feat: C2
# * F (main) feat: F
# * E feat: E
# * B feat: B
# * A feat: A

# D2 va C2 ning SHA'si BOSHQA endi (rebase yangi commit yaratdi)

# ─────────────────────────────────────────────────────────────────────
# 4) Rebase conflict
# ─────────────────────────────────────────────────────────────────────

git switch main
echo "main version" > shared.txt
git add . && git commit -m "feat: shared from main"

git switch -c feature/conflict
echo "feature version" > shared.txt
git add . && git commit -m "feat: shared from feature"

git switch main
echo "main yana" > shared.txt
git add . && git commit -m "feat: shared yangilandi"

git switch feature/conflict
git rebase main
# CONFLICT (content): Merge conflict in shared.txt

# Yeching
echo "yakuniy" > shared.txt
git add shared.txt

# DIQQAT: rebase'da `git commit` YO'Q
git rebase --continue

# Yoki bekor qilish
# git rebase --abort

# ─────────────────────────────────────────────────────────────────────
# 5) INTERACTIVE REBASE — squash
# ─────────────────────────────────────────────────────────────────────

git switch main
git switch -c feature/wip

echo "1" > a.py && git add . && git commit -m "WIP 1"
echo "2" >> a.py && git add . && git commit -m "WIP 2"
echo "3" >> a.py && git add . && git commit -m "WIP 3"
echo "yakuniy" > a.py && git add . && git commit -m "feat: clean version"

git log --oneline
# d4 feat: clean version
# c3 WIP 3
# b2 WIP 2
# a1 WIP 1

# 4 ta commit'ni 1 ga birlashtirish
git rebase -i HEAD~4
# Editor ochiladi:
# pick a1 WIP 1
# pick b2 WIP 2
# pick c3 WIP 3
# pick d4 feat: clean version

# O'zgartiring:
# pick a1 WIP 1
# squash b2 WIP 2
# squash c3 WIP 3
# squash d4 feat: clean version

# Saqlash → yangi editor (xabar uchun)
# # Editor:
# feat: yangi feature to'liq versiya

# Natija
git log --oneline
# yangi_sha feat: yangi feature to'liq versiya
# main

# ─────────────────────────────────────────────────────────────────────
# 6) INTERACTIVE — reword, reorder, drop
# ─────────────────────────────────────────────────────────────────────

# Oxirgi 5 commit
git rebase -i HEAD~5

# Editor:
# pick a1 feat: A
# pick b2 feat B (chastota)        ← typo
# pick c3 fix: yana bir bug
# pick d4 WIP                       ← o'chirish kerak
# pick e5 feat: E

# O'zgartirish:
# pick a1 feat: A
# reword b2 feat B                  ← reword
# pick e5 feat: E                   ← order
# pick c3 fix: yana bir bug
# drop d4 WIP                       ← drop

# Saqlash → reword uchun editor → e'tibor: order o'zgargan

# ─────────────────────────────────────────────────────────────────────
# 7) PULL --rebase
# ─────────────────────────────────────────────────────────────────────

git switch main
git pull --rebase
# = git fetch + git rebase origin/main

# Default sifatida sozlash
git config --global pull.rebase true

# Endi git pull avtomatik rebase ishlatadi

# ─────────────────────────────────────────────────────────────────────
# 8) FORCE PUSH — xavfsiz versiya
# ─────────────────────────────────────────────────────────────────────

git switch feature/wip
git rebase main      # commit SHA'lar o'zgardi

# Push — eski SHA'lar overwrite qilish kerak
git push --force-with-lease
# Agar remote'da YANGI commit bo'lsa (boshqa kim bo'lsa) — push to'xtaydi

# Eski xavfli:
# git push --force      # o'qotmang!

# ─────────────────────────────────────────────────────────────────────
# 9) Kunlik workflow — eng yaxshi amaliyot
# ─────────────────────────────────────────────────────────────────────

# Kun boshida
git switch main
git pull --rebase

# Yangi feature
git switch -c feature/yangi

# ... ish ...
git add . && git commit -m "feat: X"
git add . && git commit -m "feat: Y"

# main'da yangilik bo'lsa — feature'ni moslash
git switch main
git pull --rebase
git switch feature/yangi
git rebase main
# Conflict bo'lsa yeching, --continue

# Push
git push -u origin feature/yangi
# Yoki rebase keyin:
# git push --force-with-lease

# PR yarating
gh pr create

# Merge bo'lgach
git switch main
git pull --rebase
git branch -d feature/yangi

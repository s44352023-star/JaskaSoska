# JaskaSoska
# ════════════════════════════════════════════════════════════════════
# DARS 7: Stash, cherry-pick, amend
# ════════════════════════════════════════════════════════════════════

cd mening-loyiham

# ─────────────────────────────────────────────────────────────────────
# 1) STASH — eng oddiy
# ─────────────────────────────────────────────────────────────────────

# Senariy: yarim ish, boshqa branchga o'tish kerak
echo "# Yarim ish" >> README.md

git status
# modified: README.md

# Vaqtinchalik saqlash
git stash
# Saved working directory: WIP on main

git status
# nothing to commit, working tree clean

# Boshqa branchga o'tib ish qilish
git switch -c hotfix/critical
echo "BUG FIX" >> main.py
git add main.py
git commit -m "fix: critical"

# Yarim ishga qaytish
git switch main
git stash pop
# o'zgarish qaytdi:
git status
# modified: README.md

# ─────────────────────────────────────────────────────────────────────
# 2) STASH list, show, named
# ─────────────────────────────────────────────────────────────────────

# Bir nechta stash
echo "ish 1" >> a.txt
git stash push -m "WIP: yangi feature"

echo "ish 2" >> b.txt
git stash push -m "WIP: bug fix"

# Ro'yxat
git stash list
# stash@{0}: On main: WIP: bug fix
# stash@{1}: On main: WIP: yangi feature

# Mazmunni ko'rish
git stash show
git stash show -p stash@{1}    # to'liq diff

# Belgilangan stash'ni qaytarish
git stash apply stash@{1}
# (faqat apply — stash'da qoladi)

# pop — qaytarish + o'chirish
git stash pop stash@{0}

# Hammasini o'chirish
git stash clear

# ─────────────────────────────────────────────────────────────────────
# 3) STASH untracked fayllar
# ─────────────────────────────────────────────────────────────────────

# Yangi fayl yaratamiz
echo "yangi" > yangi.txt

git stash
# yangi.txt stash'ga TUSHMAYDI (untracked)

# -u flag bilan
git stash -u
# yangi.txt ham stash'ga tushdi

# Hammasini (gitignore'dagilarni ham)
# git stash -a    # xavfli, ehtiyot bo'ling

# ─────────────────────────────────────────────────────────────────────
# 4) STASH'dan branch
# ─────────────────────────────────────────────────────────────────────

# Stash'dagi ish endi to'liq feature bo'lib qoldi
echo "katta ish" >> README.md
git stash push -m "yangi katta feature"

git stash list
# stash@{0}: WIP yangi katta feature

# Bevosita branch yaratish
git stash branch feature/katta-ish stash@{0}
# yangi branchda turibsiz, stash o'chgan, o'zgartirishlar tiklangan

# ─────────────────────────────────────────────────────────────────────
# 5) CHERRY-PICK
# ─────────────────────────────────────────────────────────────────────

# Boshqa branchda commit'lar
git switch -c feature/many
echo "ish 1" > f1.py && git add . && git commit -m "feat: 1"
echo "ish 2" > f2.py && git add . && git commit -m "feat: 2"
echo "BUG FIX" > f3.py && git add . && git commit -m "fix: critical bug"
echo "ish 4" > f4.py && git add . && git commit -m "feat: 4"

git log --oneline
# d4 (HEAD -> feature/many) feat: 4
# c3 fix: critical bug
# b2 feat: 2
# a1 feat: 1

# main'ga qaytamiz va faqat fix'ni olamiz
git switch main

# c3 SHA'sini eslab qoling
git cherry-pick c3
# [main yangi_sha] fix: critical bug

git log --oneline
# yangi_sha (HEAD -> main) fix: critical bug
# ... (oldingi)

# ─────────────────────────────────────────────────────────────────────
# 6) CHERRY-PICK ko'pchilik
# ─────────────────────────────────────────────────────────────────────

# Bir nechta
git cherry-pick a1 b2 d4

# Oraliq (a'dan d'gacha — d ham kiradi)
git cherry-pick a1^..d4

# Commit qilmasdan (faqat staging'ga)
git cherry-pick -n c3

# Conflict bo'lsa
git cherry-pick c3
# CONFLICT...
# (yeching)
git add .
git cherry-pick --continue

# Yoki bekor qilish
# git cherry-pick --abort

# ─────────────────────────────────────────────────────────────────────
# 7) AMEND — xabar tuzatish
# ─────────────────────────────────────────────────────────────────────

git commit -m "fix something"

# Xabar yomon — tuzatamiz
git commit --amend -m "fix: login formada email validatsiyasi"

# Editor ochilishi (xabar batafsil yozish)
# git commit --amend

# ─────────────────────────────────────────────────────────────────────
# 8) AMEND — fayl qo'shish
# ─────────────────────────────────────────────────────────────────────

echo "yangi qator" >> main.py
git add main.py

# --no-edit — xabar o'zgarmaydi
git commit --amend --no-edit

# Yoki yangi xabar
# git commit --amend -m "feat: to'liq versiya"

git log --oneline
# (oxirgi commit yangi SHA bilan — amend yangi commit yaratdi)

# ─────────────────────────────────────────────────────────────────────
# 9) PUSH qilingan commit'ni amend (XAVFLI)
# ─────────────────────────────────────────────────────────────────────

# git commit -m "feat: X"
# git push                        # GitHub'da
# git commit --amend -m "feat: yangi xabar"
# git push                        # REJECTED — yangi SHA

# Yagona yo'l (xavfli):
# git push --force-with-lease    # boshqa eski commit'ga ustidan emas

# Faqat shaxsiy branch'da va jamoadosh bilan kelishilgan.
# Main yoki shared branch'da — ASLO.

# ─────────────────────────────────────────────────────────────────────
# 10) FOYDALI ALIAS'LAR
# ─────────────────────────────────────────────────────────────────────

git config --global alias.s "status -s"
git config --global alias.co "checkout"
git config --global alias.br "branch"
git config --global alias.cm "commit -m"
git config --global alias.amend "commit --amend --no-edit"
git config --global alias.unstage "reset HEAD --"
git config --global alias.lg "log --oneline --graph --decorate --all"
git config --global alias.last "log -1 HEAD --stat"
git config --global alias.pf "push --force-with-lease"

# Tekshirish
git config --get-regexp alias

# Endi:
git s         # status
git lg        # tarix grafigi
git amend     # oxirgi'ga add qilish
git unstage main.py

# JaskaSoska
# ════════════════════════════════════════════════════════════════════
# DARS 9: Reset, revert, reflog
# ════════════════════════════════════════════════════════════════════

cd mening-loyiham

# ─────────────────────────────────────────────────────────────────────
# 1) RESET --soft (commit ochish, fayllar saqlanadi)
# ─────────────────────────────────────────────────────────────────────

# Test commit
echo "test" > soft.txt
git add . && git commit -m "WIP yarim ish"

git log --oneline
# abc1234 WIP yarim ish
# ... (oldingi)

# Soft reset — commit ochiladi, fayllar staging'da
git reset --soft HEAD~1

git status
# Changes to be committed:
#   new file: soft.txt

# Endi yangi xabar bilan qaytadan commit
git commit -m "feat: soft.txt qo'shildi"

# ─────────────────────────────────────────────────────────────────────
# 2) RESET --mixed (default) — fayllar saqlanadi, lekin unstaged
# ─────────────────────────────────────────────────────────────────────

echo "ish 1" > a.py
git add . && git commit -m "commit 1"

echo "ish 2" > b.py
git add . && git commit -m "commit 2"

git log --oneline
# def commit 2
# abc commit 1
# ...

# Mixed reset
git reset HEAD~2          # default --mixed

git status
# Untracked: a.py, b.py
# (yoki: not staged: modified — a.py, b.py)

# Tanlab add qilish mumkin
git add a.py
git commit -m "feat: a"

git add b.py
git commit -m "feat: b"

# ─────────────────────────────────────────────────────────────────────
# 3) RESET --hard (XAVFLI — fayllarni yo'qotasiz)
# ─────────────────────────────────────────────────────────────────────

echo "muhim" > muhim.txt
git add . && git commit -m "feat: muhim"

git log --oneline | head -3

# HAMMASINI yo'qotish (oxirgi commit)
# git reset --hard HEAD~1
# (kommit + staging + working — hammasi yo'qoladi)
# muhim.txt YO'Q

# Yoki origin/main'ga moslashtirish
# git reset --hard origin/main

# Bu — eng kuchli reset. Faqat aniq xohlaganingizda ishlatish.

# ─────────────────────────────────────────────────────────────────────
# 4) REVERT — push qilingan uchun xavfsiz
# ─────────────────────────────────────────────────────────────────────

# Bad commit
echo "yomon kod" > yomon.py
git add . && git commit -m "BUG: yomon"

git log --oneline
# bad_sha BUG: yomon
# good_sha feat: ...

# Revert — yangi commit yaratadi
git revert HEAD
# Editor ochiladi, default xabar: Revert "BUG: yomon"
# Yoki:
# git revert --no-edit HEAD     # default xabar bilan

git log --oneline
# revert_sha Revert "BUG: yomon"
# bad_sha BUG: yomon
# good_sha feat: ...

# Fayl yo'q (revert bekor qildi)
ls yomon.py
# No such file

# Push xavfsiz
# git push

# ─────────────────────────────────────────────────────────────────────
# 5) REVERT bir nechta commit
# ─────────────────────────────────────────────────────────────────────

# Faraz oxirgi 3 ta yomon
git log --oneline
# c3 (HEAD) feat 3
# b2 feat 2
# a1 feat 1

# Hammasini revert (reverse order)
git revert HEAD~2..HEAD
# 3 ta yangi revert commit yaratiladi

# Yoki bir commit'ga birlashtirish
git revert --no-commit HEAD~2..HEAD
git commit -m "revert: oxirgi 3 ta yomon ish bekor qilindi"

# ─────────────────────────────────────────────────────────────────────
# 6) REFLOG — hayot saqlovchi
# ─────────────────────────────────────────────────────────────────────

# Reflog ko'rish
git reflog
# abc1234 HEAD@{0}: commit: oxirgi ish
# def5678 HEAD@{1}: reset: moving to HEAD~1
# 9012345 HEAD@{2}: commit: muhim ish    ← YO'QOLGAN
# ...

# Yo'qolgan commit'ga qaytish
git reset --hard 9012345

# Yoki yangi branch yaratish
git branch saved-work 9012345

# Filter
git reflog --since="1 hour ago"
git reflog feature/login

# ─────────────────────────────────────────────────────────────────────
# 7) Senariy: branch tasodifan o'chirildi
# ─────────────────────────────────────────────────────────────────────

git switch -c feature/important
echo "muhim ish" > muhim.py
git add . && git commit -m "feat: muhim ish"

git switch main

# Tasodifan
git branch -D feature/important
# Deleted branch feature/important (was abc1234).

# PANIKA YO'Q
git reflog
# abc1234 HEAD@{...} commit: feat: muhim ish

# Branchni qaytarish
git branch feature/important abc1234

git switch feature/important
ls muhim.py
# muhim.py — qaytdi 🎉

# ─────────────────────────────────────────────────────────────────────
# 8) Senariy: tasodifan reset --hard
# ─────────────────────────────────────────────────────────────────────

git switch main

echo "yana muhim" > yana.txt
git add . && git commit -m "feat: yana muhim"

# Tasodifan
git reset --hard HEAD~3
# 3 ta commit yo'qoldi (ko'rinishda)

ls yana.txt
# No such file

# REFLOG
git reflog
# def5678 HEAD@{0}: reset: moving to HEAD~3
# abc1234 HEAD@{1}: commit: feat: yana muhim    ← oxirgi yaxshi holat

git reset --hard HEAD@{1}
# Eski holatga
ls yana.txt
# yana.txt — qaytdi

# ─────────────────────────────────────────────────────────────────────
# 9) DETACHED HEAD
# ─────────────────────────────────────────────────────────────────────

# Belgilangan commit'ga
git checkout abc1234
# You are in 'detached HEAD' state...

# Yangi branch yaratish
git switch -c yangi-eksperiment
# Endi haqiqiy branch'da

# Yoki main'ga qaytish
git switch main

# ─────────────────────────────────────────────────────────────────────
# 10) CLEAN — kuzatilmayotgan fayllarni o'chirish
# ─────────────────────────────────────────────────────────────────────

# Yangi fayl
touch tmp1.log tmp2.tmp
mkdir bekor && touch bekor/x.txt

git status
# Untracked: tmp1.log, tmp2.tmp, bekor/

# Dry run — nimani o'chiradi
git clean -n
# Would remove tmp1.log
# Would remove tmp2.tmp
# Would remove bekor/  (faqat -d bilan)

# Papkalarni ham
git clean -nd

# Bajarish
git clean -fd
# Removed tmp1.log, tmp2.tmp, bekor/

# 📌 leviathan-ctf

## 💻 | What's leviathan ctf? | 💻
</> | This is a beginner CTF for introducing people to Reverse Engineering CTFs
</> | I enjoyed it so I wanted to make my attempt public

## ℹ️ | Infos | ℹ️
ⓘ | Link: https://overthewire.org/wargames/leviathan/
ⓘ | To start run `ssh leviathan0@leviathan.overthewire.org -p 2223`

## 📝 | Levels | 📝
This challeng is built on 7 levels.

### 👾 | Level 0 | 👾
1. This one is easy, to start you just input `leviathan0`

### 👾 | Level 0->1 | 👾
1. You need to look at hidden folders and you fille find `.backup` inside `$HOME`
2. There will be a file called `bookmark.html` and with `cat bookmark.html | grep password` you will find the passowrd for the next stage which is: `3QJ3TgzHDq`

### 👾 | Level 1->2 | 👾
1. We see that there's an executable file called `check`, let's run it with `gdb`
2. We set a break to the `main+123` just before the `strcmp` runs.
3. We inspect for %eax and find the word `sex`, let's use this as input
4. Boom, we are in a new shell, we run `whoami` and see we are as `leviathan2`, now we can simply run `cat /etc/leviathan_pass/leviathan2`
5. We find: `NsN1HwFoyN` 

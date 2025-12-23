# 📌 leviathan-ctf

## 💻 | What's leviathan ctf? | 💻
</> | This is a beginner CTF for introducing people to Reverse Engineering CTFs<br>
</> | I enjoyed it so I wanted to make my attempt public

## ℹ️ | Infos | ℹ️
ⓘ | Link: https://overthewire.org/wargames/leviathan/<br>
ⓘ | To start run `ssh leviathan0@leviathan.labs.overthewire.org -p 2223`

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

### 👾 | Level 2->3 | 👾
1. This one is a bit tricky, first we see that there's a file called `printfile` that we can execute and is owned by `leviathan3` - that's a hint!
2. We try to run `./printfile /etc/leviathan_pass/leviathan3` and we get `You can't have this file ...`, fair enough.
3. Let's run `gdb printfile` and `lay asm` so we can inspect the ASM code.
4. At some point `printfile`, if we can access the file, runs `seteuid()` so after this call we are running as `leviathan3`.
5. That's the trick, forcing to read a file after the check is made!
6. First we create a new temp folder with `mktemp -d -> /tmp/tempFolder`
7. We create a simple file `echo "test" > /tmp/tempFolder/tempFile`
8. Now we link it with `ln -s /tmp/tempFolder/tempFile /tmp/tempFolder/fileLink` and run `chmod 777 /tmp/tempFolder`.
9. Let's try `./printfile /tmp/tempFolder/fileLink` and we get `temp`!
10. The trick it's simple, we run `./printfile /tmp/tempFolder/fileLink` in a `while true` loop and while it's running we do, on another shell:
    ```
    while true do
      ln -sf /tmp/tempFolder/tempFile /tmp/tempFolder/fileLink
      ln -sf /etc/leviathan_pass/leviathan3 /tmp/tempFolder/fileLink
    done;
    ```
11. We are trying to change the link after the check is made.
12. We run `./printfile /tmp/tempFolder/fileLink` and finally, between some random prints, we get the leviathan3 pass: `f0n8h2iWLP`!

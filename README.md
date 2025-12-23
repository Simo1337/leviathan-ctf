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

### 👾 | Level 3->4 | 👾
1. As always we check the directory with `ls -alh` and we see that there's a file called `level3` which is executable and owned by `leviathan4`.
2. Let's run it, we get asked for a password, we just input something random and, ofc nothing happens.
3. Now run `objdump -d level3` and check for a `strcmp`, it's easy to find it under the `do_stuff` function.
4. We also notice that there's a call to `system()` so we will probably get a shell or run something from `/bin/`.
5. Let's run `level3` under `ltrace` and there's our secret password: `snlprintf`.
6. We input it and, as we expected, get a shell.
7. If we run `id` we can confirm `uid=leviathan4`.
8. Now we can easily run `cat /etc/leviathan_pass/leviathan4` and get: `WG1egElCvO`!

### 👾 | Level 4->5 | 👾
1. This is really straight forward.
2. We run `ls -alh` and find a hidden folder `.trash/`.
3. Inside that we see an executable called `bin`.
4. We run it and we get `00110000 01100100 01111001 01111000 01010100 00110111 01000110 00110100 01010001 01000100 00001010` which converted to ASCII/UTF-8 is: `0dyxT7F4QD`.
5. We found what we were looking for.

### 👾 | Level 5->6 | 👾
1. This time we have a file called `leviathan5` inside our `$HOME`.
2. If we run it we get `Cannot open /tmp/file.log` and it returns, well let's create that file then with `touch /tmp/file.log`
3. We run `leviathan5` again and we get nothing.
4. We try with `ltrace ./leviathan5` and we can confirm it is actually reading that file with `leviathan6` id
5. What if we link `/etc/leviathan_pass/leviathan6` to `/tmp/file.log`?
6. The answer is: we get the `leviathan6` password `szo7HDB88w`!  

### 👾 | Level 6->7 | 👾
1. In this level we need to guess a four digit number, in these cases I suggest always run the file inside `gdb`.
2. Let's look at the ASM code:
   ```
   8049212:       e8 89 fe ff ff          call   80490a0 <atoi@plt>
   8049217:       83 c4 10                add    $0x10,%esp
   804921a:       39 45 f4                cmp    %eax,-0xc(%ebp)
   804921d:       75 2b                   jne    804924a <main+0x84>
   804921f:       e8 2c fe ff ff          call   8049050 <geteuid@plt>
   8049224:       89 c3                   mov    %eax,%ebx
   8049226:       e8 25 fe ff ff          call   8049050 <geteuid@plt>
   804922b:       83 ec 08                sub    $0x8,%esp
   804922e:       53                      push   %ebx
   804922f:       50                      push   %eax
   8049230:       e8 5b fe ff ff          call   8049090 <setreuid@plt>
   8049235:       83 c4 10                add    $0x10,%esp
   8049238:       83 ec 0c                sub    $0xc,%esp
   804923b:       68 22 a0 04 08          push   $0x804a022
   8049240:       e8 2b fe ff ff          call   8049070 <system@plt>
   8049245:       83 c4 10                add    $0x10,%esp
   8049248:       eb 10                   jmp    804925a <main+0x94>
   804924a:       83 ec 0c                sub    $0xc,%esp
   804924d:       68 2a a0 04 08          push   $0x804a02a
   8049252:       e8 09 fe ff ff          call   8049060 <puts@plt>
   ```
3. We notice that our input inside `%EAX` is being compared with whatever is at the address `$EBP-0xc`.
4. We can take a look at that address inside `gdb` adding a breakpoint before the `cmp` and run `x $ebp-0xc`.
5. We then get: `7123` which is the correct guess.
6. Now we run `leviathan6` in our shell and we get a new shell under `leviathan7` id.
7. At this point, as always, we can run `cat /etc/leviathan_pass/leviathan7` and get: `qEs5Io5yM8`!

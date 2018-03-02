# searchpass
Determine if a password is among haveibeenpwned database, safely

Changing your password is a hassle. Do you really need to? One motivation is whether the bad guys definitely, already, have it among the lists of passwords they use to "brute force" a login. They keep records of all the passwords ever recovered from captured servers.

How can you check whether a password is among these known passwords without compromising the password?

Certain [good guys](https://haveibeenpwned.com/) have made this database downloadable, currently with 500 million well-known passwords. You can download that file and check your password locally. Better yet, the good guys added a clever online search technique that involves hashing a password first, and sending up only the first 5 characters of the hash to check. The server sends back a list of all the hash matches so you can easily see if there's an exact match.

Here's how to check a password online, safely, in about a minute:

Prerequisite: If necessary, [install `curl`](http://macappstore.org/curl/) on macOS. On Linux, use `apt-get` or `yum` to install `curl`.

First, bring up a new terminal window (on macOS, Applications -> Utilities -> Terminal). You will close this terminal as the last step below.

Next, enter your password into a variable. The following commands will show a password prompt and record your password without echoing on the screen:

```bash
set +x
read -s -p "Password: " PASSWORD && echo ""
```

Let's make sure the password was correctly typed:

```bash
read -s -p "Password Confirm: " pass_confirm && echo ""
```

Execute the following commands in the terminal. This can be copied entirely and pasted into the terminal window.

```bash
if [ "$PASSWORD" != "$pass_confirm" ]; then
   echo "Password confirmation didn't match; start again." && exit -1
fi

sha=$(echo -n "$PASSWORD" | shasum | tr [a-z] [A-Z] | awk '{ print $1 }')
result=$(curl -s https://api.pwnedpasswords.com/range/${sha:0:5} | grep ${sha:5})

if [ "$result" == "" ]; then
   echo "---Not found, happy :)"
else
   echo '---That password IS known; time to change it!'
fi
```

Read the output: it should be either happily not found, or "time to change".

Close the terminal window to clear the local password variables that were created above.



(This entire script is [downloadable](./showpass.sh))


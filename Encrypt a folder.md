1

[](https://superuser.com/posts/182115/timeline)

Quick and easy way is to `tar` and `compress` and then `bcrypt`.

tar cfj safe-archive.tar.bz2 Directory/ 
bcrypt safe-archive.tar.bz2 
# will ask you an 8 char password twice to lock it up.
# But, remember to delete your Directory after this,
rm -rf Directory/ 
# And, I hope you don't forget the password, or your data is gone!

Makes `safe-archive.tar.bz2.bfe` -- which you can rename if you feel paranoid about it.

To open the encrypted pack,

bcrypt safe-archive.tar.bz2.bf3 # Or, whatever you called it
tar xfj safe-archive.tar.bz2 
# And, your directory is back!

[[Encryption]]
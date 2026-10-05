# Curieux SMS

## Objective
Several researchers are suspected to have leaked classified information via text.
We have to find the leaked data in the forensic image of the text database.

## Resolution
The database is separated in three directories `1`, `2`, and `3`. We're going to
look at these directories one by one.

### First directory
The first directory simply contains a database (`mmssms.db`). When opening the
database with SQLite, we can see that it contains three tables
(`pdu_restricted`, `sms`, and `sms_restricted`).
```sql
-- Open the database
sqlite3 mmssms.db

-- List the tables
.tables
pdu_restricted  sms             sms_restricted
```

Selecting all fields from `sms` or `sms_restricted` give us the first part of the flag
```sql
select * from sms;
-- or
select * from sms_restricted;
-- gives us
`Le mot de passe pour accéder au dossier est  404CTF{m4r13_||0|1|-1|com.android.messaging|1`
```
To make sure what the first part of the flag is we simply print what's printable in
the database.
```bash
strings mmssms.db
```
This allows us to see the first part with more clarity and confirm that it is
`404CTF{m4r13_`.

### Second directory
The second directory also only contains a single database `mmssms.db` except this time
it contains more tables
```sql
.tables
android_metadata               draft_parts_view             
conversation_image_parts_view  messages                     
conversation_list_view         participants                 
conversation_participants      parts                        
conversations
```
When selecting all fields from `conversation_list_view` we collect the second part of
the flag.
```sql
select * from conversation_list_view;
`1|0698725361|1|0|1|messaging://avatar/d?i=0698725361|-2||0698725361|1778951436856|0|||||||1|1||1|0|100|0|4||0698725361|0|cur13_|`
```
Just for the previous directory, using `strings` allow us to see the second part of
the flag clearly.
```bash
strings mmssms.db
# data above
cur13_
# data under
```
We can confirm the second part of the flag is `cur13_`.

### Third directory
Things are a bit different with the third directory. Apart from containing the usual
`mmssms.db`, this directory also contains another directory named `app_parts`.

Listing the contents of `app_parts` with `tree` let's us see there quite a handful of
files here.
```bash
tree app_parts/
app_parts/

# List of all the files

1 directory, 92 files
```

We'll get back to `app_parts` later and focus on `mmssms.db` for now.
The same commands used for the two previous directories allow us to see that the last
part of the flag is contained in `pdu_restricted`.
```sql
select * from pdu_restricted;
`62|1|1774803902812|1774803902812|1|1||1898}|106|application/vnd.wap.multipart.related||604800||132|18||129|129||128||tr_ctf_001|||0|||||0|0|-1|1||0
`
```
We can confirm the last part of the flag is `1898}` using `strings`.
```bash
strings mmssms.db
# data before
..cut for clarity..1898}..cut for clarity..
# data after
```
Back to `app_parts`. Determining the file type of all these files let's us see
that only a few of them are of interest.
```bash
file *
# listing interesting files only
5:   OpenPGP Public Key
24:  gzip compressed data # cut for clarity
62:  JPEG image data # cut for clarity
```
We start by opening file `62` since it's the easiest to deal with:
```bash
open 62
```
An image opens with `r4d1um_`, thus giving us the missing part of the flag.


Upon reconstruction of it, we can confirm the flag for this challenge
is: `404CTF{m4r13_cur13_r4d1um_1898}`
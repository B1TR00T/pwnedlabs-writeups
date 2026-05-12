So i was provided with an s3 bucket publicly accessible :
![[01-s3-listing.png.png]]

i found the migration/asana-cloud-migration-backup.json file after downloading and digging in that file, i found access key and the secret access key
![[02-credentials-found.png.png]]
so i used it to access to the account linked to them:
![[03-caller-identity.png.png]]

Now using pacu and the "iam__bruteforce_permissions" module to check what permissions this account have i found that it can call some dynamodb APIS in multiple regions the regions are us-east-1 us-east-2 us-west-1 us-west-2 
![[04-pacu-enum.png.png]]
Now i use the dynamodb__enum module to check the dynamodb tables in these regions 
![[05-dynamodb-dump.png.png]]
Basically only the us-east-1 region has a dynamodb tables the others only have empty db as showen in the screentshot there 2 tables only 1 can be enumerated and downloadable the other one needs special permissions when pacu downloads something it is stored by defautl in 
/home/USERNAME/pacu/sessions/SESSIONNAME/downloads/ 
so in this path i found the downloaded file:
![[06-file-extracted.png.png]]

When i first opened the file i saw 51 users with emails first and last names roles and password hashes i made a file that has username:password format and used john to crack the password hashes 
![[07-cracked-passwords.png.png]]
I got only 18 crackes passwords out of 51
then we used a sprayer tools "GoAWSConsoleSpray" which is a very good tools that used for password spraying
Using this tool got me a valid user :
![[08-spray-success.png.png]]

I tested the connection to aws console 
![[10-breaking-into-the-account.png.png]]
As we know earlier we found 2 dynamodb tables one of them isn't accessible "user_order_logs"
![[11-checking-the-table.png.png]]
exploring the table items we find 43 item i downloaded them into csv
![[12-checking-the-table-content.png.png]]
after some search i found the flag in line 33
![[13-flag.png.png]]

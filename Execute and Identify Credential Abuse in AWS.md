So i was provided with an s3 bucket publicly accessible :
![[Pasted image 20260403211926.png]]
i found the migration/asana-cloud-migration-backup.json file after downloading and digging in that file, i found access key and the secret access key
![[Pasted image 20260403214252.png]]
so i used it to access to the account linked to them:
![[Pasted image 20260403214333.png]]

Now using pacu and the "iam__bruteforce_permissions" module to check what permissions this account have i found that it can call some dynamodb APIS in multiple regions the regions are us-east-1 us-east-2 us-west-1 us-west-2 
![[Pasted image 20260512153108.png]]
Now i use the dynamodb__enum module to check the dynamodb tables in these regions 
![[Pasted image 20260512161147.png]]
Basically only the us-east-1 region has a dynamodb tables the others only have empty db as showen in the screentshot there 2 tables only 1 can be enumerated and downloadable the other one needs special permissions when pacu downloads something it is stored by defautl in 
/home/USERNAME/pacu/sessions/SESSIONNAME/downloads/ 
so in this path i found the downloaded file:
![[Pasted image 20260512161403.png]]

When i first opened the file i saw 51 users with emails first and last names roles and password hashes i made a file that has username:password format and used john to crack the password hashes 
![[Pasted image 20260512180611.png]]
I got only 18 crackes passwords out of 51
then we used a sprayer tools "GoAWSConsoleSpray" which is a very good tools that used for password spraying
Using this tool got me a valid user :
![[Pasted image 20260512181333.png]]

I tested the connection to aws console 
![[Pasted image 20260512181512.png]]
As we know earlier we found 2 dynamodb tables one of them isn't accessible "user_order_logs"
![[Pasted image 20260512181707.png]]
exploring the table items we find 43 item i downloaded them into csv
![[Pasted image 20260512181818.png]]
after some search i found the flag in line 33
![[Pasted image 20260512181950.png]]

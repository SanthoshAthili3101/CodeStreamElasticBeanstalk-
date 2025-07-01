
✅ Full Step-by-Step Guide: Deploy .NET Web API to Elastic Beanstalk (Docker) & Clean Up
⚙️ 1. Prerequisites
✅ AWS Account (logged in)

✅ IAM user with EB/EC2/S3/IAM permissions

✅ AWS CLI installed: aws --version

✅ EB CLI installed: eb --version

✅ .NET SDK installed

✅ Docker installed and running

✅ Optional: Visual Studio or VS Code

📦 2. Create a .NET Web API Project

mkdir CodeStreamEBS
cd CodeStreamEBS
dotnet new webapi -n CodeStreamEBS
cd CodeStreamEBS
Test it locally:


dotnet run
Check http://localhost:5000/weatherforecast

🐳 3. Add a Dockerfile
In the root of the project (CodeStreamEBS/), create a file named Dockerfile:

dockerfile
Copy
Edit
# Build stage
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /app
COPY *.csproj .
RUN dotnet restore
COPY . .
RUN dotnet publish -c Release -o out

# Runtime stage
FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /app
COPY --from=build /app/out .
EXPOSE 80
ENTRYPOINT ["dotnet", "CodeStreamEBS.dll"]
⚠️ Replace CodeStreamEBS.dll with your actual DLL name (match .csproj).

🛑 4. Add .ebignore to Avoid Permission Errors
Create .ebignore in your root folder with this content:

pgsql
Copy
Edit
.vs/
bin/
obj/
*.user
*.suo
*.log
*.db
docker-compose*
.git/
Dockerfile.debug
🌍 5. Initialize EB CLI
From your project folder (CodeStreamEBS/):


eb init -p docker codestreamebs --region ap-south-1
Choose:

AWS access keys if prompted

Region: ap-south-1 (Mumbai)

Default environment: leave blank for now

🚀 6. Create and Launch the Environment

eb create codestreamebs-env

Application available at http://codestreamebs-env.eba-xxxx.ap-south-1.elasticbeanstalk.com
🌐 7. Test the API
Open in browser:
eb open
Or go directly to:

http://codestreamebs-env.eba-xxxx.ap-south-1.elasticbeanstalk.com/weatherforecast
🔁 8. Update Code & Redeploy (e.g., Add Controller)
Add a new controller in your project.

Save changes.

Redeploy:

eb deploy
✅ Done!

🧪 9. Optional: View Logs
bash
Copy
Edit
eb logs
🛑 10. Clean Up After Testing (to Avoid Charges)
✅ Terminate Environment
bash
Copy
Edit
eb terminate codestreamebs-env
Wait until it confirms termination.

✅ (Optional) Delete the EB Application
If you're fully done with this app:

bash
Copy
Edit
aws elasticbeanstalk delete-application --application-name codestreamebs --terminate-env-by-force --region ap-south-1
You can also delete it from the EB Console.

✅ (Optional) Manually Delete S3 Bucket
Go to S3 console → find bucket like:

Copy
Edit
elasticbeanstalk-ap-south-1-123456789012
Delete to free up storage.

💰 Billing Notes
EC2 instance (t2.micro): ~₹1.0/hour

Load balancer: ~₹1.0/hour

S3/app versions: negligible unless huge

💸 If you delete within an hour, expect ~₹2–₹3 max

✅ Summary of Key Commands
dotnet new webapi -n CodeStreamEBS
eb init -p docker codestreamebs --region ap-south-1
eb create codestreamebs-env
eb open
eb deploy
eb logs
eb terminate codestreamebs-env
aws elasticbeanstalk delete-application --application-name codestreamebs --terminate-env-by-force


-------------------------------------------------------------------------------------------

sometimes permission denied while creating the ebs, then remove the vs folder in the root and then run these two commands
1-Remove-Item -Recurse -Force .\.vs
2-Get-ChildItem -Force . | Where-Object { $_.Name -eq ".vs" }

# Feladat (Dockerfájl készítés)  

## 1. MySQL
Készíts dockerfájlt ami létrehozz egy MySQL konténert az alábbi adatbázissal feltöltve.  
https://github.com/harryho/db-samples  
Az adatbázis tartalma akkor is megmaradjon ha leállítjuk vagy töröljük a konténert.

---  

### 2. MSSQL (ha van hozzá kedved)

Készíts dockerfájlt ami létrehozz egy MSSQL konténert az alábbi adatbázissal feltöltve.  
MSSQL konténer példaadatbázissal:   
```powershell

https://github.com/wsargent/docker-cheat-sheet

MSSQL container

New-Item -Name sql1data -ItemType Directory
Set-Location sql1data

docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=Password123!" `
   --name "sql1" -p 1433:1433 `
   -v sql1data:/var/opt/mssql `
   -d mcr.microsoft.com/mssql/server:2019-latest

# Backup dir létrehozás
docker exec -it sql1 mkdir /var/opt/mssql/backup   

#AW2019 letöltés git-ről az aktuális könyvtáraba 
# már letöltöttem az sql1data könyvtárba, még 1x nem fogom
# curl -OutFile "adventureworks2019.bak" "https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak"

# Bemásolás a containerbe az aktuális könytárból
docker cp "adventureworks2019.bak" sql1:/var/opt/mssql/backup

# Adatbázis visszaállítása
docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd `
   -S localhost -U SA -P "Password123!" `
   -Q "RESTORE DATABASE AdventureWorks2019 FROM DISK = '/var/opt/mssql/backup/adventureworks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf' "
# docker logs sql1

# Ellenőrzés
docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd `
   -S localhost -U SA -P "Password123!" `
   -Q "SELECT Name FROM sys.Databases"

# Egyéb parancsok amiket közben használtam:   

# docker pull mcr.microsoft.com/mssql/server
# docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=yourStrong(!)Password" -p 1433:1433 -d mcr.microsoft.com/mssql/server:2019-latest
<#
# Change SA password   
docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd `
   -S localhost -U SA -P "<YourStrong!Passw0rd>" `
   -Q "ALTER LOGIN SA WITH PASSWORD='<YourNewStrong!Passw0rd>'"   

Filenév ellenőrzés
docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd -S localhost `
   -U SA -P "Password123!" `
   -Q "RESTORE FILELISTONLY FROM DISK = '/var/opt/mssql/backup/adventureworks2019.bak'"   
#>   
```
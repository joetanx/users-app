## 1. Prepare Databases

### 1.1. MySQL

```
yum -y install mysql-server
systemctl enable --now mysqld
firewall-cmd --add-service mysql --permanent && firewall-cmd --reload
curl -sLo /tmp/users-my.sql https://github.com/joetanx/users-app/raw/main/users-my.sql
mysql -u root < /tmp/users-my.sql
mysql -u root -e "SELECT id,firstName,lastName,email,mobile FROM users.users ORDER BY RAND() LIMIT 0,1;"
mysql -u root -e "SELECT id,firstName,lastName,email,mobile FROM users.users WHERE firstName LIKE '%jack%';"
rm -f /tmp/users-my.sql
```

### 1.2. PostgreSQL

```
yum -y install postgresql-server postgresql-contrib
postgresql-setup --initdb
systemctl enable --now postgresql
firewall-cmd --add-service postgresql --permanent && firewall-cmd --reload
curl -sLo /tmp/users-pg.sql https://github.com/joetanx/users-app/raw/main/users-pg.sql
sudo -u postgres psql -d postgres -f /tmp/users-pg.sql
sudo -u postgres psql -d users -c "SELECT id,firstName,lastName,email,mobile FROM users ORDER BY RANDOM() LIMIT 1 OFFSET 0;"
sudo -u postgres psql -d users -c "SELECT id,firstName,lastName,email,mobile FROM users WHERE LOWER(firstName) LIKE '%jack%';"
```

<details><summary>Manually create database, table and insert data</summary>

```
echo "SELECT 'CREATE DATABASE users' WHERE NOT EXISTS (SELECT FROM pg_database WHERE datname = 'users')\gexec" | sudo -u postgres psql -d postgres
sudo -u postgres psql -d users -c "
CREATE TABLE IF NOT EXISTS users (
  id SERIAL PRIMARY KEY,
  firstName VARCHAR(128) NOT NULL DEFAULT '',
  lastName VARCHAR(128) NOT NULL DEFAULT '',
  email VARCHAR(128) NOT NULL DEFAULT '',
  mobile VARCHAR(128) NOT NULL DEFAULT ''
);"
sudo -u postgres psql -d users -c "INSERT INTO users VALUES (DEFAULT,'Liam','Johnson','liam.johnson@example.com','+6584582486');"
⋮
```

</details>

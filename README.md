# snippetbox
Following AlexEdwards letsgo book to learn about golang webapi

Todo: 
- Prepopulate MYSQL database when creating a docker container
- Get (Gateway) IP from container, set it as ENV, and pass it onto GO app. 

### 
https://www.alexedwards.net/blog/disable-http-fileserver-directory-listings
Page 55: 
features of ServeFile


###
Concurrent, blazing fast, but do look out for race-conditions
https://www.alexedwards.net/blog/understanding-mutexes




### setup database for CHAPTER 4
```bash
docker run -p 3306:3306 --name snippetbox-mysql -e MYSQL_ROOT_PASSWORD=password -d mysql

docker inspect snippetbox-mysql -f "{{json .NetworkSettings.Networks.bridge.Gateway }}"

mysql -uroot -ppassword

CREATE DATABASE snippetbox CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE snippetbox;

CREATE TABLE snippets (
id INTEGER NOT NULL PRIMARY KEY AUTO_INCREMENT, title VARCHAR(100) NOT NULL,
content TEXT NOT NULL,
created DATETIME NOT NULL,
expires DATETIME NOT NULL
);

CREATE INDEX idx_snippets_created ON snippets(created);

INSERT INTO snippets (title, content, created, expires) VALUES (
'An old silent pond',
'An old silent pond...\nA frog jumps into the pond,\nsplash! Silence again.\n\n– Matsuo Bashō', UTC_TIMESTAMP(),
DATE_ADD(UTC_TIMESTAMP(), INTERVAL 365 DAY)
);
INSERT INTO snippets (title, content, created, expires) VALUES (
'Over the wintry forest',
'Over the wintry\nforest, winds howl in rage\nwith no leaves to blow.\n\n– Natsume Soseki', UTC_TIMESTAMP(),
DATE_ADD(UTC_TIMESTAMP(), INTERVAL 365 DAY)
);
INSERT INTO snippets (title, content, created, expires) VALUES (
'First autumn morning',
'First autumn morning\nthe mirror I stare into\nshows my father''s face.\n\n– Murakami Kijo', UTC_TIMESTAMP(),
DATE_ADD(UTC_TIMESTAMP(), INTERVAL 7 DAY)
);

CREATE USER 'web'@'172.17.0.1';
#  GRANT SELECT, INSERT, UPDATE ON snippetbox.* TO 'web'@'localhost'; 
GRANT SELECT, INSERT, UPDATE ON snippetbox.* TO 'web'@'172.17.0.1';

# -- Important: Make sure to swap 'pass' with a password of your own choosing.
ALTER USER 'web'@'172.17.0.1' IDENTIFIED BY 'pass';

# TEST: 
docker exec -it snippetbox-mysql bash
mysql -D snippetbox -u web -ppass
SELECT id, title, expires FROM snippets;
```

### GO
set ip in DSN! 

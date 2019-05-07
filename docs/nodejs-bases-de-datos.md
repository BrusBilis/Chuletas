
# NODEJS para BASES DE DATOS

---


## MYSQL 5.7.X

### Install

* **install**

```ssh
wget from here => `https://dev.mysql.com/downloads/repo/apt/` 
dpkg -i packake.deb
nano /etc/apt/sources.list.d/mysql.list 
apt-get update
apt-get install mysql-server
mysql_secure_installation
```

* **configuration**

```ssh
nano /etc/mysql/my.cnf
// redirects to ...
nano /etc/mysql/mysql.conf.d/mysqld.cnf
// Uncomment this line to avoid external access
bind-address            = 127.0.0.1
service mysql restart

// to create other users , we use % instead localhost to allow 
// external access
mysql -u root -pPasswordYouDesire
mysql>CREATE USER 'username'@'%' IDENTIFIED BY 'password';
mysql>GRANT ALL ON databaseName.* TO 'username'@'%';
mysql> FLUSH PRIVILEGES;
```

* **truncate table with fk**
```
SET FOREIGN_KEY_CHECKS = 0; 
TRUNCATE table table_name; 
SET FOREIGN_KEY_CHECKS = 1;
```

---

# MYSQL 8.0.X

```
MySQL 8 uses a new authentication based on improved SHA256-based            
 │ password methods. It is recommended that all new MySQL Server               
 │ installations use this method going forward. This new authentication        
 │ plugin requires new versions of connectors and clients, with support for    
 │ this new 8 authentication (caching_sha2_password). Currently MySQL 8        
 │ Connectors and community drivers built with libmysqlclient21 support        
 │ this new method. Clients built with older versions of libmysqlclient may    
 │ not be able to connect to the new server.                                   
 │                                                                             
 │ To retain compatibility with older client software, the default             
 │ authentication plugin can be set to the legacy value                        
 │ (mysql_native_password) This should only be done if required third-party    
 │ software has not been updated to work with the new authentication           
 │ method. The change will be written to the file                    
 │ /etc/mysql/mysql.conf.d/default-auth-override.cnf                           
 │                                                                             
 │ After installation, the default can be changed by setting the               
 │ default_authentication_plugin server setting.   
```


* **install**

```ssh
wget from here => `https://dev.mysql.com/downloads/repo/apt/` 
dpkg -i packake.deb
nano /etc/apt/sources.list.d/mysql.list 
apt-get update
apt-get install mysql-server
mysql_secure_installation
```

* **configuration**

```ssh
nano /etc/mysql/my.cnf
// redirects to ...
nano /etc/mysql/mysql.conf.d/mysqld.cnf
bind-address = 0.0.0.0 // allow remote access
service mysql restart

// to create other users , we use % instead localhost to allow 
// external access
mysql -u root -pPasswordYouDesire
mysql>CREATE USER 'username'@'%' IDENTIFIED BY 'password';
mysql>GRANT ALL ON databaseName.* TO 'username'@'%';
mysql> FLUSH PRIVILEGES;
```

```
// turn off mysql password validation
UNINSTALL COMPONENT 'file://component_validate_password';
```

---

## Nodejs mysql

[2.16.0 https://github.com/mysqljs/mysql](https://github.com/mysqljs/mysql)

`npm install --save mysql`

```js
const mysql = require('mysql');
const con = mysql.createConnection({
  host     : 'localhost' || process.env.HOST,
  user     : 'username' || process.env.USER,
  password : 'password' || process.env.PASSWORD,
  database : 'databaseName' || process.env.DB
});
```
#### test

```js
function testDB () {
  console.log('Connecting ......');
  // console.log(con)
  con.connect(function (err) {
    if (err) {
      console.log('Error connecting to DB => ', err);
    } else {
      console.log('Connection OK');
    }
  });
}
```

#### connection config

```js
const TABLE = process.env.TABLE;
const TABLE2 = process.env.TABLE2;

const mysql = require('mysql');
const con = mysql.createConnection({
  host: process.env.HOST,
  user: process.env.MYSQLUSER,
  password: process.env.PASSWORD,
  database: process.env.DB,
  connectTimeout: 20000, // avoid ETIMEDOUT
  acquireTimeout: 20000 // avoid ETIMEDOUT
});
```

#### select

```js
function loadIP () {
  let sql = 'SELECT INET6_NTOA(ip) AS ip FROM ??;';
  const inserts = [TABLE];
  sql = mysql.format(sql, inserts);
  con.query(sql, function (err, rows) {
    if (err) {
      console.log('ERR => ', err);
    }
    console.log(rows[1].ip);
  });
}
```

```js

```

#### insert

```js
function insertHit (d, id, callback) {
  let sql = 'INSERT INTO ?? (ip, time) VALUE (INET6_ATON(?), ?);';
  const inserts = [TABLE, d.ip, d.time];
  sql = mysql.format(sql, inserts);
  con.query(sql, function (err, rows) {
    if (err) {
      console.log('Insert HIT error =>', err);
    // throw err
    } else {
      // console.log(rows)
      callback();
    }
  });
}
```

```js

```

#### update

```js
function updateDB (dbData) {
  let sql = 'UPDATE bandwith ';
  sql += 'SET data = ? ';
  sql += 'WHERE server = ?';
  const inserts = [dbData.data, dbData.server];
  sql = mysql.format(sql, inserts);
  con.query(sql, function (err) {
    if (err) {
      throw err;
    } else {
      con.end(function () {
        console.log('Update OK');
        return;
      });
    }
  });
}
```

```js
function insertHit (data) {
  let sql = 'INSERT INTO ?? (time, alexa, loc, stars, proxy, headers, 
  weather)';
  sql += ' VALUES (?, 0, 0, 0, 0, 0, 0)';
  sql += ` ON DUPLICATE KEY UPDATE ${data.service} = ${data.service} + 1;`;
  const inserts = [MY_TABLE, data.time]; // , data.service, data.service]
  sql = mysql.format(sql, inserts);
  con.query(sql, function (err, rows) {
    if (err) {
      console.log('Insert HIT error =>', err);
    // throw err
    } else {
      // console.log(rows)
    }
  });
}
```

### cascade

```js
function dailyUpdate () {
  const today = new Date();
  const yesterday = lib.addDays(today, -1).toISOString().split('T')[0];
  console.log('yesterday', yesterday);
  let sql = 'SELECT count(*) as hits FROM ?? WHERE time=?';
  const inserts = [TABLE2, yesterday];
  sql = mysql.format(sql, inserts);
  con.query(sql, function (err, rows) {
    if (err) {
      console.log('ERR => ', err);
      return;
    }
    console.log(rows[0].hits);
    // UPDATE
    let sql2 = 'INSERT INTO ?? (day, day_hits) VALUE (?, ?);';
    const inserts2 = [TABLE1, yesterday, rows[0].hits];
    sql2 = mysql.format(sql2, inserts2);
    con.query(sql2, function (err, rows) {
      if (err) {
        console.log('Select origin2 ERROR =>', err);
        throw err;
      } else {
        console.log('UPDATED ', TABLE2);
        // DELETE YESTERDAY HITS
        let sql3 = 'delete FROM ?? where time=?;';
        const inserts3 = [TABLE2, yesterday];
        sql3 = mysql.format(sql3, inserts3);
        con.query(sql3, function (err, rows) {
          if (err) {
            console.log('Delete yesterday hits ERROR =>', err);
            throw err;
          } else {
            console.log('UPDATED ', TABLE2);
            con.end(function () {});
          }
        });
      }
    });
  });
}
```

#### example

```js
// tested with mysql driver 2.12.0
const db = {
  getData: function (req, res , callback) {
    const sql = 'SELECT id, nombre FROM ??';
    const inserts = [tableName];
    sql = mysql.format(sql, inserts);
    con.query(sql, function (err, rows) {
      if (err) {
        throw err;
      } else {
        callback(rows);
      }
    });
  },
  getDataJoin: function (req, res, callback) {
    var sql = 'SELECT puesto, bidonlote, bidonorden, adhesivos.nombre AS
    adhesivo, oentrada,  operarios.nombre AS operario, fentrada ';
    sql += 'FROM registros ';
    sql += 'JOIN adhesivos ON registros.adhesivo = adhesivos.id ';
    sql += 'JOIN operarios ON registros.oentrada = operarios.id ';
    sql += 'WHERE fsalida IS NULL ';
    sql += 'ORDER BY puesto ASC;';
    var inserts = [];
    sql = mysql.format(sql, inserts);
    con.query(sql, function (err, rows) {
      if (err) {
        throw err;
      } else {
        callback(res);
      }
    });
  },
  insertData: function (req, res, callback) {
    const sql = 'INSERT INTO table (value1, value2) VALUES (?, ?)';
    const inserts = [req.body.value1, req.body.value2];
    sql = mysql.format(sql, inserts);
    con.query(sql, function (err) {
      if (err) {
        throw err;
      } else {
        console.log('Insert OK');
      }
    });
  },
  updateData: function (req, res, callback) {
    const sql = 'UPDATE records ';
    sql += 'SET field1 = ?, field2 = ? ';
    sql += 'WHERE field1 IS NULL AND field2 IS NULL AND field3 = ?';
    const inserts = [req.body.field1, new Date(), req.body.field3];
    sql = mysql.format(sql, inserts);
    con.query(sql, function (err) {
      if (err) {
        throw err;
      } else {
        console.log('Update OK');
      }
    });
  },
  testConnection: function (req, res, callback) {
    console.log('Connecting ......')
    con.connect(function (err) {
      if (err) {
        console.log('Error connecting to DB')
        res.status = false;
      } else {
        console.log('Connection OK')
        res.status = true;
      }
      con.end(function () {});
      callback(res);
    });
  }
};
```

`db.testConnection` - when is several times executed, once is ok once is error and so on. Is related about asynchonous. If you dont use connect neither end, only query works well. 

---

## REDIS 4.0.X

### Install

```ssh
apt -t stretch-backports install "redis-server"
service redis status
```

### Config


```ssh
nano /etc/redis/redis.conf
appendonly yes
appendfsync everysec
service redis restart
```

### node redis

```ssh
npm install redis
```

##### test

```js
const redis = require('redis');
// default redis.createClient() will use 127.0.0.1 and port 6379
const client = redis.createClient();
// custom
const client = redis.createClient(port, host);

client.on('connect', function() {
    console.log('Redis OK');
});

client.on('error', function (err) {
    console.log('Error => ' + err);
});
```

---

## MONGODB 3.6.X

### Install

```ssh
apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv
2930ADAE8CAF5059EE73BB4B58712A2291FA4AD5
nano /etc/apt/sources.list // strectch repo not yet
deb http://repo.mongodb.org/apt/debian jessie/mongodb-org/3.6 main
apt-get update
apt-get install mongodb-org
```

[create systemd service](https://jolav.me/notes/debian/#systemd)
```
[Unit]
Description=High-performance, schema-free document-oriented database
After=network.target
Documentation=https://docs.mongodb.org/manual

[Service]
Restart=on-failure
User=mongodb
Group=mongodb
ExecStart=/usr/bin/mongod --quiet --auth --config /etc/mongod.conf

[Install]
WantedBy=multi-user.target
```

* **Uninstall**

```ssh
service mongod stop
apt-get purge mongodb-org*
rm -r /var/log/mongodb
rm -r /var/lib/mongodb
```

### Config

```ssh
nano /etc/mongod.conf
bindIp : X.X.X.X
// 127.0.0.1 only localhost 
// 0.0.0.0 any Ipv4 allowed
```

* **create users**
```ssh
$ mongo
> use admin
> db.createUser({user:"adminUser",pwd:"password"
>                   ,roles:[{role:"root",db:"admin"}]})

$ mongo -u 'adminUser' -p 'password' --authenticationDatabase 'admin'
> use some_db
> db.createUser({user: "username",pwd: "password",roles: ["readWrite"]})
```

### node-mongodb-native

[3.0.1 - https://github.com/mongodb/node-mongodb-native](https://github.com/mongodb/node-mongodb-native)

`npm install --save mongodb`

```js
const mongo = require('mongodb');
connection="mongodb://user:password@database.host.com:port/database"
```

#### test

```js
// try - catch doesnt work because of asynchronous
function testDB () {
  mongo.connect(connection, function (err, db) {
    if (err) return console.log(err);
    console.log('Connected to database ... OK');
    db.close();
  });
}
```

#### connection config

```js
let c = {
  connection: process.env.DB_URI,
  dbname: process.env.DB_NAME,
  collectionName: collectioName
};
```

#### find

* **findOne**

```js
mongo.connect(connection, function (err, db) {
  if (err) throw err;
  const database = db.db('database');
  const collection = database.collection('collection');
  collection.findOne({'field': value}, function (err, result) {
  const id = mongo.ObjectID(id);
  collection.findOne({ '_id': id }, function (err, result) {
    if (err) throw err;
    db.close();
    cb(result);
  });
});
```

```js
function getBook (c, bookid, cb) {
  const target = c.collectionName;
  mongo.connect(c.connection, function (err, db) {
    if (err) throw err;
    const database = db.db(c.dbname);
    const collection = database.collection(target);
    collection.findOne({ '_id': parseInt(bookid) }, 
    function (err, result) {
      if (err) throw err;
      db.close();
      if (!result) {
        const err = {
          message: 'no book exists',
          status: 400
        };
        return cb(err);
      }
      cb(result);
    });
  });
}

```

* **find**

```js
mongo.connect(connection, function (err, db) {
  if (err) throw err;
  const database = db.db('database');
  const collection = database.collection('collection');
  collection.find({ 'field': value }, {}).next(function (err, result) {
    if (err) throw err;
    db.close();
    cb(result);
  });
});

mongo.connect(connection, function (err, db) {
  if (err) throw err;
  const database = db.db('database');
  const collection = database.collection('collection');
  collection.find({}, {projection: {'_id': 0}}
  .toArray(function (err, result) {
    if (err) throw err;
    db.close();
    cb(result);
  });
});

// eq => equal
// gte => greater or equal
// gt => greater
// in => in array 
// lt => less than
// lte => less or equal
// ne => not equal
// nin => not in array
mongo.connect(connection, function (err, db) {
  if (err) throw err;
  const database = db.db('database');
  const collection = database.collection('collection');
  collection.find({ 'time': { '$gte': new Date(otherDate) } },
  {projection: {'_id': 0}})
    .toArray(function (err, result) {
      if (err) throw err;
      db.close();
      cb(result);  
    });
});


const search = {
  user: req.query.user,
  from: req.query.from,
  to: req.query.to,
  limit: req.query.limit || 0
};
function searchExercises (c, s, cb) {
  const target = c.collectionName;
  mongo.connect(c.connection, function (err, db) {
    if (err) return console.log(err);
    const database = db.db(c.dbname);
    const collection = database.collection(target);
    let option1 = { 'user': s.user };
    let option2 = {
      'user': s.user,
      'date': {
        '$gte': s.from,
        '$lte': s.to
      }
    };
    let search = option1;
    if (s.from) {
      search = option2;
    }
    let limit = parseInt(s.limit);
    if (isNaN(limit) || limit < 1) limit = 100;
    collection.find(search, {projection: {'_id': 0}}).limit(limit)
    toArray(function (err, result) {
      if (err) return console.log(err);
      db.close();
      cb(result);
    });
  });
}
```

#### insert 

* **insert**

```js
mongo.connect(connection, function (err, db) {
  if (err) throw err;
  const database = db.db('database');
  const collection = database.collection('collection');
  collection.insert(input, function (err, result) {
    if (err) throw err;
    db.close();
  });
});
```

```js
mongo.connect(connection, function (err, db) {
  if (err) throw err;
  const database = db.db('database')
  const collection = database.collection('collection')
  collection.insertMany(docs, function (err, result) {
    if (err) {
      console.log('ERR =>', err);
      throw err;
    }
    console.log('Inserted = ', result.result.n, ' - ', result.ops.length);
  });
});
```

#### delete

* **deleteOne**

```js
mongo.connect(connection, function (err, db) {
  if (err) throw err;
  const database = db.db('database');
  const collection = database.collection('collection');
  collection.deleteOne({'field': value}, function (err, result) {
    if (err) throw err;
      db.close();
  });
});
```

* **deleteMany**

```js
function deleteAll (c, cb) {
  const msg = {
    message: undefined,
    status: 200
  };
  const target = c.collectionName;
  mongo.connect(c.connection, function (err, db) {
    if (err) throw err;
    const database = db.db(c.dbname);
    const collection = database.collection(target);
    collection.deleteMany({ '_id': {$gt: 0}}, function (err, result) {
      if (err) throw err;
      db.close();
      cb(msg);
    });
  });
}
```

#### update

* **updateOne**

```js
mongo.connect(connection, function (err, db) {
  if (err) throw err;
  const database = db.db('database');
  const collection = database.collection('collection');  
  collection.updateOne({ 'field':value }, 
    { $set: { 'field': newValue}}, // changes field to newValue 
    { $push: { 'array': newElement } }, // adds newElement to array
    function (err, res) {
      if (err) throw err;
      db.close();
  });
});
```

```js
function saveComment (c, bookid, comment, cb) {
  const target = c.collectionName;
  mongo.connect(c.connection, function (err, db) {
    if (err) return console.log(err);
    const database = db.db(c.dbname);
    const collection = database.collection(target);
    collection.updateOne({'_id': parseInt(bookid)},
      {
        $push: { 'comments': comment },
        $inc: { 'commentCount': 1 }
      },
      function (err, result) {
        if (err) return console.log(err);
        console.log('RESULT=>', result.ok);
        db.close();
        const msg = {
          message: undefined,
          status: 200
        };
        cb(msg);
      });
  });
}
```

* **update many**

```js
mongo.connect(connection, function (err, db) {
  if (err) throw err;
  const database = db.db('database');
  const collection = database.collection('collection');
  collection.updateMany({}, {$set: {time: Date.now() / 1000}},
    function (err, hits) {
      if (err) throw err;
      db.close();
    });
});
```

* **find and update**

```js
// change all records in a collection
mongo.connect(connection, function (err, db) {
  if (err) throw err;
  let cont = 0;
  const database = db.db('database');
  const collection = database.collection('collection');
  collection.find({}).forEach(function (doc) {
    if (err) throw err;
    const newTime = new Date(doc.time).toISOString().split('T')[0]
    collection.updateOne({'_id': doc._id}, { $set: { time: newTime } })
    cont++
    console.log(cont)
  }, function () {
    db.close();
  }););
});
```

#### create collection

```js
function createCollection (c, cb) {
  const target = c.collectionName;
  mongo.connect(c.connection, function (err, db) {
    if (err) return console.log(err);
    const database = db.db(c.dbname);
    database.createCollection(target, function (err, created) {
      if (err) return console.log(err);
      if (created) console.log('Collection ', c.collectionName, 
            ' created');
      db.close();
      return cb();
    });
  });
}
```

#### drop collection

```js
function dropCollection (c, cb) {
  const target = c.collectionName;
  mongo.connect(c.connection, function (err, db) {
    if (err) return console.log(err);
    const database = db.db(c.dbname);
    database.dropCollection(target, function (err, isDrop) {
      if (err) return console.log(err);
      if (isDrop) console.log('Collection ', c.collectionName, 
              ' deleted');
      db.close();
      return cb();
    });
  });
}
```

#### autoincremental _id

```js
// collection counters
{
    "_id": "library",
    "sequence_value": 0
}
function saveBook (c, title, cb) {
  const target = c.collectionName;
  getNextSequenceValue(c, 'library', function (nextID) {
    mongo.connect(c.connection, function (err, db) {
      if (err) return console.log(err);
      const database = db.db(c.dbname);
      const collection = database.collection(target);
      const book = {
        '_id': nextID,
        'title': title,
        'comments': [],
        'commentCount': 0
      };
      collection.insert(book, function (err, result) {
        if (err) return console.log(err);
        db.close();
        const msg = {
          message: undefined,
          status: 200
        };
        cb(msg);
      });
    });
  });
}
function getNextSequenceValue (c, sequenceName, cb) {
  mongo.connect(c.connection, function (err, db) {
    if (err) return console.log(err);
    const database = db.db(c.dbname);
    const collection = database.collection('counters');
    collection.findAndModify(
      { '_id': sequenceName },
      [], // sort
      { '$inc': { sequence_value: 1 } },
      { new: true },
      function (err, doc) {
        return cb(doc.value.sequence_value);
      });
  });
}
```

### Backup

* **mongoexport**

```ssh
mongoexport --db dbname --collection collectioname --out output.json 
-u 'adminuser' -p 'password' --authenticationDatabase 'admin'
```

---
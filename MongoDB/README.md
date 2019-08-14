# MongoDB

## Установка

Установить MongoDB просто. На Windows она ставится как обычная программа.

Для старых версий MongoDB вам может понадобиться скрипт.

```bash
C:\mongodb\bin> mongod --directoryperdb --dbpath C:\mongodb\data\db --logpath C:\mongodb\log\mongo.log --logappend --rest --install
```

`directoryperdb` - отдельная папка под отдельную базу.<br>
`dbpath` - путь к базам.<br>
`logpath` - путь к логу.<br>

Чтобы проверить что MongoDB работает.

На **Windows** нужно запустить `mongo.exe` в папке установки.

После запуска нужно ввести.

```bash
show databases
```

## Подключение к базе

До того как начать работать нужно инициализировать проект `npm init`.

Пример подключения через БД с помощью mongoose.

```javascript
const mongoose = require('mongoose')

mongoose.Promise = global.Promise

mongoose.connect('mongodb://localhost/youtube-video', {
  useMongoClient: true
})
  .then(() => console.log('MongoDB has started ...'))
  .catch(e => console.log(e))
```

## Создание схемы

## Сохранение модели

## Создание базы данных

## Основные команды

Чтобы работать с MongoDB можно использовать CLI или MongoDB Compass.

```bash
# Создать базу данных
use new_database
# Проверить текущую базу данных
db
# Показать все базы данных
show dbs
# Но чтобы сохранить базу нужно её заполнить
db.new_collection.insert({ some_key: "some_value" })
# Создать нового пользователя с разрешениями записи
db.createUser(
  {
    user: "new_user",
    pwd: "some_password",
    roles: [ { role: "readWrite", db: "new_database" } ]
  }
)
# Поиск всех
db.new_collection.find()
# Показать коллекции
show collections
# Получение статистики
db.stats()
```


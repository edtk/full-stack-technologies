## Вступление

В React передача состояния организована таким путем что дети сообщают родителям о изменении состояния.

В Facebook решили просто взять и отделить состояние от пользовательского интерфейса.

Методология Flux состоит из трёх частей:

1. View - вид нашего приложения (пользовательский интерфейс) (компоненты).
2. Store - хранилище в котором храниться состояние нашего приложения.
3. Dispatcher - сообщает хранилищу о каком-то событию и передает в него необходимую информацию.
4. Action - событие которое происходит в приложении.

... => View => Action => Dispatcher => Store => ...

## Написание своей реализации Redux: Reducer

```javascript
let state = 0;

// Только эта функция будет иметь возможность менять состояние
function updateState (state, action) {
  
  if (action === 'INCREMENT') {
    return state + 1;
  } else if (action === 'DECREMENT') {
    return state - 1;
  } else {
    return state;
  }

}

state = updateState (state, 'INCREMENT')
console.log(state);

state = updateState (state, 'DECREMENT')
console.log(state);

state = updateState (state, 'NOTHING')
console.log(state);

// Эти функции не обновляют состояния, а возращают новые
// reducer - преобразователь

```

## Actions (действия)

```javascript
let state = 0;

// Только эта функция будет иметь возможность менять состояние
function updateState (state, action) {
  
  if (action.type === 'INCREMENT') {
    return state + action.amount;
  } else if (action.type === 'DECREMENT') {
    return state - action.amount;
  } else {
    return state;
  }

}

const incrementAction = {type: 'INCREMENT', amount: 5};
const decrementAction = {type: 'DECREMENT', amount: 3};

// В отличии от предидущего пункта тут мы будем передавать объект
state = updateState (state, incrementAction)
console.log(state);

state = updateState (state, decrementAction)
console.log(state);

state = updateState (state, {})
console.log(state);

// Эти функции не обновляют состояния, а возращают новые
// reducer - преобразователь

```

## Store (хранилище)

```javascript

// Только эта функция будет иметь возможность менять состояние
function updateState (state, action) {
  
  if (action.type === 'INCREMENT') {
    return state + action.amount;
  } else if (action.type === 'DECREMENT') {
    return state - action.amount;
  } else {
    return state;
  }

}

class Store {
  constructor (updateState,state) {
    // Названия личных свойств в JS пишут с нижнего подчеркивания (хорошая практика)
    this._updateState = updateState;
    this._state = state;
  }

  get state () {
    return this._state;
  }
  // Смысл зранилища не в том чтобы спрятать состояние от пользователя, а огнаничить влияние на него из вне
  update (action) {
    this._state = this._updateState(this._action, action)
  }
}

const store = new Store(updateState, 0);

const incrementAction = {type: 'INCREMENT', amount: 5};
const decrementAction = {type: 'DECREMENT', amount: 3};

// В отличии от предидущего пункта тут мы будем state & action
store.updateState (incrementAction)
console.log(store.state);

store.updateState (decrementAction)
console.log(store.state);

store.updateState ({})
console.log(store.state);

// Эти функции не обновляют состояния, а возращают новые
// reducer - преобразователь

```

## Подписка

Чтобы реализовать это будетиспользован паттерн наблюдатель.

```javascript

// Только эта функция будет иметь возможность менять состояние
function updateState (state, action) {
  
  if (action.type === 'INCREMENT') {
    return state + action.amount;
  } else if (action.type === 'DECREMENT') {
    return state - action.amount;
  } else {
    return state;
  }

}

class Store {
  constructor (updateState,state) {
    // Названия личных свойств в JS пишут с нижнего подчеркивания (хорошая практика)
    this._updateState = updateState;
    this._state = state;
    this._callbacks = [];
  }

  get state () {
    return this._state;
  }
  // Смысл зранилища не в том чтобы спрятать состояние от пользователя, а огнаничить влияние на него из вне
  update (action) {
    this._state = this._updateState(this._action, action)
    this._callbacks.forEach(callback => callback());
  }

  subscribe (callback) {
    this._callbacks.push(callback)
    return () => this._callbacks = this._callbacks.filter(cb => cb !== callback)
  }
}

const store = new Store(updateState, 0);

const incrementAction = {type: 'INCREMENT', amount: 5};
const decrementAction = {type: 'DECREMENT', amount: 3};

// Теперь при каждом изменении состояния будет проверка
const unsubscribe = store.subscribe(() => console.log('State changed 1', store.state))
store.subscribe(() => console.log('State changed 2', store.state))
// В конце обязательно нужно реализовать отписку

store.updateState (incrementAction)
console.log(store.state);

unsibscribe();

store.updateState (decrementAction)
console.log(store.state);

store.updateState ({})
console.log(store.state);

// Эти функции не обновляют состояния, а возращают новые
// reducer - преобразователь

```

## Состояние

Разница между передачей числового литерала, объекта и массива в том что.
1. Перевая число мы получаем копию
2. Передавая объект мы получаем ссылку
3. Передавая массив мы получаем ссылку

Что это значит, пример из жизни: при подаче документов мы подаём копии чтобы не изменять оригинал.

Чтобы решить эту проблему используют функциональное программирование в JS.

Идеи функционального программирования:
1. Чистые функции (pure functions)
2. Неизменность данных (immutability)

Чистые функции это функции которые возращают значение только от зависихших аргументов.

```javascript

function add (x, y) {
  return x + y;
}

```

Чистые функции не должны изменять состояние приложения.

Функция как будто живет в своем мире, то есть если мы изменим переменную внутри неё это не повлияет на внешнюю переменную.

Пример плохой функции (Она изменяет массив):

```javascript

function append (item, array) {
  array.push(item);
}

```

Вместо того чтобы изменять массив мы вернем новый:

```javascript
function append (item, array) {
  return array.concat(item);
  // Или так
  // return [...array, item]
}
```

Пример плохой функции с объектом:

```javascript
function update (state, value) {
  state.count = value;
}
```

Рефакторинг функции с объектом:

Это будет копия объекта но уже с обновленным значением свойства.

```javascript
function update (state, value) {
  return {
    count: state.count + value
  };
}
```

Чтобы постоянно не копировать у объекта добавили метод `Object.assign` котрый соеденяет несколько объектов в один. 

Передаются (объект_1, свойства, объект_2), всё будет переписано на свойства 2 объекта.

```javascript
function update (state, value) {
  return Object.assign({}, state, {
    count: state.count + value
  });
}
```

Уже сейчас можно делать это при помощи оператора разворота (Babel в помощь).

```javascript
function update (state, value) {
  return {...state, value};
}
```

Приступим к переписанию нашего кода.

```javascript

// Только эта функция будет иметь возможность менять состояние
function updateState (state, action) {
  
  if (action.type === 'INCREMENT') {
    return {count: state.count + action.amount};
  } else if (action.type === 'DECREMENT') {
    return {count: state.count - action.amount};
  } else {
    return state;
  }

}

class Store {
  constructor (updateState, state) {
    // Названия личных свойств в JS пишут с нижнего подчеркивания (хорошая практика)
    this._updateState = updateState;
    this._state = state;
    this._callbacks = [];
  }

  get state () {
    return this._state;
  }
  // Смысл зранилища не в том чтобы спрятать состояние от пользователя, а огнаничить влияние на него из вне
  update (action) {
    this._state = this._updateState(this._action, action)
    this._callbacks.forEach(callback => callback());
  }

  subscribe (callback) {
    this._callbacks.push(callback)
    return () => this._callbacks = this._callbacks.filter(cb => cb !== callback)
  }
}

const initialState = {count: 0};

const store = new Store(updateState, initialState);

const incrementAction = {type: 'INCREMENT', amount: 5};
const decrementAction = {type: 'DECREMENT', amount: 3};

// Теперь при каждом изменении состояния будет проверка
const unsubscribe = store.subscribe(() => console.log('State changed 1', store.state))
store.subscribe(() => console.log('State changed 2', store.state))
// В конце обязательно нужно реализовать отписку

store.updateState (incrementAction)
console.log(store.state);

unsibscribe();

store.updateState (decrementAction)
console.log(store.state);

store.updateState ({})
console.log(store.state);

// Эти функции не обновляют состояния, а возращают новые
// reducer - преобразователь

```

**Функция изменющая состояние должна быть чистой (в Redux это reducer).

## Использование хранилища

1. Импортировать хранилище
2. Инициализировать стартовое состояние
3. Описываем функцию обновления состояния
4. Создаем новой экземпляр хранилища и передаем туда (updateState, initialState)
5. Подписаться на обновления (можно подписаться в componentDidMount)
6. forceUpdate - не рекомендуется, но придётся
7. Описываем методы

## Отличия с Redux

- В Redux для создания хранилища используется функции
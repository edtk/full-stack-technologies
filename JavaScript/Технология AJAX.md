# Технология AJAX

## AJAX на стоне клиента

AJAX - Asynchronous JavaScript and XML. Технология загрузки данных / отправки форм без перезагрузки WEB страницы.

XML совершенно не обязателен, возможен прием и отправка данных любого типа. Чаще всего вместо XML используется HTML либо JSON для загрузки сырых данных.

```javascript
// 1. Создается объект запроса.
// Для разных браузеров этот объект называется по разному.
var xhr = new XMLHttpRequest();
// 2. Вызывается метод open, он указывает с каким HTTP методом и по какому URL нужно отправлять данный запрос.
// Флаг true означает что запрос должен быть асинхронным.
xhr.open('POST', '/xhr/test.html', true);
// 3. Задаём функцию обработчик.
// Метод onreadystatechange имеет несколько состояний (когда от был отправлен и т.д.).
// Чаще всего нам нужно состояние когда ответ получен readyState == 4.
// После получения ответа будут доступны разные переменные, такие как status, responseText и т.д.
xhr.onreadystatechange = function () {
  if (xhr.readyState == 4) {
    if (xhr.status == 200) {
      alert(xhr.responseText);
    }
  }
}
// 4. Вызываем метод send (отправка запроса).
// В метод send передаётся тело запроса.
// Тело должно передаваться уже в закодированном виде, в нашем случае x-www-form-urlencoded.
xhr.send('a=5&b=4');
```

## AJAX запросы с использованием jQuery

Стандартое API браузера очень сырое и чаще всего разработчики используют библиотеки, такие как jQuery, Axios.

Пример отправки запроса с помощью jQuery.

```javascript
$.ajax({
  url: '/blog/comment/add/',
  type: 'POST',
  // jQuery берет на себя ответсвенность за сериализацию данных, то есть нам не нужно кодировать данные вручную!
  data: {
    post_id: 12,
    text: 'Занятная идея!'
  }
}).success(function(data){
  if(data.status == 'ok') {
    console.log(data.comment_id);
  }
}).error(function(){
  console.log('HTTP Error');
});
```

Ограничения AJAX:

- Same Origin Policy - AJAX запросы можно отправлять только на свой домен. В современных браузерах есть CORS.
- Т.к. данные передаются явно в методе send, то нельзя загружать файлы. В HTML5 есть FormData.
- AJAX на стороне сервера не отличается от обычного запроса. jQuery добавляет заголовок X-Requested-With: XMLHttpRequest.
- Результаты запроса передаются в JavaScript функцию поэтому стандартные методы ошибок - не работают.

## Передача HTML и JSON в AJAX запросах

Чаще всего при использовании AJAX в ответе возращают JSON, HTML или XML.

Пример отправки HTML на Python.

```python
def comments_list(request):
  post_id = request.GET.get('post_id')
  post = get_object_or_404(Post, post_id)
  comments = paginate(request, post.comments)
  return render(request, 'blog/comments.html', {
    'comments': comments
  })
```

В более сложных случаях нам нужно каким-то образом структурировать данные.

Для этого используется JSON.

```javascript
// Запрос
{
  "status": "ok",
  "comment_id": 123
}

// Ответ
{
  "status": "error",
  "code": "no_auth",
  "message": "Вы не авторизованы!"
}
```

Пример создания обработчиков HttpResponseAjax в Python:

```python
import json

class HttpResponseAjax(HttpResponse):
  def __init__(self, status = "ok", **kwargs):
    # kwargs - произвольный словарь.
    kwargs['status'] = status
    super(HttpResponseAjax, self).__init__(
      content = json.dumps(kwargs),
      content_type = 'application/json'
    )

class HttpResponseAjaxError(HttpResponseAjax):
  def __init__(self, code, message):
    kwargs['status'] = status
    super(HttpResponseAjaxError, self).__init__(
      status = 'error', code = code, message = message
    )
```

Пример view которая добавляет комментарии:

```python
@login_required_ajax
def comment_add(request):
  form = AddCommentForm(request.POST)
  if form.is_valid():
    comment = form.save()
    return HttpResponseAjax(comment_id=comment.id)
  else:
    return HttpResponseAjaxError (
      code = "bad_params",
      # message = form.errors.as_data()
      # message = form.errors.as_json()
      message = form.errors.as_text()
    )
```

Пример декоратора для редиректа JSON:

```python
def login_required_ajax(view)
def view2(request, *args, **kwargs):
  if request.user.is_authenticated():
    return view(request, *args, **kwargs)
  elif:
    request.is_ajax():
    return HttpResponseAjaxError(
      code = "no_auth",
      message = u'Требуется авторизация.'
    )
  else:
    redirect('/login/?continue=' + requser.get_full_path())
```

## Cross Origin Resource Sharing

CORS позволяет серверу явно разрешать использование данных при кросс-доменных запросах.

По такому принципу работает популярный сервис Disqus.

При каждом кросс-доменном запросе браузер передает специальный заголовок Origin (происхождение, сайт который вызывает запрос). С помощью этого заголовка сервер может понять откуда приходит запрос.

Сервер должен дать разрешение с помощью заголовка Access-Control-Allow-Origin.

Заголовки CORS:

- Origin - указывает URL запрашиваемого приложения.
- Access-Control-Allow-Origin: origin - разрешает использование данных в кросс-доменном запросе. origin должен либо совпадать с заголовком Origin в запросе, либо *.
- Access-Control-Allow-Credentials - позволяет использовать данные, если были переданные cookie.

Хорошей практикой является проверка домена по списку доверенных.

Пример декоратора CORS для Python:

```python
def allow_cors(view):
  def view2(request, *args, **kwargs):
    response = view(request, *args, **kwargs)
    origin = request.META.get('HTTP_ORIGIN')
    if not origin:
      return response
    for domain in settings.CORS_WHITE_LIST:
      if origin.endswitch('.' + domain):
        response['Access-Control-Allow-Origin'] = origin
    return response
  return view2
```

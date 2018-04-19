# Тестирование в Laravel: HTTP тесты
	- Введение
		- Настройка заголовков запросов
	- Сессии / Аутентификация (Проверка подлинности)
## Введение

Laravel предоставляет очень свободный API для создания HTTP-запросов к вашему приложению и изучения выходных данных. Например, взгляните на тест, определенный ниже:

```php
namespace Tests\Feature;

use Tests\TestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithoutMiddleware;

class ExampleTest extends TestCase
{
    /**
     * A basic test example.
     *
     * @return void
     */
    public function testBasicTest()
    {
        $response = $this->get('/');

        $response->assertStatus(200);
    }
}
```

Метод `get` делает запрос `GET` в приложение, в то время как метод `assertStatus` утверждает, что возвращенный ответ должен иметь данный код состояния HTTP. В дополнение к этому простому утверждению Laravel также содержит множество утверждений для проверки заголовков ответов, содержимого, структуры JSON и т.д.

## Настройка заголовков запросов

Можно использовать метод withHeaders для настройки заголовков запроса перед его отправкой в приложение. Это позволяет добавлять любые пользовательские заголовки, которые вы хотели бы запросить:

```php
class ExampleTest extends TestCase
{
    /**
     * A basic functional test example.
     *
     * @return void
     */
    public function testBasicExample()
    {
        $response = $this->withHeaders([
            'X-Header' => 'Value',
        ])->json('POST', '/user', ['name' => 'Sally']);

        $response
            ->assertStatus(201)
            ->assertJson([
                'created' => true,
            ]);
    }
}
```

## Сессии / Аутентификация (Проверка подлинности)

Laravel предоставляет несколько помощников для работы с сеансом во время HTTP-тестирования. Во-первых, вы можете установить данные сеанса в заданный массив с помощью метода `withSession`. Это полезно для загрузки сеанса с данными перед отправкой запроса в приложение:

```php
class ExampleTest extends TestCase
{
    public function testApplication()
    {
        $response = $this->withSession(['foo' => 'bar'])
                         ->get('/');
    }
}
```

Конечно, одно общее использование сеанса для поддержания состояния для аутентифицированного пользователя. Вспомогательный метод `actingAs` предоставляет простой способ проверки подлинности данного пользователя в качестве текущего пользователя. Например, мы можем использовать фабрику моделей для создания и аутентификации пользователя:

```php 
use App\User;

class ExampleTest extends TestCase
{
    public function testApplication()
    {
        $user = factory(User::class)->create();

        $response = $this->actingAs($user)
                         ->withSession(['foo' => 'bar'])
                         ->get('/');
    }
}
```

Можно также указать, какой guard должен использоваться для аутентификации данного пользователя, передавая имя guard в качестве второго аргумента методу `actingAs`:

```php
$this->actingAs($user, 'api')
```

## 
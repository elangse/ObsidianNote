Catatan / notebook dari pembelajaran course Programming Zaman Now di Udemy

## Unit Test
Laravel ada 2 jenis test
1. Unit test (PHPUnit, unit test biasa)
2. Feature / Integration Test (Laravel, testing fitur2 laravel)
Membuat file Unit Test
`php artisan make:test <nama> --unit`

Membuat file Feature / Integration Test
`php artisan make:test <nama>`
## Environtment Variable
Fungsi utama-nya adalah membuat konfigurasi yang mudah dirubah dan dipindah-pindah, settingan biasanya tersimpan di file _.env_

#### Cara memanggil
1. env(key) atau env(key, default)
2. Env::get(key) atau Env::get(key, default)

## Configuration
Semua config terletak di folder _config_.
Kita bisa membuat file config sendiri.

berikut contoh file config/contoh_config.php
```php
<?php
return [
    "author" => [
        "first" => "Eko",
        "last" => "Khannedy"
    ],
    "email" => "echo.khannedy@gmail.com",
    "web" => "https://www.programmerzamannow.com/"
];
```

untuk ambil data konfig, kita tinggal pake code `config(key, default);`
key config memiliki rule `file.array.nestedArray`
Contoh `config("contoh.author.first")`, `config("contoh.email")`

#### Tips & Trick!
* Saat sudah memiliki banyak config, kita bisa cache config kita loh!
`php artisan config:cache` untuk cache config
`php artisan config:clear` untuk hapus cache config

## Dependency Injection
Dependency Injection adalah memasukkan library / class / depedency ke Code Laravel kita.

Ada beberapa cara injection
1. Cara biasa, memasukkan class pada umumnya menggunakan `new nameClass()`
2. dengan fitur Service Container
`$this->app->make(Foo::class)`.
3. jika ingin ubah cara pembuatan Dependency-nya, gunakan bind.
```php
$this->app->bind(Person::class, function($app) {
	return new Person("Eko", "Khannedy");
});
$person1 = $this->app->make(Person::class);
$person2 = $this->app->make(Person::class);
// $person1 & $person2 berbeda object.
```
4. Jika ingin membuat object class yang sama seperti pointer yg sama **secara default & massal** . Gunakan **singleton**
```php
$this->app->singleton(Person::class, function($app) {
	return new Person("Eko", "Khannedy");
})
$person1 = $this->app->make(Person::class);
$person2 = $this->app->make(Person::class); // object yg sama dengan $person1
```
5. Jika ingin menggunakan Object yg sudah ada, gunakan **Instance**.
```php
$person = new Person("Eko", "Khannedy");
$this->app->instance(Person::class, $person);

$person1 = $this->app->make(Person::class);
$person2 = $this->app->make(Person::class);

// $person, $person1, $person2 mereka semua adalah object yg sama, dengan memory address yg sama pula.
```
6. Injection di dalam Closure.
```php
$this->app->singleton(Foo::class, function ($app) {
	return new Foo();
});
$this->app->singleton(Bar::class, function ($app) {
	return new Bar($app->make(Foo::class));
});

$bar1 = $this->app->make(Bar::class);
$bar2 = $this->app->make(Bar::class);
```
7. Binding Interface ke Class
   dengan memanfaat interface sebagai dasar / kontrak class , kita bisa mengimplementasikan class secara berbeda-beda tanpa harus mengubah kontrak interface-nya.
**membuat interface**
```php
<?php
namespace App\Services;

interface HelloService
{
	function hello(string $name): string;
}
```
**menggunakan / binding interface**
```php
namespace App\Services;
class HelloServiceIndonesia implements HelloService
{
	function hello(string $name): string
	{
		return "Halo $name";
	}
}
```
**testing interface**
```php
use App\Services\HelloServiceIndonesia;

public function test_hello_service()
{
	$helloService = $this->app->singleton(HelloService::class, HelloService::class);
	self::assertEquals("Halo Eko", $helloService->hello("Eko"));
}
```

## Service Provider
Di laravel, untuk melakukan depedency injection biasanya menggunakan fitur Service Provider. config ini terletak pada direktori App\Providers.
Membuat service provider
`php artisan make:provider NamaServiceProvider`

Di service provider ada 2 function dasar
1. register(), untuk membuat / registrasi dependency
2. boot(), setelah diregister(). boot() akan berjalan

**Info Penting!!**
Service provider tidak dijalankan otomatis, kita perlu setting provider yang akan dijalankan melalui config/app.php
contoh
```php
'providers' => ServiceProvider::defaultProviders()->merge([
        /*
         * Package Service Providers...
         */

        /*
         * Application Service Providers...
         */
        App\Providers\AppServiceProvider::class,
        App\Providers\AuthServiceProvider::class,
        // App\Providers\BroadcastServiceProvider::class,
        App\Providers\EventServiceProvider::class,
        App\Providers\RouteServiceProvider::class,
        App\Providers\FooBarServiceProvider::class, // provider yang saya tambahkan sendiri
    ])->toArray(),
```
Jika kita hanya butuh binding sederhana, misal dari interfcae ke class. Kita bisa menggunakan fitur binding via properties di service provider.
Atau jika ingin melakukan binding singleton
```php
// binding singleton properties
class FooBarServiceProvider extends ServiceProvider implements DeferrableProvider
{
    public array $singletons = [
        HelloService::class => HelloServiceindonesia::class
    ];
    // ...
```

### Deferred Provider
secara default semua Service Provider akan diload oleh laravel.
dengan fitur Deferred Provider, kita bisa menandai Service Provider agar tidak diload jika tidak dibutuhkan depedency-nya.
Setiap ada request baru, maka Service Provider yg sudah Deferred tidak akan diload jika memang tidak dibutuhkan.

Untuk menandai Deferred kita hanya perlu menambahkan `implements DeferrableProvider` dan public function `provides()` di dalamnya
```php
class FooBarServiceProvider extends ServiceProvider implements DeferrableProvider
{
	public function provides(): array
	{
		return [HelloService::class, Foo::class, Bar::class];
	}
}
```

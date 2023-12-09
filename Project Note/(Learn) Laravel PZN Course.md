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

## Dependency Injenction

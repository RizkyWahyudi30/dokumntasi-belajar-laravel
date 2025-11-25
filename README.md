# DOKUMENTASI BELAJAR LARAVEL 12
 materi yang saya ambil ini dari bermacam resource yang terdapat di media sosial

## 1. Belajar Routing & View, Route Wildcard dan Passing Data ke View
Materi Routing dan Viwes pada Laravel merupakan materi dasar yang penting dipelajari, berikut penjelasannya :
- Route adalah sebuah sistem atau mekanisme pada laravel yang dengan method HTTP ke suatu folder ataupun file tertentu (sebutlah URL). Pada laravel kita bisa cek di folder `routes/web.php`
- Views adalah tempat untuk menampilkan respon dari Route tadi dengan GUI. Biasanya di laravel berupa Blade. Pada laravel kita bisa cek di `resources/views/... .blade.php`

Hubungan antar Route dan View 
```bash
routes/web.php

Route::get('/', function () {
    return view('welcome');
});

-------------------------------------------------------------------------------

resources/views/welcome.blade.php

<body>
    <h1>Hello, saya wahyu sedang belajar laravel 12</h1>
</body>
```
Pada bagian `'/'` di web.php itu mengarahkan ke folder resources/views/ , dan bagian `return view('')` itu kita memasukkan nama file yang ada di dalam `views/` 

Jadi, kalau misalkan kita mau membuat URL baru, kita bisa mengarahkan nya dengan slash(/) di URL browser. Basicnya kan seperti ini : `http://127.0.0.1:8000/`, kalau  mau menambahkan URL baru, tinggal create seperti ini dan buka di URL `http://127.0.0.1:8000/about` :
```bash
routes/web.php

Route::get('/', function () {
    return view('welcome');
});

route baru

Route::get('/about', function () {
    return view('about');
});

-------------------------------------------------------------------------------

resources/views/about.blade.php

┣ 📂 resources/
 ┃ ┣ 📂 views/
 ┃ ┃ ┣ 📜 about.blade.php
 ┃ ┃ ┣ 📜 welcome.blade.php

<body>
    <h1>Ini halaman testing about</h1>
</body>
```

Jika kalian ingin membuat struktur folder yang lebih spesifik / nested folder, misalnya `resources/views/admin/....` pada routes, kalian bisa melakukan seperti ini :
```bash
di admin

Route::get('/admin', function () {
    return view('admin.home');   -> mengarahkan ke /admin/home.blade.php
});
```
Jika ingin membuka nya di URL, kalian bisa membuka nya sama seperti biasanya `http://127.0.0.1:8000/admin/`.

Passing data dari route ke view dapat dilaukan dengan beberapa cara :
- Array        ==> view ('v', ['x' => 1]);
- compact()    ==> view ('v', compact('x'));
- with()       ==> view ('v')->with('x', 1);

Seperti ini praktiknya :

Array 
```bash
Route::get('/about', function () {
    // return view('about'); // --> basic

    $data = 'Rizky Wahyudi';
    $umur = 19;
    return view('about', ['nama' => $data, 'umur' => $umur]);  --> ini menggunakan array asosiation (key => value)
});
```

compact()
```bash
Route::get('/about', function () {
    // return view('about'); // --> basic

    $data = 'Rizky Wahyudi';
    $umur = 19;
    return view('about', compact('data', 'umur')); // ini compact, caranya bisa begini 
});
```

with()
```bash
Route::get('/about', function () {
    // return view('about'); // --> basic

    $data = 'Rizky Wahyudi';
    $umur = 19;
    return view('about')
        ->with('data', 'Wahyudi')
        ->with('umur', 17);
});
```

Selanjutnya materi Route Wildcard. Route wildcard ini berguna untuk memungkinkan kita membuat URL Dinamis, seperti /product/123, /user/john. Kita bisa melakukannya seperti ini pada kodingan kita 
```bash
routes/web.php

Route::get('/admin/{id}', function ($id) {
    return view('admin.home')->with('id', $id);
});

-------------------------------------------------------------------

/admin/home.blade.php

<body>
    <h1>Ini home page admin</h1>
    <p>page {{ $id }}</p>
</body>
```
Testing pada URL kalian bisa test secara bebas -> `/admin/1` ataupun angka selain 1, bisa text juga

## 2. Blade and Blade Directive, Layout and Slots, dan Components, Attributes and Props
lanjut materi basic Blade dan apa itu Bladde Directive. Berikut penjelasannya

- Blade adalah sebuah template enginee bawaan untuk memudahkan pengelolaan tampilan, serta memudahkan menuliskan kode HTML dan PHP secara gabung
- Blade directive adalah perintah untuk mengelola percabangan atau perulangan, yang umum digunakan seperti :
  - `@if | @else | @endif` : perintah untuk situasi kondisional atau percabangan
  - `@foreach | @endforeach` : perintah untuk perulangan array atau korelasi data

Pada praktik koding nya bisa seperti ini :
```bash
routes/web.php

Route::get('/about', function () {
    // return view('about'); // --> basic

    $data = 'Rizky Wahyudi';
    $umur = 19;
    return view('about')
        ->with('data', $data)
        ->with('umur', $umur);
});

resources/views/about.blade.php
<h1>Ini halaman testing about</h1>
    <p>Halo, ini {{ $data }}, dan umurnya {{ $umur }}</p> <!-- get nya menggunakan cara seperti ini, panggil key nya -->
    <!-- menggunakan if else directive -->
    @if ($umur > 19)
    <p>Kamu sudah dewasa dan bisa memulai karir</p>
    @else
    <p>Kamu masih dibawah kata dewasa</p>
    @endif
```

dan jika penggunaan foreach seperti ini :
```bash
routes/web.php

Route::get('/admin', function () {
    $data_admin = [
        ['id' => "001", 'name' => "admin1", 'status' => true],
        ['id' => "002", 'name' => "admin2", 'status' => true],
        ['id' => "003", 'name' => "admin3", 'status' => false],
    ];

    return view('admin.home')->with('administrator', $data_admin);
});

resources/views/about.blade.php
<h1>Ini home page admin</h1>
    <ul>
        <!-- menggunakan foreach directive -->
        @foreach ( $administrator as $access )
        <li>{{ $access['id'] }} - {{ $access['name'] }}</li>
        @endforeach
    </ul>
```

Selanjutnya, materi Layout dan slots. Materi ini berfungsi untuk mempermudahkan kita untuk membuat bagian bagian yang tampil di dalam view website, tetapi kita tidak membuat nya satu persatu di setiap file.

Kita bisa membuat folder baru di dalam views `/views/components/...` dan kita bisa membuat file bernama `layout.blade.php` 

Berikut demonstrasi nya :
```bash
/components/layout.blade.php

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <nav>
        <div>
            <a href="/">Home</a>
            <a href="/about">About</a>
            <a href="/contact">Contact</a>
            <a href="/admin">Administrator</a>
        </div>
    </nav>

    <main>
        {{ $slot }}
    </main>
</body>
</html>

------------------------------------------------------------

/views/welcome.blade.php

<x-layout>
    <h1>welcome</h1>
</x-layout>
```
Jadi, kita pada file welcome, kita bisa menghapus struktur dasar HTML, dan menggantinya dengan tag `<x-layout>` dan bisa isikan seperti itu. `$slot` pada layout.blade.php berguna untuk menggunakan metode nya, jika kita ingin membuat footer, kita bisa membuat seperti ini :
```bash
layout.blade.php

---- tambahkan ini di paling atas / diatar kode html nya
@props ['footer']

<main>
    {{ $slot }}
</main>

------ tambah ini ------
<footer>
    Ini adalah footer dari page: <span>{{ $footer }}</span>
</footer>

--------------------------------------------

welcome.blade.php

<x-layout>
    <h1>welcome</h1>


    -------- tambah ini --------
    <x-slot:footer>
        Home page
    </x-slot:footer>
</x-layout>
```
Jadi bisa seperti itu. 


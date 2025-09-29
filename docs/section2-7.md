# 工程で苦労した点
* テーブル定義はmigrationファイルで定義するのではなく、クエリエディタを使ってSQL実行したかった。
* Welcomeページをいじろうとしたら、全然うまくいかなかった。<br>
  →Laravelではよくあることらしい。フルパス表記で解決

```php
<?php

use App\Http\Controllers\ProfileController;
use Illuminate\Foundation\Application;
use Illuminate\Support\Facades\Route;
use Inertia\Inertia;
use App\Http\Controllers\ItemController;
use App\Http\Controllers\PurchaseController;


Route::resource('items', ItemController::class)
        ->middleware(['auth', 'verified']);

Route::middleware(['auth', 'verified'])->group(function () {

    Route::controller(PurchaseController::class)->group(function () {
        Route::get('/purchases', 'index')->name('purchases.index');
        Route::post('/purchases', 'store')->name('purchases.store');
        Route::get('/purchasese', 'show')->name('purchases.show');
        Route::put('/purchases', 'update')->name('purchases.update');
    });

});

Route::get('/',[App\Http\Controllers\WelcomeController::class,'index']);
/*
Route::get('/', function () {
    return Inertia::render('Welcome', [
        'canLogin' => Route::has('login'),
        'canRegister' => Route::has('register'),
        'laravelVersion' => Application::VERSION,
        'phpVersion' => PHP_VERSION,
    ]);
});
*/
Route::get('/dashboard', function () {
    return Inertia::render('Dashboard');
})->middleware(['auth', 'verified'])->name('dashboard');

Route::middleware('auth')->group(function () {
    Route::get('/profile', [ProfileController::class, 'edit'])->name('profile.edit');
    Route::patch('/profile', [ProfileController::class, 'update'])->name('profile.update');
    Route::delete('/profile', [ProfileController::class, 'destroy'])->name('profile.destroy');
```

* 貸出PCをアカデミックルームで使っていると一部機能にアクセスできなかった。
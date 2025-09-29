# コーディング作業について

## Laravel開発重要ポイント　　~viエディタでもなんとかなった理由~

**1. Artisanコマンドを使い倒す**

コーディングを最速で行うための必須コマンドで、かなり強力です。<br>

例えば、在庫管理の機能を追加するとします。

```bash
php artisan make:model Zaiko -a
```

このコマンドで以下のファイルが一括生成されます。

* app/Models/Post.php (モデル SQL)
* database/factories/PostFactory.php (ファクトリ)
* database/migrations/xxx_create_zaiko_table.php (マイグレーション DBテーブル)
* app/Http/Controllers/PostController.php (コントローラ)
* app/Policies/PostPolicy.php (ポリシー アクセス制御とか)
* database/seeders/zaikoseeder.php (シーダー　DBデータ準備)
* app/Http/Requests/StoreZaikoRequest.php（バリデーションルール）
* app/Http/Requests/UpdateZaikoRequest.php（バリデーションルール）

手作業でファイルを生成するよりも安全で、高速に新規開発が出来ます。<br>
さらに、Controllerも見てみると標準で以下のソースが記載されています。

<div class="page"/>

```php
<?php

namespace App\Http\Controllers;

use App\Http\Requests\StoreZaikoRequest;
use App\Http\Requests\UpdateZaikoRequest;
use App\Models\Zaiko;
use Illuminate\Http\Request;
use Inertia\Inertia;
use lluminate\Support\Facades\Auth;

class ZaikoController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index(Request $request)
    {
        // 
    }

    /**
     * Show the form for creating a new resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function create()
    {
        //
    }

    /**
     * Store a newly created resource in storage.
     *
     * @param  \App\Http\Requests\StoreZaikoRequest  $request
     * @return \Illuminate\Http\Response
     */
    public function store(StoreZaikoRequest $request)
    {
        // 
    }

    /**
     * Display the specified resource.
     *
     * @param  \App\Models\Zaiko  $zaiko
     * @return \Illuminate\Http\Response
     */
    public function show(Request $request)
    {
        // 
    }

    /**
     * Show the form for editing the specified resource.
     *
     * @param  \App\Models\Zaiko  $zaiko
     * @return \Illuminate\Http\Response
     */
    public function edit(Zaiko $zaiko)
    {
        //
    }

    /**
     * Update the specified resource in storage.
     *
     * @param  \App\Http\Requests\UpdateZaikoRequest  $request
     * @param  \App\Models\Zaiko  $zaiko
     * @return \Illuminate\Http\Response
     */
    public function update(UpdateZaikoRequest $request)
    {
        // 
    }
    

    /**
     * Remove the specified resource from storage.
     *
     * @param  \App\Models\Zaiko  $zaiko
     * @return \Illuminate\Http\Response
     */
    public function destroy(Zaiko $zaiko)
    {
        //
    }
}
```

<div class="page"/>

勿論、Controllerだけ作ること可能です。<br>
Artisanコマンドにより開発時間の大幅な短縮、コード品質の向上、チーム開発の効率化が担保されます。

一番使ったのは下記コマンドで、ルーティングの設定を見ることが出来ます。

```bash
php artisan route:list

 GET|HEAD        / .................................................................... WelcomeController@index
  GET|HEAD        _debugbar/assets/javascript ...... debugbar.assets.js › Barryvdh\Debugbar › AssetController@js
  GET|HEAD        _debugbar/assets/stylesheets ... debugbar.assets.css › Barryvdh\Debugbar › AssetController@css
  DELETE          _debugbar/cache/{key}/{tags?} debugbar.cache.delete › Barryvdh\Debugbar › CacheController@del…
  GET|HEAD        _debugbar/clockwork/{id} debugbar.clockwork › Barryvdh\Debugbar › OpenHandlerController@clock…
  GET|HEAD        _debugbar/open ....... debugbar.openhandler › Barryvdh\Debugbar › OpenHandlerController@handle
  POST            _debugbar/queries/explain debugbar.queries.explain › Barryvdh\Debugbar › QueriesController@ex…
  POST            _ignition/execute-solution ignition.executeSolution › Spatie\LaravelIgnition › ExecuteSolutio…
  GET|HEAD        _ignition/health-check . ignition.healthCheck › Spatie\LaravelIgnition › HealthCheckController
  POST            _ignition/update-config ignition.updateConfig › Spatie\LaravelIgnition › UpdateConfigControll…
  GET|HEAD        api/user ..................................................................................... 
  GET|HEAD        confirm-password .................. password.confirm › Auth\ConfirmablePasswordController@show
  POST            confirm-password .................................... Auth\ConfirmablePasswordController@store
  GET|HEAD        dashboard .......................................................................... dashboard
  POST            email/verification-notification verification.send › Auth\EmailVerificationNotificationControl…
  GET|HEAD        forgot-password ................... password.request › Auth\PasswordResetLinkController@create
  POST            forgot-password ...................... password.email › Auth\PasswordResetLinkController@store
  GET|HEAD        items ..................................................... items.index › ItemController@index
  POST            items ..................................................... items.store › ItemController@store
  GET|HEAD        items/create ............................................ items.create › ItemController@create
  GET|HEAD        items/{item} ................................................ items.show › ItemController@show
  PUT|PATCH       items/{item} ............................................ items.update › ItemController@update
  DELETE          items/{item} .......................................... items.destroy › ItemController@destroy
  GET|HEAD        items/{item}/edit ........................................... items.edit › ItemController@edit
  GET|HEAD        login ..................................... login › Auth\AuthenticatedSessionController@create
  POST            login .............................................. Auth\AuthenticatedSessionController@store
  POST            logout .................................. logout › Auth\AuthenticatedSessionController@destroy
  PUT             password .................................... password.update › Auth\PasswordController@update
  GET|HEAD        profile ................................................ profile.edit › ProfileController@edit
  PATCH           profile ............................................ profile.update › ProfileController@update
  DELETE          profile .......................................... profile.destroy › ProfileController@destroy
```

<div class="page"/>

DBテーブル作るなら

```bash
php artisan migrate
```

DBデータ入れるなら

```bash
php artisan migrate:fresh --seed   
```

ローカルでデバッグするなら

```bash
php artisan serve   
```

**2. npmコマンドはエラーチェックに有効**
このコマンドはローカル上でデバッグ確認するときによく使います。

```bash
npm run dev
```

Vue.jsを変更したらビルドすることで/publicフォルダにjsファイルが生成され、本番リリースが出来ます。

```bash
npm run build
```

これらのコマンドは、Vue.jsで「{」とか、「;」 とか抜けていないか構文チェックをしてくれるのでそこそこ助かりました。<br>
その他エラーはブラウザでF12[開発者モード]で確認し、ぐりぐり進めていきました。

<div class="page"/>

## 開発例 画像表示

以下の例では、どうやって商品一覧画面を表示しているのかについて紹介します。<br>
routes/web.php

```bash
<?php

use App\Http\Controllers\ProfileController;
use Illuminate\Foundation\Application;
use Illuminate\Support\Facades\Route;
use Inertia\Inertia;
use App\Http\Controllers\ItemController;
use App\Http\Controllers\PurchaseController;

Route::resource('items', ItemController::class)
        ->middleware(['auth', 'verified']);
```

resources/js/Pages/Items/Index.vue

```bash
<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head, Link } from '@inertiajs/vue3';
import Pagination from '@/Components/Pagination.vue';
import FlashMessage from '@/Components/FlashMessage.vue';
import { Inertia } from '@inertiajs/inertia'
import { ref } from 'vue'

defineProps({
  items: Object
})

const search = ref('')
const searchItems = () => {
    Inertia.get(route('items.index', { search: search.value }))
}
</script>

<template>

  <Head title="商品一覧" />

  <AuthenticatedLayout>
    <template #header>
      <h2 class="font-semibold text-xl text-gray-800 leading-tight">商品一覧</h2>
    </template>
    <FlashMessage />
    
    <div class="py-12">
      <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
        <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
            <div class="flex w-full justify-center items-end mt-6">
              <div>
                 <input input type="text" name="search" v-model="search">
              </div>
              <button @click="searchItems" focus:outline-none hover:bg-indigo-600 rounded text-lg">検索</button>
            </div>  

          <div class="p-6 text-gray-900">
            <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-6 p-4">
              <div v-for="item in items.data" :key="item.id"
                class="bg-white shadow rounded-lg p-4 flex flex-col items-center">
                <img alt="image" class="object-cover object-center w-full h-full block"
                    :src="item.image_url"                  />
                <h3 class="text-gray-500 text-xs tracking-widest title-font mb-1">{{ item.author }}</h3>
                <h2 class="text-gray-900 title-font text-lg font-medium">{{ item.name }}</h2>
                <p class="mt-1">&yen{{ item.price.toLocaleString() }}</p>
                <Link class="text-blue-400" :href="route('items.show', { item: item.id })">詳細を見る</Link>
              </div>
            </div>
          </div>
        <div class="flex justify-center mb-4"> 
        <Pagination class="mt-6" :links="items.links"></Pagination>
        </div>
        </div>
      </div>
    </div>
  </AuthenticatedLayout>
</template>
```

<div class="page"/>

app/Http/Controllers/ItemController.php

```php
<?php

namespace App\Http\Controllers;

use App\Http\Requests\StoreItemRequest;
use App\Http\Requests\UpdateItemRequest;
use App\Models\Item;
use Inertia\Inertia;
use Illuminate\Support\Facades\Storage;
use Illuminate\Http\Request;

class ItemController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index(Request $request)
    {
        $items = Item::searchItems($request->search)
                ->select('id','name','author','price','is_selling','image_id')->paginate(8)->through(function ($item) {
                    return [
                       'id' => $item->id,
                       'name' => $item->name,
                       'author' => $item->author,
                       'price' => $item->price,
                       'is_selling' => $item->is_selling,
                       'image_id' => $item->image_id, // 元のimage_idも残しておく
                       'image_url' => asset('images/' . $item->image_id), // asset()を使ってURLを生成
                    ];
                  });

        return Inertia::render('Items/Index',[
            'items' => $items
        ]);
    }
}
```

<div class="page"/>

/app/Models/Item.php

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use app\Models\Item;

class Item extends Model
{
    use HasFactory;
    protected $fillable =[
 'name',
 'author',
 'memo',
 'price',
 'is_selling',
 'image_id',
    ];

    public function purchases()
    {
       return $this->belongsToMany(Purchase::class)
       ->withPivot('quantity');
    }

    public function scopeSearchItems($query, $input = null)
    {
        if(!empty($input)){
            if(Item::where('name', 'like', '%' .  $input . '%' )
            ->orWhere('author', 'like', $input . '%')->exists())
                {
                return $query->where('name', 'like', '%' . $input . '%' )
                ->orWhere('author', 'like', $input . '%');
                }
        }
    }
}
```

よく見たら著者名が前方一致になってました。

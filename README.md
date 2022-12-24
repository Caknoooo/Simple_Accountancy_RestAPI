# Laravel 8 learning outcomes

Today, I implemented my laravel 8 result. I started learning laravel 8 on 12/20/2022. This repository was created on 24/12/2022. I created a simple database using laravel framework with its implementation using rest-API

## Code Explanation

I used the help of ```php artisan make:model Transaction -m``` to create the model
then the file ```2022_12_24_075759_create_transactions_table``` will be automatically created

then I modified the file so that a new table would be created

```php
public function up()
{
    Schema::create('transactions', function (Blueprint $table) {
        $table->id();
        $table->string('title');
        $table->double('amount');
        $table->timestamp('time')->default(now());
        $table->enum('type', ['expense', 'revenue']); // Hanya bisa diisi sesuai parameter ke 2
        $table->timestamps();
    });
}
```

inside the ```Transaction.php``` file I added the following code
```php
class Transaction extends Model
{
    use HasFactory;

    protected $fillable = ['title', 'amount', 'time', 'type'];
}
```

to make the table I use the help of ```php artisan``` 
```
php artisan migrate
```

then the table will be automatically created in our database

after that, I created a controller class with the following keywords
```
php artisan make:controller TransactionController --resource
```
then in the TransactionController file there are several methods that aim for CRUD

**GET**
```php
public function index()
{
    $transaction = Transaction::orderBy('time', 'DESC')->get();
    $response = [
        'message' => 'List Transaction order by time',
        'data' => $transaction
    ];

    return response()->json($response, Response::HTTP_OK);
}
```

**CREATE**
```php
public function store(Request $request)
{
    $validator = Validator::make($request->all(), [
        'title' => ['required'],
        'amount' => ['required', 'numeric'],
        'type' => ['required', 'in:expense,revenue']
    ]);

    // Jika gagal, maka keluarkan error unproccess
    if($validator->fails()){
        return response()->json($validator->error(), 
        Response::HTTP_UNPROCESSABLE_ENTITY);
    }

    // Jika berhasil, maka lakukanlah hal berikut, menggunakan try catch dengan tujuan untuk menghindari kesalahan yang tidak terduga
    try {
        $transaction = Transaction::create($request->all());
        $response = [
            'message' => 'Transaction created',
            'data' => $transaction
        ];

        return response()->json($response, Response::HTTP_CREATED);
    } catch (QueryException $e) {
        return response()->json([
            'message' => 'Failed' . $e->errorInfo
        ]);
    }
}
```

**READ**
```php
public function show($id)
{
    $transaction = Transaction::findOrFail($id);
    $response = [
        'message' => 'Detail of Transaction Resource',
        'data' => $transaction
    ];

    return response()->json($response, Response::HTTP_OK);
}
```

**UPDATE**
```php
public function update(Request $request, $id)
{
    $transaction = Transaction::findOrFail($id);

    $validator = Validator::make($request->all(), [
        'title' => ['required'],
        'amount' => ['required', 'numeric'],
        'type' => ['required', 'in:expense,revenue']
    ]);

    // Jika gagal, maka keluarkan error unproccess
    if($validator->fails()){
        return response()->json($validator->error(), 
        Response::HTTP_UNPROCESSABLE_ENTITY);
    }

    // Jika berhasil, maka lakukanlah hal berikut, menggunakan try catch dengan tujuan untuk menghindari kesalahan yang tidak terduga
    try {
        $transaction->update($request->all());
        $response = [
            'message' => 'Transaction Updated',
            'data' => $transaction
        ];

        return response()->json($response, Response::HTTP_OK);
    } catch (QueryException $e) {
        return response()->json([
            'message' => 'Failed' . $e->errorInfo
        ]);
    }
}
```

**DELETE**
```php
public function destroy($id)
{
    $transaction = Transaction::findOrFail($id);

    try {
        $transaction->delete();
        $response = [
            'message' => 'Transaction Deleted',
        ];

        return response()->json($response, Response::HTTP_OK);
    } catch (QueryException $e) {
        return response()->json([
            'message' => 'Failed' . $e->errorInfo
        ]);
    }
}
```

After all the CRUD is done, then I create the route in file ```api.php```
```php
Route::get('/transaction', [TransactionController::class, 'index']);
Route::get('/transaction/{id}', [TransactionController::class, 'show']);
Route::post('/transaction', [TransactionController::class, 'store']); 
Route::put('/transaction/{id}', [TransactionController::class, 'update']);
Route::delete('/transaction/{id}', [TransactionController::class, 'destroy']);
```

Actually, the code route is still inefficient, here is one of Laravel's defaults
```php
Route::resource('/transaction', Transaction::class)->except(['create', 'edit']);
```

But the code has a weakness. If we change the default method that was created the first time the TransactionController is changed, then the resource function will not work
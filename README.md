# Laravel 8 learning outcomes

Today, I implemented my laravel 8 result. I started learning laravel 8 on 12/20/2022. This repository was created on 24/12/2022. I created a simple database using laravel framework with its implementation using rest-API

## Code Explanation

I used the help of 
```
php artisan make:model Transaction -m
``` 
to create the model
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

## Output 
Here I use the help of the Postman application to make the rest-API

**GET**

![GET](https://user-images.githubusercontent.com/92671053/209437871-c82435f5-b7d1-47e2-8666-bf26a9556677.PNG)

**CREATE**

![CREATED](https://user-images.githubusercontent.com/92671053/209437877-611d109c-8f79-4e60-a898-937abf75dfca.PNG)

**READ**

![READ](https://user-images.githubusercontent.com/92671053/209437873-1cf717a3-00e1-428d-9a10-207e5ef50aeb.PNG)

**UPDATE**

![UPDATE](https://user-images.githubusercontent.com/92671053/209437875-02ec0844-ab29-4453-93e5-a3b09414c799.PNG)

**DELETE**

![DELETED](https://user-images.githubusercontent.com/92671053/209437870-b8f773e4-fd2c-4d88-90cd-d59f7ccd8fd7.PNG)

After all is done, the final result is as follows

## Result

![AFTER_DELETE](https://user-images.githubusercontent.com/92671053/209437876-55571c93-91c3-4f58-93b5-1bba90f90589.PNG)
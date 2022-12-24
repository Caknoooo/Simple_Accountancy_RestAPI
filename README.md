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
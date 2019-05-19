# Laravel notes
This repo contains notes, warnings, tricks and tips from my development experience with Laravel.

## Change Timezone
config folder - app.php file
in config array on "timezone" key change the value from UTC to date time you want, search in [PHP DOCS](https://www.php.net/manual/es/timezones.php)

## Work with SqlServer:
- "SA" is the root user, password is from installation or you could create a user with SQL Server Manager 
- Enable TCP protocol from SQL Server Config
- If use Linux could install from [Microsoft guide] ()
- if use Windows you will need ODBC installer and SQL Server Manager
- dates not work as well as in Mysql or Sql lite so you need to change dates from migrations in Models files, you have two options:

Cast the property $dateFormat that join created_at and updated_at fields with the next code: 
  
```php
    protected $dateFormat = 'Ymd H:i:s';
```
  
 Or overwrite the original method that writes the date in Eloquent with a trait, something like this:
 
```php
  namespace App;
  trait SqlServerGetDateFormat
  {
      public function getDateFormat()
      {
          return 'Y-m-d H:i:s';
      }

  }
```

 This allow to add the trait to all models from app folder with and elegant way and no need to cast all models dateFormat fileds, like.
 
 ```php
  namespace App;
  use Illuminate\Notifications\Notifiable;
  use Illuminate\Contracts\Auth\MustVerifyEmail;
  use Illuminate\Foundation\Auth\User as Authenticatable;
  class User extends Authenticatable
  {
      use Notifiable, SqlServerGetDateFormat;
      ...
  }
 ```
 
 Other way to fix this is to cut the length of the default date format of Laravel migrations to:
 
  ```php
    $table->timestamps(4);
  ```
  ## Migraciones
  
 From Laravel 5.6 the auth could have a user migration with type "bigIncrement" instead of "increment".
 both could be use but if a migration have bigIncrement in some field to be referenced as foreign key you will need
 to be explicit with it as "unsignedInteger" if its "increment" or "unsignedBigInteger" if its "bigIncrement".
 you could read more about it [here a Larvel Daily post](https://laraveldaily.com/be-careful-laravel-5-8-added-bigincrements-as-defaults/)
 
 File Of strong type model.
 
 ```php
  Schema::create('transactions_types', function (Blueprint $table) {
    $table->bigIncrements('id');
    $table->string('name');
    $table->timestamps();
  });
  ```
  File of week type
  
 ```php
  Schema::create('transactions', function (Blueprint $table) {
      $table->bigIncrements('id');
      $table->bigInteger('transaction_type_id')->unsigned();//--------------------------Here is the change from integer to bigInteger.
      $table->foreign('transaction_type_id')->references('id')->on('transactions_types');
      $table->decimal('amount');
      $table->timestamps();
  });
 ```
  
  ## Model changes
  
  - you could cast values of certain fields
  - you could use the convention of model as singular and table as plural but you could change this as: 
  
 ```php
  protected $table = '*TableNamePlaceholder*';
 ```
  - you could maintain the convention of table id as 'id', but if you change it the model binding and the find() method
  will fail so to use it as usually you need to change the primary key value explicit as:
  
 ```php
  protected $primaryKey = '*idPlaceholder*';
 ```
  - either you could change the default name of timestamps values 'created_at' and 'updated_at' like this:
  
 ```php
  const CREATED_AT = '*createdAtTitleChangedPlaceholder*';
  const UPDATED_AT = '*updatedAtTitleChangedPlaceholder*';
 ```
  - Remember to fill data in table you need to be explicit to avoid the massive assignment prevention of Laravel as:
  
 ```php
  protected $fillable = [ 'row1', 'row2' .... ];
 ```
 
  - a pretty interesting setting in model is the ability to set some property as softdelete to protect it and the delete method work only as a softdelete, you could use softDelete() method but in some cases you will set this in the model to prevent to delete a specific field and to work as some interface to other developers or the same in the future to protect from him or herself.
  
 ```php
  use Illuminate\Database\Eloquent\SoftDeletes;
  class post extends Model 
  {
    use SoftDeletes;//this set a softDelete only to this field on DB or property of model. (here delete() works same as softDelete() ).
    protected $date = 'nameOfTheDateRowOrWhateverRowTypeOrNameIsJustAnExample';
  }
 ```
 
 
 

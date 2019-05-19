# Laravel notes
This repo contains notes, warnings, tricks and tips from my development experience with Laravel.

## Change Timezone
config folder - app.php file
in config array on "timezone" key change the value from UTC to date time you want, search in [PHP DOCS] : (https://www.php.net/manual/es/timezones.php)

## Work with SqlServer:
- "SA" is the root user, password is from installation or you could create a user with SQL Server Manager 
- Enable TCP protocol from SQL Server Config
- If use Linux could install from [Microsoft guide] ()
- if use Windows you will need ODBC installer and SQL Server Manager
- dates not work as well as in Mysql or Sql lite so you need to change dates from migrations in Models files, you have two options:
  - Cast the property $dateFormat that join created_at and updated_at fields with the next code: 
  
  ```php
    protected $dateFormat = 'Ymd H:i:s';
  ```
  
  or overwrite the original method that writes the date in Eloquent with a trait, something like this: 
  
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
 This allow to add the trait to all models from app folder with and elegant way and no need to cast all models dateFormat fileds, like:
 
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
  
  

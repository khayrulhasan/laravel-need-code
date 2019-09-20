# Ajax
```php
Route::POST('/getAllSegment/team', 'TeamController@getAllSegment')->name('get.all.segment');
```
```php
$(document).on('change', '#process', function()
{
    var id = $(this).val();
    $.ajax({
        url: "{{ route('get.all.segment') }}",
        type: 'POST',
        data: {"_token": "{{ csrf_token() }}","id": id},
        success: function(data) {
         console.log(data);
      }
    });
});
```

```php
public function getAllSegment(Request $request)
{
    dd($request->input('id'));
}
```

# Eloquent Relation
1. One to One :
             hasOne


2. Inverse One to One : 
            belongsTo


3. One to Many :
             hasMany


4. Many to Many (By PIVOT) :
            belongsToMany


5. Has Many Through : (Join Three table)
            hasManyThrough


6. Polymorphic Relation 
   main model === morphTo
   accessible model = morphMany   
   
   
   ---------------------------
   
## SELECT your table columns in different way
### You can use any type of method to select table columns for your expected result.
   ```php
   $user = User::query();
   $user->select('`name` as another_name','age','onanno');
   $user->select(['name','age','onanno',DB::raw('count(*) as user_count')]);
   $user->selectRaw("`age` * ?",[2]);
   $user->addSelect('column1','column2');
   ```
   
## Writing where clause
### Boost up your query writing skill with eloquent to know different type of use of where clause
   ```php
   $model = Model::where('age',20); // where age = 20
   $model->where(age,'<',20); // where age < 20
   $model->where([
       ['status' ,'=','active'],
       ['age' ,'<',20],
   ]);
   $model->where(function($query){
       $query->where('a','=','n');
       $query->orWhere('b','=','x');
   });
   
   
   $model->orWhere('gender','!=','...');
   $model->orWhere(function($query){
       $query->where('a','=','n');
       $query->orWhere('b','=','x');
   });
   
   
   $model->whereStatus('active'); // where `status` = 'active'
   $model->whereColumn('updated_at','>','created_at');
   
   
   $model->whereRaw("`col1` * `col2` > ?",[100]);
   $model->whereRaw(DB::raw("`COL` IN (SELECT `Y1` FROM `Y` GROUP BY `Y`)"));
   
   
   $model->whereExists(function($query){
       $query->select(DB::raw(1))->from('something')
       ->whereRaw('something.user_id = another.id');
   });
   $model->whereNotExists(function($query){
       $query->select(DB::raw(1))->from('something')
       ->whereRaw('something.user_id = another.id');
   });
   
   
   $model->whereBetween('age',[18,30]);
   $model->whereNotBetween('age',[18,30]);
   
   
   $model->whereIn('id',[1,2,3]);
   $model->whereNotIn('id',[1,2,3]);
   
   
   $model->whereNull('mula');
   $model->whereNotNull('mula');
   
   
   $model->whereIsNull('status'); // where status is null
   $model->whereIsNotNull('status'); // where status is null
   
   
   $model->whereDate('date_time','YYYY-MM-DD');
   $model->whereYear('date_time','2018');
   $model->whereMonth('date_time','12');
   $model->whereDay('date_time','1');
   $model->whereTime('date_time','1:00');
   // Another whereHas for relational model
   $model->whereHas('phone',function($q){
       $q->whereActive(1);
   });
   $model->whereDoesntHave('phone',function($q){
       $q->whereActive(0);
   });
   ```
   
## join your model with whatever you want
### Don't be stress to join with a model to another table,model,query query builder gives us opportunity to do different types of joining.
   ```php
   $model = Model::query();
   $model->join('table2','model.id','=','model.user_id');
   $model->leftJoin('table2','model.id','=','model.user_id');
   $model->join('table2',function($q){
       $q->on('model.id','=','model.user_id');
       $q->whereStatus('active')
   );
   $model->join(DB::raw("(SELECT * FROM omuk where tomuk IS NULL) O"),function($q){
       $q->on('O.id','=','K.name')
   })
   ```
## Use of Ordering, Grouping, Limit, Offset
   ```php
   $query = Model::query();
   $query->orderBy('column1','asc')->orderBy('column2','desc');
   $query->orderByRaw("(column1 - column3) asc, column2 desc");
   $query->latest(); // 'created_at asc'
   $query->oldest(); // 'created_at desc'
   $query->inRandomOrder(); // random result
   
   
   $query->groupBy('column','column2');
   
   
   $query->skip(5); // skip and take both 
   $query->take(2);
   $query->offset(5); // offset and limit both
   $query->limit(2);
   ``` 
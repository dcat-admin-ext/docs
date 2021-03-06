# BETA version update log

## v2.0.19-beta

Release date 2021/2/21

To upgrade, step-by-step execute the following command and clear the browser cache
```bash
composer remove dcat/laravel-admin
composer require dcat/laravel-admin:"2.0.19-beta"
php artisan admin:publish --assets --migrations --force
php artisan migrate
```

### BUG FIXES

1. fix ``no access`` problem when requesting ``built-in api`` in non-admin role
2. fix the problem of using time range form in popup window to report error




## v2.0.18-beta

Released on 2021/2/20

To upgrade, run the following commands step by step and clear the browser cache
```bash
composer remove dcat/laravel-admin
composer require dcat/laravel-admin:"2.0.18-beta"
php artisan admin:publish --assets --migrations --force
php artisan migrate
```

### Feature improvements


**1. Add horizontal layout at the top of the menu (Horizontal)**

Set the value of the configuration parameter `admin.layout.horizontal_menu` to `true` to enable this feature, the effect is as follows

![](https://cdn.learnku.com/uploads/images/202102/20/38389/SpmXMujJ3D.png!large)

**Permission middleware and skip login judgment can fill in the route alias and do not need to increase the prefix **

Configuration file and permission setting route alias without filling in the route prefix

```php
    'permission' => [
        ...
    
        // Skip the permissions
        'except' => [
            // You can fill in the route alias directly, and you don't need to write the route prefix
            'custom.users',
        ],

    ],
```

**3. Add `_index` field to save row number for data table row data**

The `_index` field is used to save the row number, starting from `0`, and is used as follows

``` php
// Use in the display callback
$grid->column('serial number')->display(function () {
    return $this->_index + 1;
});


// used in a row action
$grid->actions(function ($actions) {
    $index = $this->_index;
    
    ...
});
```


**4. Rename markdown component static resource aliases to avoid conflicts with custom blade tags**

**5. Add configuration parameter `admin.menu.default_icon` to set the default menu icon**

`admin.menu.default_icon` is used to set the default menu icon, the default value is `feather icon-circle`

**6. Add new block locations `NAVBAR_BEFORE` and `NAVBAR_AFTER`**

```php
use Dcat\Admin\Admin;

// Output content to the front of the top navigation bar
admin_inject_section(Admin::SECTION['NAVBAR_BEFORE'], view('...'));

// Output content after the top navigation bar
admin_inject_section(Admin::SECTION['NAVBAR_AFTER'], view('...'));
```

**6. Optimize form field selector code**


### BUG FIXES

1. fix the problem of abnormal display of `new` tag on the extension management page [#1044](https://github.com/jqhph/dcat-admin/issues/1044)
2. fix the problem of error reported after successful file uploading and direct deletion [#1058](https://github.com/jqhph/dcat-admin/issues/1058)
3. repair the abnormal input value problem of `Form::number` form after using `min` and `max` methods



## v2.0.17-beta

Release date 2021/2/5

To upgrade, step-by-step execute the following commands and clear the browser cache
```bash
composer remove dcat/laravel-admin
composer require dcat/laravel-admin:"2.0.17-beta"
php artisan admin:publish --assets --migrations --force
php artisan migrate
```

### Function improvement

**1. Optimize the table sorting function**

Support `orderBy` to sort directly using related table fields, note that only `one to one` and `one to many` related relationships are supported here

```php
$grid->model()->orderBy('profile.age');
```

**2. Add the function of customizing `parent_id` value in the model tree and tree table**

Model tree and tree table can be customized in `model` with `parent_id` value, the default value is `0`.
```php
class Category extends Model
{
	use ModelTree;

    // Set the default parent_id to A
	protected $defaultParentId = 'A';
}
```

**3. Data details `file` supports displaying multiple files**

[#985](https://github.com/jqhph/dcat-admin/pull/985)

```php
$show->field('...')->files();
```

Result

![](https://cdn.learnku.com/uploads/images/202102/02/38389/B0a2qZEBUL.png!large)


**4.`Form::input` supports array batch settings**

```php
$form->submitted(function ($form) {
    $form->input(['k1' => 'v1', 'k2' => 'v2' ...]);
});
```

**5. Extension management supports `logo` and `alternate display`**

Refer to the documentation [extensions](extension-dev.md#logo) for detailed usage


**6. Add admin_route method to get URL by alias**

The `app/Admin/routes.php` route is registered as follows
```php
Route::group([
    'prefix'        => config('admin.route.prefix'),
    'namespace'     => config('admin.route.namespace'),
    'middleware'    => config('admin.route.middleware'),
], function (Router $router) {
	// Set alias
	$router->resource('users', 'UserController', [
		'names' => ['index' => 'my-users'],
	]);

});
```

Get URL by alias

```php
// Get the url
$url = admin_route('my-users');

// Determine the route
$isUsers = request()->routeIs(admin_route_name('users'));
```

**JsonResponse::location allows not to pass parameters**

The current page will be automatically refreshed after ``1`` seconds if no parameters are passed

```php
return Admin::json()->success('operation successful')->location();
```

**9.Page LayoutLayout\Column support equal width layout**

Equal-width layout is used when the column width is set to `0` [#1018](https://github.com/jqhph/dcat-admin/pull/1018)

```php
use Dcat\Admin\Layout\Row;
use Dcat\Admin\Layout\Content;

return Content::make()
	->body(function (Row $row) {
	    $row->column(0, view('...'));
	});
```

**10. The page layout Layout\Row supports no-gutters property**

`.row` with `margin-left: -15px;margin-right: -15px;` attribute, you can eliminate this attribute by defining the `.no-gutters` attribute on `.row` so that the page is not `30px` extra wide, i.e. `<div class="row no- gutters"... `
```php
$content->row(function (Row $row) {
	// Enable no-gutters
	$row->noGutters();

	$row->column(9, function (Column $column) {
		$column->row($this->card(['col-md-12', 20], '#4DB6AC'));
		
		$column->row(function (Row $row) {
			// Enable no-gutters
			$row->noGutters();

			$row->column(4, $this->card(['col-md-4', 30], '#80CBC4'));
			$row->column(4, $this->card(['col-md-4', 30], '#4DB6AC'));
			$row->column(4, function (Column $column) {
				$column->row(function (Row $row) {
					// Enable no-gutters
					$row->noGutters();

					$row->column(6, $this->card(['col-md-6', 30], '#26A69A'));
					$row->column(6, $this->card(['col-md-6', 30], '#26A69A'));
				});
			});
		});
	});
});
```

The effect is as follows

![](https://cdn.learnku.com/uploads/images/202102/05/38389/4YlO8aOPCW.jpg!large)

**11. Retain the get parameter of the URL after deleting data from the form**

In the previous version, the `get` parameter of the `URL` was lost after deleting data, resulting in jumping back to the first page of the form. This version has optimized this feature, and the `get` parameter of the `URL` is still retained after deletion [#961](https://github.com/jqhph/dcat-admin/issues/ 961)


**12. Refactor file upload front-end code**

This feature is a technical optimization, this version refactored the file upload front-end code, split the code to make it easier to read and maintain

### BUG FIXES

1. fix `MultipleSelect` form style exception problem [#967](https://github.com/jqhph/dcat-admin/issues/967)
2. fix the abnormal problem of using `select2` form after loading `markdown` component [#990](https://github.com/jqhph/dcat-admin/issues/990)
3. fix the problem of losing traditional Chinese file name saved under `Linux` server when uploading files [#993](https://github.com/jqhph/dcat-admin/issues/993)
4. fix the problem that `Widgets\Dropdown::click` cannot display default options
5. fix the problem of `NaN` when clicking the plus/minus button when the text content of `number` component in `form` form is empty [#995](https://github.com/jqhph/dcat-admin/issues/995)
6. repair the problem that the picture preview fails to indicate that the translation file cannot be used
7. repair the problem of abnormal judgment of `Range` form of one-to-one association relationship using validation rules
8. repair the problem of route alias conflict under multiple applications



## v2.0.16-beta

Release date 2021/1/11

To upgrade, step-by-step execute the following command
```bash
composer remove dcat/laravel-admin
composer require dcat/laravel-admin:"2.0.16-beta"
php artisan admin:publish --assets --migrations --force
php artisan migrate
```


### Breaking changes

**1. Adjusted `disableHorizontal` to `horizontal` for form fields **

Change the form field layout to `horizontal`, this is enabled by default and is used as follows

```php
// Disable the horizontal layout
$form->text('...')->horizontal(false);
```

### Feature improvements

**1. Enhance the sortable function of form fields**

Make form fields support sortable for relational table fields and `json` fields

> Note that associative relationships only support `hasOne` and `belongsTo` types of field sorting, and do not support multiple levels of nesting!

```php
// Sorting fields in the associated table
$grid->column('profile.age')->sortable();

// Specify the name of the field to be sorted
$grid->column('my_age')->sortable('profile.age');

// json field sorting
$grid->column('options.price')->sortable('options->price');
// Sort the json fields of the association table
$grid->column('profile.options.price')->sortable('profile.options->price');
```

Support `MySql` ```order by cast(`{field}` as {type})``` usage

```php
$grid->column('profile.age')->sortable(null, 'SIGNED');

$grid->column('profile.options.price')->sortable('profile.options->price', 'SIGNED');
```


**2. Add admin_exist function to replace exit**

`admin_exist` is used to interrupt the execution of the program and respond to the browser with data for display, instead of `exit` and `die`, the following is a brief description of usage


Usage 1, return `Content` layout object, this usage can be used to return error messages to the front end
```php
use Dcat\Admin\Widgets\Alert;
use Dcat\Admin\Layout\Content;

// Interrupt the program and display a custom page to the front end
admin_exit(
    Content::make()
        ->title('title')
        ->description('description')
        ->body('Page content 1')
        ->body(Alert::make('Server error~', 'Error')->danger())
);
```

The effect is as follows

![](https://cdn.learnku.com/uploads/images/202101/11/38389/FLg6C7kwRq.png!large)

Usage 2, returning `json` formatted data, often used for `api` request interception for form submissions, or `api` request interception for `Action`

```php
use Dcat\Admin\Admin;

admin_exit(
    Admin::json()
        ->success('succeeded')
        ->refresh()
        ->data([
            ...
        ])
);

// Of course you can also respond directly to the array
admin_exit([
   ...
]);
```

Usage 3, direct corresponding `Response` object or string

```php
admin_exit('Hello world');

admin_exit(response('Hello world', 500));
```


**3. Add Show\Field::bool() and Show\Field::bold() methods**

Show `✓` if the field value is true, otherwise show `✗` [#940](https://github.com/jqhph/dcat-admin/pull/940)

```php
$show->field('...')->bool();
```

Bolded field values

```php
$show->field('...') ->bold();

// Specify the color
$show->field('...') ->bold(admin_color()->primary());
```

**4. Add Form\Footer::view() method**

The ``Form\Footer::view()` method allows you to customize the bottom view of a data form [#957](https://github.com/jqhph/dcat-admin/pull/957)

```php
$form->footer(function ($footer) {
    $footer->view('...' , [...]) ;
});
```


**5. Add form default to show specified Tab function**

```php
// Show the Tab with the title Title 2 by default
$form->getTab()->active('Title2');
// You can also specify an offset
$form->getTab()->activeByIndex(1);

$form->tab('Heading 1', function ($form) {
    ...
});

$form->tab('Title2', function ($form) {
    ...
});
```

**6. Add form Form\Row::horizontal() method**

Set the layout to ``horizontal``

```php
$form->row(function (Form\Row $form) {
	$form->horizontal();

	...
});
```

**7. Form Modal adds custom icon functionality**


```php
$grid->column('...') ->modal(function ($modal) {
    // Custom icons
    $modal->icon('feather icon-x');
    
    return ... ;
});
```


**8. Add route domain restriction configuration**

You can restrict the domain name of a route by configuring the parameter `admin.route.domain`, opening the configuration file `config/admin.php`

```php
    'route' => [
        'domain' => env('ADMIN_ROUTE_DOMAIN'),

        'prefix' => env('ADMIN_ROUTE_PREFIX', 'admin'),

        'namespace' => 'App\\Admin\\Controllers',

        'middleware' => ['web', 'admin'],
    ],
```


**9. Add enable or disable configuration for admin.session middleware**

After `2.0` version `admin.session` middleware is no longer enabled by default, if your application has both frontend and backend, you need to enable `admin.session` middleware, otherwise it will cause front and backend `session` conflict problem.

Set the value of the configuration parameter `admin.route.enable_session_middleware` to `true` to enable it
```php
    'route' => [
        'domain' => env('ADMIN_ROUTE_DOMAIN'),

        'prefix' => env('ADMIN_ROUTE_PREFIX', 'admin'),

        'namespace' => 'App\Admin\\Controllers',

        'middleware' => ['web', 'admin'],
        
        // enable admin.session middleware
        'enable_session_middleware' => true,
    ],
```

### BUG FIXES

1. fix the problem that `Model` is converted to `array` format in the first parameter of `Grid::header` and `Grid::footer` callback of data table
2. repair the problem that the color of file upload button cannot be changed when switching themes [#938](https://github.com/jqhph/dcat-admin/issues/938)
3. repair the invalid setting of the third parameter of `Widgets\Table` construction method
4. fix the problem of using `config(['admin.layout.color' => '...']) in `app/Admin/bootstrap.php` ' Override theme color may be invalid
5. fix the problem of invalid data table filter reset association form fields [#949](https://github.com/jqhph/dcat-admin/issues/949)
6. repair the problem of abnormal display of `group` function of table filter [#929](https://github.com/jqhph/dcat-admin/issues/929)
7. fix the problem that only the first set `title` is displayed in all popups when there are multiple `selectTable` forms on the page [#926](https://github.com/jqhph/dcat-admin/issues/926)

## v2.0.15-beta

Release date 2021/1/3

To upgrade method, step by step execute the following command
```bash
composer remove dcat/laravel-admin
composer require dcat/laravel-admin:"2.0.15-beta"
php artisan admin:publish --assets --migrations --force
php artisan migrate
```

### Feature improvements

**1. Upgrade select2 to v4.1.x-beta version**


Upgrade `select` component to `v4.1.x-beta`, make `tags` form experience better, and support multi-language translation.

**Widgets/Modal added vertical centering and scrollable popups**.

Use as follows [#901](https://github.com/jqhph/dcat-admin/pull/901)

```php
$modal = Modal::make()
    ->xl()
    ->centered() // Set the pop-up window to be vertically centered
    ->scrollable() // Set the content of the pop-up window to be scrollable
    ->title(...)
    ->body(...);
```

**3. `Admin::requiredAssets` supports passing dynamic parameters**

```php
use Dcat\Admin\Admin;

// Register front-end component aliases
// {lang} is a dynamic parameter
Admin::asset()->alias('@test', [
    'js' => ['/vendor/test/js/{lang}.min.js'],
]);

// {lang} will be replaced with zh_CN
Admin::requireAssets('@test', ['lang' => 'zh_CN']);
// It can also be used like this
Admin::requireAssets('@test?lang=zh_CN');
```


### Bug修复

1. Fix the problem that the form `block` layout cannot save data [#883](https://github.com/jqhph/dcat-admin/issues/883)
2. Fix `currency` failure under `hasMany` form [#886](https://github.com/jqhph/dcat-admin/issues/886)
3. Repair the problem of automatically jumping to the detail page after saving the data form [#893](https://github.com/jqhph/dcat-admin/issues/893)
4. Fix the problem that `editor` form can not clear the data [#895](https://github.com/jqhph/dcat-admin/issues/895)
5. Fix the `required` validation exception of `tags` under `hasMany` form [#905](https://github.com/jqhph/dcat-admin/issues/905)
6. Repair the problem of all files being emptied when deleting a single file in multi-file upload form [#914](https://github.com/jqhph/dcat-admin/issues/914)
7. fix the problem that form fields cannot use model accessor



## v2.0.14-beta

Release Date 2020/12/24

To upgrade the method, step by step execute the following command
```bash
composer remove dcat/laravel-admin
composer require dcat/laravel-admin:"2.0.14-beta"
php artisan admin:publish --assets --migrations --force
php artisan migrate
```

### Function improvement

**1. Optimize the error message prompt for file upload failure**

In the old version, the error message for file upload failure was not very clear, which made it difficult to define the reason for the error, so in this version, the error message is optimized, and the specific reason will be displayed once the file upload fails.

### Bug fixes

1. fix the problem that the form field conflicts with the model `casts` attribute and the abnormal display problem using string splicing in the `display` closure [#876](https://github.com/jqhph/dcat-admin/issues/876)
2. fix the problem that the dynamic display function of the form cannot be used [#879](https://github.com/jqhph/dcat-admin/issues/879)
3. fix the problem of not being able to display editing data when using `Block` layout [#877](https://github.com/jqhph/dcat-admin/issues/877)



## v2.0.13-beta

Release Date 2020/12/23

To upgrade the method, step by step execute the following command
```bash
composer remove dcat/laravel-admin
composer require dcat/laravel-admin:"2.0.13-beta"
php artisan admin:publish --assets --migrations --force
php artisan migrate
```

### Bug fixes

1. fix the problem that the table may report an error when displaying related fields when the related data does not exist [#867](https://github.com/jqhph/dcat-admin/issues/867)
2. fix the problem that `display` method is invalid when the table uses data repository to return arrays or non-model `collection` [#869](https://github.com/jqhph/dcat-admin/issues/869)


## v2.0.12-beta

Release Date 2020/12/22

To upgrade the method, step by step execute the following command
```bash
composer remove dcat/laravel-admin
composer require dcat/laravel-admin:"2.0.12-beta"
php artisan admin:publish --assets --migrations --force
php artisan migrate
```

### Disruptive changes

**1. image/file upload form `removeable` renamed to `removable`**

```php
$form->file('...')->removable();
```

### Feature improvement

**1.Support PHP8.0**

**2. Image/file upload form supports listening to WebUploader events**

The `on` method can listen to [WebUploader file upload related events](http://fex.baidu.com/webuploader/doc/index.html#WebUploader_Uploader_events)

```php
$form->file('...')
	->on('startUpload', <<<JS
		function () {
			console.log('File upload started...', this);
			
			// Attach custom parameters to the file upload interface before uploading files
			this.uploader.options.formData['custom_field'] = '...';
		}
JS
    )
	->on('uploadFinished', <<<JS
    	function () {
    		console.log('File upload is complete');
    	}
JS
    );
    
//       
```

**3. Listen for changes when a file is successfully uploaded or deleted**

You can listen for changes when a file **is uploaded successfully** or when a file **is deleted** by the following methods

```php
$file = $form->file('...');

Admin::script(
	<<<JS
$('{$file->getElementClassSelector()} .file-input').on('change', function () {
	console.log('Document changed', this.value);
});
JS
);
```

**4. Allow intercepting and responding to error messages in the uploading event**

Starting from this version, we support intercepting file uploads in the `uploading` event of the form and responding to error messages on the front-end.

```php
$form->uploading(function (Form $form) {
	return $form->response()->error('File upload failed, please try again!');
});
```



**5. Listen to selectTable selected value change**

```php
$selectTable = $form->selectTable('...')->from(...);

Admin::script(
	<<<JS
$('{$selectTable->getElementClassSelector()} input[type="hidden"]').on('change', function () {
	console.log('Change of selected values', this.value);
});
JS
);
```

**6. adjust the tree form no data return, cancel the return of 404 status code **

**7. Adjust the value type of row attribute of table displayer class to model**

**8. Dark mode details optimization**

```php
$grid->column(...)->modal(function () {
    // $this points to the model object
    dd($this);
});

$grid->actions(function () {
    // $this points to the model object
    dd($this);
});
```


**9. Optimize the problem of overflowing chart display in cards**

[#822](https://github.com/jqhph/dcat-admin/pull/822)


**10.widget component add when method**

```php
$modal = Dcat\Admin\Widgets\Modal::make()
	->when($condition, function ($modal) {
		// When the value of $condition is true, the logic inside the closure will be executed
	    $modal->xl();
	})
	->body(...)
	->render();
```



### Bug fixes

1. fix `Grid\Filter::group` can't keep selected state [#739](https://github.com/jqhph/dcat-admin/pull/793)
2. fix `Form::hasMany` problem of validating `required` even after form entry is deleted [#795](https://github.com/jqhph/dcat-admin/pull/795)
3. fix the problem that map form cannot be used
4. fix the problem that `guessClassFileName` will report error when `composer` class mapping file is generated and the class file is deleted
5. fix the problem of error when using `Fetched` event for data export [#815](https://github.com/jqhph/dcat-admin/issues/815)
6. fix the problem that `filter` cannot be reset after setting `Grid name`.
7. fix the problem that `select2` can't use Chinese language pack automatically [#839](https://github.com/jqhph/dcat-admin/issues/839)
8. fix the problem that `continue to create` and `continue to edit` jump route error [#814](https://github.com/jqhph/dcat-admin/issues/814)
9. fix `range` form setting `rules` invalid when one-to-one association is enabled
10. fix the problem that time filtering dropdown will be blocked when `fixColumns` is enabled [#833](https://github.com/jqhph/dcat-admin/issues/833)
11. fix the problem that menu `fa` icon can't be aligned automatically [#758](https://github.com/jqhph/dcat-admin/pull/758)
12. fix the problem of submitting errors with `hasMany` in `row` layout [#801](https://github.com/jqhph/dcat-admin/issues/801)
13. fix form `hasMany` can't use `select` linkage [#769](https://github.com/jqhph/dcat-admin/issues/769)



## v2.0.11-beta

Release date 2020/12/06

To upgrade, step-by-step execute the following command
```bash
composer remove dcat/laravel-admin
composer require dcat/laravel-admin: "2.0.11-beta"
php artisan admin:publish --assets --force
php artisan admin:publish --migrations --force # table structure changes
php artisan migrate
```


### Bug fixes

1. fix `Dcat.init` listener failure due to repeated page refreshes using `pjax`
2. fix `admin:export-seed --users` which generates redundant code
3. repair the problem of abnormal jumping after saving the form editing page
4. repair the problem that `hasMany` will add data repeatedly if you choose to continue editing on the form page
5. fix the problem that the original `select2 config` is lost after linking `select` forms [#779](https://github.com/jqhph/dcat-admin/issues/779)
6. fix `map` form loading exception problem [#764](https://github.com/jqhph/dcat-admin/issues/764)
7. fix the problem that the page cannot be refreshed automatically after the data is deleted by the batch delete function
8. fix the problem that `hasMany` form editing page cannot display `row` and `column` layouts properly
9. fix the problem that `Dcat.init` listener will be unbound by asynchronous pop-up window
10. fix a bug that the table toolbar dropdown menu is blocked by the fixed list pane [#728](https://github.com/jqhph/dcat-admin/issues/728)
11. fix a problem where cached content is still read when `showColumnSelector` is disabled

### Functional improvements

**1. Add title parameter to Form::divider**.

Add `title` parameter to display the title in the middle of the divider, usage

``` php
$form->divider('title');
```


**2. Grid::footer and Grid::header adjusted to support multiple callbacks**

``` php
$grid->header(...) ;

$grid->header(...) ;

$grid->header(...) ;
```

**3. Optimize form specification filters and select form styles**


## v2.0.10-beta


Posted on 2020/11/29

To upgrade the method, execute the following step-by-step commands
```bash
composer remove dcat/laravel-admin
composer require dcat/laravel-admin:"2.0.10-beta"
php artisan admin:publish --assets --force
php artisan admin:publish --migrations --force # Table structure changed.
php artisan migrate
```
### Functional improvements

**1. Add a prompt window in the upper right corner of the form to display field validation error messages**.

This feature is enabled by default and can be disabled by the `validationErrorToastr` method.

```php
$form->validationErrorToastr(false);
```

**2.Add Tree::maxDepth method to limit the maximum level of the model tree **.

```php
$tree->maxDepth(5);
```

**3. Optimize the export function, support title settings associated with the relational fields and automatically read the title of the grid column **.

In the current version, exported columns are the same as `column` columns by default, so you no longer need to set the exported column name and translation manually, and the associated relational fields are supported.

```php
$grid->column('id');
$grid->column('name');
...

// Default is the same as the column above
$grid->export();
```

**4. Add a resetButton and submitButton method to the tool form**.

```php
// Disable the Reset and Submit buttons
$form->resetButton(false);
$form->submitButton(false);
```

**5. Add parameters to the `disable` and `readOnly` methods of the form fields to control whether they are enabled or not**.

```php
// pass false to disable
$form->text(...)->disable(false);
```

**6. Adding `withDeleteData` to file uploads allows users to set request parameters and add primary key fields to the upload and delete interfaces**.

The `withDeleteData` method allows you to pass custom parameters to the file delete interface.

```php
$form->file(...)->withDeleteData(['key' => 'value]);
```

**7. Add `embeds` form to disable display of titles**.

The second parameter, passed as `false`, does not display the title.

```php
$form->embeds('field', false, function ($form) {
    ...
});
```

**8. Rewrite some of the unit test cases to support 2.x usage**.

### Bug fixes

1. fix the problem that existing permissions cannot be selected on admin detail page.
2. fix the problem of `admin:export-seed` command exporting `seeder` class name exception.
3. fix the form deletion jump exception
4. fix the problem of form jumping exception when continuing to edit.
5. fix the problem that parent table fields cannot be saved when parent table and `hasMany` have the same field name.
6. fix the problem that the style of selected submenu is abnormal in dark mode [#712](https://github.com/jqhph/dcat-admin/issues/712)
7. fix the problem that form dynamic display function is invalid under form `block` layout [#723](https://github.com/jqhph/dcat-admin/issues/723)
8. optimize the display of `selectOptions` hierarchy and solve the problem of prefix rendering increasing with the hierarchy depth index [#618](https://github.com/jqhph/dcat-admin/issues/618)
9. fix the problem that `admin_view` does not return data.
10. fix the problem that links set by `select` form, `ajax` and `load` cannot take parameters [#745](https://github.com/jqhph/dcat-admin/issues/745)
11. fix the problem that the `handle` method of the table row operation `action` can only get `id` of the last row of data.
12. fix the problem that `list` form edit page cannot delete existing data [#759](https://github.com/jqhph/dcat-admin/issues/759)
13. Fix the error in the `embeds` scope form's `name` attribute.
    
## v2.0.9-beta

Posted on 2020/11/18

To upgrade the method, execute the following step-by-step commands
```bash
composer remove dcat/laravel-admin
composer require dcat/laravel-admin:"2.0.9-beta"
php artisan admin:publish --assets --force
php artisan admin:publish --migrations --force # 表结构有变动
php artisan migrate
```


**Bug Fix**.

1. fix the function failure of form `filter::select` form remote load `/load/ajax` etc.
2. fix the front-end `moment-timezone` component path loading error [#701](https://github.com/jqhph/dcat-admin/issues/701)
3. fix the problem of not being able to set permissions due to `Form::tree` not being able to save data.
4. fix the problem of filling default value exception when the `hasMany` form has the same field name as the parent table.
5. repair the problem of adding new page report when form `tab` layout is nested with `row` layout [#648](https://github.com/jqhph/dcat-admin/issues/648)
6. fix the problem of not being able to get all the values under `range` after submission when the form has `range` type field.
7. fix the problem that select2 component is invalid when `Form::select` uses form linkage.


## v2.0.8-beta

Posted on 2020/11/16

To upgrade the method, execute the following command step by step
```bash
composer remove dcat/laravel-admin
composer require dcat/laravel-admin:"2.0.8-beta"
php artisan admin:publish --assets --force
php artisan admin:publish --migrations --force # Table structure changed
php artisan migrate
```

As a supplement to `2.0.7-beta`, the following issues are fixed in this release

1. fix the problem of not being able to view permissions on the admin page
2. fix the problem of form block layout failure
3. fix the problem of abnormal initialization of file upload form.
4. fix the problem that some form fields don't support rendering multiple fields on a single page at the same time.


## v2.0.7-beta

Posted on 2020/11/15

To upgrade the method, execute the following command step by step
```bash
composer remove dcat/laravel-admin
composer require dcat/laravel-admin:"2.0.7-beta"
php artisan admin:publish --assets --force
php artisan admin:publish --migrations --force # Changes in table structure
php artisan migrate
```

**Functional improvements**

1. Introduce the [jquery.initialize](https://github.com/pie6k/jquery.initialize) component to listen to dynamically generated page elements and set a callback, the following is a simple example to demonstrate usage.

In older versions, if an element is dynamically generated by `JS`, and we need to bind a click event to that element, then we usually do this

```html
<div class="selector">test</div>

<script>
Dcat.ready(function () {
    // You need to be off first and then on, otherwise page refresh will cause double bind problem.
    $(document).off('click', '.selector').on('click', '.selector', function () {
        ...
    })
});
</script>
```

The above approach is troublesome, you need to `off` and then `on`; secondly, you can not do some special treatment for dynamically generated elements, for example, if you want to change the background color after `.selector` is generated, there is no way to do this.

In this version we can use the `Dcat.init` method to listen to the dynamically generated elements.

```html
<div class="selector">test</div>

<script>
Dcat.ready(function () {
    // $this is the jquery dom object of the current element.
    // id is the id attribute of the current element, if the current element has no id, a random id will be generated automatically.
    Dcat.init('.selector', function ($this, id) {
        // Change the background color of the element.
        $this.css({background: "#fff"});
        
        // There's no need for off and then on again, because the anonymous function will only be executed once!
        $this.on('click', function () {
            ...
        });
    });
});
</script>
```

Thanks to the introduction of the [jquery.initialize](https://github.com/pie6k/jquery.initialize) component, in the current version we have optimized the front-end code of the form component to support the dynamically generated form type `HasMany` more easily. Significantly reduces the complexity of the code.


2.Form::hasMany and Form::array forms support column and row layout

If there are more fields, you can use `column` and `row` layout to save space.

```php
$form->array('field', function (NestedForm $form) {
    $form->column(6, function (NestedForm $form) {
        $form->text('...');
        
        ...
    });
    
    $form->column(6, function (NestedForm $form) {
        ...
    });
});
```

3.Routes configured with the admin.auth.except parameter do not require authentication privileges [#673](https://github.com/jqhph/dcat-admin/issues/673)


4. Add when method to Form, Grid and Show field classes

Usage examples, similar to `Laravel QueryBuilder`s `when` method

in the table
```php
// Closing code will be executed when the first parameter is true.
$grid->column('title')->when(true, function (Grid\Column $column, $value) {
    $column->label();
});
```

form (document)
```php
// Closing code will be executed when the first parameter is true.
$form->text('email')->when(true, function (Form\Field\Text $text, $value) {
    $text->type('email');
});
```

5. Administrator model add canSeeMenu method to control whether the menu is visible or not.

```php
<?php

namespace App\Models;

use Dcat\Admin\Models\Administrator as Model;

class Administrator extends Model
{
    /**
     * :: Control whether the menu is visible or not, return true by default
     * 
     * @param array|\Illuminate\Database\Eloquent\Model $menu menu node
     * @return bool
     */
    public function canSeeMenu($menu)
    {
        return true;
    }
}
```

6.Add admin_script、admin_style、admin_js、admin_css and admin_require_assets functions

```php
// Equivalent to Admin::script
admin_script('console.log(xxx)');

// Equivalent to Admin::style
admin_style('.my-class {color: red}');

// Equivalent to Admin::js() 
admin_js(['@admin/xxx.js']);

// Equivalent to Admin::css() 
admin_css(['@admin/xxx.css']);

// Equivalent to Admin::requireAssets() 
admin_require_assets(['@select2']);
```

7. simplify the action (Action) of the `JS` code logic, to remove the memory of the `selector` function

**BUG FIX**

1. Fix anomaly in the orderable form [#674](https://github.com/jqhph/dcat-admin/issues/674)
2. fix the JsonResponse methodIf error.
3. fix table, form, and data detail specifying `label` [#684](https://github.com/jqhph/dcat-admin/issues/684)
4. fix the problem that the table `Grid::rows` callback doesn't work properly.
5. fix the problem of some types of statistical cards failing to load asynchronously due to exceptions in adding `JS` code to widgets.
6. fix the getKey method exception for table row operations [#691](https://github.com/jqhph/dcat-admin/issues/691)
7. fix the problem of not being able to use the linkage function when there are multiple select forms in the page.
8. Fix the problem of the table not being able to refresh automatically after deleting data.


## v2.0.6-beta

Posted on 2020/11/7

To upgrade the method, execute the following command step by step
```bash
composer remove dcat/laravel-admin
composer require dcat/laravel-admin:"2.0.6-beta"
php artisan admin:publish --assets --force
php artisan admin:publish --migrations --force # 表结构有变动
php artisan migrate
```

**Breaking Changes**

1.`Form::tags` form is saved as `array` type by default
```php
// You need to convert the format you save to the database yourself
$form->tags('tag')->saveAsJson();
```

2.The session middleware is disabled by default

3.`Form\Tree::disableFilterParents` renamed to `Form\Tree::exceptParentNode`
```php
$form->tree('cate')->exceptParentNode(false);
```

4. Adjust the method name of the file upload form part
```php
// Enable chunked uploads, disableChunked changed to chunked
$form->image('avatar')->chunked(true);

// Enable auto-save field values, disableAutoSave changed to autoSave
$form->image('avatar')->autoSave(false);

// Enable the file deletion function, disableRemove changed to removable
$form->image('avatar')->removable(false);
```


**Functional improvements**

1.Code generator add field dragging sorting function, this method is contributed by partner [@codingyu](https://github.com/codingyu).

2. menu table to add `show` and `extension` field, `show` field is used to control whether to display the menu; `extension` field is used to mark whether to expand the menu

3.`Form::table`、`Form::array`、`Form::embeds` supports relational fields
```php
$form->table('profile.options', function ($form) {
    ...
});
```

4. Add vertical display of `Form::checkbox` and `Form::radio` form options.
```php
$form->checkbox('xxx')->inline(false)->options([...]);
```

5. Configuration file skip login and permission authentication allows configuration of routing aliases.
```php
'auth' => [
    'except' => [
        ...
        'user.login',
    ],
],
```

6.`Form\Row` adds `getKey` and `model` methods

7. Optimize the form filter select form selection effect, the default is not selected

8. Form tab layout optimization


**BUG FIX**
1. fix `Form::checkbox` problem when check/uncheck all options.
2. fix the problem that the default menu TITLE can not be translated in Taiwan Traditional Chinese. 
3. fix the problem that the `number` field in `NestedForm` is filtered when the input value is 0 [#634](https://github.com/jqhph/dcat-admin/issues/634)
4. fix the problem of getting primary key error when the model tree `RowAction` asynchronously processes the interface.
5. Fix the problem of table filters failing to reset the search value of the associated table fields [#650] (https://github.com/jqhph/dcat-admin/issues/650)
6. fix form filter multipleSelect form exception problem


## v2.0.5-beta 

Posted on 2020/10/29

BUG FIXES
1. fix the problem of table search multiple related table fields sql error [I232T7](https://gitee.com/jqhph/dcat-admin/issues/I232T7)
2. fix the problem of `Form::datetimeRange` form not being able to select logs.
3. Fix the problem of not being able to add multiple `Form::table` form fields [#627](https://github.com/jqhph/dcat-admin/issues/627)
4. fix the error in the form filter MultipleSelectTable.
5. Fix the problem of abnormal style of table specification filter.


## v2.0.4-beta 

Posted on 2020/10/27

BUG FIXES
1. fix the problem that the admin_javascript_json function will automatically empty-filter an array of null values.
2. fix the error of using tab layout for data form [#620](https://github.com/jqhph/dcat-admin/issues/620)
3. fix the problem of abnormal permissions for temporary directories generated by extended local installation [#625](https://github.com/jqhph/dcat-admin/issues/625)
4. fix the error when using html method to set view with script tag [#624](https://github.com/jqhph/dcat-admin/issues/624).
5. Fix the error of displaying data details using correlations (one-to-many) [#623](https://github.com/jqhph/dcat-admin/issues/623)
6. fix the dropdown menu calculation display position exception [#I22S2N](https://gitee.com/jqhph/dcat-admin/issues/I22S2N)


## v2.0.3-beta 

Posted on 2020/10/27

BUG FIXES
1. fix the problem of abnormal display of return information in form row editing
2. Fix invalid setting of `admin.auth.member` [#613](https://github.com/jqhph/dcat-admin/issues/613)
3. fix the abnormal Chinese translation problem of `editor` form [#611](https://github.com/jqhph/dcat-admin/issues/611)
4. fix the problem that `Filter::scope` can't filter pagination parameters when selecting filter item.
5. fix issues related to form event interception
6. Fix the problem of abnormal use of tree form [#619](https://github.com/jqhph/dcat-admin/issues/619)

Functional improvements
1. add the configuration of skip privileges and login authentication
2. Extending the service provider to include middleware and route-checking registrations
3. batch operation change event monitoring optimization
4. Increase `RowSelector` robustness to avoid errors due to unprocessed `json` array type fields [#609](https://github.com/jqhph/dcat-admin/pull/609)

## v2.0.2-beta 

Posted on 2020/10/21

BUG FIXES
1. Fix naming space exception in controller file generated by code generator #600 
2. fix the problem of configuration file logo path error
3. Fix the problem of invalid search for associated fields in tables
4. fix the problem of duplicate selectors generated by model tree operation
5. fix the problem of accessing permissionless page report
6. Fix the problem that table filter multipleSelect cannot select the value of the associated table field #603 
7. Fix invalid form tab layout #605 

Functional improvements
1. Auth\Permission to move to Http directory
2. Replace the json field in the data table with text
3. add login password error translation
4. add admin_javascript_json function, make most of the component configuration support passing JS code
5. Admin::color Add dark mode color.




## v2.0.1-beta 

Posted on 2020/10/20

BUG FIXES
- Fix data table filter search bug #599 
- Fix code generator error in generating controller base class namespace #600 

Functional improvements

- Code Generator adds page TITLE and breadcrumb translation functionality.
- Exception handling optimization
- Add admin_setting_array function.
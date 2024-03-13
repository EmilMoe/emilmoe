# Laravel Request Handler Package

The Laravel Request Handler package enhances request data handling within Laravel applications, providing a `RequestHandler` trait and a custom `Request` class. This package simplifies advanced input retrieval, including support for nested data, model data merging, and integration with Laravel's validation features.

## Features

- **Custom Request Handling**: Automate the merging of model data into request inputs, simplifying data management in your controllers.
- **Flexible Input Retrieval**: Utilize the `inputOrModel` method to seamlessly access request data, model attributes, or specify default values.
- **Graceful Validation Error Handling**: Enhance the user experience by efficiently repopulating forms with previously submitted data upon validation failures.

## Usage

### Editing Resources with Model Binding

Easily manage resource editing by automatically merging model data into the request, enabling straightforward access to both request inputs and model attributes:

```php
use App\Models\UserActivity;
use Illuminate\Http\Request;

class UserActivityController extends Controller
{
    use RequestHandler;

    public function edit(UserActivity $userActivity)
    {
        $this->mergeModelIntoRequest($userActivity);
        
        $category_id = $this->request()->inputOrModel('category_id', 'norm.activity.category.id');
        
        return view('userActivities.edit', compact('userActivity', 'category_id'));
    }
}
```

### Creating Resources

Simplify the creation of new resources by using the custom request functionality to retrieve input data, handling complex nested structures and validation errors effortlessly:

```php
use Illuminate\Http\Request;

class UserActivityController extends Controller
{
    use RequestHandler;

    public function store(Request $request)
    {
        $category_id = $this->request()->inputOrModel('category_id');
        
        // Perform validation and resource storage logic here...
        
        return redirect()->route('userActivities.index');
    }
}
```

### Handling Validation Errors

Enhance form submission handling by storing and repopulating forms with input data upon validation failures:

```php
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Contracts\Validation\Validator;
use Illuminate\Support\Facades\Session;

class YourCustomFormRequest extends FormRequest
{
    protected function failedValidation(Validator $validator)
    {
        // Store the entire request input in the session
        Session::put('validation_errors', request()->all());

        // Proceed with default failed validation handling
        parent::failedValidation($validator);
    }
}
```

In your controller, leverage the `RequestHandler` trait to merge any validation errors into the current request, ensuring forms are accurately repopulated:

```php
use Illuminate\Http\Request;

class YourController extends Controller
{
    use RequestHandler;

    public function create()
    {
        $inputValue = $this->request()->inputOrModel('your_input_key', 'default_value');
        
        // Return your create view with the form repopulated on validation failure
        return view('yourView.create', compact('inputValue'));
    }
}
```

## Configuration

Customize the package's behavior through the `config/requesthandler.php` file (if applicable), adjusting settings to fit your application's requirements.

## Support

For assistance, questions, or to report issues, please open an issue on the [GitHub repository](#) for this package.

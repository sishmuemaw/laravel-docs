# Views

- [View file များဖန်တီးခြင်း](#creating-views)
- [Passing Data To Views](#passing-data-to-views)
    - [Sharing Data With All Views](#sharing-data-with-all-views)
- [View Composers](#view-composers)

<a name="creating-views"></a>
## View file များဖန်တီးခြင်း

View file တွေမှာ သင့် applicationမှ ပြသချင်တဲ့ Html တွေပါဝင်ပြီး သင့်ရဲ့ presentation logic တွေကို controller(သို့)application logic တွေနဲ့ ခွဲခြားပေးပါတယ်။ View file တွေကို `resources/views` folder လမ်းကြောင်းအောက်မှာ သိမ်းထားပါတယ်။ နမူနာ view တစ်ခုကို အောက်မှာပြထားပါတယ်။

    <!-- View stored in resources/views/greeting.php -->

    <html>
        <body>
            <h1>Hello, {{ $name }}</h1>
        </body>
    </html>

ထို view file ကို `resources/views/greeting.php` လမ်းကြောင်းအောက်မှာသိမ်းထားပြီးလျှင် global `view` helper ကို အသုံးပြုကာ အောက်ပါအတိုင်း return ပြန်ပေးနိုင်ပါတယ်။

    Route::get('/', function () {
        return view('greeting', ['name' => 'James']);
    });

သင်မြင်ရတဲ့ အတိုင်းပါပဲ `view` helper ထဲကို ပေးလိုက်တဲ့ ပထမ argument က  `resources/views`လမ်းကြောင်းအောက်မှာရှိတဲ့ view file ရဲ့ fileနာမည် ဖြစ်ပါတယ်။
ဒုတိယ argument ကတော့ view file မှာပြန်အသုံးပြုနိုင်တဲ့ data တွေပါဝင်တဲ့ array ဖြစ်ပါတယ်။ ယခု နမူနာမှာတော့ ကျွန်တော်တို့ဟာ `name` ဆိုတဲ့ variable တစ်ခုကို ထည့်ပေးလိုက်ပြီးတော့ view file မှာ [Blade syntax](/docs/{{version}}/blade) ကိုအသုံးပြုပြီး ပြန်လည်ထုတ်ပြထားပါတယ်။

view file တွေကို `resources/views` လမ်းကြောင်းအောက်မှာ folder အဆင့်ဆင့် ခွဲပြီး သိမ်းလို့ရပါတယ်။  "Dot" သင်္ကေတဖြင့် အဆင့်ဆင့်ခွဲပြီးသိမ်းထားတဲ့ view file တွေကိုညွှန်ပြနိုင်ပါတယ်။ ဥပမာ သင့်ရဲ့ view file ကို `resources/views/admin/profile.php` လမ်းကြောင်းအတိုင်းသိမ်းထားမည်ဆိုလျှင် အောက်ပါအတိုင်း ပြန်ခေါ်နိုင်ပါတယ်။

    return view('admin.profile', $data);

#### view file ရှိ မရှိ စစ်ဆေးခြင်း

အကယ်လို့ view file ရှိမရှိစစ်ဆေးဖို့လိုလာလျှင် `View` facade ကို အသုံးပြုနိုင်ပါတယ်။ view file သာရှိနေခဲ့မည်ဆိုလျှင်  `exists` method က `true` return ပြန်ပေးမှာဖြစ်ပါတယ်။

    use Illuminate\Support\Facades\View;

    if (View::exists('emails.customer')) {
        //
    }

<a name="passing-data-to-views"></a>
## Passing Data To Views

As you saw in the previous examples, you may pass an array of data to views:

    return view('greetings', ['name' => 'Victoria']);

When passing information in this manner, `$data` should be an array with key/value pairs. Inside your view, you can then access each value using its corresponding key, such as `<?php echo $key; ?>`. As an alternative to passing a complete array of data to the `view` helper function, you may use the `with` method to add individual pieces of data to the view:

    return view('greeting')->with('name', 'Victoria');

<a name="sharing-data-with-all-views"></a>
#### Sharing Data With All Views

Occasionally, you may need to share a piece of data with all views that are rendered by your application. You may do so using the view facade's `share` method. Typically, you should place calls to `share` within a service provider's `boot` method. You are free to add them to the `AppServiceProvider` or generate a separate service provider to house them:

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\View;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            View::share('key', 'value');
        }

        /**
         * Register the service provider.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }

<a name="view-composers"></a>
## View Composers

View composers are callbacks or class methods that are called when a view is rendered. If you have data that you want to be bound to a view each time that view is rendered, a view composer can help you organize that logic into a single location.

For this example, let's register the view composers within a [service provider](/docs/{{version}}/providers). We'll use the `View` facade to access the underlying `Illuminate\Contracts\View\Factory` contract implementation. Remember, Laravel does not include a default directory for view composers. You are free to organize them however you wish. For example, you could create an `App\Http\ViewComposers` directory:

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\View;
    use Illuminate\Support\ServiceProvider;

    class ComposerServiceProvider extends ServiceProvider
    {
        /**
         * Register bindings in the container.
         *
         * @return void
         */
        public function boot()
        {
            // Using class based composers...
            View::composer(
                'profile', 'App\Http\ViewComposers\ProfileComposer'
            );

            // Using Closure based composers...
            View::composer('dashboard', function ($view) {
                //
            });
        }

        /**
         * Register the service provider.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }

> {note} Remember, if you create a new service provider to contain your view composer registrations, you will need to add the service provider to the `providers` array in the `config/app.php` configuration file.

Now that we have registered the composer, the `ProfileComposer@compose` method will be executed each time the `profile` view is being rendered. So, let's define the composer class:

    <?php

    namespace App\Http\ViewComposers;

    use Illuminate\View\View;
    use App\Repositories\UserRepository;

    class ProfileComposer
    {
        /**
         * The user repository implementation.
         *
         * @var UserRepository
         */
        protected $users;

        /**
         * Create a new profile composer.
         *
         * @param  UserRepository  $users
         * @return void
         */
        public function __construct(UserRepository $users)
        {
            // Dependencies automatically resolved by service container...
            $this->users = $users;
        }

        /**
         * Bind data to the view.
         *
         * @param  View  $view
         * @return void
         */
        public function compose(View $view)
        {
            $view->with('count', $this->users->count());
        }
    }

Just before the view is rendered, the composer's `compose` method is called with the `Illuminate\View\View` instance. You may use the `with` method to bind data to the view.

> {tip} All view composers are resolved via the [service container](/docs/{{version}}/container), so you may type-hint any dependencies you need within a composer's constructor.

#### Attaching A Composer To Multiple Views

You may attach a view composer to multiple views at once by passing an array of views as the first argument to the `composer` method:

    View::composer(
        ['profile', 'dashboard'],
        'App\Http\ViewComposers\MyViewComposer'
    );

The `composer` method also accepts the `*` character as a wildcard, allowing you to attach a composer to all views:

    View::composer('*', function ($view) {
        //
    });

#### View Creators

View **creators** are very similar to view composers; however, they are executed immediately after the view is instantiated instead of waiting until the view is about to render. To register a view creator, use the `creator` method:

    View::creator('profile', 'App\Http\ViewCreators\ProfileCreator');

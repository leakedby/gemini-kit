# Laravel 12 Skill

## Overview
Laravel 12 patterns, architecture, and modern PHP development best practices.

## Core Concepts

### 1. Application Structure
```
app/
├── Console/            # Artisan commands
├── Exceptions/         # Exception handling
├── Http/
│   ├── Controllers/    # Request handlers
│   ├── Middleware/     # HTTP middleware
│   └── Requests/       # Form requests
├── Models/             # Eloquent models
├── Providers/          # Service providers
└── Services/           # Business logic
resources/
├── views/              # Blade templates
├── js/                 # Frontend assets
└── css/
routes/
├── web.php             # Web routes
├── api.php             # API routes
└── console.php         # Console routes
```

### 2. Routing
```php
// Basic routes
Route::get('/users', [UserController::class, 'index']);
Route::post('/users', [UserController::class, 'store']);
Route::put('/users/{user}', [UserController::class, 'update']);
Route::delete('/users/{user}', [UserController::class, 'destroy']);

// Resource routes
Route::resource('posts', PostController::class);
Route::apiResource('posts', PostApiController::class);

// Route model binding
Route::get('/users/{user}', function (User $user) {
    return $user;
});

// Route groups with middleware
Route::middleware(['auth'])->group(function () {
    Route::get('/dashboard', DashboardController::class);
});
```

### 3. Eloquent ORM
```php
// Model definition
class Post extends Model
{
    protected $fillable = ['title', 'body', 'user_id'];

    protected $casts = [
        'published_at' => 'datetime',
        'metadata' => 'array',
    ];

    // Relationships
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }

    public function comments(): HasMany
    {
        return $this->hasMany(Comment::class);
    }

    public function tags(): BelongsToMany
    {
        return $this->belongsToMany(Tag::class);
    }
}

// Querying
$posts = Post::with('user', 'comments')
    ->where('published', true)
    ->orderByDesc('created_at')
    ->paginate(15);

// Scopes
public function scopePublished(Builder $query): void
{
    $query->where('published_at', '<=', now());
}

$posts = Post::published()->get();
```

### 4. Form Requests & Validation
```php
class StorePostRequest extends FormRequest
{
    public function authorize(): bool
    {
        return $this->user()->can('create', Post::class);
    }

    public function rules(): array
    {
        return [
            'title'   => ['required', 'string', 'max:255'],
            'body'    => ['required', 'string'],
            'tags'    => ['array'],
            'tags.*'  => ['exists:tags,id'],
        ];
    }
}

// Controller usage
public function store(StorePostRequest $request): RedirectResponse
{
    $post = Post::create($request->validated());
    return redirect()->route('posts.show', $post);
}
```

### 5. Jobs & Queues
```php
class ProcessPodcast implements ShouldQueue
{
    use Queueable;

    public function __construct(public Podcast $podcast) {}

    public function handle(AudioProcessor $processor): void
    {
        $processor->process($this->podcast);
    }

    public function failed(Throwable $exception): void
    {
        // Notify team of failure
    }
}

// Dispatching
ProcessPodcast::dispatch($podcast)->onQueue('processing');
ProcessPodcast::dispatchAfterResponse($podcast);
```

### 6. Events & Listeners
```php
// Event
class OrderShipped
{
    public function __construct(public Order $order) {}
}

// Listener
class SendShipmentNotification
{
    public function handle(OrderShipped $event): void
    {
        Mail::to($event->order->user)->send(new OrderShippedMail($event->order));
    }
}

// Dispatching
OrderShipped::dispatch($order);
```

### 7. API Resources
```php
class PostResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id'         => $this->id,
            'title'      => $this->title,
            'body'       => $this->body,
            'author'     => new UserResource($this->whenLoaded('user')),
            'created_at' => $this->created_at->toISOString(),
        ];
    }
}
```

### 8. Service Container & Dependency Injection
```php
// Binding in a service provider
$this->app->bind(PaymentGateway::class, StripeGateway::class);
$this->app->singleton(Cache::class, RedisCache::class);

// Automatic injection
class OrderController extends Controller
{
    public function __construct(private PaymentGateway $gateway) {}

    public function checkout(Order $order): JsonResponse
    {
        $this->gateway->charge($order);
        return response()->json(['status' => 'paid']);
    }
}
```

## Laravel 12 Features

### Simplified Application Bootstrapping
```php
// bootstrap/app.php
return Application::configure(basePath: dirname(__DIR__))
    ->withRouting(
        web: __DIR__.'/../routes/web.php',
        api: __DIR__.'/../routes/api.php',
        commands: __DIR__.'/../routes/console.php',
    )
    ->withMiddleware(function (Middleware $middleware) {
        $middleware->validateCsrfTokens(except: ['/stripe/*']);
    })
    ->withExceptions(function (Exceptions $exceptions) {
        $exceptions->render(function (InvalidOrderException $e) {
            return response()->json(['error' => $e->getMessage()], 422);
        });
    })->create();
```

### Typed Configuration Access
```php
$name  = config()->string('app.name');
$debug = config()->boolean('app.debug');
$port  = config()->integer('app.port', 8080);
```

### Starter Kits
Laravel 12 ships with official starter kits using React, Vue, or Livewire with Tailwind CSS, providing authentication scaffolding out of the box:
```bash
composer create-project laravel/laravel my-app
php artisan install:api        # API authentication (Sanctum)
php artisan install:broadcasting  # Reverb broadcasting
```

## Best Practices
- Use Form Requests for all validation logic
- Prefer Eloquent scopes over raw query logic in controllers
- Use `$fillable` (allowlist) over `$guarded` (denylist) for mass assignment
- Apply the Service layer pattern for complex business logic
- Use Laravel Pint for automated code style enforcement
- Run `php artisan optimize` in production
- Leverage Octane (Swoole/FrankenPHP) for high-performance deployments
- Use `php artisan about` to inspect application environment

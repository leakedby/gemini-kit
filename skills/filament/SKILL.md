# Filament v4 Skill

## Overview
Filament v4 admin panel, form builder, table builder, and UI component patterns for Laravel.

## Core Concepts

### 1. Panel Setup
```php
// app/Providers/Filament/AdminPanelProvider.php
class AdminPanelProvider extends PanelProvider
{
    public function panel(Panel $panel): Panel
    {
        return $panel
            ->default()
            ->id('admin')
            ->path('admin')
            ->login()
            ->colors(['primary' => Color::Amber])
            ->discoverResources(in: app_path('Filament/Resources'), for: 'App\\Filament\\Resources')
            ->discoverPages(in: app_path('Filament/Pages'), for: 'App\\Filament\\Pages')
            ->discoverWidgets(in: app_path('Filament/Widgets'), for: 'App\\Filament\\Widgets')
            ->middleware([
                EncryptCookies::class,
                AddQueuedCookiesToResponse::class,
                StartSession::class,
                AuthenticateSession::class,
                ShareErrorsFromSession::class,
                VerifyCsrfToken::class,
                SubstituteBindings::class,
                DisableBladeIconComponents::class,
                DispatchServingFilamentEvent::class,
            ])
            ->authMiddleware([Authenticate::class]);
    }
}
```

### 2. Resources
```php
// app/Filament/Resources/PostResource.php
class PostResource extends Resource
{
    protected static ?string $model = Post::class;
    protected static ?string $navigationIcon = 'heroicon-o-document-text';
    protected static ?string $navigationGroup = 'Content';

    public static function form(Form $form): Form
    {
        return $form->schema([
            Forms\Components\TextInput::make('title')
                ->required()
                ->maxLength(255)
                ->columnSpanFull(),

            Forms\Components\RichEditor::make('body')
                ->required()
                ->columnSpanFull(),

            Forms\Components\Select::make('user_id')
                ->relationship('user', 'name')
                ->searchable()
                ->preload()
                ->required(),

            Forms\Components\Toggle::make('published')
                ->default(false),

            Forms\Components\DateTimePicker::make('published_at'),
        ]);
    }

    public static function table(Table $table): Table
    {
        return $table
            ->columns([
                Tables\Columns\TextColumn::make('title')
                    ->searchable()
                    ->sortable(),

                Tables\Columns\TextColumn::make('user.name')
                    ->label('Author')
                    ->sortable(),

                Tables\Columns\IconColumn::make('published')
                    ->boolean(),

                Tables\Columns\TextColumn::make('created_at')
                    ->dateTime()
                    ->sortable()
                    ->toggleable(isToggledHiddenByDefault: true),
            ])
            ->filters([
                Tables\Filters\TrashedFilter::make(),
                Tables\Filters\SelectFilter::make('user')
                    ->relationship('user', 'name'),
            ])
            ->actions([
                Tables\Actions\EditAction::make(),
                Tables\Actions\DeleteAction::make(),
            ])
            ->bulkActions([
                Tables\Actions\BulkActionGroup::make([
                    Tables\Actions\DeleteBulkAction::make(),
                    Tables\Actions\ForceDeleteBulkAction::make(),
                    Tables\Actions\RestoreBulkAction::make(),
                ]),
            ]);
    }

    public static function getRelations(): array
    {
        return [
            CommentsRelationManager::class,
        ];
    }

    public static function getPages(): array
    {
        return [
            'index'  => Pages\ListPosts::route('/'),
            'create' => Pages\CreatePost::route('/create'),
            'edit'   => Pages\EditPost::route('/{record}/edit'),
        ];
    }
}
```

### 3. Form Components
```php
// Text inputs
Forms\Components\TextInput::make('name')
    ->required()
    ->minLength(2)
    ->maxLength(255)
    ->placeholder('John Doe'),

// Select & relationships
Forms\Components\Select::make('status')
    ->options([
        'draft'     => 'Draft',
        'published' => 'Published',
        'archived'  => 'Archived',
    ])
    ->default('draft'),

// File uploads
Forms\Components\FileUpload::make('avatar')
    ->image()
    ->imageEditor()
    ->disk('public')
    ->directory('avatars'),

// Repeater
Forms\Components\Repeater::make('items')
    ->schema([
        Forms\Components\TextInput::make('name')->required(),
        Forms\Components\TextInput::make('price')->numeric()->prefix('$'),
    ])
    ->columns(2)
    ->minItems(1),

// Layout components
Forms\Components\Section::make('Metadata')
    ->schema([
        Forms\Components\KeyValue::make('meta'),
    ])
    ->collapsed(),

Forms\Components\Tabs::make('Details')
    ->tabs([
        Forms\Components\Tabs\Tab::make('General')
            ->schema([/* ... */]),
        Forms\Components\Tabs\Tab::make('SEO')
            ->schema([/* ... */]),
    ]),
```

### 4. Table Columns & Filters
```php
// Columns
Tables\Columns\TextColumn::make('email')
    ->copyable()
    ->icon('heroicon-m-envelope'),

Tables\Columns\ImageColumn::make('avatar')
    ->circular()
    ->defaultImageUrl(fn ($record) => "https://ui-avatars.com/api/?name={$record->name}"),

Tables\Columns\BadgeColumn::make('status')
    ->colors([
        'danger'  => 'archived',
        'warning' => 'draft',
        'success' => 'published',
    ]),

// Filters
Tables\Filters\Filter::make('published')
    ->query(fn (Builder $query) => $query->where('published', true))
    ->toggle(),

Tables\Filters\DateRangeFilter::make('created_at'),
```

### 5. Actions
```php
// Table action with confirmation
Tables\Actions\Action::make('approve')
    ->icon('heroicon-o-check')
    ->color('success')
    ->requiresConfirmation()
    ->action(fn (Post $record) => $record->update(['status' => 'published'])),

// Action with form modal
Tables\Actions\Action::make('reject')
    ->form([
        Forms\Components\Textarea::make('reason')->required(),
    ])
    ->action(function (Post $record, array $data): void {
        $record->reject($data['reason']);
        Notification::make()
            ->title('Post rejected')
            ->warning()
            ->send();
    }),
```

### 6. Widgets
```php
// Stats overview
class StatsOverviewWidget extends BaseWidget
{
    protected function getStats(): array
    {
        return [
            Stat::make('Total Users', User::count())
                ->description('All registered users')
                ->descriptionIcon('heroicon-m-arrow-trending-up')
                ->color('success'),

            Stat::make('Posts', Post::published()->count())
                ->description('Published posts')
                ->chart([7, 2, 10, 3, 15, 4, 17])
                ->color('warning'),
        ];
    }
}

// Chart widget
class PostsChart extends ChartWidget
{
    protected static ?string $heading = 'Posts Over Time';

    protected function getData(): array
    {
        return [
            'datasets' => [
                [
                    'label' => 'Posts',
                    'data'  => Post::selectRaw('COUNT(*) as count, DATE(created_at) as date')
                        ->groupBy('date')
                        ->pluck('count')
                        ->toArray(),
                ],
            ],
            'labels' => ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
        ];
    }

    protected function getType(): string
    {
        return 'line';
    }
}
```

### 7. Notifications
```php
use Filament\Notifications\Notification;

Notification::make()
    ->title('Saved successfully')
    ->success()
    ->send();

Notification::make()
    ->title('Order failed')
    ->body('Please try again.')
    ->danger()
    ->actions([
        Action::make('retry')
            ->button()
            ->url(route('orders.index')),
    ])
    ->sendToDatabase($user);
```

## Filament v4 Features

### Improved Schema API
Filament v4 unifies the schema builder across forms, infolists, and table columns, making it easy to reuse component definitions.

### Cluster Navigation
```php
// Group resources into clusters
class SettingsCluster extends Cluster
{
    protected static ?string $navigationIcon = 'heroicon-o-cog-6-tooth';
}

class GeneralSettingsResource extends Resource
{
    protected static ?string $cluster = SettingsCluster::class;
}
```

### Custom Themes
```bash
php artisan make:filament-theme
```
```css
/* resources/css/filament/admin/theme.css */
@import '/vendor/filament/filament/resources/css/theme.css';

:root {
    --sidebar-width: 20rem;
}
```

## Best Practices
- Authorise access using `canAccess()`, `canCreate()`, `canEdit()`, `canDelete()` on Resources
- Use `->lazy()` on Select components with large datasets
- Cache expensive widget queries with `protected static ?string $pollingInterval = '60s'`
- Prefer `->relationship()` helpers over manual `->options()` for Eloquent data
- Use `SoftDeletes` on models and add `TrashedFilter` to tables
- Generate resources with `php artisan make:filament-resource Post --generate`

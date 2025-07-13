---
title: Chapter XIII. Meat & Laravel Integraton
---

## Meat & Laravel Integration 
_Full-stack flow with Blade precision_

Meat’s Laravel integration—via the meat-laravel subproject—brings reactive frontend logic directly into Blade views with minimal ceremony and maximum control. 

Instead of manually injecting JavaScript state or wiring custom events, you declare frontend bindings through macros, and Laravel handles the rest. 

The result? Blade stays expressive, Meat becomes reactive, and your app becomes a clean, full-stack flow.

### Core Features

- **Blade Macros**: `@meatHydrate`, `@meatSync`, `@meatSyncEvent`, `@meatScripts`.
- **Middleware**: `InjectMeatKey` auto-injects HMAC keys across views.
- **MeatHasher**: Encrypts and validates state sync keys.
- **Sync Endpoint**: `/meat-sync` **POST** route for reactive updates.
- **Hydration Bridge**: _meat.js_ handles runtime hydration and event binding.

### Setup

1. Install meat-laravel
2. Register InjectMeatKey middleware:
```php
   ->withMiddleware(function (Middleware $middleware): void {
       $middleware->append(\App\Http\Middleware\InjectMeatKey::class);
   });
```
3. Add MEAT_SECRET to your .env file:
```
   MEAT_SECRET=your-meat-secret
```
4. Publish frontend assets:
```bash
   php artisan vendor:publish --tag=meat-assets
```
5. Use macros in Blade views

```blade
{{-- Reactive Binding in Blade --}}
@meatHydrate(['theme' => 'dark', 'username' => $user->name])
@meatSync('username')
@meatSyncEvent('username', \App\Events\UsernameChanged::class)
@meatScripts
```

This setup will hydrate the frontend Meat store, bind the "username" key to reactive mutations, and dispatch a Laravel event when the frontend changes it.

### Hydration Bridge Behavior
- Bootstraps Meat state from Blade
- Watches for meat.set() mutations
- Sends HMAC-signed payloads to Laravel
- Dispatches declared backend events
- Verifies sync integrity before processing

Controller Logic Example
```php
event(new UsernameChanged($user));
```
This matches the macro’s event reference and completes the signal chain.

### Secure Sync Flow

The frontend bridge sends signed payloads to:

```php
Route::middleware(['web', VerifyMeatTransaction::class])
     ->post('/meat-sync', MeatSyncController::class)
     ->name('meat.sync');
```

Each payload includes the hashed key generated from:
```php
$key = MeatHasher::hash(['field' => 'username']);
```
The backend uses this signature to validate the request.

### Examples

#### Example Page
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Reactive Laravel</title>
  <meta name="csrf-token" content="{{ csrf_token() }}">
</head>
<body>

  <h1>Hello, {{ $user->name }}</h1>

  <input id="message" value="Hi!" />
  <button onclick="document.getElementById('message').value += ' !'">Boost</button>

  @meatHydrate(['message' => 'Hi!'])
  @meatSync('message')
  @meatSyncEvent('message', \App\Events\MessageUpdated::class)
  @meatScripts

</body>
</html>
```

#### Live Dashboard Pattern

```blade
@meatHydrate(['status' => 'active', 'notifications' => []])
@meatSync('notifications')
@meatSyncEvent('notifications', \App\Events\NotifyUser::class)
```

Use `meat.watch('notifications', renderToast)` to show updates in real time.

#### Form Draft Example
```blade
@meatHydrate(['draft.title' => '', 'draft.body' => ''])
@meatSync('draft.title')
@meatSync('draft.body')
@meatSyncEvent('draft.title', \App\Events\FormFieldUpdated::class)
```
Attach `meat.persist()` on input to autosave with localStorage.

### Macros Summary

| Macro                     | Purpose                                               |
|---------------------------|-------------------------------------------------------|
| `@meatHydrate($state)`    | Injects initial state as JSON into the Blade template |
| `@meatSync($key)`         | Declares a reactive binding for a given key           |
| `@meatSyncEvent($key, EventClass)` | Links state key to backend event dispatch via handler |
| `@meatScripts`            | Loads the bridge script from `public/vendor/meat/js`  |

<!-- Page Break --><div style="page-break-before: always;"></div>

### Folder Structure

```plaintext
meat-laravel/
├── Blade/                   // macro definitions
├── Helpers/                 // MeatHasher, config
├── Http/                    // sync controller
├── Middleware/              // InjectMeatKey, verification
├── macros/                  // view injectors
├── public/vendor/meat/js/  // meat.js hydration bridge
└── MeatServiceProvider.php // macro registration
```

### Authoring Tips

- All macros are parsed at runtime; order matters.
- Use `Class::class` syntax for events.
- Payloads must be serializable.
- Middleware injects keys automatically—no need to manually share.
- Use `@meatHydrate` early in your view to ensure bridge bootstraps correctly.
- Debug events using `meat.onAny()` in your frontend.

### Why This Is Great

- Pure Blade remains untouched by JS framework noise.
- Laravel Events link to frontend mutations automatically.
- Sync verification is cryptographically secure.
- Your frontend responds in real time—with no extra boilerplate.
- Admin panels, dashboards, notification systems, and form workflows gain reactivity with minimal effort.
- No duplication between PHP and JS.
- Your Laravel stack becomes observably full-stack.

When you change state, Meat syncs.  
When Meat syncs, Laravel reacts.  
When Laravel reacts, your view updates.  

All declarative. All in sync. All yours.

# PushNotifications in laravel
Testing browser's push notification in laravel

extraced from https://github.com/laravel-notification-channels/webpush

1.
whip up the composer package 
composer require laravel-notification-channels/webpush


2.
// config/app.php


'providers' => [
    ...
    NotificationChannels\WebPush\WebPushServiceProvider::class,
],



3.
Add the NotificationChannels\WebPush\HasPushSubscriptions trait to your User model:

use NotificationChannels\WebPush\HasPushSubscriptions;

class User extends Model
{
    use HasPushSubscriptions;
}

4.
Next publish the migration with:

php artisan vendor:publish --provider="NotificationChannels\WebPush\WebPushServiceProvider" --tag="migrations"



5.
Run the migrate command to create the necessary table:

php artisan migrate

6.
You can also publish the config file with:

php artisan vendor:publish --provider="NotificationChannels\WebPush\WebPushServiceProvider" --tag="config"

7.

Generate the VAPID keys (required for browser authentication) with:

php artisan webpush:vapid


This command will set VAPID_PUBLIC_KEY and VAPID_PRIVATE_KEYin your .env file.

These keys must be safely stored and should not change.

If you still want support Google Cloud Messaging set the GCM_KEY and GCM_SENDER_ID in your .env file.


Usage
Now you can use the channel in your via() method inside the notification as well as send a web push notification:

use Illuminate\Notifications\Notification;
use NotificationChannels\WebPush\WebPushMessage;
use NotificationChannels\WebPush\WebPushChannel;

class AccountApproved extends Notification
{
    public function via($notifiable)
    {
        return [WebPushChannel::class];
    }

    public function toWebPush($notifiable, $notification)
    {
        return (new WebPushMessage)
            ->title('Approved!')
            ->icon('/approved-icon.png')
            ->body('Your account was approved!')
            ->action('View account', 'view_account');
            // ->data(['id' => $notification->id])
            // ->badge()
            // ->dir()
            // ->image()
            // ->lang()
            // ->renotify()
            // ->requireInteraction()
            // ->tag()
            // ->vibrate()
    }
}
Save/Update Subscriptions
To save or update a subscription use the updatePushSubscription($endpoint, $key = null, $token = null) method on your user:

$user = \App\User::find(1);

$user->updatePushSubscription($endpoint, $key, $token);
The $key and $token are optional and are used to encrypt your notifications. Only encrypted notifications can have a payload.

Delete Subscriptions
To delete a subscription use the deletePushSubscription($endpoint) method on your user:

$user = \App\User::find(1);

$user->deletePushSubscription($endpoint);

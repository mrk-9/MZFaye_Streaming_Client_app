MZFayeClient
===========

Faye streaming feed Client for iOS. 

## Connect to realtime updates for Stream feeds

```objc
MZFayeClient *client = [[MZFayeClient alloc] initWithURL:[NSURL URLWithString:@"wss://faye.getstream.io/faye"]];

[client connect:nil failure:^(NSError *error) {
    NSLog(@"Error connecting: %@", error.userInfo);
}];

NSString *api_key = @"apikey";
NSString *user_id = [NSString stringWithFormat:@"site-%@-feed-%@", @"appid", @"feedid"];

// signature must be created server-side (feed's readOnly token)
NSString *signature = @"JWT-TOKEN";
NSString *channel = [NSString stringWithFormat:@"/%@", user_id];

[client setExtension:@{@"user_id": user_id, @"api_key": api_key,
                            @"signature": signature}
               forChannel:@"/meta/subscribe"];

[client subscribeToChannel:channel success:^(NSDictionary *extension){NSLog(@"Subscribed successfully to channel!");} failure:^(NSError *error) {NSLog(@"Error subscribing to channel: %@", error.userInfo);} receivedMessage:^(NSDictionary *message, NSDictionary *extension) {
    NSLog(@"Server message %@,\nextension: %@",message,extension);
}];
```

## How To Use - Example

```
self.client = [[MZFayeClient alloc] initWithURL:[NSURL URLWithString:@"ws://localhost:9292/faye"]];

[self.client subscribeToChannel:@"/server" success:^{
    NSLog(@"Subscribed successfully to 'server' channel!");
} failure:^(NSError *error) {
    NSLog(@"Error subscribing to 'server' channel: %@", error.userInfo);
} receivedMessage:^(NSDictionary *message) {
    NSLog(@"Message on 'server' channel: %@", message);
}];

[self.client subscribeToChannel:@"/browser" success:nil failure:nil receivedMessage:^(NSDictionary *message) {
    NSLog(@"Message on 'browser' channel: %@", message);
}];

[self.client connect:^{
    [self.client sendMessage:@{@"text": @"hello!"} toChannel:@"/server" success:^{
        NSLog(@"Message sent successfully.");
    } failure:^(NSError *error) {
        NSLog(@"Error sending message: %@", error.userInfo);
    }];
} failure:^(NSError *error) {
    NSLog(@"Error connecting: %@", error.userInfo);
}];
```

## Available methods

### Initializing
```
- (instancetype)initWithURL:(NSURL *)url;
+ (instancetype)clientWithURL:(NSURL *)url;
```

### Publishing
```
- (void)sendMessage:(NSDictionary *)message toChannel:(NSString *)channel success:(MZFayeClientSuccessHandler)successHandler failure:(MZFayeClientFailureHandler)failureHandler;
- (void)sendMessage:(NSDictionary *)message toChannel:(NSString *)channel usingExtension:(NSDictionary *)extension success:(MZFayeClientSuccessHandler)successHandler failure:(MZFayeClientFailureHandler)failureHandler;
```

### Subscribing/unsubscribing
```
- (void)subscribeToChannel:(NSString *)channel success:(MZFayeClientSuccessHandler)successHandler failure:(MZFayeClientFailureHandler)failureHandler receivedMessage:(MZFayeClientSubscriptionHandler)subscriptionHandler;
- (void)unsubscribeFromChannel:(NSString *)channel success:(MZFayeClientSuccessHandler)successHandler failure:(MZFayeClientFailureHandler)failureHandler;
```

### Connecting/disconnecting
```
- (void)connect:(MZFayeClientSuccessHandler)successHandler failure:(MZFayeClientFailureHandler)failureHandler;
- (void)disconnect:(MZFayeClientSuccessHandler)successHandler failure:(MZFayeClientFailureHandler)failureHandler;
```

### Using Faye extensions

For more info about extensions, see the [Faye site](http://faye.jcoglan.com/node/extensions.html),

```
- (void)setExtension:(NSDictionary *)extension forChannel:(NSString *)channel;
- (void)removeExtensionForChannel:(NSString *)channel;
```

## Faye Server
If you can to run a Faye server to test the client, you can find all the information you need there: <http://faye.jcoglan.com/>
You can also run the `faye.rb` file under the `Server` directory:

```
gem install faye thin eventmachine

rackup faye.ru -s thin -E production
```

```
curl http://localhost:9292/faye -d 'message={"channel":"/server", "data":"hello"}'
```

## Dependencies

#### SocketRocket
A conforming WebSocket (RFC 6455) client library maintained by Square, 
<https://github.com/square/SocketRocket>

#### Base64
Objective-C Base64 Additions for NSData and NSString
<https://github.com/ekscrypto/Base64>

## Requirements

MZFayeClient requires either iOS 7.x and above.

## ARC

MZFayeClient uses ARC.


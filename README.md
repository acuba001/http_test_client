# http_test_client
A package for testing http clients on flutter or the Dart vm.


## Example
The following example forces all HTTP requests to return a
successful empty response in a test enviornment.  No actual HTTP requests will be performed.

```dart
import 'dart:io';
import 'dart:math';
import 'package:test/test.dart';
import 'package:http_test_client/http_test_client.dart';

var someRandomNumber = Random().nextInt(999999);

class MyHttpOverrides extends HttpOverrides {
  @override
  HttpClient createHttpClient(_) {
    return HttpTestClient((request, client) {
        // the default response is an empty 200.
        return HttpTestResponse(statusCode: someRandomNumber);
    });
  }
}

void main() {
  group('HttpClient', () {
    setUp(() {
      // overrides all HttpClients.
      HttpOverrides.global = new MyHttpOverrides();
    });

    test('returns OK', () async {
      // this is actually an instance of [HttpTestClient].
      final client = new HttpClient();
      client.getUrl(Uri.https('google.com','/'))
        .then((req) => req.close())
        .then((res){
          expect(res.statusCode, someRandomNumber);
        });
    });
  });
}
```

# Platform Channel

### 1.Method Channel

#### 1.1桥接示例
官方文档提供了一个dart调用原生获取电量的示例。

原生测注册一个handler：
```swift
let controller : FlutterViewController = window?.rootViewController as! FlutterViewController

let batteryChannel = FlutterMethodChannel(name: "samples.flutter.dev/battery",binaryMessenger: controller.binaryMessenger)
batteryChannel.setMethodCallHandler({
    [weak self] (call: FlutterMethodCall, result: @escaping FlutterResult) -> Void in
    guard call.method == "getBatteryLevel" else {
        result(FlutterMethodNotImplemented)
        return
    }
    self?.receiveBatteryLevel(result: result)
})
```

Handler里判断method调用对应原生方法：
```swift
private func receiveBatteryLevel(result: FlutterResult) {
    let device = UIDevice.current
    device.isBatteryMonitoringEnabled = true
    if device.batteryState == UIDevice.BatteryState.unknown {
    result(FlutterError(code: "UNAVAILABLE",
                        message: "Battery level not available.",
                        details: nil))
    } else {
    result(Int(device.batteryLevel * 100))
    }
}
```

Dart侧：
```dart
  static const platform = MethodChannel('samples.flutter.dev/battery');

  Future<void> getBatteryLevel() async {
    try {
      final result = await platform.invokeMethod<int>('getBatteryLevel');
      if (result != null) {
          print('----- Battery:$result');
      }
    } on PlatformException catch (e) {
      print("Failed to get battery level: '${e.message}'.");
    }
  }

```


#### 1.2Pigeon
官方文档：
https://github.com/flutter/packages/blob/main/packages/pigeon/example/README.md


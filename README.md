# Flutter-QRCode-Scanner

```dart
import 'package:flutter/cupertino.dart';
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

class QRCodeScanner extends StatefulWidget {
  // Again connecting to the same channel
  static const platform = const MethodChannel('com.cgpay.app/qrcodeScanner');

  @override
  _QRCodeScannerState createState() => _QRCodeScannerState();
}

class _QRCodeScannerState extends State<QRCodeScanner> {
  String _qrcode = "";

  @override
  void initState() {
    super.initState();
    _getCode();
  }

  @override
  Widget build(BuildContext context) {
    return Container(
      child: Stack(
        children: [
          UiKitView(
            viewType: 'FLQRCodeScannerView',
          ),
          Visibility(
            visible: _qrcode != "",
            child: Center(
              child: Text(
                _qrcode,
                style: TextStyle(color: Colors.white),
              ),
            ),
          )
        ],
      ),
    );
  }

  Future<void> _getCode() async {
    String qrcode;
    try {
      final String result = await QRCodeScanner.platform.invokeMethod('getQRCode');
      setState(() => _qrcode = result);
      qrcode = result;
    } on PlatformException catch (e) {
      if (e.code == 'PERMISSION_NOT_GRANTED') {
        setState(() {
          _qrcode = 'The user did not grant the camera permission!';
        });
      } else {
        setState(() => _qrcode = 'Unknown error: $e');
      }
    } on FormatException {
      setState(() => _qrcode = 'null (User returned using the "back"-button before scanning anything. Result)');
    } catch (e) {
      setState(() => _qrcode = 'Unknown error: $e');
    }
  }
}

```

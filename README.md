# sms_consent_for_otp_autofill
Flutter plugin for otp auto fill, used SMS Consent API to retrieve the otp of a single SMS message if the user gives permission.No  permission required in manifest. For Android, that's where this package is useful. No need for iOS, using Android's [SMS User Consent API](https://developers.google.com/identity/sms-retriever/user-consent/overview)


1] Create an instance, **optionally** supply phone number listener and sms listener
```dart
import 'package:sms_consent_for_otp_autofill/sms_consent_for_otp_autofill.dart';

SmsConsentForOtpAutofill smsConsentForOtpAutoFill = SmsConsentForOtpAutofill(
        // optionally, do something when user selects a number.
        // You can even add/update this listener later on by simply 
        // calling smsUserConsent.updatePhoneNumberListener(updatedListener)
        phoneNumberListener: (number) {},
        
        // optionally, do something when user receives sms.
        // You can even add/update this listener later on by simply 
        // calling smsUserConsent.updateSmsListener(updatedListener)
        smsListener: (otpcode) {}
);
```

2a] **OPTIONAL** : Request user's phone number

```dart
smsConsentForOtpAutoFill.requestPhoneNumber();
```
Once the user selects a phone number, it can be accessed as

```dart
smsConsentForOtpAutoFill.selectedPhoneNumber;
```

2b] **OPTIONAL** : Request to receive SMS
```dart
smsConsentForOtpAutoFill.requestSms(); 
```
or you can specify the phone number you wish to capture the SMS from

```dart
smsConsentForOtpAutoFill.requestSms(senderPhoneNumber: sender_number);
```
Once the user receives a SMS and the user taps **Allow**, it can be accessed as

```dart
smsConsentForOtpAutoFill.receivedSms;
```

3] Finally, dispose the instance

```dart
smsConsentForOtpAutoFill.dispose();
```

### Note

As per the [SMS User Consent API](https://developers.google.com/identity/sms-retriever/user-consent/overview),  otp will be received by the plugin only if it meets these criteria:

* The message contains 6-10 numbers.
* The message was sent by a phone number that's not in the user's contacts.
* If you specified the sender's phone number, the message was sent by that number.

## Contributing

* Found a bug or idea to improve the plugin? Send a PR.
* my portfolio [Portfolio](https://mdyousufbhuiyan.github.io/yousuf-portfolio/)
* Want to hire me for a gig? Let's talk on [LinkedIn](https://www.linkedin.com/in/yousufappspecialist)
#   s m s _ c o n s e n t _ f o r _ o t p _ a u t o f i l l 

App level build gradel

dependencies {
    implementation platform('com.google.firebase:firebase-bom:32.7.0')
    implementation 'com.google.firebase:firebase-auth'
//    implementation platform('com.google.firebase:firebase-bom:29.3.1')
    implementation 'com.google.firebase:firebase-analytics'
    implementation 'com.google.firebase:firebase-messaging:23.4.1'
    implementation 'com.google.firebase:firebase-crashlytics'

    implementation 'com.google.android.gms:play-services-auth:21.3.0'
    implementation 'com.google.android.gms:play-services-auth-api-phone:18.2.0'
    implementation 'com.google.android.gms:play-services-identity:18.0.1' // ✅ Required for HintRequest
    implementation 'com.google.android.gms:play-services-base:18.2.0'

    // ✅ Correct and available version of Credentials API
    implementation "com.google.android.libraries.identity.googleid:googleid:1.1.0"


    coreLibraryDesugaring 'com.android.tools:desugar_jdk_libs:1.2.2'
    implementation "androidx.multidex:multidex:2.0.1"
}


phone number lisner 

smsConsentForOtpAutoFill = SmsConsentForOtpAutofill(
      phoneNumberListener: (number) {
        printLog("number...................$number");
        if (number == null || number.isEmpty) {
          printLog("📵 No number selected. Showing keyboard.");
          Future.delayed(const Duration(milliseconds: 300), () {
            _showKeyboard(_phoneFocusNode); // ⬅️ use new helper
          });
        } else {
          String cleanedNumber = number.replaceAll(RegExp(r'\D'), '');
          if (cleanedNumber.length > 10) {
            cleanedNumber = cleanedNumber.substring(cleanedNumber.length - 10);
          }

          controller.phoneController.text = cleanedNumber;
          controller.validatePhone(cleanedNumber);
          controller.update();

          if (cleanedNumber.length == 10) {
            FocusManager.instance.primaryFocus?.unfocus();
          }
        }
      },
      smsListener: (otpcode) {
        printLog("otp...................  $otpcode");
      },
    );


  late SmsConsentForOtpAutofill smsConsentForOtpAutoFill;

  @override
  void initState() {
    super.initState();
    smsConsentForOtpAutoFill =
        SmsConsentForOtpAutofill(phoneNumberListener: (number) {
      printLog("number...................$number");
    }, smsListener: (otpcode) {
      otpController.clear();
      otpController.text = otpcode;
      printLog("otp...................  $otpcode");
    });
    smsConsentForOtpAutoFill.requestPhoneNumber();
  }

  @override
  void dispose() {
    smsConsentForOtpAutoFill.dispose();
    super.dispose();
  }
 
 

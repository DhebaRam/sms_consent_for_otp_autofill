# sms_consent_for_otp_autofill
fix: add receiver visibility flag for SMS retriever on Android 12+  Android 12 and above require specifying receiver visibility when registering  BroadcastReceivers dynamically. Added Context.RECEIVER_EXPORTED for compatibility.

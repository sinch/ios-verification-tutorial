# iOS Verify a phone number in your app
In this tutorial we are going to explore our Verification SDK for iOS. As of writing this the method we support for iOS is SMS verification that you we have written about in earlier tutorials. With our Verification SDK you wont need any backend your self if you don't want to, or you can implement a simple endpoint to receive that a number is verified. If you still prefer to roll your own you can read about that here. 

## Setup
I created a start project that contains the framework and a couple of screens you can download here (github link). If you prefer to add it to you app directly  here is how you set it up
1. Download the SDK here http://sinch.com/download/
2. Add the SinchVerification.Framework to your app

or use [cocoapods](http://cocoapods.org) `pod 'SinchVerification'` 

## Verifying a phone number
First of all we need to collect the users phone number as we do in the starter project "EnterPhonenumberViewController" and request to send an SMS to that number. Open the **EnterPhonenumberViewController.m** Find the **requestCode** method find the row `[self performSegueWithIdentifier:@"verifyCodeSeg" sender:nil];` and replace it with the below:

*EnterPhonenumberViewController.m*

```
//start the verification process with the phonenumber in the field
_verification = [SINVerification 
 					SMSVerificationWithApplicationKey:@"YOURKEY" 
 					phoneNumber:_phoneNumber.text];
//set up the initiate the process
[_verification initiateWithCompletionHandler:^(BOOL success, NSError *error) {
   [spinner stopAnimating];
   if (success) {
      [self performSegueWithIdentifier:@"verifyCodeSeg" sender:nil];
   }
   else {
      _status.text = [error description];
   }
}];
```

What we are doing in the above code is to first, create verification object, and then start the two step process of verifying a number. If it fails, display an error message, otherwise continue to the **EnterCode** screen. In the stater project I also prepared the prepareForSegue: method to pass the verification object to the EnterCode controller.

```
- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
    VerifyCodeViewController* vc = [segue destinationViewController];
    vc.verification = _verification;
}
```
Here we set the verification object to the current verification. Next up is to verify the code sent to the phone. Open up **VerifyCodeViewController.m** and find the method **verifyCode:** and make and replace the line `[self performSegueWithIdentifier:@"verifyCodeSeg" sender:nil];
` with 

**VerifyCodeViewController.m**

```
    [self.verification
     verifyCode:code.text
     completionHandler:^(BOOL success, NSError* error) {
         if (success) {
             [_spinner stopAnimating];
             [self performSegueWithIdentifier:@"confirmedSeg" sender:nil];
             // Phone number was successfully verified, you should 
             //probably notify your backend or use the callbacks to store that the phone is
             //verified.
         } else {
             // Ask user to re-attempt verification
             status.text = [error description];
         }
     }];
```

## Thats it
With a few lines of code you can implement a solid solution to verify phone numbers of your users. Next up is implementing the callbacks on your server side. 




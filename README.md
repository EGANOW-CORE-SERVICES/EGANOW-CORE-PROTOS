# Access & Rights

Always access the service with the basic auth header & pass the `country_code` & `language_id` in the metadata.

```javascript
const metadata = new grpc.Metadata();
metadata.add('x-country-code', 'GH0233');
metadata.add('x-language-id', 'en');
metadata.add('x-ega-user-access-token', '<provide the access token for the current user>');
metadata.add('Authorization', 'Basic ' + Buffer.from('username:password').toString('base64'));

const client = new MerchantOnboardingSvcClient('https://185-3-92-47.ip.linodeusercontent.com:9999', null, null);

```

```ts
client.checkIfMerchantAccountExists(req, (err, res) => {
    if (err.code == grpc.NOT_FOUND) {
        // Merchant does not exist, proceed to request for OTP
        console.error(err);
    } else {
        console.log(res);
    }
});

```

# Business Customer Onboarding

1. Get Signup countries (`SharedDataSvc/GetCountries#COUNTRY_FILTER_SIGNUP`)
2. Check if merchant exists (`MerchantOnboardingSvc/CheckIfMerchantAccountExists`)
3. Log email address of customer and send OTP (`OtpSvc/SendOTP`)
4. Verify email OTP (`OtpSvc/VerifyOTP`)
5. Get Operating Countries (`SharedDataSvc/GetCountries#COUNTRY_FILTER_OPERATING`)
6. Register Business Customer (`MerchantOnboardingSvc/CreateMerchantAccount`)

# Forgot Password

1. Request for a temporal password sent via email (`MerchantOnboardingSvc/RequestPasswordReset`)
2. Login with the temporal password (`MerchantOnboardingSvc/LoginMerchant`)
3. Reset the password (`MerchantOnboardingSvc/ResetPassword`)
4. Log user in with the new password (happens automatically after resetting the password)
5. `AuthMerchantResponse` will contain the new access token and user details

# Update Business Information for a Merchant (Authorized)

1. Get list of regulators (`MerchantCommonSvc/GetActiveRegulators`)
2. Get list of sectors (`MerchantCommonSvc/GetBusinessSectors`)
3. Get list of industries (`MerchantCommonSvc/GetActiveIndustries`)
4. Provide all required fields in `UpdateBusinessInfoRequest` and call `MerchantAccountSvc/UpdateBusinessInfo`
5. The response will contain the success message, else an error will be thrown

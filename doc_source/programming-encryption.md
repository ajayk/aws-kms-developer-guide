# Encrypting and Decrypting Data Keys<a name="programming-encryption"></a>

The examples in this topic use the [Encrypt](http://docs.aws.amazon.com/kms/latest/APIReference/API_Encrypt.html), [Decrypt](http://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html), and [ReEncrypt](http://docs.aws.amazon.com/kms/latest/APIReference/API_ReEncrypt.html) operations in the AWS KMS API\. 

These operations are designed to encrypt and decrypt [data keys](concepts.md#data-keys)\. They use an AWS KMS [customer master key](concepts.md#master_keys) \(CMK\) in the encryption operations and they cannot accept more than 4 KB \(4096 bytes\) of data\. Although you might use them to encrypt small amounts of data, such as a password or RSA key, they are not designed to encrypt application data\.

To encrypt application data, use the server\-side encryption features of an AWS service, or a client\-side encryption library, such as the [AWS Encryption SDK](http://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/) or the [Amazon S3 encryption client](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingClientSideEncryption.html)\. 

**Topics**
+ [Encrypting a Data Key](#encryption)
+ [Decrypting a Data Key](#decryption)
+ [Re\-Encrypting a Data Key Under a Different Customer Master Key](#reencryption)

## Encrypting a Data Key<a name="encryption"></a>

The [Encrypt](http://docs.aws.amazon.com/kms/latest/APIReference/API_Encrypt.html) operation is designed to encrypt data keys, but it is not frequently used\. The [GenerateDataKey](http://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKey.html) and [GenerateDataKeyWithoutPlaintext](http://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKeyWithoutPlaintext.html) operations return encrypted data keys\. You might use this method when you are moving encrypted data to a new region and want to encrypt its data key with a CMK in the new region\. 

This example uses the KMS client object that you created in [Creating a Client](programming-client.md)\.

------
#### [ Java ]

For details, see the [encrypt method](http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/kms/AWSKMSClient.html#encrypt-com.amazonaws.services.kms.model.EncryptRequest-) in the *AWS SDK for Java API Reference*\.

```
// Encrypt a data key
//
// Replace the following fictitious CMK ARN with a valid CMK ID or ARN
String keyId = "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab";
ByteBuffer plaintext = ByteBuffer.wrap(new byte[]{1,2,3,4,5,6,7,8,9,0});

EncryptRequest req = new EncryptRequest().withKeyId(keyId).withPlaintext(plaintext);
ByteBuffer ciphertext = kmsClient.encrypt(req).getCiphertextBlob();
```

------
#### [ C\# ]

For details, see the [Encrypt method](http://docs.aws.amazon.com/sdkfornet/v3/apidocs/items/KeyManagementService/MKeyManagementServiceEncryptEncryptRequest.html) in the *AWS SDK for \.NET*\.

```
// Encrypt a data key
//
// Replace the following fictitious CMK ARN with a valid CMK ID or ARN
String keyId = "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab";
MemoryStream plaintext = new MemoryStream();
plaintext.Write(new byte[] { 1, 2, 3, 4, 5, 6, 7, 8, 9, 0 }, 0, 10);

EncryptRequest encryptRequest = new EncryptRequest()
{
    KeyId = keyId,
    Plaintext = plaintext
};
MemoryStream ciphertext = kmsClient.Encrypt(encryptRequest).CiphertextBlob;
```

------
#### [ Python ]

For details, see the [encrypt method](http://boto3.readthedocs.org/en/latest/reference/services/kms.html#KMS.Client.encrypt) in the AWS SDK for Python \(Boto 3\)\.

```
# Encrypt a data key

# Replace the following fictitious CMK ARN with a valid CMK ID or ARN
key_id = 'arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab'
plaintext = b'\x01\x02\x03\x04\x05\x06\x07\x08\x09\x00'

response = kms_client.encrypt(
    KeyId=key_id,
    Plaintext=plaintext
)

ciphertext = response['CiphertextBlob']
```

------
#### [ Ruby ]

For details, see the [encrypt](http://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/KMS/Client.html#encrypt-instance_method) instance method in the [AWS SDK for Ruby](http://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/KMS.html)\.

```
# Encrypt a data key

# Replace the following fictitious CMK ARN with a valid CMK ID or ARN
keyId = 'arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab'
plaintext = "\x01\x02\x03\x04\x05\x06\x07\x08\x09\x00"

response = kmsClient.encrypt({
  key_id: keyId,
  plaintext: plaintext
})

ciphertext = response.ciphertext_blob
```

------
#### [ PHP ]

For details, see the [Encrypt method](http://docs.aws.amazon.com/aws-sdk-php/v3/api/api-kms-2014-11-01.html#encrypt) in the *AWS SDK for PHP*\.

```
// Encrypt a data key
//
// Replace the following fictitious CMK ARN with a valid CMK ID or ARN
$keyId = 'arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab';
$message = pack('c*',1,2,3,4,5,6,7,8,9,0);

$result = $KmsClient->encrypt([
    'KeyId' => $keyId, 
    'Plaintext' => $message, 
]);

$ciphertext = $result['CiphertextBlob'];
```

------
#### [ Node\.js ]

For details, see the [encrypt property](http://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/KMS.html#encrypt-property) in the AWS SDK for JavaScript in Node\.js\.

```
// Encrypt a data key
//
// Replace the following fictitious CMK ARN with a valid CMK ID or ARN
const KeyId = 'arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab';
const Plaintext = Buffer.from([1, 2, 3, 4, 5, 6, 7, 8, 9, 0]);
kmsClient.encrypt({ KeyId, Plaintext }, (err, data) => {
  if (err) console.log(err, err.stack); // an error occurred
  else {
    const { CiphertextBlob } = data;
      ...
  }
});
```

------

## Decrypting a Data Key<a name="decryption"></a>

To decrypt a data key, use the [Decrypt](http://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html) operation\.

The `ciphertextBlob` that you specify must be the value of the `CiphertextBlob` field from a [GenerateDataKey](http://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKey.html), [GenerateDataKeyWithoutPlaintext](http://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKeyWithoutPlaintext.html), or [Encrypt](http://docs.aws.amazon.com/kms/latest/APIReference/API_Encrypt.html) response\.

This example uses the KMS client object that you created in [Creating a Client](programming-client.md)\.

------
#### [ Java ]

For details, see the [decrypt method](http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/kms/AWSKMSClient.html#decrypt-com.amazonaws.services.kms.model.DecryptRequest-) in the *AWS SDK for Java API Reference*\.

```
// Decrypt a data key
//

ByteBuffer ciphertextBlob = Place your ciphertext here;

DecryptRequest req = new DecryptRequest().withCiphertextBlob(ciphertextBlob);
ByteBuffer plainText = kmsClient.decrypt(req).getPlaintext();
```

------
#### [ C\# ]

For details, see the [Decrypt method](http://docs.aws.amazon.com/sdkfornet/v3/apidocs/items/KeyManagementService/MKeyManagementServiceDecryptDecryptRequest.html) in the *AWS SDK for \.NET*\.

```
// Decrypt a data key
//

MemoryStream ciphertextBlob = new MemoryStream();
// Write ciphertext to memory stream

DecryptRequest decryptRequest = new DecryptRequest()
{
    CiphertextBlob = ciphertextBlob
};
MemoryStream plainText = kmsClient.Decrypt(decryptRequest).Plaintext;
```

------
#### [ Python ]

For details, see the [decrypt method](http://boto3.readthedocs.org/en/latest/reference/services/kms.html#KMS.Client.decrypt) in the AWS SDK for Python \(Boto 3\)\.

```
# Decrypt a data key

ciphertext = 'Place your ciphertext here'

response = kms_client.decrypt(
    CiphertextBlob=ciphertext
)

plaintext = response['Plaintext']
```

------
#### [ Ruby ]

For details, see the [decrypt](http://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/KMS/Client.html#decrypt-instance_method) instance method in the [AWS SDK for Ruby](http://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/KMS.html)\.

```
# Decrypt a data key

ciphertext = 'Place your ciphertext here'
ciphertext_packed = [ciphertext].pack("H*")

response = kmsClient.decrypt({
  ciphertext_blob: ciphertext_packed
})

plaintext = response.plaintext
```

------
#### [ PHP ]

For details, see the [Decrypt method](http://docs.aws.amazon.com/aws-sdk-php/v3/api/api-kms-2014-11-01.html#decrypt) in the *AWS SDK for PHP*\.

```
// Decrypt a data key
//
$ciphertext = 'Place your cipher text blob here';

$result = $KmsClient->decrypt([
     'CiphertextBlob' => $ciphertext
]);

$plaintext = $result['Plaintext'];
```

------
#### [ Node\.js ]

For details, see the [decrypt property](http://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/KMS.html#decrypt-property)\] in the *AWS SDK for JavaScript in Node\.js*\.

```
// Decrypt a data key
//
const CiphertextBlob = 'Place your cipher text blob here';
kmsClient.decrypt({ CiphertextBlob }, (err, data) => {
  if (err) console.log(err, err.stack); // an error occurred
  else {
    const { Plaintext } = data;
    ...
  }
});
```

------

## Re\-Encrypting a Data Key Under a Different Customer Master Key<a name="reencryption"></a>

To decrypt an encrypted data key, and then immediately re\-encrypt the data key under a different customer master key \(CMK\), use the [ReEncrypt](http://docs.aws.amazon.com/kms/latest/APIReference/API_ReEncrypt.html) operation\. The operations are performed entirely on the server side within AWS KMS, so they never expose your plaintext outside of AWS KMS\.

The `ciphertextBlob` that you specify must be the value of the `CiphertextBlob` field from a [GenerateDataKey](http://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKey.html), [GenerateDataKeyWithoutPlaintext](http://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKeyWithoutPlaintext.html), or [Encrypt](http://docs.aws.amazon.com/kms/latest/APIReference/API_Encrypt.html) response\.

This example uses the KMS client object that you created in [Creating a Client](programming-client.md)\.

------
#### [ Java ]

For details, see the [reEncrypt method](http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/kms/AWSKMSClient.html#reEncrypt-com.amazonaws.services.kms.model.ReEncryptRequest-) in the *AWS SDK for Java API Reference*\.

```
// Re-encrypt a data key

ByteBuffer sourceCiphertextBlob = Place your ciphertext here;

// Replace the following fictitious CMK ARN with a valid CMK ID or ARN
String destinationKeyId = "arn:aws:kms:us-west-2:111122223333:key/0987dcba-09fe-87dc-65ba-ab0987654321";

ReEncryptRequest req = new ReEncryptRequest();
req.setCiphertextBlob(sourceCiphertextBlob);
req.setDestinationKeyId(destinationKeyId);
ByteBuffer destinationCipherTextBlob = kmsClient.reEncrypt(req).getCiphertextBlob();
```

------
#### [ C\# ]

For details, see the [ReEncrypt method](http://docs.aws.amazon.com/sdkfornet/v3/apidocs/items/KeyManagementService/MKeyManagementServiceReEncryptReEncryptRequest.html) in the *AWS SDK for \.NET*\.

```
// Re-encrypt a data key

MemoryStream sourceCiphertextBlob = new MemoryStream();
// Write ciphertext to memory stream

// Replace the following fictitious CMK ARN with a valid CMK ID or ARN
String destinationKeyId = "arn:aws:kms:us-west-2:111122223333:key/0987dcba-09fe-87dc-65ba-ab0987654321";

ReEncryptRequest reEncryptRequest = new ReEncryptRequest()
{
    CiphertextBlob = sourceCiphertextBlob,
    DestinationKeyId = destinationKeyId
};
MemoryStream destinationCipherTextBlob = kmsClient.ReEncrypt(reEncryptRequest).CiphertextBlob;
```

------
#### [ Python ]

For details, see the [re\_encrypt method](http://boto3.readthedocs.org/en/latest/reference/services/kms.html#KMS.Client.re_encrypt) in the AWS SDK for Python \(Boto 3\)\.

```
# Re-encrypt a data key
ciphertext = 'Place your ciphertext here'

# Replace the following fictitious CMK ARN with a valid CMK ID or ARN
key_id = 'arn:aws:kms:us-west-2:111122223333:key/0987dcba-09fe-87dc-65ba-ab0987654321'

response = kms_client.re_encrypt(
    CiphertextBlob=ciphertext,
    DestinationKeyId=key_id
)

destination_ciphertext_blob = response['CiphertextBlob']
```

------
#### [ Ruby ]

For details, see the [re\_encrypt](http://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/KMS/Client.html#re_encrypt-instance_method) instance method in the [AWS SDK for Ruby](http://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/KMS.html)\.

```
# Re-encrypt a data key

ciphertext = 'Place your ciphertext here'
ciphertext_packed = [ciphertext].pack("H*")

# Replace the following fictitious CMK ARN with a valid CMK ID or ARN
keyId = 'arn:aws:kms:us-west-2:111122223333:key/0987dcba-09fe-87dc-65ba-ab0987654321'

response = kmsClient.re_encrypt({
  ciphertext_blob: ciphertext_packed,
  destination_key_id: keyId
})

destination_ciphertext_blob = response.ciphertext_blob.unpack('H*')
```

------
#### [ PHP ]

For details, see the [ReEncrypt method](http://docs.aws.amazon.com/aws-sdk-php/v3/api/api-kms-2014-11-01.html#reencrypt) in the *AWS SDK for PHP*\.

```
// Re-encrypt a data key

$ciphertextBlob = 'Place your ciphertext here';

// Replace the following fictitious CMK ARN with a valid CMK ID or ARN
$keyId = 'arn:aws:kms:us-west-2:111122223333:key/0987dcba-09fe-87dc-65ba-ab0987654321';

$result = $KmsClient->reEncrypt([
    'CiphertextBlob' => $ciphertextBlob,
    'DestinationKeyId' => $keyId, 
]);
```

------
#### [ Node\.js ]

For details, see the [reEncrypt property](http://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/KMS.html#reEncrypt-property) in the *AWS SDK for JavaScript in Node\.js*\.

```
// Re-encrypt a data key
const CiphertextBlob = 'Place your cipher text blob here';
// Replace the following fictitious CMK ARN with a valid CMK ID or ARN
const DestinationKeyId = 'arn:aws:kms:us-west-2:111122223333:key/0987dcba-09fe-87dc-65ba-ab0987654321';
kmsClient.reEncrypt({ CiphertextBlob, DestinationKeyId }, (err, data) => {
  ...
});
```

------
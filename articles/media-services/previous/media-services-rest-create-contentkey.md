---
title: Vytváření klíčů obsahu pomocí REST | Microsoft Docs
description: Tento článek ukazuje, jak vytvořit klíče obsahu, které poskytují zabezpečený přístup k prostředkům.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 95e9322b-168e-4a9d-8d5d-d7c946103745
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: a9960ece05b7cab4ddd26fdac7516f4adbccf5b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017252"
---
# <a name="create-content-keys-with-rest"></a>Vytváření klíčů obsahu pomocí REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Media Services vám umožní doručovat šifrované prostředky. **ContentKey** zajišťuje zabezpečený přístup k vašim **assetům** s. 

Při vytváření nového prostředku (například před [odesláním souborů](media-services-rest-upload-files.md)) můžete zadat následující možnosti šifrování: **StorageEncrypted**, **CommonEncryptionProtected** nebo **EnvelopeEncryptionProtected**. 

Při doručování prostředků klientům můžete [nakonfigurovat, aby bylo možné dynamicky šifrovat prostředky](media-services-rest-configure-asset-delivery-policy.md) pomocí jednoho z následujících dvou šifrování: **DynamicEnvelopeEncryption** nebo **DynamicCommonEncryption**.

Šifrované prostředky musí být přidruženy k **ContentKey** s. Tento článek popisuje, jak vytvořit klíč obsahu.

Níže jsou uvedené obecné kroky pro generování klíčů obsahu, které přiřadíte k prostředkům, které chcete zašifrovat. 

1. Náhodně vygenerujte 16bitový klíč AES (pro Common a šifrování obálek) nebo klíč AES 32-Byte (pro šifrování úložiště). 
   
    Toto je klíč obsahu pro váš Asset, což znamená, že všechny soubory přidružené k tomuto prostředku musí během dešifrování používat stejný klíč obsahu. 
2. Voláním metod [GetProtectionKeyId](/rest/api/media/operations/rest-api-functions#getprotectionkeyid) a [GetProtectionKey](/rest/api/media/operations/rest-api-functions#getprotectionkey) získáte správný certifikát X. 509, který se musí použít k zašifrování klíče obsahu.
3. Zašifrujte klíč obsahu pomocí veřejného klíče certifikátu X. 509. 
   
   Media Services .NET SDK používá při šifrování RSA s výplně OAEP.  Příklad můžete vidět ve [funkci EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Vytvořte hodnotu kontrolního součtu (na základě algoritmu kontrolního součtu klíče PlayReady AES) vypočítanou pomocí identifikátoru klíče a klíče obsahu. Další informace najdete v části algoritmus "PlayReady AES Key Checksum" v dokumentu objektu hlavičky [PlayReady, kde se nachází.](https://www.microsoft.com/playready/documents/)
   
    Následující příklad rozhraní .NET vypočítá kontrolní součet pomocí části GUID identifikátoru klíče a vymazat klíč obsahu.

    ```console
    public static string CalculateChecksum(byte[] contentKey, Guid keyId)
     {
       byte[] array = null;
         using (AesCryptoServiceProvider aesCryptoServiceProvider = new AesCryptoServiceProvider())
         {
             aesCryptoServiceProvider.Mode = CipherMode.ECB;
             aesCryptoServiceProvider.Key = contentKey;
             aesCryptoServiceProvider.Padding = PaddingMode.None;
             ICryptoTransform cryptoTransform = aesCryptoServiceProvider.CreateEncryptor();
             array = new byte[16];
             cryptoTransform.TransformBlock(keyId.ToByteArray(), 0, 16, array, 0);
         }
         byte[] array2 = new byte[8];
         Array.Copy(array, array2, 8);
         return Convert.ToBase64String(array2);
     }
    ```

5. Vytvořte klíč obsahu pomocí **EncryptedContentKey** (převedeno na řetězec kódovaný v kódování Base64), **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType** a hodnot **kontrolního součtu** , které jste obdrželi v předchozích krocích.
6. Přidružte entitu **ContentKey** k entitě **Asset** pomocí operace $Links.

Tento článek neukazuje, jak vygenerovat klíč AES, jak klíč zašifrovat a vypočítat kontrolní součet. 

> [!NOTE]
> 
> Při přístupu k entitám v Media Services musíte nastavit konkrétní pole a hodnoty hlaviček v požadavcích HTTP. Další informace najdete v tématu [instalace Media Services REST APIm vývoji](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní API AMS, najdete v tématu [přístup k rozhraní Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="retrieve-the-protectionkeyid"></a>Načtení ProtectionKeyId
Následující příklad ukazuje, jak načíst ProtectionKeyId a kryptografický otisk certifikátu pro certifikát, který je nutné použít při šifrování klíče obsahu. Proveďte tento krok, abyste se ujistili, že už máte na svém počítači příslušný certifikát.

Požadavek:

```console
GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
Host: media.windows.net
```

Odpověď:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 139
charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:42:52 GMT

$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}
```

## <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Načtení ProtectionKey pro ProtectionKeyId
Následující příklad ukazuje, jak načíst certifikát X. 509 pomocí ProtectionKeyId, který jste obdrželi v předchozím kroku.

Požadavek:

```console
GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
Host: media.windows.net
```


Odpověď:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 1227
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Thu, 05 Feb 2015 07:52:30 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String", "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}
```

## <a name="create-the-contentkey"></a>Vytvoření ContentKey
Po načtení certifikátu X. 509 a použití jeho veřejného klíče k zašifrování klíče obsahu vytvořte entitu **ContentKey** a odpovídajícím způsobem nastavte její hodnoty vlastností.

Jedna z hodnot, které je třeba nastavit při vytváření klíče obsahu, je typ. Vyberte jednu z těchto hodnot:

```console
public enum ContentKeyType
{
    /// <summary>
    /// Specifies a content key for common encryption.
    /// </summary>
    /// <remarks>This is the default value.</remarks>
    CommonEncryption = 0,

    /// <summary>
    /// Specifies a content key for storage encryption.
    /// </summary>
    StorageEncryption = 1,

    /// <summary>
    /// Specifies a content key for configuration encryption.
    /// </summary>
    ConfigurationEncryption = 2,

    /// <summary>
    /// Specifies a content key for Envelope encryption.  Only used internally.
    /// </summary>
    EnvelopeEncryption = 4
    }
```

Následující příklad ukazuje, jak vytvořit **ContentKey** s **ContentKeyType** sadou pro šifrování úložiště ("1") a **ProtectionKeyType** nastavenou na "0", která označuje, že ID klíče ochrany je kryptografický otisk certifikátu X. 509.  

Žádost

```console
POST https://media.windows.net/api/ContentKeys HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
Host: media.windows.net
{
"Name":"ContentKey",
"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
"ContentKeyType":"1", 
"ProtectionKeyType":"0",
"EncryptedContentKey":"your encrypted content key",
"Checksum":"calculated checksum"
}
```

Odpověď:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 777
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
Server: Microsoft-IIS/8.5
request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:37:46 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
"Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
"LastModified":"2015-02-04T02:37:46.9684379Z",
"ContentKeyType":1,
"EncryptedContentKey":"your encrypted content key",
"Name":"ContentKey",
"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
"ProtectionKeyType":0,
"Checksum":"calculated checksum"}
```

## <a name="associate-the-contentkey-with-an-asset"></a>Přidružte ContentKey k Assetu.
Po vytvoření ContentKey ho přidružte k Assetu pomocí operace $links, jak je znázorněno v následujícím příkladu:

Požadavek:

```console
POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
Host: media.windows.net

{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}
```

Odpověď:

```console
HTTP/1.1 204 No Content 
```

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

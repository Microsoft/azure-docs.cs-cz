---
title: Šifrování obsahu pomocí šifrování úložiště s použitím rozhraní AMS REST API
description: Zjistěte, jak k šifrování obsahu pomocí šifrování úložiště s použitím rozhraní AMS REST API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2019
ms.author: juliako
ms.openlocfilehash: ff198bc5e921f1c78e2d7cb7c80bfe0615fc91bc
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003257"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Šifrování obsahu pomocí šifrování úložiště 

Důrazně doporučujeme k šifrování vašeho obsahu místně pomocí 256bitového šifrování AES-256 a nahrajte ho do služby Azure Storage kde jsou uložená v klidovém stavu zašifrovaná.

Tento článek poskytuje přehled o šifrování úložiště AMS a ukazuje, jak nahrát obsah šifrování úložiště:

* Vytvořte klíč obsahu.
* Vytvoření Assetu. Nastavte AssetCreationOption StorageEncryption při vytváření prostředku.
  
     Šifrované prostředky jsou spojeny s symetrické klíče.
* Klíč obsahu odkaz na prostředek.  
* Nastavte parametry související se šifrováním na AssetFile entity.

## <a name="considerations"></a>Požadavky 

Pokud chcete dodávat šifrované prostředků úložiště, musíte nakonfigurovat zásady doručení assetu. Předtím, než můžete Streamovat prostředek, server streamování zruší šifrování úložiště a streamuje obsah pomocí zadaného doručování zásad. Další informace najdete v tématu [konfigurace zásad doručení Assetu](media-services-rest-configure-asset-delivery-policy.md).

Při přístupu k entity ve službě Media Services, musíte nastavit specifická pole hlaviček a hodnoty v požadavcích HTTP. Další informace najdete v tématu [instalace pro vývoj pro Media Services REST API](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Šifrování na straně úložiště

|Možnost šifrování|Popis|Media Services v2|Media Services v3|
|---|---|---|---|
|Šifrování úložiště služby Media Services|Šifrování AES-256, klíče spravované službou Media Services|Podporované<sup>(1)</sup>|Nepodporuje<sup>(2)</sup>|
|[Šifrování služby Storage pro neaktivní uložená Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Šifrování na straně serveru nabízených službou Azure Storage, klíče spravované službou Azure nebo podle zákazníka|Podporováno|Podporováno|
|[Šifrování na straně klienta úložiště](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Šifrování na straně klienta nabízené službou Azure storage, klíče spravované zákazníkem ve službě Key Vault|Nepodporuje se|Nepodporuje se|

<sup>1</sup> při Media Services podporuje zpracování obsahu v nezašifrované podobě nebo bez jakéhokoli typu šifrování, učiníte tak se nedoporučuje.

<sup>2</sup> v Media Services v3 šifrování úložiště (šifrování AES-256) je pouze podporována pro zpětné kompatibility při vaše prostředky se vytvořily pomocí Media Services v2. To znamená v3 spolupracuje s existující úložiště šifrované prostředky, ale nebude umožňovat vytváření nových.

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní API pro AMS, naleznete v tématu [přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Přehled šifrování služby Storage
Šifrování úložiště AMS platí **AES PEV.cenu** režim šifrování pro celý soubor.  Režim PEV.cenu AES je bloková šifra, která můžete šifrovat data libovolné délky bez nutnosti odsazení. Funguje tím, že šifruje blok čítač s algoritmus AES a XOR-ing výstup AES s daty pro šifrování nebo dešifrování.  Používá blok čítače je vytvořený tak, že zkopírujete hodnoty InitializationVector bajty 0 až 7 hodnota čítače a 8 až 15 bajtů hodnoty čítačů jsou nastaveny na nulu. Bloku 16 bajtů čítače se používají bajtů 8 až 15 (to znamená, že nejméně významných bajtů) jednoduché 64bitové celé číslo bez znaménka, která se zvýší o jedna pro každý další blok dat zpracovaných a je uložen v pořadí bajtů sítě. Pokud toto celé číslo nedosáhne maximální hodnoty (0xFFFFFFFFFFFFFFFF), jeho zvýšením resetuje čítač bloku na nulu (v bajtech 8 až 15) bez vlivu 64 bitů čítače (to znamená, bajty 0 až 7).   Zachování zabezpečení režim šifrování AES-PEV.cenu InitializationVector hodnota pro daný identifikátor klíče pro každý klíč k obsahu musí být jedinečný pro každý soubor a soubory musí být menší než 2 ^ 64 bloky v délce.  Tato jedinečná hodnota je zajistit, že hodnota čítače se nikdy znovu použije k danému klíči. Další informace o režimu PEV.cenu najdete v tématu [této wiki stránce](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (článku na wiki používá termín "Nonce" místo "InitializationVector").

Použití **šifrování úložiště** k zašifrování obsahu místně pomocí AES-256, přenosové šifrování a nahrajte ho do služby Azure Storage kde jsou uložená v klidovém stavu zašifrovaná. Prostředky chráněné pomocí šifrování úložiště jsou automaticky bez šifrování umístěny do systému souborů EFS před kódováním a volitelně se znovu zašifrují před jejich odesláním zpět v podobě nového výstupního prostředku. Případem primárního použití šifrování úložiště je, pokud chcete zajistit vysoce kvalitními vstupními multimediálními soubory pomocí silného šifrování v klidovém stavu na disku.

Aby bylo možné poskytovat šifrované prostředků úložiště, musíte nakonfigurovat zásady doručení assetu, Media Services ví, jak chcete dodávat váš obsah. Předtím, než můžete Streamovat prostředek, server streamování zruší šifrování úložiště a streamuje obsah pomocí zadaného doručování zásad (například AES, používat standard common encryption nebo bez šifrování).

## <a name="create-contentkeys-used-for-encryption"></a>Vytvoření klíčů ContentKeys používá pro šifrování
Šifrované prostředky jsou přidruženy k ukládání šifrovacích klíčů. Vytvoření klíče k obsahu pro šifrování před vytvořením souborů prostředků. Tato část popisuje postup vytvoření klíče k obsahu.

Následují obecné kroky pro generování klíčů k obsahu, které spojují s prostředky, které mají být šifrována. 

1. Šifrování úložiště náhodně Generovat klíč standardu AES 32 bajtů. 
   
    Klíč standardu AES 32 bajtů je klíče k obsahu pro váš prostředek, což znamená, že všechny soubory přidružené k tento asset nutnost používat stejný klíč obsahu při dešifrování. 
2. Volání [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) a [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) metody, chcete-li získat správný certifikát X.509 použitý k šifrování klíče.
3. Šifrování klíče pomocí veřejného klíče certifikátu X.509. 
   
   Media Services .NET SDK používá RSA s OAEP při provádění šifrování.  Vidíte příklad v .NET [EncryptSymmetricKeyData funkce](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Vytvoří hodnotu kontrolního součtu vypočítává pomocí identifikátoru klíče a klíč k obsahu. Následující příklad .NET počítá kontrolní součet pomocí části identifikátoru GUID je identifikátor klíče a vymazat obsah klíče.

    ```csharp
            public static string CalculateChecksum(byte[] contentKey, Guid keyId)
            {
                const int ChecksumLength = 8;
                const int KeyIdLength = 16;

                byte[] encryptedKeyId = null;

                // Checksum is computed by AES-ECB encrypting the KID
                // with the content key.
                using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
                {
                    rijndael.Mode = CipherMode.ECB;
                    rijndael.Key = contentKey;
                    rijndael.Padding = PaddingMode.None;

                    ICryptoTransform encryptor = rijndael.CreateEncryptor();
                    encryptedKeyId = new byte[KeyIdLength];
                    encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
                }

                byte[] retVal = new byte[ChecksumLength];
                Array.Copy(encryptedKeyId, retVal, ChecksumLength);

                return Convert.ToBase64String(retVal);
            }
    ```

5. Vytvořte klíč obsahu se **EncryptedContentKey** (převést na řetězec s kódováním base64), **ProtectionKeyId**, **ProtectionKeyType**,  **ContentKeyType**, a **kontrolního součtu** hodnoty, které jste obdrželi v předchozích krocích.

    Pro šifrování úložiště by měl obsahovat následující vlastnosti v textu požadavku.

    Vlastnost text žádosti    | Popis
    ---|---
    ID | ContentKey ID je generována pomocí následujícího formátu "nb:kid:UUID:<NEW GUID>".
    ContentKeyType | Typ obsahu key je celé číslo, které definují klíč. Pro formát šifrování úložiště hodnota je 1.
    EncryptedContentKey | Vytvoříme nový obsahu hodnotu klíče, který je hodnota oborového 256bitového (32 bajtů). Že je klíč zašifrovaný pomocí storage šifrovací certifikát X.509, který se nám načíst ze služby Microsoft Azure Media Services pomocí provádí požadavek HTTP GET pro GetProtectionKeyId a GetProtectionKey metody. Jako příklad najdete v následujícím kódu .NET: **EncryptSymmetricKeyData** metody definované [tady](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | Toto je ochrana ID klíče pro certifikát X.509 šifrování úložiště, který se použil k zašifrování naše klíče k obsahu.
    ProtectionKeyType | Jedná se o typ šifrování pro ochranu klíč, který se použil k zašifrování klíče k obsahu. Tato hodnota je StorageEncryption(1) v našem příkladu.
    Kontrolní součet |Vypočtené kontrolní součet MD5 pro klíč k obsahu. Je vypočítán tím, že šifruje ID obsahu pomocí klíče k obsahu. Příklad kódu ukazuje, jak vypočítat kontrolní součet.


### <a name="retrieve-the-protectionkeyid"></a>Načíst ProtectionKeyId
Následující příklad ukazuje, jak načíst ProtectionKeyId kryptografický otisk certifikátu pro certifikát, který je nutné použít při šifrování klíče. Proveďte tento krok, abyste měli jistotu, že máte příslušný certifikát na svém počítači.

Požadavek:

    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

Odpověď:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 139
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:42:52 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Načíst ProtectionKey ProtectionKeyId
Následující příklad ukazuje, jak načíst certifikát X.509 pomocí ProtectionKeyId, které že jste získali v předchozím kroku.

Požadavek:

    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    Host: media.windows.net

Odpověď:

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

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
    "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}

### <a name="create-the-content-key"></a>Vytvoření klíče k obsahu
Poté, co mají načíst certifikát X.509 a používá svůj veřejný klíč k šifrování klíče, vytvoření **ContentKey** entity a nastavte jeho vlastnost hodnoty odpovídajícím způsobem.

Jedna z hodnot, je nutné nastavit při vytváření obsahu je typ klíče. Při použití šifrování úložiště, nastavte hodnotu na '1'. 

Následující příklad ukazuje, jak vytvořit **ContentKey** s **ContentKeyType** pro šifrování úložiště ("1") a **ProtectionKeyType** nastavena na "0", která označuje, že Ochrana klíče ID je kryptografický otisk certifikátu X.509.  

Žádost

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net
    {
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
    "ContentKeyType":"1", 
    "ProtectionKeyType":"0",
    "EncryptedContentKey":"your encrypted content key",
    "Checksum":"calculated checksum"
    }

Odpověď:

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

## <a name="create-an-asset"></a>Vytvoření assetu
Následující příklad ukazuje, jak vytvořit asset.

**Požadavek HTTP**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

    {"Name":"BigBuckBunny" "Options":1}

**Odpověď HTTP**

V případě úspěchu se vrátí následující odpověď:

    HTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":1,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

## <a name="associate-the-contentkey-with-an-asset"></a>ContentKey přidružit určitý prostředek
Po vytvoření ContentKey, jeho přiřazení k vaší Assetu pomocí operace $links, jak je znázorněno v následujícím příkladu:

Požadavek:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Odpověď:

    HTTP/1.1 204 No Content 

## <a name="create-an-assetfile"></a>Vytvoření AssetFile
[AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) entity představuje videa nebo zvukový soubor, který je uložený v kontejneru objektů blob. Soubor prostředků je vždy přidružena k assetu a prostředek může obsahovat jeden nebo více souborů prostředků. Kodér Media Services úloh selže, pokud objekt souboru prostředku není přidružen k digitálnímu souboru v kontejneru objektů blob.

**AssetFile** instance a samotný mediální soubor jsou dva různé objekty. AssetFile instance obsahuje metadata do souboru média, zatímco mediální soubor obsahuje skutečné mediálního obsahu.

Po odeslání digitální mediální soubor do kontejneru objektů blob použijete **sloučit** požadavek HTTP na aktualizaci AssetFile s informacemi o souboru média (není zobrazené v tomto článku). 

**Požadavek HTTP**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net
    Content-Length: 164

    {  
       "IsEncrypted":"true",
       "EncryptionScheme" : "StorageEncryption", 
       "EncryptionVersion" : "1.0",       
       "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector" : "397304628502661816</d:InitializationVector",
       "Options":0,
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }

**Odpověď HTTP**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion": "1.0",
       "EncryptionScheme": "StorageEncryption",
       "IsEncrypted":true,
       "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector":"397304628502661816</d:InitializationVector",
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }

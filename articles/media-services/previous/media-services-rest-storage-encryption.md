---
title: Šifrování obsahu pomocí šifrování úložiště s využitím AMS REST API
description: Naučte se Šifrovat obsah pomocí šifrování úložiště s použitím rozhraní AMS REST API.
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
ms.date: 03/20/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 8a3a51644f61d4a1e118798986f9c6fb6c52d0e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89264160"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Šifrování obsahu pomocí šifrování úložiště

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).   > do Media Services V2 se přidávají žádné nové funkce ani funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-from-v2-to-v3.md) .
>

Tento článek poskytuje přehled šifrování úložiště AMS a ukazuje, jak nahrát šifrovaný obsah úložiště:

* Vytvořte klíč obsahu.
* Vytvořte Asset. Nastavte AssetCreationOption na StorageEncryption při vytváření Assetu.
  
     Šifrované prostředky jsou přidruženy k klíčům obsahu.
* Propojte klíč obsahu s Assetem.  
* Nastavte parametry související s šifrováním v entitách AssetFile.

## <a name="considerations"></a>Důležité informace 

Pokud chcete doručovat šifrovaný prostředek úložiště, musíte nakonfigurovat zásady doručování prostředků. Předtím, než bude možné Asset streamovat, server streamování odstraní šifrování úložiště a streamuje obsah pomocí zadaných zásad doručování. Další informace najdete v tématu [Konfigurace zásad doručení assetu](media-services-rest-configure-asset-delivery-policy.md).

Při přístupu k entitám v Media Services musíte nastavit konkrétní pole a hodnoty hlaviček v požadavcích HTTP. Další informace najdete v tématu [instalace Media Services REST APIm vývoji](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Šifrování na straně úložiště

|Možnost šifrování|Description|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services šifrování úložiště|Šifrování AES-256, Správa klíčů pomocí Media Services|Podporováno<sup>(1)</sup>|Nepodporováno<sup>(2)</sup>|
|[Šifrování služby Storage pro neaktivní neaktivní data](../../storage/common/storage-service-encryption.md)|Šifrování na straně serveru, které nabízí Azure Storage, klíč, který spravuje Azure nebo zákazník|Podporováno|Podporováno|
|[Šifrování Client-Side úložiště](../../storage/common/storage-client-side-encryption.md)|Šifrování na straně klienta, které nabízí služba Azure Storage, klíč spravovaný zákazníkem v Key Vault|Nepodporováno|Nepodporováno|

<sup>1</sup> když Media Services podporuje manipulaci s obsahem bez jakékoli formy šifrování, nedoporučuje se to.

<sup>2</sup> v Media Services V3 se šifrování úložiště (šifrování AES-256) podporuje jenom pro zpětnou kompatibilitu, když se vaše prostředky vytvořily pomocí Media Services V2. To znamená, že V3 funguje se stávajícími šifrovanými prostředky úložiště, ale neumožňuje vytváření nových.

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní API AMS, najdete v tématu [přístup k rozhraní Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Přehled šifrování úložiště
Šifrování úložiště AMS aplikuje v režimu **AES-centra** šifrování na celý soubor.  AES-centrum je bloková šifra, která může šifrovat libovolná data délky bez nutnosti vyplnění. Funguje tak, že šifruje blok čítače s algoritmem AES a potom XOR-výstupy AES s daty k šifrování nebo dešifrování.  Použitý blok čítače je vytvořen zkopírováním hodnoty InitializationVector do bajtů 0 až 7 hodnoty čítače a bajtů 8 až 15 hodnoty čítače jsou nastaveny na hodnotu nula. Z 16bitového bloku čítače jsou bajty 8 až 15 (tj. nejméně významné bajty) použity jako jednoduché 64-bit unsigned integer, který je zvýšen o jednu pro každý následující blok dat zpracovaný a je udržován v pořadí síťových bajtů. Pokud toto celé číslo dosáhne maximální hodnoty (0xFFFFFFFFFFFFFFFF), pak se zvyšuje hodnota čítače bloku na hodnotu nula (bajty 8 až 15), aniž by došlo k ovlivnění dalších 64 bitů čítače (tj. bajtů 0 až 7).   Aby bylo možné zachovat zabezpečení při šifrování v režimu AES-The, musí být hodnota InitializationVector pro daný identifikátor klíče pro každý klíč obsahu jedinečná pro každý soubor a soubory musí být kratší než 2 ^ 64 bloků.  Tato jedinečná hodnota je, aby se zajistilo, že se hodnota čítače nikdy znovu nepoužije s daným klíčem. Další informace o režimu centra najdete na [této stránce wikiwebu](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (článek na wikiwebu používá termín "nonce" místo "InitializationVector").

**Šifrování úložiště** použijte k místnímu šifrování nešifrovaného obsahu pomocí šifrování AES-256 a pak ho nahrajte do Azure Storage tam, kde je uložený zašifrovaný v klidovém stavu. Prostředky chráněné s šifrováním úložiště se před kódováním automaticky nešifrují a ukládají do systému souborů EFS a volitelně se znovu zašifrují předtím, než se znovu nahraje jako nový výstupní prostředek. Primárním případem použití šifrování úložiště je, že chcete zabezpečit vysoce kvalitní vstupní mediální soubory se silným šifrováním v klidovém provozu na disku.

Aby bylo možné doručovat šifrovaný prostředek úložiště, je nutné nakonfigurovat zásady doručení assetu, aby Media Services ví, jak chcete obsah doručovat. Předtím, než bude možné Asset streamovat, server streamování odstraní šifrování úložiště a streamuje obsah pomocí zadaných zásad doručování (například AES, Common Encryption nebo bez šifrování).

## <a name="create-contentkeys-used-for-encryption"></a>Vytvoření ContentKeys používaného pro šifrování
Šifrované prostředky jsou přidruženy k šifrovacím klíčům úložiště. Před vytvořením souborů assetu vytvořte klíč obsahu, který se má použít pro šifrování. Tato část popisuje, jak vytvořit klíč obsahu.

Níže jsou uvedené obecné kroky pro generování klíčů obsahu, které přiřadíte k prostředkům, které chcete zašifrovat. 

1. Pro šifrování úložiště vygenerujte náhodně klíč AES 32-Byte. 
   
    32 bajt AES je klíč obsahu pro váš Asset, což znamená, že všechny soubory přidružené k tomuto prostředku musí během dešifrování používat stejný klíč obsahu. 
2. Voláním metod [GetProtectionKeyId](/rest/api/media/operations/rest-api-functions#getprotectionkeyid) a [GetProtectionKey](/rest/api/media/operations/rest-api-functions#getprotectionkey) získáte správný certifikát X. 509, který se musí použít k zašifrování klíče obsahu.
3. Zašifrujte klíč obsahu pomocí veřejného klíče certifikátu X. 509. 
   
   Media Services .NET SDK používá při šifrování RSA s výplně OAEP.  Příklad rozhraní .NET můžete zobrazit ve [funkci EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Vytvořte hodnotu kontrolního součtu vypočítanou pomocí identifikátoru klíče a klíče obsahu. Následující příklad rozhraní .NET vypočítá kontrolní součet pomocí části GUID identifikátoru klíče a vymazat klíč obsahu.

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

5. Vytvořte klíč obsahu pomocí **EncryptedContentKey** (převedeno na řetězec kódovaný v kódování Base64), **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType**a hodnot **kontrolního součtu** , které jste obdrželi v předchozích krocích.

    Pro šifrování úložiště by měly být v textu požadavku zahrnuty následující vlastnosti.

    Vlastnost textu žádosti    | Description
    ---|---
    Id | ID ContentKey se generuje pomocí následujícího formátu: "NB: Kid: UUID: \<NEW GUID> ".
    ContentKeyType | Typ klíče obsahu je celé číslo, které definuje klíč. Pro formát šifrování úložiště je tato hodnota 1.
    EncryptedContentKey | Vytvoříme novou hodnotu klíče obsahu, která je 256 hodnota (32 bajtů). Klíč se šifruje pomocí certifikátu X. 509 šifrování úložiště, který načteme z Microsoft Azure Media Services spuštěním požadavku HTTP GET pro metody GetProtectionKeyId a GetProtectionKey. Příklad naleznete v následujícím kódu .NET: [zde](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)definovaná metoda **EncryptSymmetricKeyData** .
    ProtectionKeyId | Toto je ID klíče ochrany pro certifikát X. 509 šifrování úložiště, který se použil k zašifrování našeho klíče obsahu.
    ProtectionKeyType | Toto je typ šifrování pro ochranný klíč, který se použil k zašifrování klíče obsahu. Tato hodnota je StorageEncryption (1) pro náš příklad.
    Kontrolní součet |Vypočtený kontrolní součet MD5 pro klíč obsahu. Je vypočítána šifrováním ID obsahu pomocí klíče obsahu. Vzorový kód ukazuje, jak vypočítat kontrolní součet.


### <a name="retrieve-the-protectionkeyid"></a>Načtení ProtectionKeyId
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
```

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Načtení ProtectionKey pro ProtectionKeyId
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

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
"value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}
```

### <a name="create-the-content-key"></a>Vytvoření klíče obsahu
Po načtení certifikátu X. 509 a použití jeho veřejného klíče k zašifrování klíče obsahu vytvořte entitu **ContentKey** a odpovídajícím způsobem nastavte její hodnoty vlastností.

Jedna z hodnot, které je třeba nastavit při vytváření klíče obsahu, je typ. Při použití šifrování úložiště by měla být hodnota nastavená na 1. 

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

## <a name="create-an-asset"></a>Vytvoření prostředku
Následující příklad ukazuje, jak vytvořit Asset.

**Požadavek HTTP**

```console
POST https://media.windows.net/api/Assets HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net

{"Name":"BigBuckBunny" "Options":1}
```

**Odpověď HTTP**

V případě úspěchu se vrátí následující odpověď:

```console
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

## <a name="create-an-assetfile"></a>Vytvoření AssetFile
Entita [AssetFile](/rest/api/media/operations/assetfile) představuje video nebo zvukový soubor, který je uložený v kontejneru objektů BLOB. Soubor prostředků je vždy přidružen k assetu a Asset může obsahovat jeden nebo více souborů prostředků. Úloha Media Services Encoder se nezdařila v případě, že objekt souboru prostředků není přidružen k digitálnímu souboru v kontejneru objektů BLOB.

Instance **AssetFile** a skutečný mediální soubor jsou dva odlišné objekty. Instance AssetFile obsahuje metadata o souboru média, zatímco soubor média obsahuje skutečný mediální obsah.

Po nahrání digitálního mediálního souboru do kontejneru objektů BLOB budete pomocí žádosti o **sloučení** http aktualizovali AssetFile o informace o vašem mediálním souboru (neuvedené v tomto článku). 

**Požadavek HTTP**

```console
POST https://media.windows.net/api/Files HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
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
```

**Odpověď HTTP**

```console
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
```

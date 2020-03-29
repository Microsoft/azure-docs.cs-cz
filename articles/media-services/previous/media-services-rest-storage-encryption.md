---
title: Šifrování obsahu pomocí šifrování úložiště pomocí rozhraní AMS REST API
description: Přečtěte si, jak šifrovat obsah pomocí šifrování úložiště pomocí api AMS REST.
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
ms.openlocfilehash: 2a5ef1837375cc395a871f9a9860fa8bde572a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773589"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Šifrování obsahu pomocí šifrování úložiště 

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).   > Do služby Media Services v2 nejsou přidávány žádné nové funkce ani funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)
>   

Důrazně doporučujeme šifrovat obsah místně pomocí šifrování AES-256 bitů a pak jej nahrát do služby Azure Storage, kde je uložen šifrovaný v klidovém stavu.

Tento článek poskytuje přehled šifrování úložiště AMS a ukazuje, jak nahrát šifrovaný obsah úložiště:

* Vytvořte klíč obsahu.
* Vytvořte datový zdroj. Nastavte AssetCreationOption storageEncryption při vytváření asset.
  
     Šifrované datové zdroje jsou přidruženy ke klíčům obsahu.
* Propojte klíč obsahu s datovým zdrojem.  
* Nastavte parametry související s šifrováním na entitách AssetFile.

## <a name="considerations"></a>Požadavky 

Pokud chcete dodat úložiště šifrovaný prostředek, musíte nakonfigurovat zásady doručování datového zdroje. Před datovým proudem datový chod server datových proudů odebere šifrování úložiště a streamuje váš obsah pomocí zadaných zásad doručení. Další informace naleznete [v tématu Konfigurace zásad pro doručování datových zdrojů](media-services-rest-configure-asset-delivery-policy.md).

Při přístupu k entitám ve službě Media Services je nutné nastavit konkrétní pole záhlaví a hodnoty v požadavcích HTTP. Další informace naleznete [v tématu Setup for Media Services REST API Development](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Šifrování na straně úložiště

|Možnost šifrování|Popis|Media Services v2|Media Services v3|
|---|---|---|---|
|Šifrování úložiště mediálních služeb|Šifrování AES-256, klíč spravovaný mediálními službami|Podporované<sup>(1)</sup>|Není podporováno<sup>(2)</sup>|
|[Šifrování služby úložiště pro data v klidovém stavu](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Šifrování na straně serveru nabízené službou Azure Storage, klíč spravovaný Azure nebo zákazníkem|Podporuje se|Podporuje se|
|[Šifrování na straně klienta úložiště](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Šifrování na straně klienta nabízené úložištěm Azure, klíč spravovaný zákazníkem v trezoru klíčů|Nepodporuje se|Nepodporuje se|

<sup>1</sup> Zatímco Media Services podporuje zpracování obsahu v jasné /bez jakékoli formy šifrování, to se nedoporučuje.

<sup>2</sup> Ve službě Media Services v3 je šifrování úložiště (šifrování AES-256) podporováno pouze pro zpětnou kompatibilitu, když byly vaše datové zdroje vytvořeny pomocí služby Media Services v2. Což znamená, že v3 pracuje s existujícími zašifrovanými prostředky úložiště, ale neumožní vytváření nových.

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní AMS API, najdete [v tématu Přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Přehled šifrování úložiště
Šifrování úložiště AMS aplikuje šifrování režimu **AES-CTR** na celý soubor.  Režim AES-CTR je bloková šifra, která může šifrovat libovolná data délky bez nutnosti odsazení. Pracuje šifrováním protibloku pomocí algoritmu AES a poté xor-ing výstup AES s daty pro šifrování nebo dešifrování.  Použitý blok čítače je vytvořen zkopírováním hodnoty InitializationVector na bajty 0 až 7 hodnoty čítače a bajty 8 až 15 hodnoty čítače jsou nastaveny na nulu. Z bloku 16bajtového čítače se bajty 8 až 15 (tj. nejméně významné bajty) používají jako jednoduché 64bitové nepodepsané celé číslo, které je přírůstkem o jeden pro každý následující blok zpracovávaných dat a je udržováno v pořadí bajtů v síti. Pokud toto celé číslo dosáhne maximální hodnoty (0xFFFFFFFFFFFFFF), pak zvýšení obnoví čítač bloku na nulu (bajty 8 až 15) bez ovlivnění dalších 64 bitů čítače (to znamená bajtů 0 až 7).   Aby byla zachována bezpečnost šifrování režimu AES-CTR, musí být hodnota InitializationVector pro daný klíč pro každý klíč obsahu jedinečná pro každý soubor a soubory musí být kratší než 2^64 bloků.  Tato jedinečná hodnota je zajistit, že hodnota čítače je nikdy znovu použít s daným klíčem. Další informace o režimu CTR naleznete na [této wiki stránce](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (článek wiki používá termín "Nonce" místo "InitializationVector").

Pomocí **šifrování úložiště** zašifrute svůj čistý obsah místně pomocí šifrování AES-256 bitů a pak ho nahrajte do Služby Azure Storage, kde je uložen zašifrovaný v klidovém stavu. Prostředky chráněné šifrováním úložiště jsou automaticky nezašifrovány a umístěny do šifrovaného systému souborů před kódováním a volitelně znovu zašifrovány před odesláním zpět jako nový výstupní prostředek. Primární případ použití pro šifrování úložiště je, když chcete zabezpečit vysoce kvalitní vstupní mediální soubory se silným šifrováním v klidovém stavu na disku.

Chcete-li dodat úložiště šifrovaný datový zdroj, musíte nakonfigurovat zásady doručování datového zdroje tak, aby mediální služby věděly, jak chcete doručovat svůj obsah. Před datovým proudem datový proud server datových proudů odebere šifrování úložiště a streamuje váš obsah pomocí zadaných zásad doručení (například AES, společné šifrování nebo žádné šifrování).

## <a name="create-contentkeys-used-for-encryption"></a>Vytvořit klávesy ContentKeys používané pro šifrování
Šifrované datové zdroje jsou přidruženy k šifrovacím klíčům úložiště. Před vytvořením souborů datových zdrojů vytvořte klíč obsahu, který se použije pro šifrování. Tato část popisuje, jak vytvořit klíč obsahu.

Níže jsou uvedeny obecné kroky pro generování klíčů obsahu, které přidružíte k prostředkům, které chcete zašifrovat. 

1. Pro šifrování úložiště náhodně vygenerujte 32bajtova klíče AES. 
   
    32bajtový klíč AES je klíč obsahu pro váš datový zdroj, což znamená, že všechny soubory přidružené k tomuto datovému zdroji musí během dešifrování používat stejný klíč obsahu. 
2. Volání [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) a [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) metody získat správný certifikát X.509, který musí být použit k šifrování klíče obsahu.
3. Šifrujte klíč obsahu pomocí veřejného klíče certifikátu X.509. 
   
   Media Services .NET SDK používá RSA s OAEP při šifrování.  Příklad rozhraní .NET můžete zobrazit ve [funkci EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Vytvořte hodnotu kontrolního součtu vypočtenou pomocí identifikátoru klíče a klíče obsahu. Následující příklad .NET vypočítá kontrolní součet pomocí části GUID identifikátoru klíče a klíče vymazat obsah.

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

5. Vytvořte klíč Obsahu s hodnotami **EncryptedContentKey** (převedenými na řetězec kódovaný base64), **protectionkeyid**, **ProtectionKeyType**, **ContentKeyType**a **Kontrolní součet,** které jste obdrželi v předchozích krocích.

    Pro šifrování úložiště by měly být zahrnuty následující vlastnosti v těle požadavku.

    Vlastnost tělo požadavku    | Popis
    ---|---
    ID | ContentKey ID je generováno pomocí následujícího formátu, "nb:kid:UUID:\<NEW GUID>".
    ContentKeyType | Typ klíče obsahu je celé číslo, které definuje klíč. Pro formát šifrování úložiště je hodnota 1.
    Šifrovaný obsahový klíč | Vytvoříme novou hodnotu klíče obsahu, která je 256bitovou (32 bajtů). Klíč je šifrován pomocí certifikátu šifrování úložiště X.509, který načteme ze služby Microsoft Azure Media Services spuštěním požadavku HTTP GET pro metody GetProtectionKeyId a GetProtectionKey. Jako příklad například viz následující kód .NET: metoda **EncryptSymmetricKeyData** [definovaná zde](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | Toto je ID ochranného klíče pro certifikát šifrování úložiště X.509, který byl použit k šifrování našeho klíče obsahu.
    ProtectionKeyType | Toto je typ šifrování pro ochranný klíč, který byl použit k šifrování klíče obsahu. Tato hodnota je StorageEncryption(1) pro náš příklad.
    Kontrolní součet |Vypočítaný kontrolní součet MD5 pro klíč obsahu. Vypočítá se šifrováním ID obsahu pomocí klíče obsahu. Ukázkový kód ukazuje, jak vypočítat kontrolní součet.


### <a name="retrieve-the-protectionkeyid"></a>Načíst ProtectionKeyId
Následující příklad ukazuje, jak načíst ProtectionKeyId, kryptografický otisk certifikátu, pro certifikát, který je nutné použít při šifrování klíče obsahu. Tento krok se ujistěte, že již máte v počítači příslušný certifikát.

Požadavek:

    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Načíst ProtectionKey pro ProtectionKeyId
Následující příklad ukazuje, jak načíst certifikát X.509 pomocí ProtectionKeyId, které jste obdrželi v předchozím kroku.

Požadavek:

    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
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

### <a name="create-the-content-key"></a>Vytvoření klíče obsahu
Po načtení certifikátu X.509 a použití jeho veřejného klíče k šifrování klíče obsahu vytvořte entitu **ContentKey** a odpovídajícím způsobem nastavte její hodnoty vlastností.

Jednou z hodnot, které je nutné nastavit při vytváření klíče obsahu je typ. Při použití šifrování úložiště by měla být hodnota nastavena na hodnotu 1. 

Následující příklad ukazuje, jak vytvořit **ContentKey** s **ContentKeyType** nastavit pro šifrování úložiště ("1") a **ProtectionKeyType** nastavena na "0" označující, že ID ochranného klíče je x.509 kryptografický otisk certifikátu.  

Žádost

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

## <a name="create-an-asset"></a>Vytvoření datového zdroje
Následující příklad ukazuje, jak vytvořit datový zdroj.

**Požadavek HTTP**

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

**Odpověď HTTP**

Pokud je úspěšná, je vrácena následující odpověď:

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

## <a name="associate-the-contentkey-with-an-asset"></a>Přidružení obsahu klíče k datovému zdroji
Po vytvoření ContentKey, přidružte jej k asset pomocí operace $links, jak je znázorněno v následujícím příkladu:

Požadavek:

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

Odpověď:

    HTTP/1.1 204 No Content 

## <a name="create-an-assetfile"></a>Vytvoření souboru assetů
Entita [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) představuje video nebo zvukový soubor, který je uložen v kontejneru objektů blob. Soubor datového zdroje je vždy přidružen k datovému zdroji a datový zdroj může obsahovat jeden nebo více souborů datových zdrojů. Úloha kodéru mediálních služeb se nezdaří, pokud objekt souboru datového zdroje není přidružen k digitálnímu souboru v kontejneru objektů blob.

Instance **AssetFile** a skutečný mediální soubor jsou dva odlišné objekty. Instance AssetFile obsahuje metadata o mediálním souboru, zatímco mediální soubor obsahuje skutečný mediální obsah.

Po nahrání digitálního mediálního souboru do kontejneru objektů blob použijete požadavek **HTTP sloučení** k aktualizaci souboru AssetFile s informacemi o mediálním souboru (není zobrazen v tomto článku). 

**Požadavek HTTP**

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

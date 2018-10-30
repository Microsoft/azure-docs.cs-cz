---
title: Začínáme s doručováním obsahu na vyžádání pomocí rozhraní REST | Dokumentace Microsoftu
description: Tento kurz vás provede jednotlivými kroky implementace aplikace pro doručování obsahu na vyžádání pomocí Azure Media Services pomocí rozhraní REST API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: fbfb52b758089acf65f89c62f716d46d62b0f289
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233816"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Začínáme s doručováním obsahu na vyžádání pomocí REST
[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

Tento rychlý start vás provede jednotlivými kroky implementace aplikace pro doručování obsahu videa na vyžádání (Video-on-demand) pomocí rozhraní REST API služby Azure Media Services (AMS).

Kurz představuje základní pracovní postup služby Media Services a nejběžnější programovací objekty a úkoly, které Media Services vyžaduje. Po dokončení tohoto kurzu budete moct Streamovat nebo progresivně stáhnout ukázkový multimediální soubor, který jste nahráli, kódování a stáhli.

Následující obrázek ukazuje některé z nejčastěji používaných objektů při vývoji aplikace VoD na základě modelu Media Services OData.

Kliknutím na obrázek zobrazíte jeho plnou velikost.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>Požadavky
Jak začít s vývojem pomocí služby Media Services pomocí rozhraní REST API jsou požadovány následující součásti.

* Účet Azure. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* Účet Media Services. Pokud chcete vytvořit účet Media Services, přečtěte si článek [Jak vytvořit účet Media Services](media-services-portal-create-account.md).
* Přehled o tom, jak vývoj s využitím REST API služby Media Services. Další informace najdete v tématu [přehled rozhraní REST API pro Media Services](media-services-rest-how-to-use.md).
* Aplikace podle vašeho výběru, který může odesílat požadavky a odpovědi HTTP. Tento kurz používá [Fiddler](http://www.telerik.com/download/fiddler).

Tyto úlohy jsou uvedeny v tomto rychlém startu.

1. Spuštění koncového bodu streamování (pomocí webu Azure Portal).
2. Připojte se k účtu Media Services pomocí rozhraní REST API.
3. Vytvoření nového prostředku a odeslání videosouboru pomocí rozhraní REST API.
4. Zdrojový soubor zakódujte do sady souborů MP4 s adaptivní přenosovou rychlostí pomocí rozhraní REST API.
5. Publikování assetu a získání streamování a progresivního stahování adresy URL pomocí rozhraní REST API.
6. Přehrání obsahu

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždycky používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odesílání), použijte stejné ID zásad. Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

Podrobnosti o entitách AMS REST použité v tomto článku najdete v tématu [Reference k rozhraní API REST Azure Media Services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference). Viz také [konceptech Azure Media Services](media-services-concepts.md).

>[!NOTE]
>Při přístupu k entity ve službě Media Services, musíte nastavit specifická pole hlaviček a hodnoty v požadavcích HTTP. Další informace najdete v tématu [instalace pro vývoj pro Media Services REST API](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Spuštění koncového bodu streamování pomocí webu Azure Portal

Při práci s Azure Media Services je jedním nejběžnější scénářů doručování videa prostřednictvím streamování s adaptivní přenosovou rychlostí. Služba Media Services poskytuje dynamické balení, které umožňuje doručovat obsah s adaptivní přenosovou rychlostí s kódováním MP4 ve formátech streamování podporovaných službou Media Services (MPEG DASH, HLS, technologie Smooth Streaming). není přitom potřeba ukládat předem zabalené verze pro každý z těchto formátů streamování.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**.

Pokud chcete spustit koncový bod streamování, postupujte takto:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. V okně Nastavení klikněte na Koncové body streamování.
3. Klikněte na výchozí koncový bod streamování.

    Zobrazí se okno VÝCHOZÍ KONCOVÝ BOD STREAMOVÁNÍ – PODROBNOSTI.

4. Klikněte na ikonu Spustit.
5. Kliknutím na tlačítko Uložit uložte provedené změny.

## <a id="connect"></a>Připojte se k účtu Media Services pomocí rozhraní REST API

Informace o tom, jak se připojit k rozhraní API pro AMS, naleznete v tématu [přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a id="upload"></a>Vytvoření nového prostředku a odeslání videosouboru pomocí rozhraní REST API

Ve službě Media Services můžete digitální soubory nahrát do assetu. **Asset** entita může obsahovat video, zvuk, obrázky, kolekci miniatur, text sleduje a titulků soubory (a metadata o těchto souborech.)  Jakmile soubory odešlete do assetu, váš obsah bezpečně uložen v cloudu pro další zpracování a streamování.

Jedna z hodnot, které je nutné zadat při vytváření prostředku je možností vytvoření prostředku. **Možnosti** je vlastnost, která popisuje možnosti šifrování, které je možné vytvořit prostředek s hodnotou výčtu. Platná hodnota je jedna z hodnot v seznamu níže není kombinací hodnot z tohoto seznamu:

* **Žádný** = **0** – nepoužívá se žádné šifrování. Při použití této možnosti není váš obsah chráněný během přenosu ani při umístění v úložišti.
    Pokud chcete pomocí progresivního stahování dodávat obsah ve formátu MP4, použijte tuto možnost.
* **StorageEncrypted** = **1** – šifruje vaše nešifrovaného obsahu pomocí 256bitového šifrování AES-256 a nahraje ho do služby Azure Storage kde jsou uložená v klidovém stavu zašifrovaná. Prostředky chráněné pomocí šifrování úložiště jsou před kódováním automaticky bez šifrování umístěny do systému souborů EFS a volitelně se znovu zašifrují před jejich odesláním zpět v podobě nového výstupního prostředku. Případem primárního použití šifrování úložiště je situace, kdy chcete zabezpečit soubory s vysoce kvalitními vstupními multimediálními soubory pomocí silného šifrování na disku.
* **CommonEncryptionProtected** = **2** – tuto možnost použijte, pokud nahráváte obsah, který byl zašifrován a chráněný běžným šifrováním nebo DRM PlayReady (například technologie Smooth Streaming chránit pomocí technologie PlayReady DRM).
* **EnvelopeEncryptionProtected** = **4** – tuto možnost použijte, pokud odesíláte HLS se šifrováním pomocí standardu AES. Soubory se musí kódováním a zašifrované pomocí Správce transformací.

### <a name="create-an-asset"></a>Vytvoření assetu
Prostředek je kontejner pro více typy nebo množiny objektů ve službě Media Services, včetně videa, zvuk, obrázky, kolekci miniatur, textové stopy a soubory s titulky. Vytvoření prostředku v rozhraní REST API vyžaduje odesílá požadavek POST do služby Media Services a umístit všechny informace o vlastnosti týkající se vašeho prostředku v textu požadavku.

Následující příklad ukazuje, jak vytvořit asset.

**Požadavek HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45

    {"Name":"BigBuckBunny.mp4", "Options":"0"}


**Odpověď HTTP**

V případě úspěchu se vrátí následující:

    HTTP/1.1 201 Created
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
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

### <a name="create-an-assetfile"></a>Vytvoření AssetFile
[AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) entity představuje videa nebo zvukový soubor, který je uložený v kontejneru objektů blob. Soubor prostředků je vždy přidružena k assetu a prostředek může obsahovat jeden nebo více AssetFiles. Kodér Media Services úloh selže, pokud objekt souboru prostředku není přidružen k digitálnímu souboru v kontejneru objektů blob.

Po odeslání souboru digitálních médií do kontejneru objektů blob, můžete použít **sloučit** požadavku HTTP AssetFile aktualizovat informace o souboru média (jak je uvedeno dále v tomto tématu).

**Požadavek HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164

    {  
       "IsEncrypted":"false",
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
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Vytváření AccessPolicy pomocí oprávnění pro zápis
Před nahráním všech souborů do úložiště objektů blob, nastavte přístup zásady oprávnění pro zápis do assetu. K tomuto účelu POŠLE požadavek HTTP do sady entit AccessPolicies. Definovat hodnotu doba trvání v minutách, po vytvoření nebo se zobrazí chybovou zprávu 500 interní Server vrátit v odpovědi. Další informace o AccessPolicies najdete v tématu [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

Následující příklad ukazuje, jak vytvořit AccessPolicy:

**Požadavek HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74

    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}

**Odpověď HTTP**

V případě úspěchu se vrátí následující odpověď:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>Získat adresu URL pro odeslání

Získat adresu URL skutečné nahrávání, vytvořte Lokátor SAS. Lokátory definují čas spuštění a typ koncového bodu připojení pro klienty, kteří mají přístup k souborům v prostředku. Můžete vytvořit více Lokátor entit pro danou AccessPolicy a Asset dvojici pro zpracování různých klientských požadavků a potřebám. Každá z těchto lokátory používá hodnoty StartTime plus hodnotu doba trvání v minutách AccessPolicy určit dobu, kterou lze použít adresu URL. Další informace najdete v tématu [Lokátor](https://docs.microsoft.com/rest/api/media/operations/locator).

Adresa URL SAS má následující formát:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Musí být splněny určité předpoklady:

* Nemůžete mít více než pět jedinečné lokátory přidružené k dané Assetu najednou. 
* Pokud je potřeba nahrát soubory hned, byste měli nastavit položka StartTime hodnotu 5 minut před aktuálním časem. Je to proto můžou existovat hodiny nerovnoměrné rozdělení mezi klientské počítače a služby Media Services. Navíc musí být hodnota StartTime v následujícím formátu data a času: rrrr-MM-: ssZ (například "2014-05-23T17:53:50Z").    
* Může být druhý 30 – 40 zpoždění, jakmile se vytvoří Lokátor, až bude k dispozici pro použití. Tento problém se vztahuje [adresy URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) a lokátory původu.

Následující příklad ukazuje, jak vytvořit lokátor adresy URL SAS, tak jak je definoval vlastnost typ v textu požadavku ("1" pro Lokátor SAS) a "2" pro Lokátor origin na vyžádání. **Cesta** vlastnost vrácená obsahuje adresu URL, kterou je nutné použít k odeslání souboru.

**Požadavek HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178

    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**Odpověď HTTP**

V případě úspěchu se vrátí následující odpověď:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Nahrání souboru do kontejneru úložiště objektů blob
Jakmile budete mít AccessPolicy a Lokátor nastavit, skutečný soubor nahraje do kontejneru úložiště objektů blob v Azure pomocí rozhraní REST API služby Azure Storage. Musíte nahrát soubory jako objekty BLOB bloku. Objekty BLOB stránky nejsou podporovány službou Azure Media Services.  

> [!NOTE]
> Je nutné přidat název souboru pro soubor, který chcete nahrát do Lokátor **cesta** hodnotu přijatou v předchozí části. Například https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?. . . .
>
>

Další informace o práci s objekty BLOB služby Azure storage najdete v tématu [rozhraní REST API služby Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

### <a name="update-the-assetfile"></a>Aktualizace AssetFile
Teď, když nahrajete váš soubor, aktualizujte informace o FileAsset velikosti (a další). Příklad:

    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Odpověď HTTP**

V případě úspěchu se vrátí následující:

    HTTP/1.1 204 No Content
    ...

## <a name="delete-the-locator-and-accesspolicy"></a>Odstraňte Lokátor a AccessPolicy
**Požadavek HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**Odpověď HTTP**

V případě úspěchu se vrátí následující:

    HTTP/1.1 204 No Content
    ...

**Požadavek HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

**Odpověď HTTP**

V případě úspěchu se vrátí následující:

    HTTP/1.1 204 No Content
    ...

## <a id="encode"></a>Zdrojový soubor zakódovat do sady souborů MP4 s adaptivní přenosovou rychlostí

Po ingestování, které je možné kódovat prostředky do Media Services, média, transmuxovat, označit a tak dále než ho se doručí do klientů. Tyto aktivity se plánují a spouštějí s několika instancemi role na pozadí, abyste měli zajištěný vysoký výkon a dostupnost. Tyto aktivity se nazývají úlohy a každá úloha se skládá z atomických úloh, které vykonávají samotnou práci na souboru prostředku (Další informace najdete v tématu [úlohy](https://docs.microsoft.com/rest/api/media/operations/job), [úloh](https://docs.microsoft.com/rest/api/media/operations/task) popisy).

Jak jsem už zmínili dřív, při práci se službou Azure Media Services je jedním nejběžnější scénářů doručování adaptivní přenosové rychlosti streamování vašim klientům. Služba Media Services umí dynamicky balit sady souborů MP4 do jedné z následujících formátů: HTTP Live Streaming (HLS), technologie Smooth Streaming, MPEG DASH.

Následující části ukazuje, jak vytvořit úlohu, která obsahuje jednu úlohu kódování. Úloha Určuje převod souboru mezzanine do sady s adaptivní přenosovou rychlostí soubory MP4 rychlostmi pomocí **kodéru Media Encoder Standard**. V části také ukazuje, jak monitorovat úlohu průběh zpracování. Po dokončení úlohy by mohli k vytvoření lokátorů, které jsou potřeba k získání přístupu k vaše prostředky.

### <a name="get-a-media-processor"></a>Získat procesor médií
Ve službě Media Services mediálním procesorem je komponenta, která zpracovává zpracování specifické pro úlohy, jako je například kódování, převod formátů, šifrování nebo dešifrování mediálního obsahu. Pro úlohu kódování uvedené v tomto kurzu budeme používat Media Encoder Standard.

Následující kód požádá o id kodér.

**Požadavek HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**Odpověď HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>Vytvoření úlohy
Každá úloha může mít jednu nebo více úloh v závislosti na typu zpracování, které chcete dosáhnout. Prostřednictvím rozhraní REST API, můžete vytvořit úlohy a jejich související úlohy v jedné ze dvou způsobů: úloh může být definována vložením prostřednictvím navigační vlastnost úkoly u úlohy entit nebo dávkovým zpracováním OData. Media Services SDK používá dávkové zpracování. Pro lepší čitelnost příklady kódu v tomto článku, ale úlohy jsou definována vložením. Informace o zpracování služby batch najdete v tématu [Open Data Protocol (OData), dávkové zpracování](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

Následující příklad ukazuje, jak vytvořit a odeslat úlohu pomocí jednoho úkolu nastavená na kódování videa na konkrétní řešení a kvalitu. Následující část dokumentace obsahuje seznam všech [předvolby úloh](http://msdn.microsoft.com/library/mt269960) podporován pomocí procesoru Media Encoder Standard.  

**Požadavek HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"Adaptive Streaming",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**Odpověď HTTP**

V případě úspěchu se vrátí následující odpověď:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  

             ]
          }
       }
    }


Poznámka: v jakékoli žádosti o úlohu některé důležité věci:

* Taskbody – vlastnosti používaly literál XML definovat počet vstupních nebo výstupních prostředky, které jsou používány úkolu. Tento článek úloh obsahuje definici schématu XML pro XML.
* V definici taskbody – každé vnitřní hodnota <inputAsset> a <outputAsset> musí být nastavena jako JobInputAsset(value) nebo JobOutputAsset(value).
* Úkol může mít více výstupní assety. Jeden JobOutputAsset(x) jde použít jenom jednou jako výstup úlohy v rámci úlohy.
* Můžete zadat JobInputAsset nebo JobOutputAsset jako vstupní asset úlohy.
* Úlohy nesmí tvoří cyklus.
* Hodnota parametru, který můžete předat JobInputAsset nebo JobOutputAsset představuje hodnotu indexu pro určitý prostředek. Skutečné prostředky jsou definovány v navigační vlastnosti InputMediaAssets a OutputMediaAssets na definici entit úlohy.

> [!NOTE]
> Protože Media Services je založená na protokolu OData v3, jednotlivé prostředky v InputMediaAssets a OutputMediaAssets navigační vlastnost kolekce odkazují "__metadata: identifikátor uri" dvojice název hodnota.
>
>

* InputMediaAssets mapuje na jeden nebo více prostředků, které jste vytvořili ve službě Media Services. OutputMediaAssets jsou vytvořeny v systému. Neodkazovat existující prostředek.
* OutputMediaAssets může mít název pomocí atributu assetName. Pokud tento atribut není k dispozici, pak OutputMediaAsset jmenuje bez ohledu na hodnotu vnitřní text z <outputAsset> element je s příponou hodnotu název úlohy nebo úlohu s Id hodnoty (v případě, kdy není definována vlastnost Name). Například pokud nastavíte hodnotu assetName na "Ukázkový", pak název OutputMediaAsset vlastnost by být nastavena na "Ukázkový". Ale pokud jste nenastavili hodnotu assetName, ale nastaven název úlohy, který "NewJob", pak název OutputMediaAsset bude "_NewJob JobOutputAsset (hodnota)".

    Následující příklad ukazuje, jak nastavit atribut assetName:

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* Pokud chcete povolit řetězení úloh:

  * Úloha musí mít aspoň dva úkoly
  * Musí existovat alespoň jeden úkol, jejichž vstup je výstup jiný úkol v úloze.

Další informace najdete v tématu [vytvoření úlohy kódování pomocí Media Services REST API](media-services-rest-encode-asset.md).

### <a name="monitor-processing-progress"></a>Monitorování průběh zpracování
Stav úlohy můžete načíst pomocí vlastnosti State, jak je znázorněno v následujícím příkladu:

**Požadavek HTTP**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**Odpověď HTTP**

V případě úspěchu se vrátí následující odpověď:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT

    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Zrušení úlohy
Služba Media Services můžete zrušit probíhající úlohy prostřednictvím funkce CancelJob. Toto volání vrátí kód 400 chyby, pokud se pokusíte zrušit úlohu při zrušení jeho stav, zrušení, chyby nebo dokončení.

Následující příklad ukazuje, jak volat CancelJob.

**Požadavek HTTP**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


Pokud je úspěšná, kód 204 odpovědi je vrácen s žádné zprávy.

> [!NOTE]
> Je nutné kódování URL úlohu s id (obvykle nb:jid:UUID: nějaká_hodnota) při předávání v CancelJob jako parametr.
>
>

### <a name="get-the-output-asset"></a>Získání prostředku výstupu
Následující kód ukazuje, jak požádat o prostředku výstupu ID.

**Požadavek HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**Odpověď HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }

## <a id="publish_get_urls"></a>Publikování assetu a získání streamování a progresivního stahování adresy URL pomocí rozhraní REST API

Pokud chcete prostředek streamovat nebo stáhnout, musíte ho nejdřív „publikovat“ vytvořením lokátoru. Lokátory zajišťují přístup k souborům, které jsou obsaženy v assetu. Služba Media Services podporuje dva typy lokátorů: lokátor OnDemandOrigin, používaný ke streamování médií (například MPEG DASH, HLS nebo technologie Smooth Streaming), a lokátor s přístupovým podpisem (SAS), používaný ke stahování mediálních souborů. 

Po vytvoření lokátorů můžete sestavit adresy URL, které se používají ke streamování a stahování souborů.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**.

Streamovací adresa URL pro technologii Smooth Streaming má následující formát:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Streamovací adresa URL pro HLS má následující formát:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Streamovací adresa URL pro MPEG DASH má následující formát:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

SAS adresa URL používaná ke stahování souborů má následující formát:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Tato část ukazuje, jak provádět následující úlohy nezbytné "publikovat" vaše prostředky.  

* Vytváří AccessPolicy s oprávněním ke čtení
* Vytvoření SAS URL pro stahování obsahu
* Vytváření původní adresu URL pro streamování obsahu

### <a name="creating-the-accesspolicy-with-read-permission"></a>Vytváří AccessPolicy s oprávněním ke čtení
Před stažením nebo streamování všechny mediální obsah, nejdřív definovat AccessPolicy s oprávněním pro čtení a vytvořit odpovídající Lokátor entitu, která určuje typ mechanismy, které chcete povolit pro klienty. Další informace o dostupných vlastnostech najdete v části [vlastností Entity AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties).

Následující příklad ukazuje, jak určit AccessPolicy pro oprávnění ke čtení pro daný prostředek.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Pokud je úspěšná, kód 201 úspěchu je vrácena popisující AccessPolicy entitu, kterou jste vytvořili. Potom použijte AccessPolicy Id spolu s Id prostředku asset, který obsahuje soubor, který má být dodána mohla vytvořit entita Lokátor (například výstupní asset).

> [!NOTE]
> Tento základní pracovní postup je stejný jako nahrání souboru při ingestování prostředek (jak bylo popsáno dříve v tomto tématu). Také jako je odesílání souborů, pokud vy (nebo klienty) potřebovat přístup k souborům okamžitě, můžete nastavte položka StartTime hodnotu 5 minut před aktuálním časem. Tato akce je nezbytná, protože můžou existovat hodiny nerovnoměrné rozdělení mezi klientem a Media Services. Hodnota StartTime musí být ve formátu data a času: rrrr-MM-: ssZ (například "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Vytvoření SAS URL pro stahování obsahu
Následující kód ukazuje, jak získat adresu URL, která slouží k stažení souboru média, vytvořili a nahráli dříve. AccessPolicy má sadu oprávnění pro čtení a Lokátor cesty odkazuje na adresu URL SAS ke stažení.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

V případě úspěchu se vrátí následující odpověď:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }

Vrácený **cesta** vlastnost obsahuje adresu URL SAS.

> [!NOTE]
> Pokud si stáhnete obsah šifrování úložiště, musíte ručně dešifrovat před vykreslením nebo použít MediaProcessor dešifrování úložiště v rámci úlohy zpracování výstupních zpracovaných souborů v nezašifrované podobě do OutputAsset a pak si stáhnout z tohoto prostředku. Další informace o zpracování naleznete v tématu Vytvoření úlohy kódování pomocí Media Services REST API. SAS URL lokátory navíc nejde aktualizovat, po jejich vytvoření. Například nelze znovu použít stejné Lokátor se aktualizovaná hodnota StartTime. Toto je vzhledem ke způsobu vytvoření adres URL SAS. Pokud chcete pro přístup k prostředku ke stažení po vypršení platnosti lokátoru, musíte vytvořit nový s novou StartTime.
>
>

### <a name="download-files"></a>Stažení souborů
Jakmile budete mít AccessPolicy a Lokátor nastavit, si můžete stáhnout soubory pomocí rozhraní REST API služby Azure Storage.  

> [!NOTE]
> Je nutné přidat název souboru pro soubor, který chcete stáhnout Lokátor **cesta** hodnotu přijatou v předchozí části. Například https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?. . . .
>
>

Další informace o práci s objekty BLOB služby Azure storage najdete v tématu [rozhraní REST API služby Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

V důsledku úlohy kódování, které jste provedli, starší (kódování do sady adaptivní MP4) budete mít soubory MP4 s více, které jste ho progresivně stahovat. Příklad:    

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>Vytvoření adresy URL streamování pro streamování obsahu
Následující kód ukazuje, jak vytvořit lokátor streamování adresy URL:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

V případě úspěchu se vrátí následující odpověď:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Datový proud Smooth Streaming adresu URL zdroje v streamování media player, připojte cestu souboru, za nímž následuje manifestu vlastnost s názvem technologie Smooth Streaming "/ manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Streamování HLS, připojte (format = m3u8-aapl) po "/ manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Streamování MPEG DASH, připojte (format = mpd-time-csf) po "/ manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Přehrání obsahu
Pokud chcete video streamovat, použijte [přehrávač služby Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Pokud chcete otestovat progresivní stahování, vložte adresu URL do prohlížeče (například aplikace Internet Explorer, Chrome, Safari).

## <a name="next-steps-media-services-learning-paths"></a>Další kroky: Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

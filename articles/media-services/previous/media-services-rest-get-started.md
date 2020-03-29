---
title: Začínáme s doručováním obsahu na vyžádání pomocí REST | Dokumenty společnosti Microsoft
description: Tento kurz vás provede kroky implementace aplikace pro doručování obsahu na vyžádání pomocí Azure Media Services pomocí rozhraní REST API.
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8989acc6d21a3c53be9d97c74ed7fbf03ba54819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773683"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Začínáme s doručováním obsahu na vyžádání pomocí rest  

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Tento rychlý start vás provede kroky implementace aplikace pro doručování obsahu video na vyžádání (VoD) pomocí azure media services (AMS) REST API.

Kurz představuje základní pracovní postup služby Media Services a nejběžnější programovací objekty a úkoly, které Media Services vyžaduje. Po dokončení kurzu můžete streamovat nebo postupně stahovat ukázkový mediální soubor, který jste nahráli, zakódovali a stáhli.

Následující obrázek ukazuje některé z nejčastěji používaných objektů při vývoji aplikace VoD na základě modelu Media Services OData.

Kliknutím na obrázek zobrazíte jeho plnou velikost.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>Požadavky
Následující předpoklady jsou nutné pro spuštění vývoje s media services s REST API.

* Účet Azure. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* Účet Media Services. Pokud chcete vytvořit účet Media Services, přečtěte si článek [Jak vytvořit účet Media Services](media-services-portal-create-account.md).
* Pochopení toho, jak vyvíjet pomocí rozhraní REST API mediálních služeb. Další informace naleznete v tématu [Přehled rozhraní REST API služby Media Services](media-services-rest-how-to-use.md).
* Aplikace podle vašeho výběru, která může odesílat požadavky http a odpovědi. Tento kurz používá [Šumař](https://www.telerik.com/download/fiddler).

Následující úkoly jsou zobrazeny v tomto rychlém startu.

1. Spuštění koncového bodu streamování (pomocí webu Azure Portal).
2. Připojte se k účtu Mediálních služeb pomocí rozhraní REST API.
3. Vytvořte nový datový zdroj a nahrajte video soubor s rozhraním REST API.
4. Zakódujte zdrojový soubor do sady adaptivních souborů MP4 s přenosovou rychlostí pomocí rozhraní REST API.
5. Publikujte datové zdroje a získejte streamované a progresivní adresy URL stahování pomocí rozhraní REST API.
6. Přehrání obsahu

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Stejné ID zásad použijte, pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez nahrávání). Další informace naleznete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

Podrobnosti o entitách AMS REST použitých v tomto článku najdete v článku [odkaz na rozhraní REST API služby Azure Media Services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference). Viz také [koncepty Mediálních služeb Azure](media-services-concepts.md).

>[!NOTE]
>Při přístupu k entitám ve službě Media Services je nutné nastavit konkrétní pole záhlaví a hodnoty v požadavcích HTTP. Další informace naleznete [v tématu Setup for Media Services REST API Development](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Spuštění koncového bodu streamování pomocí webu Azure Portal

Při práci s Azure Media Services je jedním z nejběžnějších scénářů doručování videa prostřednictvím adaptivního streamování datového toku. Služba Media Services poskytuje dynamické balení, které umožňuje doručovat obsah s adaptivní přenosovou rychlostí s kódováním MP4 ve formátech streamování podporovaných službou Media Services (MPEG DASH, HLS, technologie Smooth Streaming). není přitom potřeba ukládat předem zabalené verze pro každý z těchto formátů streamování.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**.

Pokud chcete spustit koncový bod streamování, postupujte takto:

1. Přihlaste se na [webu Azure Portal](https://portal.azure.com/).
2. V okně Nastavení klikněte na Koncové body streamování.
3. Klikněte na výchozí koncový bod streamování.

    Zobrazí se okno VÝCHOZÍ KONCOVÝ BOD STREAMOVÁNÍ – PODROBNOSTI.

4. Klikněte na ikonu Spustit.
5. Kliknutím na tlačítko Uložit uložte provedené změny.

## <a name="connect-to-the-media-services-account-with-rest-api"></a><a id="connect"></a>Připojení k účtu Mediálních služeb pomocí rozhraní REST API

Informace o tom, jak se připojit k rozhraní AMS API, najdete [v tématu Přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-new-asset-and-upload-a-video-file-with-rest-api"></a><a id="upload"></a>Vytvoření nového datového zdroje a nahrání videosouboru pomocí rozhraní REST API

Ve službě Media Services můžete digitální soubory nahrát do assetu. Entita **Asset** může obsahovat video, zvuk, obrázky, kolekce miniatur, textové stopy a soubory skrytých titulků (a metadata o těchto souborech).)  Jakmile jsou soubory nahrány do datového zdroje, váš obsah je bezpečně uložen v cloudu pro další zpracování a streamování.

Jednou z hodnot, které musíte poskytnout při vytváření majetku, jsou možnosti vytváření majetku. Vlastnost **Options** je hodnota výčtu, která popisuje možnosti šifrování, které lze vytvořit s Asset. Platná hodnota je jednou z hodnot z níže uvedeného seznamu, nikoli kombinací hodnot z tohoto seznamu:

* **Žádný** = **0** - Není použito žádné šifrování. Při použití této možnosti není váš obsah chráněn při přenosu nebo v klidovém stavu.
    Pokud chcete pomocí progresivního stahování dodávat obsah ve formátu MP4, použijte tuto možnost.
* **StorageEncrypted** = **1** – Šifruje váš čistý obsah místně pomocí šifrování AES-256 bitů a pak ho nahraje do Služby Azure Storage, kde je uložen šifrovaný v klidovém stavu. Prostředky chráněné pomocí šifrování úložiště jsou před kódováním automaticky bez šifrování umístěny do systému souborů EFS a volitelně se znovu zašifrují před jejich odesláním zpět v podobě nového výstupního prostředku. Případem primárního použití šifrování úložiště je situace, kdy chcete zabezpečit soubory s vysoce kvalitními vstupními multimediálními soubory pomocí silného šifrování na disku.
* **CommonEncryptionProtected** = **2** - Tuto možnost použijte, pokud nahráváte obsah, který již byl zašifrován a chráněn pomocí běžného šifrování nebo playready DRM (například plynulé streamování chráněné pomocí služby PlayReady DRM).
* **EnvelopeEncryptionProtected** = **4** – Tuto možnost použijte, pokud nahráváte HLS zašifrované pomocí AES. Soubory musí být zakódovány a zašifrovány správcem transformace.

### <a name="create-an-asset"></a>Vytvoření datového zdroje
Datový zdroj je kontejner pro více typů nebo sad objektů ve službě Media Services, včetně videa, zvuku, obrázků, kolekcí miniatur, textových stop a souborů skrytých titulků. V rozhraní REST API vyžaduje vytvoření datového zdroje odeslání požadavku POST službě Media Services a umístění veškerých informací o vlastnostech o vašem datovém zdroji do těla požadavku.

Následující příklad ukazuje, jak vytvořit datový zdroj.

**Požadavek HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45

    {"Name":"BigBuckBunny.mp4", "Options":"0"}


**Odpověď HTTP**

V případě úspěchu je vrácena následující:

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

### <a name="create-an-assetfile"></a>Vytvoření souboru assetů
Entita [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) představuje video nebo zvukový soubor, který je uložen v kontejneru objektů blob. Soubor datového zdroje je vždy přidružen k datovému zdroji a datový zdroj může obsahovat jeden nebo více souborů AssetFiles. Úloha kodéru mediálních služeb se nezdaří, pokud objekt souboru datového zdroje není přidružen k digitálnímu souboru v kontejneru objektů blob.

Po nahrání digitálního mediálního souboru do kontejneru objektů blob použijete požadavek **HTTP sloučení** k aktualizaci souboru AssetFile s informacemi o mediálním souboru (jak je znázorněno dále v tématu).

**Požadavek HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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


### <a name="creating-the-accesspolicy-with-write-permission"></a>Vytvoření zásad accessu s oprávněním k zápisu
Před nahráním souborů do úložiště objektů blob nastavte práva zásad přístupu pro zápis do datového zdroje. Chcete-li to provést, post požadavek HTTP na accesspolicies entity set. Definujte hodnotu DurationInMinutes při vytvoření nebo se v odpovědi zobrazí chybová zpráva 500 Internal Server. Další informace o zásadách přístupu naleznete v [tématu AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

Následující příklad ukazuje, jak vytvořit zásady accessu:

**Požadavek HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74

    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}

**Odpověď HTTP**

Pokud je úspěšná, je vrácena následující odpověď:

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

### <a name="get-the-upload-url"></a>Získat adresu URL pro nahrávání

Chcete-li získat skutečnou adresu URL pro nahrání, vytvořte lokátor SAS. Lokátory definují čas zahájení a typ koncového bodu připojení pro klienty, kteří chtějí získat přístup k souborům ve datovém zdroji. Můžete vytvořit více lokátoru entity pro daný PřístupZásad a Asset dvojice pro zpracování různých požadavků a potřeb klientů. Každý z těchto lokátorů používá hodnotu StartTime plus hodnotu DurationInMinutes zásad accesspolicy k určení doby, po kterou lze použít adresu URL. Další informace naleznete v [tématu Locator](https://docs.microsoft.com/rest/api/media/operations/locator).

Adresa URL SAS má následující formát:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Musí být splněny určité předpoklady:

* K danému prostředku nelze najednou přidružit více než pět jedinečných lokátorů. 
* Pokud potřebujete okamžitě nahrát soubory, měli byste nastavit hodnotu StartTime na pět minut před aktuálním časem. Důvodem je, že může být hodiny zkosení mezi klientským počítačem a media services. Hodnota StartTime musí být také v následujícím formátu DateTime: YYYY-MM-DDTHH:mm:ssZ (například "2014-05-23T17:53:50Z").    
* Může být 30-40 sekund zpoždění po vytvoření lokátoru, když je k dispozici pro použití. Tento problém se týká [adresy URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) i lokátorů původu.

Následující příklad ukazuje, jak vytvořit Lokátor URL SAS, jak je definovántypem vlastnosti v těle požadavku ("1" pro lokátor SAS a "2" pro lokátor původu na vyžádání). Vrácená vlastnost **Path** obsahuje adresu URL, kterou musíte použít k nahrání souboru.

**Požadavek HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178

    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**Odpověď HTTP**

Pokud je úspěšná, je vrácena následující odpověď:

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
Jakmile budete mít accesspolicy a locator nastavit, skutečný soubor se nahraje do kontejneru úložiště objektů blob Azure pomocí Azure Storage REST API. Soubory je nutné nahrát jako objekty BLOB bloku. Objekty BLOB stránky nejsou podporovány službou Azure Media Services.  

> [!NOTE]
> Je nutné přidat název souboru, který chcete nahrát, do **hodnoty** Cesty lokátoru přijaté v předchozí části. Například, `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

Další informace o práci s objekty BLOB úložiště Azure najdete v [tématu rozhraní REST API služby blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

### <a name="update-the-assetfile"></a>Aktualizace souboru assetfile
Teď, když jste nahráli soubor, aktualizujte informace o velikosti souboru FileAsset (a další). Například:

    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net

    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Odpověď HTTP**

V případě úspěchu je vrácena následující:

    HTTP/1.1 204 No Content
    ...

## <a name="delete-the-locator-and-accesspolicy"></a>Odstranění zásad locatoru a přístupu
**Požadavek HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net


**Odpověď HTTP**

V případě úspěchu je vrácena následující:

    HTTP/1.1 204 No Content
    ...

**Požadavek HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net

**Odpověď HTTP**

V případě úspěchu je vrácena následující:

    HTTP/1.1 204 No Content
    ...

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a><a id="encode"></a>Zakódování zdrojového souboru do sady souborů MP4 s adaptivní přenosovou rychlostí

Po požití datového zdroje do služby Media Services lze médium před doručením klientům zakódovat, převést, označit vodoznakem a tak dále. Tyto aktivity se plánují a spouštějí s několika instancemi role na pozadí, abyste měli zajištěný vysoký výkon a dostupnost. Tyto aktivity se nazývají Úlohy a každá úloha se skládá z atomických úloh, které provádějí skutečnou práci na souboru majetku (další informace naleznete v [tématu Úloha](https://docs.microsoft.com/rest/api/media/operations/job), [Popisy úkolů).](https://docs.microsoft.com/rest/api/media/operations/task)

Jak již bylo zmíněno dříve, při práci s Azure Media Services jedním z nejběžnějších scénářů je poskytování adaptivního datového toku streamování vašim klientům. Mediální služby mohou dynamicky zabalit sadu souborů MP4 s adaptivní množí datovou rychlostí do jednoho z následujících formátů: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

Následující část ukazuje, jak vytvořit úlohu, která obsahuje jednu úlohu kódování. Úloha určuje překódování souboru mezanina do sady modulů MP4 s adaptivním datovým tokem pomocí **standardu Media Encoder Standard**. V části je také uvedeno, jak sledovat průběh zpracování úlohy. Po dokončení úlohy budete moci vytvořit lokátory, které jsou potřebné k získání přístupu k vašim datovým zdrojům.

### <a name="get-a-media-processor"></a>Získání mediálního procesoru
Ve službě Media Services je mediální procesor součástí, která zpracovává určitou úlohu zpracování, jako je kódování, převod formátu, šifrování nebo dešifrování mediálního obsahu. Pro úlohu kódování uvedenou v tomto kurzu použijeme standard kodéru médií.

Následující kód požaduje id kodéru.

**Požadavek HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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
Každá úloha může mít jeden nebo více úkolů v závislosti na typu zpracování, které chcete provést. Prostřednictvím rozhraní REST API můžete vytvořit úlohy a jejich související úkoly jedním ze dvou způsobů: Úkoly lze definovat vsazení prostřednictvím vlastnosti Navigace úkolů na entitách Úlohy nebo prostřednictvím dávkového zpracování OData. Sada Media Services SDK používá dávkové zpracování. Pro čitelnost příkladů kódu v tomto článku jsou však úkoly definovány vtextu. Informace o dávkovém zpracování naleznete v tématu [Open Data Protocol (OData) Batch Processing](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

Následující příklad ukazuje, jak vytvořit a zaúčtovat úlohu s jednou sadou úloh pro kódování videa v určitém rozlišení a kvalitě. Následující část dokumentace obsahuje seznam všech [přednastavení úloh podporovaných](https://msdn.microsoft.com/library/mt269960) procesorem Media Encoder Standard.  

**Požadavek HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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

Pokud je úspěšná, je vrácena následující odpověď:

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


V každé žádosti o úlohu je třeba poznamenat několik důležitých věcí:

* Vlastnosti TaskBody MUSÍ používat literál XML k definování počtu vstupních nebo výstupních datových zdrojů, které task používá. Úkol článek obsahuje definici schématu XML pro XML.
* V definici TaskBody musí `<inputAsset>` být `<outputAsset>` každá vnitřní hodnota pro a musí být nastavena jako JobInputAsset(hodnota) nebo JobOutputAsset(hodnota).
* Úloha může mít více výstupních datových zdrojů. Jeden JobOutputAsset(x) lze použít pouze jednou jako výstup úkolu v úloze.
* Jako vstupní majetek úkolu můžete zadat JobInputAsset nebo JobOutputAsset.
* Úkoly nesmí tvořit cyklus.
* Parametr hodnoty, který předáte JobInputAsset nebo JobOutputAsset představuje hodnotu indexu pro Asset. Skutečné datové zdroje jsou definovány v navigačních vlastnostech InputMediaAssets a OutputMediaAssets v definici entity Úloha.

> [!NOTE]
> Vzhledem k tomu, že media services je postaven a OData v3, jednotlivé prostředky v InputMediaAssets a OutputMediaAssets navigační vlastnosti kolekce jsou odkazovány prostřednictvím "__metadata : uri" název-hodnota dvojice.
>
>

* InputMediaAssets se mapuje na jeden nebo více datových zdrojů, které jste vytvořili ve službě Media Services. OutputMediaAssets jsou vytvořeny systémem. Neodkazují na existující majetek.
* OutputMediaAssets lze pojmenovat pomocí atributu assetName. Pokud tento atribut není k dispozici, pak název OutputMediaAsset je `<outputAsset>` bez ohledu na vnitřní textovou hodnotu prvku je s příponou buď název úlohy hodnotu nebo Id úlohy (v případě, kdy Name vlastnost není definována). Pokud například nastavíte hodnotu assetName na "Sample", bude vlastnost OutputMediaAsset Name nastavena na hodnotu Ukázka. Pokud jste však nenastavili hodnotu pro assetName, ale nastavili název úlohy na "NewJob", pak název OutputMediaAsset bude "JobOutputAsset(hodnota)_NewJob".

    Následující příklad ukazuje, jak nastavit atribut assetName:

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* Povolení řetězení úloh:

  * Úloha musí mít alespoň dva úkoly.
  * Musí existovat alespoň jeden úkol, jehož vstup je výstupem jiného úkolu v úloze.

Další informace naleznete [v tématu Vytvoření úlohy kódování pomocí rozhraní REST API mediálních služeb](media-services-rest-encode-asset.md).

### <a name="monitor-processing-progress"></a>Průběh zpracování monitoru
Stav úlohy můžete načíst pomocí vlastnosti State, jak je znázorněno v následujícím příkladu:

**Požadavek HTTP**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**Odpověď HTTP**

Pokud je úspěšná, je vrácena následující odpověď:

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
Služba Media Services umožňuje zrušit spuštěné úlohy prostřednictvím funkce CancelJob. Toto volání vrátí kód chyby 400, pokud se pokusíte zrušit úlohu, když je její stav zrušen, zrušení, chyba nebo dokončení.

Následující příklad ukazuje, jak volat CancelJob.

**Požadavek HTTP**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


Pokud je úspěšná, je vrácen kód odpovědi 204 bez textu zprávy.

> [!NOTE]
> Musíte URL-kódovat id úlohy (obvykle nb:jid:UUID: somevalue) při předávání jako parametr CancelJob.
>
>

### <a name="get-the-output-asset"></a>Získání výstupního datového zdroje
Následující kód ukazuje, jak požádat o Id výstupního prostředku.

**Požadavek HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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

## <a name="publish-the-asset-and-get-streaming-and-progressive-download-urls-with-rest-api"></a><a id="publish_get_urls"></a>Publikování datového zdroje a získání datových proudů a progresivnístahování adres URL pomocí rozhraní REST API

Pokud chcete prostředek streamovat nebo stáhnout, musíte ho nejdřív „publikovat“ vytvořením lokátoru. Lokátory zajišťují přístup k souborům, které jsou obsaženy v assetu. Služba Media Services podporuje dva typy lokátorů: lokátor OnDemandOrigin, používaný ke streamování médií (například MPEG DASH, HLS nebo technologie Smooth Streaming), a lokátor s přístupovým podpisem (SAS), používaný ke stahování mediálních souborů. 

Po vytvoření lokátorů můžete vytvořit adresy URL, které se používají k streamování nebo stahování souborů.

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

V této části je uvedeno, jak provádět následující úkoly nezbytné k "publikování" datových zdrojů.  

* Vytvoření zásad accesspolicy s oprávněním ke čtení
* Vytvoření adresy URL SAS pro stahování obsahu
* Vytvoření původní adresy URL pro streamování obsahu

### <a name="creating-the-accesspolicy-with-read-permission"></a>Vytvoření zásad accesspolicy s oprávněním ke čtení
Před stažením nebo streamováním jakéhokoli mediálního obsahu nejprve definujte zásadu AccessPolicy s oprávněními ke čtení a vytvořte příslušnou entitu Lokátoru, která určuje typ mechanismu doručování, který chcete povolit pro své klienty. Další informace o dostupných vlastnostech naleznete v tématu [Vlastnosti entity AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties).

Následující příklad ukazuje, jak zadat AccessPolicy pro oprávnění ke čtení pro daný prostředek.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Pokud je úspěšná, je vrácen kód úspěchu 201 popisující entitu AccessPolicy, kterou jste vytvořili. Potom použijete ID AccessPolicy spolu s ID datového zdroje, který obsahuje soubor, který chcete dodat (například výstupní majetek) k vytvoření entity Lokátoru.

> [!NOTE]
> Tento základní pracovní postup je stejný jako nahrávání souboru při ingestování datového zdroje (jak bylo popsáno dříve v tomto tématu). Také, stejně jako nahrávání souborů, pokud vy (nebo vaši klienti) potřebujete okamžitě přistupovat k souborům, nastavte hodnotu StartTime na pět minut před aktuálním časem. Tato akce je nezbytná, protože mezi klientem a mediálními službami může existovat zkosení hodin. Hodnota StartTime musí být v následujícím formátu DateTime: YYYY-MM-DDTHH:mm:ssZ (například "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Vytvoření adresy URL SAS pro stahování obsahu
Následující kód ukazuje, jak získat adresu URL, kterou lze použít ke stažení mediálního souboru vytvořeného a nahraného dříve. AccessPolicy má oprávnění ke čtení nastavit a cesta lokátoru odkazuje na adresu URL pro stažení SAS.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Pokud je úspěšná, je vrácena následující odpověď:

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

Vrácená **vlastnost Path** obsahuje adresu URL SAS.

> [!NOTE]
> Pokud stáhnete šifrovaný obsah úložiště, musíte jej před vykreslením ručně dešifrovat nebo použít mediální procesor dešifrování úložiště v úloze zpracování k výstupu zpracovaných souborů v programu clear to a OutputAsset a následném stažení z tohoto datového zdroje. Další informace o zpracování naleznete v tématu Vytvoření úlohy kódování pomocí rozhraní REST API mediálních služeb. Locátory adres URL SAS také nelze aktualizovat po jejich vytvoření. Například nelze znovu použít stejný Lokátor s aktualizovanou hodnotou StartTime. Důvodem je způsob, jakým jsou vytvářeny adresy URL SAS. Pokud chcete získat přístup ke zdroji ke stažení po vypršení platnosti lokátoru, musíte vytvořit nový s novým časem StartTime.
>
>

### <a name="download-files"></a>Stažení souborů
Jakmile budete mít accesspolicy a locator nastavit, můžete stahovat soubory pomocí rozhraní API úložiště Azure STORAGE.  

> [!NOTE]
> Název souboru, který chcete stáhnout, je nutné přidat do **hodnoty** Cesty lokátoru přijaté v předchozí části. Například https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?. . . .

Další informace o práci s objekty BLOB úložiště Azure najdete v [tématu rozhraní REST API služby blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

V důsledku úlohy kódování, kterou jste provedli dříve (kódování do sady Adaptive MP4), máte více souborů MP4, které můžete postupně stahovat. Například:    

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>Vytvoření stránky URL streamování pro streamování obsahu
Následující kód ukazuje, jak vytvořit lokátor URL streamování:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Pokud je úspěšná, je vrácena následující odpověď:

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

Chcete-li streamovat adresu URL původu hladkého streamování v přehrávači datových proudů médií, musíte připojit vlastnost Path s názvem souboru manifestu Plynulé streamování následovaném "/manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Chcete-li streamovat HLS, připojujte (format=m3u8-aapl) za "/manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Chcete-li streamovat MPEG DASH, připojujte (format=mpd-time-csf) za "/manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a name="play-your-content"></a><a id="play"></a>Přehrání obsahu
Pokud chcete video streamovat, použijte [přehrávač služby Azure Media Services](https://aka.ms/azuremediaplayer).

Chcete-li otestovat postupné stahování, vložte adresu URL do prohlížeče (například IE, Chrome, Safari).

## <a name="next-steps-media-services-learning-paths"></a>Další kroky: Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

---
title: Začněte s doručováním obsahu na vyžádání pomocí REST | Microsoft Docs
description: Tento kurz vás provede jednotlivými kroky implementace aplikace pro doručování obsahu na vyžádání pomocí Azure Media Services pomocí REST API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: ccf4bc18270dcdbcae02f7b45bea2a05ed663227
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008871"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Začínáme s doručováním obsahu na vyžádání pomocí REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Tento rychlý Start vás provede jednotlivými kroky implementace aplikace pro doručování obsahu videa na vyžádání (VoD) s využitím rozhraní REST API pro Azure Media Services (AMS).

Kurz představuje základní pracovní postup služby Media Services a nejběžnější programovací objekty a úkoly, které Media Services vyžaduje. Po dokončení tohoto kurzu budete moct streamovat nebo postupně stahovat ukázkový mediální soubor, který jste nahráli, zakódovi a stáhli.

Následující obrázek ukazuje některé z nejčastěji používaných objektů při vývoji aplikace VoD na základě modelu Media Services OData.

Kliknutím na obrázek zobrazíte jeho plnou velikost.  

[![Diagram znázorňující některé z nejčastěji používaných objektů v datovém modelu Azure Media Services objektů pro vývoj aplikací pro video na vyžádání.](./media/media-services-rest-get-started/media-services-overview-object-model-small.png)](./media/media-services-rest-get-started/media-services-overview-object-model.png#lightbox)

## <a name="prerequisites"></a>Požadavky
Pro zahájení vývoje pomocí Media Services s rozhraními REST API je potřeba splnit následující požadavky.

* Účet Azure: Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* Účet Media Services. Pokud chcete vytvořit účet Media Services, přečtěte si článek [Jak vytvořit účet Media Services](media-services-portal-create-account.md).
* Seznámení s vývojem pomocí Media Services REST API. Další informace najdete v tématu [Media Services REST API Overview](media-services-rest-how-to-use.md).
* Aplikace podle vašeho výběru, která může odesílat požadavky HTTP a odpovědi. V tomto kurzu se používá [Fiddler](https://www.telerik.com/download/fiddler).

V tomto rychlém startu se zobrazují následující úkoly.

1. Spuštění koncového bodu streamování (pomocí webu Azure Portal).
2. Připojte se k účtu Media Services pomocí REST API.
3. Vytvořte nový Asset a nahrajte videosoubor pomocí REST API.
4. Zakódovat zdrojový soubor do sady souborů MP4 s adaptivní přenosovou rychlostí pomocí REST API.
5. Publikování assetu a získání adres URL streamování a progresivního stahování pomocí REST API.
6. Přehrání obsahu

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Použijte stejné ID zásad, pokud vždycky používáte stejné dny nebo přístupová oprávnění, například zásady pro Lokátory, které mají zůstat v platnosti po dlouhou dobu (zásady bez nahrávání). Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

Podrobnosti o entitách služby AMS REST používaných v tomto článku naleznete v tématu [Azure Media Services REST API Reference](/rest/api/media/operations/azure-media-services-rest-api-reference). Přečtěte si také téma [Azure Media Services koncepty](media-services-concepts.md).

>[!NOTE]
>Při přístupu k entitám v Media Services musíte nastavit konkrétní pole a hodnoty hlaviček v požadavcích HTTP. Další informace najdete v tématu [instalace Media Services REST APIm vývoji](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Spuštění koncového bodu streamování pomocí webu Azure Portal

Při práci s Azure Media Services je jedním z nejběžnějších scénářů doručování videa prostřednictvím streamování s adaptivní přenosovou rychlostí. Služba Media Services poskytuje dynamické balení, které umožňuje doručovat obsah s adaptivní přenosovou rychlostí s kódováním MP4 ve formátech streamování podporovaných službou Media Services (MPEG DASH, HLS, technologie Smooth Streaming). není přitom potřeba ukládat předem zabalené verze pro každý z těchto formátů streamování.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**.

Pokud chcete spustit koncový bod streamování, postupujte takto:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. V okně Nastavení klikněte na Koncové body streamování.
3. Klikněte na výchozí koncový bod streamování.

    Zobrazí se okno VÝCHOZÍ KONCOVÝ BOD STREAMOVÁNÍ – PODROBNOSTI.

4. Klikněte na ikonu Spustit.
5. Kliknutím na tlačítko Uložit uložte provedené změny.

## <a name="connect-to-the-media-services-account-with-rest-api"></a><a id="connect"></a>Připojení k účtu Media Services pomocí REST API

Informace o tom, jak se připojit k rozhraní API AMS, najdete v tématu [přístup k rozhraní Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-new-asset-and-upload-a-video-file-with-rest-api"></a><a id="upload"></a>Vytvoření nového assetu a nahrání videosouboru pomocí REST API

Ve službě Media Services můžete digitální soubory nahrát do assetu. Entita **assetu** může obsahovat video, zvuk, obrázky, kolekce miniatur, textové stopy a soubory titulků (a metadata o těchto souborech.)  Po nahrání souborů do assetu je váš obsah bezpečně uložen v cloudu pro další zpracování a streamování.

Jedna z hodnot, které musíte zadat při vytváření assetu, jsou možnosti vytváření prostředků. Vlastnost **Options** je hodnota výčtu, která popisuje možnosti šifrování, pomocí kterých lze vytvořit Asset. Platná hodnota je jedna z hodnot níže uvedeného seznamu, nikoli kombinace hodnot z tohoto seznamu:

* **Žádné**  =  **0** – nepoužívá se žádné šifrování. Když použijete tuto možnost, váš obsah se nechrání během přenosu nebo v klidovém úložišti.
    Pokud chcete pomocí progresivního stahování dodávat obsah ve formátu MP4, použijte tuto možnost.
* **StorageEncrypted**  =  **1** – šifruje obsah místně pomocí šifrování AES-256 a pak ho nahraje, aby Azure Storage tam, kde je uložený zašifrovaný v klidovém stavu. Prostředky chráněné pomocí šifrování úložiště jsou před kódováním automaticky bez šifrování umístěny do systému souborů EFS a volitelně se znovu zašifrují před jejich odesláním zpět v podobě nového výstupního prostředku. Případem primárního použití šifrování úložiště je situace, kdy chcete zabezpečit soubory s vysoce kvalitními vstupními multimediálními soubory pomocí silného šifrování na disku.
* **CommonEncryptionProtected**  =  **2** – tuto možnost použijte, pokud nahráváte obsah, který už je zašifrovaný a chráněný pomocí Common Encryption nebo PlayReady DRM (například Smooth Streaming chráněná pomocí technologie PlayReady DRM).
* **EnvelopeEncryptionProtected**  =  **4** – tuto možnost použijte, pokud odesíláte HLS ŠIFROVANÝ pomocí AES. Soubory musí být zakódované a šifrované pomocí nástroje pro transformaci.

### <a name="create-an-asset"></a>Vytvoření prostředku
Asset je kontejner pro více typů nebo sad objektů v Media Services, včetně videa, zvuku, obrázků, kolekcí miniatur, textových stop a souborů titulků. V REST API vytvoření Assetu vyžaduje odeslání žádosti POST do Media Services a umístění jakýchkoli vlastností informací o prostředku do textu žádosti.

Následující příklad ukazuje, jak vytvořit Asset.

**Požadavek HTTP**

```console
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
```

**Odpověď HTTP**

V případě úspěchu se vrátí následující:

```console
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
    odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
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
```

### <a name="create-an-assetfile"></a>Vytvoření AssetFile
Entita [AssetFile](/rest/api/media/operations/assetfile) představuje video nebo zvukový soubor, který je uložený v kontejneru objektů BLOB. Soubor assetu je vždy přidružen k assetu a Asset může obsahovat jeden nebo více AssetFiles. Úloha Media Services Encoder se nezdařila v případě, že objekt souboru prostředků není přidružen k digitálnímu souboru v kontejneru objektů BLOB.

Po nahrání digitálního mediálního souboru do kontejneru objektů BLOB se pomocí žádosti o **sloučení** http aktualizuje AssetFile s informacemi o vašem mediálním souboru (jak je uvedeno dále v tématu).

**Požadavek HTTP**

```console
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
```

### <a name="creating-the-accesspolicy-with-write-permission"></a>Vytvoření AccessPolicy s oprávněním k zápisu
Před nahráním jakýchkoli souborů do úložiště objektů BLOB nastavte práva zásad přístupu pro zápis do assetu. Provedete to tak, že ODEŠLEte požadavek HTTP do sady entit AccessPolicies. Při vytváření Definujte hodnotu DurationInMinutes nebo v odpovědi obdržíte interní chybovou zprávu serveru 500. Další informace o AccessPolicies najdete v tématu [AccessPolicy](/rest/api/media/operations/accesspolicy).

Následující příklad ukazuje, jak vytvořit AccessPolicy:

**Požadavek HTTP**

```console
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
```

**Odpověď HTTP**

V případě úspěchu se vrátí následující odpověď:

```console
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
```

### <a name="get-the-upload-url"></a>Získat adresu URL pro odeslání

Pokud chcete přijmout skutečnou adresu URL pro odeslání, vytvořte Lokátor SAS. Lokátory definují čas spuštění a typ koncového bodu připojení pro klienty, kteří chtějí získat přístup k souborům v prostředku. Můžete vytvořit více entit lokátoru pro určitý AccessPolicy a dvojici prostředků pro zpracování různých požadavků klientů a potřeb. Každá z těchto lokátorů používá hodnotu StartTime a hodnotu DurationInMinutes AccessPolicy k určení doby, po kterou lze adresu URL použít. Další informace najdete v tématu [Lokátor](/rest/api/media/operations/locator).

Adresa URL SAS má následující formát:

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

Musí být splněny určité předpoklady:

* K danému prostředku nelze současně přidružit více než pět jedinečných lokátorů. 
* Pokud potřebujete nahrávat soubory hned, měli byste nastavit hodnotu Čas_spuštění na pět minut před aktuálním časem. Důvodem je, že může dojít k naklonění času mezi klientským počítačem a Media Services. Hodnota StartTime musí být také v následujícím formátu data a času: RRRR-MM-DDTHH: mm: ssZ (například "2014-05-23T17:53:50Z").    
* Po vytvoření lokátoru v případě, že je k dispozici pro použití, může docházet ke zpoždění 30-40 sekund. Tento problém se týká [adresy URL SAS](../../storage/common/storage-sas-overview.md) i lokátorů původu.

Následující příklad ukazuje, jak vytvořit Lokátor adresy URL SAS, jak je definováno vlastností typ v textu žádosti ("1" pro Lokátor SAS a "2" pro Lokátor původu na vyžádání). Vrácená vlastnost **path** obsahuje adresu URL, kterou je nutné použít k nahrání souboru.

**Požadavek HTTP**

```console
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
```

**Odpověď HTTP**

V případě úspěchu se vrátí následující odpověď:

```console
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
```

### <a name="upload-a-file-into-a-blob-storage-container"></a>Nahrání souboru do kontejneru úložiště objektů BLOB
Jakmile budete mít AccessPolicy a lokátor, vlastní soubor se nahraje do kontejneru úložiště objektů BLOB v Azure pomocí rozhraní REST API Azure Storage. Soubory je třeba nahrát jako objekty blob bloku. Azure Media Services nepodporuje objekty blob stránky.  

> [!NOTE]
> Je nutné přidat název souboru, který chcete odeslat do hodnoty **cesty** lokátoru přijaté v předchozí části. Například, `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

Další informace o práci s objekty blob služby Azure Storage najdete v tématu [REST API služby BLOB](/rest/api/storageservices/blob-service-rest-api).

### <a name="update-the-assetfile"></a>Aktualizace AssetFile
Teď, když jste nahráli soubor, aktualizujte informace o velikosti Assetového prostředku (a dalších). Například:

```console
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
```

**Odpověď HTTP**

V případě úspěchu se vrátí následující:

```console
HTTP/1.1 204 No Content
...
```

## <a name="delete-the-locator-and-accesspolicy"></a>Odstranit Lokátor a AccessPolicy
**Požadavek HTTP**

```console
DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**Odpověď HTTP**

V případě úspěchu se vrátí následující:

```console
HTTP/1.1 204 No Content
...
```

**Požadavek HTTP**

```console
DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**Odpověď HTTP**

V případě úspěchu se vrátí následující:

```console
HTTP/1.1 204 No Content
...
```

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a><a id="encode"></a>Zakódovat zdrojový soubor do sady souborů MP4 s adaptivní přenosovou rychlostí

Po ingestování prostředků do Media Services se média dají kódovat, transmuxovat, s vodotiskem a tak dále, než se doručí klientům. Tyto aktivity se plánují a spouštějí s několika instancemi role na pozadí, abyste měli zajištěný vysoký výkon a dostupnost. Tyto aktivity se nazývají úlohy a Každá úloha se skládá z atomických úloh, které vykonávají skutečnou práci na souboru Assetu (Další informace najdete v tématu [úlohy](/rest/api/media/operations/job), popisy [úloh](/rest/api/media/operations/task) ).

Jak bylo zmíněno dříve, při práci s Azure Media Services jedním z nejběžnějších scénářů je doručování streamování s adaptivní přenosovou rychlostí vašim klientům. Media Services může dynamicky zabalit sadu souborů MP4 s adaptivní přenosovou rychlostí do jednoho z následujících formátů: HTTP Live Streaming (HLS), Smooth Streaming, MPEG POMLČKa.

V následující části se dozvíte, jak vytvořit úlohu, která obsahuje jednu úlohu kódování. Tato úloha určuje, že se soubor Mezzanine překóduje do sady rychlostmi s adaptivní přenosovou rychlostí pomocí **Media Encoder Standard**. Část také ukazuje, jak monitorovat průběh zpracování úlohy. Po dokončení úlohy byste mohli vytvořit Lokátory, které jsou potřeba k získání přístupu k vašim prostředkům.

### <a name="get-a-media-processor"></a>Získat procesor médií
V Media Services je procesorem médií komponenta, která zpracovává konkrétní úlohu zpracování, jako je například kódování, převod formátu, šifrování nebo dešifrování mediálního obsahu. Pro úlohu kódování zobrazenou v tomto kurzu použijeme Media Encoder Standard.

Následující kód požaduje ID kodéru.

**Požadavek HTTP**

```console
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
```

**Odpověď HTTP**

```console
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
```

### <a name="create-a-job"></a>Vytvoření úlohy
Každá úloha může mít jeden nebo více úloh v závislosti na typu zpracování, které chcete provést. Prostřednictvím REST API můžete vytvořit úlohy a jejich související úkoly jedním ze dvou způsobů: úkoly lze definovat prostřednictvím vlastnosti navigace úkoly v entitách úlohy nebo prostřednictvím dávkového zpracování OData. Sada Media Services SDK používá dávkové zpracování. Nicméně pro čitelnost příkladů kódu v tomto článku jsou úkoly definovány jako vložené. Informace o dávkovém zpracování najdete v tématu [dávkové zpracování protokolu OData (Open Data Protocol)](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

Následující příklad ukazuje, jak vytvořit a publikovat úlohu s jednou nastavenou úlohou ke kódování videa v konkrétním rozlišení a kvalitě. V následující části dokumentace najdete seznam všech [přednastavení úloh](./media-services-mes-presets-overview.md) podporovaných procesorem Media Encoder Standard.  

**Požadavek HTTP**

```console
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
```

**Odpověď HTTP**

V případě úspěchu se vrátí následující odpověď:

```console
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
```

K dispozici je několik důležitých věcí, které je potřeba si uvědomit v každé žádosti o úlohu:

* Vlastnosti TaskBody – musí použít literál XML pro definování počtu vstupních nebo výstupních prostředků, které úkol používá. Článek o úloze obsahuje definici schématu XML pro XML.
* V definici TaskBody – je nutné, aby každá vnitřní hodnota pro `<inputAsset>` a `<outputAsset>` byla nastavena jako JobInputAsset (hodnota) nebo JobOutputAsset (hodnota).
* Úloha může mít více výstupních prostředků. Jeden JobOutputAsset (x) lze použít pouze jednou jako výstup úkolu v úloze.
* Jako vstupní Asset úkolu můžete zadat JobInputAsset nebo JobOutputAsset.
* Úkoly nesmí tvořit cyklus.
* Parametr hodnoty, který předáte do JobInputAsset nebo JobOutputAsset, představuje hodnotu indexu pro Asset. Skutečné prostředky jsou definovány v navigačním vlastnosti InputMediaAssets a OutputMediaAssets v definici entity úlohy.

> [!NOTE]
> Vzhledem k tomu, že Media Services je postavené na OData V3, jednotlivé prostředky v kolekcích vlastností navigace InputMediaAssets a OutputMediaAssets odkazují na dvojici název-hodnota __metadata: URI.
>
>

* InputMediaAssets se mapuje na jeden nebo více assetů, které jste vytvořili v Media Services. OutputMediaAssets jsou vytvořeny systémem. Neodkazují na stávající Asset.
* OutputMediaAssets lze pojmenovat pomocí atributu název prostředku. Pokud tento atribut není k dispozici, pak název OutputMediaAsset je bez ohledu na to, zda je vnitřní text hodnoty `<outputAsset>` prvku přípona hodnoty názvu úlohy nebo hodnota ID úlohy (v případě, že vlastnost Name není definována). Pokud například nastavíte hodnotu pro název prostředku na "Sample", vlastnost OutputMediaAsset Name bude nastavena na "Sample". Pokud jste však nenastavili hodnotu pro název prostředku, ale nastavili jste název úlohy na "NewJob", pak bude název OutputMediaAsset "JobOutputAsset (hodnota) _NewJob".

    Následující příklad ukazuje, jak nastavit atribut majetku:

    ```console
    "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
    ```
* Povolení Řetězení úloh:

  * Úloha musí mít alespoň dvě úlohy.
  * Musí existovat alespoň jeden úkol, jehož vstupem je výstup jiné úlohy v úloze.

Další informace najdete v tématu [Vytvoření úlohy kódování pomocí Media Services REST API](media-services-rest-encode-asset.md).

### <a name="monitor-processing-progress"></a>Sledování průběhu zpracování
Stav úlohy můžete načíst pomocí vlastnosti State, jak je znázorněno v následujícím příkladu:

**Požadavek HTTP**

```console
GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
Content-Length: 0
```

**Odpověď HTTP**

V případě úspěchu se vrátí následující odpověď:

```console
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
```

### <a name="cancel-a-job"></a>Zrušení úlohy
Media Services umožňuje zrušit spuštěné úlohy pomocí funkce CancelJob. Toto volání vrátí kód chyby 400, pokud se pokusíte úlohu zrušit, když je její stav zrušen, zrušení, chyba nebo dokončení.

Následující příklad ukazuje, jak volat CancelJob.

**Požadavek HTTP**

```console
GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
```

V případě úspěchu se vrátí kód odpovědi 204 bez těla zprávy.

> [!NOTE]
> Aby bylo možné CancelJob předat jako parametr, je nutné zakódovat ID úlohy (obvykle NB: JID: UUID: someValue) na adrese URL.
>
>

### <a name="get-the-output-asset"></a>Získání výstupního prostředku
Následující kód ukazuje, jak požádat o ID výstupního prostředku.

**Požadavek HTTP**

```console
GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**Odpověď HTTP**

```console
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
```

## <a name="publish-the-asset-and-get-streaming-and-progressive-download-urls-with-rest-api"></a><a id="publish_get_urls"></a>Publikování assetu a získání adres URL streamování a progresivního stahování pomocí REST API

Pokud chcete prostředek streamovat nebo stáhnout, musíte ho nejdřív „publikovat“ vytvořením lokátoru. Lokátory zajišťují přístup k souborům, které jsou obsaženy v assetu. Služba Media Services podporuje dva typy lokátorů: lokátor OnDemandOrigin, používaný ke streamování médií (například MPEG DASH, HLS nebo technologie Smooth Streaming), a lokátor s přístupovým podpisem (SAS), používaný ke stahování mediálních souborů. 

Po vytvoření lokátorů můžete sestavit adresy URL, které se použijí ke streamování nebo stahování souborů.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**.

Streamovací adresa URL pro technologii Smooth Streaming má následující formát:

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest
```

Streamovací adresa URL pro HLS má následující formát:

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)
```

Streamovací adresa URL pro MPEG DASH má následující formát:

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)
```

SAS adresa URL používaná ke stahování souborů má následující formát:

```console
{blob container name}/{asset name}/{file name}/{SAS signature}
```

V této části se dozvíte, jak provádět následující úkoly, které jsou nezbytné pro publikování vašich assetů.  

* Vytvoření AccessPolicy s oprávněním ke čtení
* Vytvoření adresy URL SAS pro stahování obsahu
* Vytvoření zdrojové adresy URL pro streamování obsahu

### <a name="creating-the-accesspolicy-with-read-permission"></a>Vytvoření AccessPolicy s oprávněním ke čtení
Před stažením nebo streamování libovolného mediálního obsahu nejdřív definujte AccessPolicy s oprávněním ke čtení a vytvořte příslušnou entitu lokátoru, která určuje typ mechanismu doručování, který chcete pro klienty povolit. Další informace o dostupných vlastnostech najdete v tématu [AccessPolicy – vlastnosti entit](/rest/api/media/operations/accesspolicy#accesspolicy_properties).

Následující příklad ukazuje, jak zadat AccessPolicy pro oprávnění ke čtení pro daný prostředek.

```console
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
```

V případě úspěchu se vrátí kód úspěšnosti 201, který popisuje entitu AccessPolicy, kterou jste vytvořili. Pak použijete ID AccessPolicy spolu s ID Assetu assetu, který obsahuje soubor, který chcete dodat (například výstupní prostředek) k vytvoření entity lokátoru.

> [!NOTE]
> Tento základní pracovní postup je stejný jako při odesílání souboru při příjmu prostředku (jak bylo popsáno výše v tomto tématu). Stejně jako při nahrávání souborů (nebo vašich klientů) potřebujete okamžitě přistupovat k souborům, nastavte hodnotu Čas_spuštění na pět minut před aktuálním časem. Tato akce je nezbytná, protože mezi klientem a Media Services může docházet k časovému zkosení. Hodnota StartTime musí být v následujícím formátu data a času: RRRR-MM-DDTHH: mm: ssZ (například "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Vytvoření adresy URL SAS pro stahování obsahu
Následující kód ukazuje, jak získat adresu URL, která se dá použít ke stažení mediálního souboru vytvořeného a nahraného dříve. AccessPolicy má nastaveno oprávnění ke čtení a cesta lokátoru odkazuje na adresu URL pro stažení SAS.

```console
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
```

V případě úspěchu se vrátí následující odpověď:

```console
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
```

Vlastnost vracená **cesta** obsahuje adresu URL SAS.

> [!NOTE]
> Pokud stáhnete zašifrovaný obsah úložiště, musíte ho před jeho vykreslením ručně dešifrovat nebo pomocí MediaProcessor dešifrování úložiště v úloze zpracování navýstupovat zpracované soubory do OutputAsset a pak je stáhnout z tohoto prostředku. Další informace o zpracování naleznete v tématu Vytvoření úlohy kódování pomocí Media Services REST API. Po vytvoření se taky nedají aktualizovat Lokátory adresy URL SAS. Nemůžete například použít stejné Lokátor s aktualizovanou hodnotou StartTime. Důvodem je to, jak se vytvářejí adresy URL SAS. Pokud chcete získat přístup ke assetu ke stažení po vypršení platnosti lokátoru, musíte vytvořit nový s novým StartTime.
>
>

### <a name="download-files"></a>Stažení souborů
Jakmile budete mít AccessPolicy a lokátor, můžete soubory stahovat pomocí rozhraní REST API pro Azure Storage.  

> [!NOTE]
> Je nutné přidat název souboru, který chcete stáhnout, do hodnoty **cesty** lokátoru přijaté v předchozí části. Například `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4` ? . . .

Další informace o práci s objekty blob služby Azure Storage najdete v tématu [REST API služby BLOB](/rest/api/storageservices/blob-service-rest-api).

V důsledku úlohy kódování, kterou jste provedli dříve (při kódování do adaptivní sady souborů MP4), máte více souborů MP4, které můžete postupně stahovat. Například:    

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

### <a name="creating-a-streaming-url-for-streaming-content"></a>Vytvoření adresy URL streamování pro obsah streamování
Následující kód ukazuje, jak vytvořit Lokátor adresy URL streamování:

```console
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
```

V případě úspěchu se vrátí následující odpověď:

```console
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
```

Chcete-li v přehrávači mediálních datových proudů streamovat Smooth Streaming zdrojové adresy URL, je nutné připojit vlastnost Path s názvem souboru manifestu Smooth Streaming následovaným řetězcem "/manifest".

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest`

Pro streamování HLS, Append (Format = M3U8-AAPL) za "/manifest".

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)`

Pokud chcete streamovat MPEG POMLČKy, přidejte (Format = MPD-Time-CSF) za "/manifest".

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)`


## <a name="play-your-content"></a><a id="play"></a>Přehrání obsahu
Pokud chcete video streamovat, použijte [přehrávač služby Azure Media Services](https://aka.ms/azuremediaplayer).

Chcete-li otestovat progresivní stahování, vložte adresu URL do prohlížeče (například IE, Chrome, Safari).

## <a name="next-steps-media-services-learning-paths"></a>Další kroky: Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
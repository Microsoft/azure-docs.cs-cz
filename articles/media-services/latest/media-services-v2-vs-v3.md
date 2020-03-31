---
title: Migrace z Azure Media Services v2 na v3
description: Tento článek popisuje změny, které byly zavedeny ve službě Azure Media Services v3 a ukazuje rozdíly mezi dvěma verzemi.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, všesměrové vysílání, live, režim offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: fd094e35ceaa718ec1b258d74106b39744cbd16f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087822"
---
# <a name="media-services-v2-vs-v3"></a>Mediální služby v2 vs. v3

Tento článek popisuje změny, které byly zavedeny ve službě Azure Media Services v3 a ukazuje rozdíly mezi dvěma verzemi.

## <a name="general-changes-from-v2"></a>Obecné změny od v2

* U datových zdrojů vytvořených pomocí v3 podporuje Media Services pouze [šifrování úložiště na straně serveru Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * Můžete použít v3 API s prostředky vytvořené s v2 API, které měly [šifrování úložiště](../previous/media-services-rest-storage-encryption.md) (AES 256) poskytované Media Services.
    * Nelze vytvořit nové datové zdroje se starším [šifrováním úložiště](../previous/media-services-rest-storage-encryption.md) AES 256 pomocí rozhraní API v3.
* Vlastnosti [Asset](assets-concept.md)v v3 se liší od v2, podívejte se, [jak vlastnosti map .](#map-v3-asset-properties-to-v2)
* Sady SDK verze 3 jsou nyní odděleny od sady Storage SDK, což vám dává větší kontrolu nad verzí sady Storage SDK, kterou chcete použít, a vyhne te se problémům se správou verzí. 
* V v3 API jsou všechny přenosové rychlosti kódování v bitech za sekundu. To se liší od standardních přednastavení v2 Media Encoder. Například přenosová rychlost ve v2 by byla určena jako 128 (kbps), ale ve v3 by to bylo 128000 (bitů za sekundu). 
* Entity AssetFiles, AccessPolicies a IngestManifests neexistují ve v3.
* Vlastnost IAsset.ParentAssets neexistuje ve v3.
* ContentKeys již není entita, je nyní vlastností lokátoru streamování.
* Podpora event gridu nahrazuje koncové body oznámení.
* Následující entity byly přejmenovány
    * Výstup úlohy nahrazuje úlohu a je nyní součástí úlohy.
    * Vyhledávač streamování nahrazuje Lokátor.
    * Live Event nahradí kanál.<br/>Fakturace živých událostí je založena na měřičích živých kanálů. Další informace naleznete v [tématu Fakturace](live-event-states-billing.md) a [ceny .](https://azure.microsoft.com/pricing/details/media-services/)
    * Živý výstup nahrazuje program.
* Živé výstupy se spustí při vytváření a zastaví se při odstranění. Programy fungovaly jinak ve v2 API, musely být zahájeny po vytvoření.
* Chcete-li získat informace o úloze, musíte znát název transformace, pod kterým byla úloha vytvořena. 
* Ve v2, [XML vstupní](../previous/media-services-input-metadata-schema.md) a [výstupní](../previous/media-services-output-metadata-schema.md) metadata soubory získat generovány v důsledku úlohy kódování. Ve v3 se formát metadat změnil z XML na JSON. 
* V media services v2 lze zadat inicializační vektor (IV). Ve službě Media Services v3 nelze zadat fairplay IV. I když to nemá vliv na zákazníky, kteří používají Mediální služby jak pro balení, tak pro doručování licencí, může to být problém při použití systému DRM třetí strany k dodání licencí FairPlay (hybridní režim). V takovém případě je důležité vědět, že FairPlay IV je odvozen z ID klíče CBCS a lze jej načíst pomocí tohoto vzorce:

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    with

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    Další informace naleznete v [kódu Azure Functions C# pro Media Services v3 v hybridním režimu pro živé i vod operace](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3).
 
> [!NOTE]
> Zkontrolujte konvence pojmenování, které jsou použity pro [prostředky služby Media Services v3](media-services-apis-overview.md#naming-conventions). Zkontrolujte také [pojmenování objektů BLOB](assets-concept.md#naming).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Nedostatky funkcí s ohledem na rozhraní API v2

Rozhraní API v3 má následující funkce mezery s ohledem na v2 API. Odstranění mezer je nedokončená práce.

* [Premium Kodéru](../previous/media-services-premium-workflow-encoder-formats.md) a starší [procesory analýzy médií](../previous/media-services-analytics-overview.md) (Azure Media Services Indexer 2 Preview, Face Redactor, atd.) nejsou přístupné přes v3.<br/>Zákazníci, kteří chtějí migrovat z media Indexer 1 nebo 2 náhled můžete okamžitě použít AudioAnalyzer přednastavení v 3 ROZHRANÍ API.  Toto nové přednastavení obsahuje více funkcí než starší media indexer 1 nebo 2. 
* Mnoho [pokročilých funkcí standardu Media Encoder Standard ve virtuálních](../previous/media-services-advanced-encoding-with-mes.md) rozhraních API není v současné době k dispozici ve verze 3, například:
  
    * Šití majetku
    * Překryvy
    * Oříznutí
    * Skřítci miniatur
    * Vložení tiché zvukové stopy, když vstup nemá žádný zvuk
    * Vložení stopy videa, pokud vstup nemá žádné video
* Živé události s překódováním v současné době nepodporují vkládání břidlice uprostřed datového proudu a vkládání značek reklamprostřednictvím volání rozhraní API. 
 
## <a name="asset-specific-changes"></a>Změny specifické pro majetek

### <a name="map-v3-asset-properties-to-v2"></a>Mapování vlastností majetku v3 na v2

Následující tabulka ukazuje, jak vlastnosti [datového zdroje](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)ve v3 mapují na vlastnosti datového zdroje ve v2.

|vlastnosti v3|vlastnosti v2|
|---|---|
|`id`- (jedinečný) úplná cesta správce prostředků Azure, viz příklady v [Asset](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name`- (jedinečný) viz [Konvence pojmenování](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`- (jedinečná) hodnota `nb:cid:UUID:` začíná předponou.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options`(možnosti vytvoření)|
|`type`||

### <a name="storage-side-encryption"></a>Šifrování na straně úložiště

Chcete-li chránit vaše prostředky v klidovém stavu, měly by být prostředky šifrovány šifrováním na straně úložiště. Následující tabulka ukazuje, jak funguje šifrování na straně úložiště ve službě Media Services:

|Možnost šifrování|Popis|Media Services v2|Media Services v3|
|---|---|---|---|
|Šifrování úložiště mediálních služeb|Šifrování AES-256, klíč spravovaný společností Media Services.|Podporované<sup>(1)</sup>|Není podporováno<sup>(2)</sup>|
|[Šifrování služby úložiště pro data v klidovém stavu](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Šifrování na straně serveru, které nabízí Azure Storage, klíč spravovaný Azure nebo zákazníkem.|Podporuje se|Podporuje se|
|[Šifrování na straně klienta úložiště](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Šifrování na straně klienta, které nabízí úložiště Azure, klíč spravovaný zákazníkem v trezoru klíčů.|Nepodporuje se|Nepodporuje se|

<sup>1</sup> Zatímco Media Services podporuje zpracování obsahu v jasné/bez jakékoli formy šifrování, není to doporučeno.

<sup>2</sup> Ve službě Media Services v3 je šifrování úložiště (šifrování AES-256) podporováno pouze pro zpětnou kompatibilitu, když byly vaše datové zdroje vytvořeny pomocí služby Media Services v2. Což znamená, že v3 pracuje s existujícími zašifrovanými prostředky úložiště, ale neumožní vytváření nových.

## <a name="code-differences"></a>Rozdíly kódu

V následující tabulce jsou uvedeny rozdíly kódu mezi v2 a v3 pro běžné scénáře.

|Scénář|V2 API|V3 API|
|---|---|---|
|Vytvoření datového zdroje a nahrání souboru |[příklad v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[příklad v3 .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Odeslání úlohy|[příklad v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[příklad v3 .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Ukazuje, jak nejprve vytvořit transformaci a potom odeslat úlohu.|
|Publikování datového zdroje pomocí šifrování AES |1. Vytvořit ContentKeyAuthorizationPolicyOption<br/>2. Vytvořit ContentKeyAuthorizationPolicy<br/>3. Vytvořit AssetDeliveryPolicy<br/>4. Vytvořit asset a nahrát obsah nebo odeslat úlohu a použít výstupní majetek<br/>5. Přidružit AssetDeliveryPolicy s majetkem<br/>6. Vytvořit ContentKey<br/>7. Připojte ContentKey k datovému zdroji<br/>8. Vytvořit zásady přístupu<br/>9. Vytvořit lokátor<br/><br/>[příklad v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Vytvořit zásady klíče obsahu<br/>2. Vytvořit datový zdroj<br/>3. Nahrát obsah nebo použít asset jako JobOutput<br/>4. Vytvořit lokátor streamování<br/><br/>[příklad v3 .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Získání podrobností o úloze a správa úloh |[Správa úloh s v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Správa úloh s v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Prosím, záložku tohoto článku a průběžně kontrolovat aktualizace.

## <a name="ask-questions-give-feedback-get-updates"></a>Ptejte se, podávejte zpětnou vazbu, získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="next-steps"></a>Další kroky

[Pokyny pro migraci pro přechod z Media Services v2 na v3](migrate-from-v2-to-v3.md)

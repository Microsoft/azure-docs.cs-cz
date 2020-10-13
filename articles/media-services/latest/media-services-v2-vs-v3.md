---
title: Migrace z Azure Media Services V2 na V3
description: Tento článek popisuje změny, které byly představeny v Azure Media Services V3, a ukazuje rozdíly mezi dvěma verzemi.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, všesměrové vysílání, live, režim offline
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/01/2020
ms.author: inhenkel
ms.openlocfilehash: 515379a4207a582b441d132b1c28ff11bc83c714
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91651748"
---
# <a name="media-services-v2-vs-v3"></a>Media Services V2 vs. v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Tento článek popisuje změny, které byly představeny v Azure Media Services V3, a ukazuje rozdíly mezi dvěma verzemi.

## <a name="general-changes-from-v2"></a>Obecné změny z v2

* Změny v souvislosti s assety najdete v části informace o [konkrétním prostředku](#asset-specific-changes) .
* Sady SDK V3 jsou teď oddělené od sady SDK úložiště, což vám dává větší kontrolu nad verzí sady SDK pro úložiště, kterou chcete použít, a vyhněte se problémům se správou verzí. 
* V rozhraních API V3 jsou všechny přenosové rychlosti kódování v bitech za sekundu. To se liší od přednastavených Media Encoder Standard v2. Například přenosová rychlost v v2 by se zadala jako 128 (KB/s), ale v v3 by byla 128000 (bity za sekundu). 
* Entity AssetFiles, AccessPolicies a IngestManifests v v3 neexistují.
* ContentKeys už není entita, teď je to vlastnost lokátoru streamování.
* Event Grid podpora nahrazuje NotificationEndpoints.
* Následující entity byly přejmenovány:

   * V3 JobOutput nahrazuje úlohu v2 a je teď součástí úlohy. Vstupy a výstupy jsou teď na úrovni úlohy. Další informace najdete v tématu [Vytvoření vstupu úlohy z místního souboru](job-input-from-local-file-how-to.md). 

       Chcete-li získat historii průběhu úlohy, naslouchat událostem EventGrid. Další informace najdete v tématu [zpracování událostí Event Grid](reacting-to-media-services-events.md).
    * Lokátor streamování nahrazuje lokátor.
    * Živá událost nahrazuje kanál.<br/>Účtování živých událostí je založeno na měřičích živých kanálů. Další informace najdete v tématu [fakturace](live-event-states-billing.md) a [ceny](https://azure.microsoft.com/pricing/details/media-services/).
    * Živý výstup nahrazuje program.
* Živé výstupy začínají při vytváření a při odstranění se zastaví. Programy fungují jinak v rozhraních API v2, musely se spustit po vytvoření.
* Chcete-li získat informace o úloze, je nutné znát název transformace, pod kterou byla úloha vytvořena. 
* Ve verzích v2 se [vstupní](../previous/media-services-input-metadata-schema.md) a [výstupní](../previous/media-services-output-metadata-schema.md) soubory metadat XML generují jako výsledek úlohy kódování. V v3 se formát metadat změnil z XML na JSON. 
* V Media Services V2 lze zadat inicializační vektor (IV). V Media Services V3 nelze zadat FairPlay IV. I když nemá vliv na zákazníky, kteří používají Media Services pro balíčky i doručování licencí, může to být problém při použití systému DRM jiného výrobce k doručování licencí FairPlay (hybridní režim). V takovém případě je důležité znát, že FairPlay IV je odvozená od ID klíče cbcs a dá se načíst pomocí tohoto vzorce:

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

    Další informace naleznete v tématu [Azure Functions C# kód pro Media Services V3 v hybridním režimu pro operace Live i vod](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3).
 
> [!NOTE]
> Projděte si zásady vytváření názvů, které se vztahují k [prostředkům Media Services V3](media-services-apis-overview.md#naming-conventions). Přečtěte si také [pojmenování objektů BLOB](assets-concept.md#naming).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Nedostatky funkcí s ohledem na rozhraní API v2

Rozhraní V3 API má následující mezery v souvislosti s rozhraním API v2. Uzavírání mezer je nedokončené.

* [Kodér úrovně Premium](../previous/media-services-premium-workflow-encoder-formats.md) a starší [procesory media Analytics](../previous/media-services-analytics-overview.md) (Azure Media Services indexer 2 Preview, Face redigování atd.) nejsou přístupné prostřednictvím v3.<br/>Zákazníci, kteří chtějí migrovat z Media Indexer 1 nebo 2 Preview, můžou hned použít předvolbu AudioAnalyzer v rozhraní V3 API.  Tato nová předvolba obsahuje více funkcí, než je starší Media Indexer 1 nebo 2. 
* Mnohé z [pokročilých funkcí Media Encoder standard v](../previous/media-services-advanced-encoding-with-mes.md) rozhraních API v2 v současnosti nejsou v systému V3 k dispozici, například:
  
    * Sešity prostředků
    * Překryvy
    * Jej
    * Sprite miniatur
    * Vložení tiché zvukové stopy, když vstup nemá žádný zvuk
    * Vložení stop videa, když vstup nemá žádné video
* Živé události s překódováním v současné době nepodporují vložení do středu a vložení značek AD prostřednictvím volání rozhraní API. 
* `https://github.com/Azure-Samples/media-services-v2-dotnet-core-restsharp-sample.git`Osvědčené postupy a vzory týkající se použití REST API v2 v systému najdete v ukázkovém kódu. NETCore SDK.

## <a name="asset-specific-changes"></a>Změny specifické pro prostředek

* Pro prostředky vytvořené pomocí v3 Media Services podporuje pouze [šifrování úložiště Azure Storage na straně serveru](../../storage/common/storage-service-encryption.md).
    * Rozhraní V3 API můžete použít s prostředky vytvořenými s rozhraními API v2, která měla [šifrování úložiště](../previous/media-services-rest-storage-encryption.md) (AES 256) poskytované Media Services.
    * Nemůžete vytvářet nové Assety se starším [šifrováním úložiště](../previous/media-services-rest-storage-encryption.md) AES 256, které používá rozhraní V3 API.
* Vlastnosti [assetu](assets-concept.md)v v3 se liší od v2, viz [jak mapování vlastností](#map-v3-asset-properties-to-v2).
* Vlastnost IAsset. ParentAssets v v3 neexistuje.

### <a name="map-v3-asset-properties-to-v2"></a>Mapování vlastností assetu V3 na v2

Následující tabulka ukazuje, jak vlastnosti [assetu](/rest/api/media/assets/createorupdate#asset)v v3 jsou mapovány na vlastnosti assetu ve verzi v2.

|vlastnosti V3|vlastnosti v2|
|---|---|
|`id`– (jedinečné) Úplná cesta Azure Resource Manager, viz příklady v [assetu](/rest/api/media/assets/createorupdate) .||
|`name`– (jedinečné) informace najdete v tématu zásady [vytváření názvů](media-services-apis-overview.md#naming-conventions) . ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` – (jedinečná) hodnota začíná `nb:cid:UUID:` předponou.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (možnosti vytváření)|
|`type`||

### <a name="storage-side-encryption"></a>Šifrování na straně úložiště

Aby bylo možné chránit vaše prostředky v klidovém stavu, prostředky by měly být šifrovány šifrováním na straně úložiště. Následující tabulka ukazuje, jak funguje šifrování na straně úložiště v Media Services:

|Možnost šifrování|Description|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services šifrování úložiště|Šifrování AES-256, klíč spravovaný pomocí Media Services.|Podporováno<sup>(1)</sup>|Nepodporováno<sup>(2)</sup>|
|[Šifrování služby Storage pro neaktivní neaktivní data](../../storage/common/storage-service-encryption.md)|Šifrování na straně serveru, které nabízí Azure Storage, klíč spravuje Azure nebo zákazník.|Podporováno|Podporováno|
|[Šifrování Client-Side úložiště](../../storage/common/storage-client-side-encryption.md)|Šifrování na straně klienta, které nabízí služba Azure Storage, klíč spravovaný zákazníkem v Key Vault.|Nepodporováno|Nepodporováno|

<sup>1</sup> když Media Services podporuje zpracování obsahu v nešifrované/bez jakékoli formy šifrování, nedoporučuje se to.

<sup>2</sup> v Media Services V3 se šifrování úložiště (šifrování AES-256) podporuje jenom pro zpětnou kompatibilitu, když se vaše prostředky vytvořily pomocí Media Services V2. To znamená, že V3 funguje se stávajícími šifrovanými prostředky úložiště, ale neumožňuje vytváření nových.

## <a name="code-differences"></a>Rozdíly v kódu

V následující tabulce jsou uvedeny rozdíly v kódu mezi v2 a v3 pro běžné scénáře.

|Scénář|V2 API|rozhraní V3 API|
|---|---|---|
|Vytvoření assetu a nahrání souboru |[Příklad v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[Příklad v3 .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Odeslat úlohu|[Příklad v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[Příklad v3 .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Ukazuje, jak nejdřív vytvořit transformaci a pak odeslat úlohu.|
|Publikování assetu pomocí šifrování AES |1. vytvoření ContentKeyAuthorizationPolicyOption<br/>2. vytvoření ContentKeyAuthorizationPolicy<br/>3. vytvoření AssetDeliveryPolicy<br/>4. vytvoření Assetu a nahrání obsahu nebo odeslání úlohy a použití výstupního prostředku<br/>5. přidružte AssetDeliveryPolicy k Assetu.<br/>6. vytvoření ContentKey<br/>7. připojení ContentKey k Assetu<br/>8. vytvoření AccessPolicy<br/>9. vytvořit Lokátor<br/><br/>[Příklad v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. vytvoření zásad klíče obsahu<br/>2. vytvoření Assetu<br/>3. nahrání obsahu nebo použití Assetu jako JobOutput<br/>4. vytvoření lokátoru streamování<br/><br/>[Příklad v3 .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Získat podrobnosti o úloze a spravovat úlohy |[Správa úloh s v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Správa úloh pomocí v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Zapište prosím tento článek do záložky a zkontrolujte, jestli jsou aktualizace.

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

[Pokyny k migraci pro přesun z Media Services V2 na V3](migrate-from-v2-to-v3.md)

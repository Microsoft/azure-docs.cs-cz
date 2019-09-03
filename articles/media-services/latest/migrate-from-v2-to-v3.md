---
title: Migrace z Azure Media Services V2 na V3 | Microsoft Docs
description: Tento článek popisuje změny, které byly představeny v Azure Media Services V3, a ukazuje rozdíly mezi dvěma verzemi. Článek také poskytuje pokyny k migraci pro přesun z Media Services V2 na v3.
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
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 901542e2a69d2c7880825d76c1d69d3795713ed2
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231170"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Pokyny k migraci pro přesun z Media Services V2 na V3

Tento článek popisuje změny, které byly představeny v Azure Media Services V3, zobrazuje rozdíly mezi dvěma verzemi a poskytuje pokyny k migraci.

Pokud máte k dispozici službu video Service na [starší verzi rozhraní api Media Services V2](../previous/media-services-overview.md), měli byste před migrací na rozhraní API V3 zkontrolovat následující pokyny a důležité informace. Rozhraní V3 API nabízí spoustu výhod a nových funkcí, které zlepšují vývojové prostředí a možnosti Media Services. Nicméně jak je uvedeno v části [známé problémy](#known-issues) v tomto článku, existují také určitá omezení v důsledku změn mezi verzemi rozhraní API. Tato stránka bude zachována, protože Media Services tým zajišťuje pokračování v vylepšeních rozhraní API v3 a řeší mezery mezi verzemi. 

> [!NOTE]
> Aktuálně nemůžete spravovat prostředky v3 pomocí webu Azure Portal. Použijte rozhraní [REST API](https://aka.ms/ams-v3-rest-ref), [rozhraní příkazového řádku](https://aka.ms/ams-v3-cli-ref) nebo některou z podporovaných sad [SDK](media-services-apis-overview.md#sdks).

## <a name="benefits-of-media-services-v3"></a>Výhody Media Services V3
  
### <a name="api-is-more-approachable"></a>Rozhraní API je více možností přístupu

*  V3 používá prostor Unified API, který zpřístupňuje funkce pro správu i provoz založené na Azure Resource Manageru. Šablony Azure Resource Manager lze použít k vytváření a nasazování transformací, koncových bodů streamování, živých událostí a dalších.
* [Openapi specifikace (dříve označované jako Swagger)](https://aka.ms/ams-v3-rest-sdk) dokument.
    Zpřístupňuje schéma pro všechny součásti služby, včetně kódování založeného na souborech.
* Sady SDK dostupné pro [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core [, Node. js](https://aka.ms/ams-v3-nodejs-ref), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Přejít](https://aka.ms/ams-v3-go-ref)a Ruby.
* Integrace [Azure CLI](https://aka.ms/ams-v3-cli-ref) pro jednoduchou podporu skriptování

### <a name="new-features"></a>Nové funkce

* Pro zpracování úloh založených na souborech můžete jako vstup použít adresu URL HTTP (S).<br/>Nemusíte mít již uložený obsah v Azure, ani nemusíte vytvářet prostředky.
* Zavádí koncepci [transformací](transforms-jobs-concept.md) pro zpracování úloh založených na souborech. Transformaci lze použít k vytvoření opakovaně použitelných konfigurací, k vytvoření Azure Resource Manager šablon a k izolaci nastavení zpracování mezi několika zákazníky nebo klienty.
* Asset může mít několik [lokátorů streamování](streaming-locators-concept.md) s různými nastaveními dynamického [balení](dynamic-packaging-overview.md) a dynamického šifrování.
* [Content Protection](content-key-policy-concept.md) podporuje více klíčových funkcí.
* Při použití Media Services pro překódování informačního kanálu s jedním přenosovou rychlostí do výstupního datového proudu s více přenosovými rychlostmi můžete streamovat živé události, které jsou až 24 hodin dlouhé.
* Nová podpora živého streamování s nízkou latencí pro živé události Další informace najdete v tématu [latence](live-event-latency.md).
* Live Event Preview podporuje [dynamické balení](dynamic-packaging-overview.md) a dynamické šifrování. To umožňuje ochranu obsahu ve verzi Preview i SPOJOVNÍK a HLS balení.
* Živý výstup je jednodušší použít než entita programu v rozhraních API v2. 
* Vylepšená podpora RTMP (zvýšená stabilita a lepší podpora zdrojového kodéru).
* Zabezpečení pro ingestování RTMP<br/>Při vytváření živé události získáte čtyři adresy URL pro příjem. 4 adresy URL pro přijímání jsou skoro stejné, mají stejný token streamování (AppId), ale liší se jenom část číslo portu. Dvě z těchto adres URL jsou primární a zálohují pro RTMP.   
* Máte v rámci svých entit řízení přístupu na základě role (RBAC). 

## <a name="changes-from-v2"></a>Změny z v2

* Pro prostředky vytvořené pomocí v3 Media Services podporuje pouze [šifrování úložiště Azure Storage na straně serveru](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * Rozhraní V3 API můžete použít s prostředky vytvořenými s rozhraními API v2, která měla [šifrování úložiště](../previous/media-services-rest-storage-encryption.md) (AES 256) poskytované Media Services.
    * Nemůžete vytvářet nové Assety se starším [šifrováním úložiště](../previous/media-services-rest-storage-encryption.md) AES 256, které používá rozhraní V3 API.
* Vlastnosti [assetu](assets-concept.md)v v3 se liší od v2, viz [jak mapování vlastností](assets-concept.md#map-v3-asset-properties-to-v2).
* Sady SDK V3 jsou teď oddělené od sady SDK úložiště, což vám dává větší kontrolu nad verzí sady SDK pro úložiště, kterou chcete použít, a vyhněte se problémům se správou verzí. 
* V rozhraních API V3 jsou všechny přenosové rychlosti kódování v bitech za sekundu. To se liší od přednastavených Media Encoder Standard v2. Například přenosová rychlost v v2 by se zadala jako 128 (KB/s), ale v v3 by byla 128000 (bity za sekundu). 
* Entity AssetFiles, AccessPolicies a IngestManifests v v3 neexistují.
* Vlastnost IAsset. ParentAssets v v3 neexistuje.
* ContentKeys už není entita, teď je to vlastnost lokátoru streamování.
* Event Grid podpora nahrazuje NotificationEndpoints.
* Následující entity byly přejmenovány
    * Výstup úlohy nahrazuje úlohu a je teď součástí úlohy.
    * Lokátor streamování nahrazuje lokátor.
    * Živá událost nahrazuje kanál.<br/>Účtování živých událostí je založeno na měřičích živých kanálů. Další informace najdete v tématu [fakturace](live-event-states-billing.md) a [ceny](https://azure.microsoft.com/pricing/details/media-services/).
    * Živý výstup nahrazuje program.
* Živé výstupy začínají při vytváření a při odstranění se zastaví. Programy fungují jinak v rozhraních API v2, musely se spustit po vytvoření.
*  Chcete-li získat informace o úloze, je nutné znát název transformace, pod kterou byla úloha vytvořena. 

> [!NOTE]
> Projděte si zásady vytváření názvů, které se vztahují k [prostředkům Media Services V3](media-services-apis-overview.md#naming-conventions). Přečtěte si také pojmenování [objektů BLOB](assets-concept.md#naming-blobs).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Mezery funkcí v souvislosti s rozhraními API v2

Rozhraní V3 API má následující mezery v souvislosti s rozhraním API v2. Uzavírání mezer je nedokončené.

* [Kodér úrovně Premium](../previous/media-services-premium-workflow-encoder-formats.md) a starší [procesory media Analytics](../previous/media-services-analytics-overview.md) (Azure Media Services indexer 2 Preview, Face redigování atd.) nejsou přístupné prostřednictvím v3.<br/>Zákazníci, kteří chtějí migrovat z Media Indexer 1 nebo 2 Preview, můžou hned použít předvolbu AudioAnalyzer v rozhraní V3 API.  Tato nová předvolba obsahuje více funkcí, než je starší Media Indexer 1 nebo 2. 
* Mnohé z [pokročilých funkcí Media Encoder standard v](../previous/media-services-advanced-encoding-with-mes.md) rozhraních API v2 v současnosti nejsou v systému V3 k dispozici, například:
  
    * Sešity prostředků
    * Překrytí
    * Jej
    * Sprite miniatur
    * Vložení tiché zvukové stopy, když vstup nemá žádný zvuk
    * Vložení stop videa, když vstup nemá žádné video
* Živé události s překódováním v současné době nepodporují vložení do středu a vložení značek AD prostřednictvím volání rozhraní API. 

> [!NOTE]
> Zapište prosím tento článek do záložky a zkontrolujte, jestli jsou aktualizace.
 
## <a name="code-differences"></a>Rozdíly v kódu

V následující tabulce jsou uvedeny rozdíly v kódu mezi v2 a v3 pro běžné scénáře.

|Scénář|V2 API|V3 API|
|---|---|---|
|Vytvoření assetu a nahrání souboru |[Příklad v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[Příklad v3 .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Odeslat úlohu|[Příklad v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[Příklad v3 .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Ukazuje, jak nejdřív vytvořit transformaci a pak odeslat úlohu.|
|Publikování assetu pomocí šifrování AES |1. Create ContentKeyAuthorizationPolicyOption<br/>2. Vytvořit ContentKeyAuthorizationPolicy<br/>3. Vytvořit AssetDeliveryPolicy<br/>4. Vytvoření Assetu a nahrání obsahu nebo odeslání úlohy a použití výstupního prostředku<br/>5. Přidružte AssetDeliveryPolicy k Assetu.<br/>6. Vytvořit ContentKey<br/>7. Připojit ContentKey k Assetu<br/>8. Vytvořit AccessPolicy<br/>9. Vytvořit Lokátor<br/><br/>[Příklad v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Vytvořit zásady pro klíč obsahu<br/>2. Vytvořit Asset<br/>3. Nahrání obsahu nebo použití Assetu jako JobOutput<br/>4. Vytvořit Lokátor streamování<br/><br/>[Příklad v3 .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Získat podrobnosti o úloze a spravovat úlohy |[Správa úloh s v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Správa úloh pomocí v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="known-issues"></a>Známé problémy

* Aktuálně nemůžete spravovat prostředky v3 pomocí webu Azure Portal. Použijte [REST API](https://aka.ms/ams-v3-rest-sdk), CLI nebo jednu z podporovaných sad SDK.
* Ve svém účtu musíte zřídit rezervované jednotky médií (MRUs), aby bylo možné řídit souběžnost a výkon vašich úloh, zejména ty, které zahrnují analýzu videa nebo zvuku. Další informace najdete v článku o [škálování zpracování médií](../previous/media-services-scale-media-processing-overview.md). MRUs můžete spravovat pomocí [CLI 2,0 pro Media Services V3](media-reserved-units-cli-how-to.md), pomocí [Azure Portal](../previous/media-services-portal-scale-media-processing.md)nebo pomocí [rozhraní API v2](../previous/media-services-dotnet-encoding-units.md). Musíte zřídit MRUs, ať už používáte rozhraní API Media Services v2 nebo V3.
* Entity Media Services vytvořené s rozhraním API V3 se nedají spravovat pomocí rozhraní API v2.  
* Nedoporučujeme spravovat entity, které byly vytvořené pomocí rozhraní API v2 prostřednictvím rozhraní V3 API. Následují příklady rozdílů, které vytvářejí nekompatibilní entity ve dvou verzích:   
    * Úlohy a úlohy vytvořené v v2 se v v3 nezobrazují, protože nejsou přidružené k transformaci. Doporučení je přepnuto na transformace v3 a úlohy. Během přepínání bude existovat poměrně krátká doba nutná ke sledování úloh inlety v2.
    * Kanály a programy vytvořené pomocí v2 (které jsou namapované na živé události a živé výstupy ve verzi V3) nemůžou dál spravovat pomocí v3. Doporučení je přepnout na hodnotu v3 živé události a živé výstupy na pohodlném zastavení kanálu.<br/>V současné době nemůžete migrovat nepřetržitě běžící kanály.  

> [!NOTE]
> Tato stránka bude zachována, protože Media Services tým zajišťuje pokračování v vylepšeních rozhraní API v3 a řeší mezery mezi verzemi.

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

Projděte si článek o [streamování souborů](stream-files-dotnet-quickstart.md), kde se dozvíte, jak je snadné začít s kódováním a streamováním videosouborů. 


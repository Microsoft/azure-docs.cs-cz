---
title: Migrace z Azure Media Services v2 na v3 | Dokumentace Microsoftu
description: Tento článek popisuje změny, které byly zavedeny v Azure Media Services v3 a jsou uvedeny rozdíly mezi dvěma verzemi. Tento článek také poskytuje pokyny k migraci pro přechod ze služby Media Services v2 na v3.
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
ms.date: 02/04/2019
ms.author: juliako
ms.openlocfilehash: 4f67158c0de8cdd161bce269059af6d421bb68b5
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56340344"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Pokyny k migraci pro přechod ze služby Media Services v2 na v3

Tento článek popisuje změny, které byly zavedeny v Azure Media Services v3, jsou uvedeny rozdíly mezi dvěma verzemi a poskytuje pokyny k migraci.

Pokud máte službu poskytování videí dnes vyvinuté v horní části [starší verze rozhraní API služby Media Services v2](../previous/media-services-overview.md), měli byste si přečíst následující pokyny a důležité informace před migrací do rozhraní API v3. Existuje mnoho výhod a nových funkcí v rozhraní API v3, které vylepšují prostředí pro vývojáře a možnosti v Media Services. Jak již však volané ve [známé problémy v sadě](#known-issues) části tohoto článku, existují také určitá omezení z důvodu změny mezi verzí rozhraní API. Tato stránka se zachová, a tým Media Services umožňuje další vylepšení rozhraní API v3 se zaměřuje na mezery mezi verzemi. 

> [!NOTE]
> V současné době nelze použít na webu Azure portal ke správě prostředků v3. Použití [rozhraní REST API](https://aka.ms/ams-v3-rest-sdk), rozhraní příkazového řádku, nebo jeden z podporovaných sad SDK.

## <a name="benefits-of-media-services-v3"></a>Výhody služby Media Services v3

### <a name="api-is-more-approachable"></a>Rozhraní API je více přístupné

*  V3 používá prostor Unified API, který zpřístupňuje funkce pro správu i provoz založené na Azure Resource Manageru. Šablony Azure Resource Manageru je možné vytvořit a nasadit transformací, koncové body streamování, živé události a další.
* [Otevřete rozhraní API (označuje se také jako Swagger) specifikace](https://aka.ms/ams-v3-rest-sdk) dokumentu.
    Udává schéma pro všechny součásti služby, včetně souborů kódování.
* Sady SDK jsou dostupné pro [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](https://aka.ms/ams-v3-nodejs-ref), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Přejít](https://aka.ms/ams-v3-go-ref)a Ruby.
* [Azure CLI](https://aka.ms/ams-v3-cli-ref) integrace pro podporu jednoduché skriptování.

### <a name="new-features"></a>Nové funkce

* Pro souborové úlohy zpracování můžete adresu URL protokolu HTTP (S) jako vstup.<br/>Není potřeba mít obsah už uložená v Azure, ani je potřeba pro vytvoření prostředků.
* Zavádí koncepci [transformuje](transforms-jobs-concept.md) pro zpracování úlohy založené na souborech. Transformace je možné vytvářet opakovaně použitelné konfigurace k vytváření šablon Azure Resource Manageru a izolovat zpracování nastavení mezi více odběratelům nebo tenantů.
* Prostředek může mít více [lokátory streamování](streaming-locators-concept.md) spolu různé [dynamické balení](dynamic-packaging-overview.md) nastavení a dynamické šifrování.
* [Ochrana obsahu](content-key-policy-concept.md) podporuje více klíčových funkcí.
* Můžete Streamovat živé události, které jsou dlouhé až 24 hodin, pokud informační kanál pomocí služby Media Services pro překódování příspěvek s jednou přenosovou rychlostí do výstupního datového proudu, který má více přenosových rychlostí.
* Nová s nízkou latencí živé streamování podpory na živé události. Další informace najdete v tématu [latence](live-event-latency.md).
* Živé události ve verzi Preview podporuje [dynamické balení](dynamic-packaging-overview.md) a dynamické šifrování. To umožňuje ochranu obsahu pro balení ve verzi Preview a DASH nebo HLS.
* Živé výstup je snazší než Program entit v rozhraní API v2. 
* (Zvýšení stability a další podporu zdrojového kodér) Vylepšená podpora RTMP ve službě.
* Ingestování RTMPS zabezpečené.<br/>Při vytváření živá událost se zobrazí 4 ingestované adresy URL. Ingestování 4 adresy URL jsou téměř identické, mít stejný token streamování (AppId), jenom část čísla portu se liší. Dva z adres URL jsou primární a záložní pro RTMPS.   
* Máte řízení přístupu na základě rolí (RBAC), vaše entity. 

## <a name="changes-from-v2"></a>Změny z v2

* Pro prostředky vytvořené pomocí v3, služba Media Services podporuje pouze [šifrování na straně serveru úložiště Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * Můžete použít rozhraní API v3 s prostředky, které jsou vytvořené pomocí rozhraní API v2, který měl [šifrování úložiště](../previous/media-services-rest-storage-encryption.md) (AES 256) poskytované službou Media Services.
    * Nelze vytvořit nové prostředky pomocí starší verze AES 256 [šifrování úložiště](../previous/media-services-rest-storage-encryption.md) pomocí rozhraní API v3.
* Sady SDK verze 3 se nyní oddělený od sady SDK služby Storage, což vám dává větší kontrolu nad verzi sady SDK služby Storage použít a správa verzí – potíže se vyhnete. 
* V rozhraní API v3 kódování přenosové rychlosti jsou všechny bity za sekundu. To se liší od v2, který přednastavení kodéru Media Encoder Standard. Například s přenosovou rychlostí v v2 by byl zadán jako 128 (kb/s), ale ve verzi 3 by bylo 128000 (bitů za sekundu). 
* Entity AssetFiles AccessPolicies a IngestManifests neexistují ve verzi 3.
* Vlastnost IAsset.ParentAssets neexistuje ve verzi 3.
* Klíčů ContentKeys už není entita, je teď součástí Lokátor streamování.
* Event Grid podporu nahradí NotificationEndpoints.
* Následující entity byly přejmenovány.
    * Výstup úlohy nahrazuje úkolů a je teď součástí projektu.
    * Lokátor streamování nahradí Lokátor.
    * Živé události nahradí kanálu.<br/>Živé události se účtuje podle měřiče živý kanál. Další informace najdete v tématu [fakturační](live-event-states-billing.md) a [ceny](https://azure.microsoft.com/pricing/details/media-services/).
    * Živé nahradí výstup programu.
* Výstupy za provozu není nutné explicitně spustit, spusťte na vytváření a zastavit při odstranění. Programy pracoval odlišně v rozhraních API v2, měly spustit po jeho vytvoření.

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Funkce mezer s ohledem na rozhraní API v2

Rozhraní API v3 má následující funkce mezer s ohledem na rozhraní API v2. Zavření mezer je ve vývoji.

* [Kodér úrovně Premium](../previous/media-services-premium-workflow-encoder-formats.md) a starší [media analytics procesorů](../previous/media-services-analytics-overview.md) (Azure Media Services Indexer 2 ve verzi Preview, Face Redactoru atd.) nejsou přístupné prostřednictvím v3.<br/>Zákazníci, kteří chtějí migrovat z Media Indexer 1 nebo 2 preview můžete okamžitě použít AudioAnalyzer přednastavení v rozhraní API v3.  Toto nové přednastavení obsahuje víc funkcí než starší Media Indexer 1 nebo 2. 
* Mnohé z pokročilých funkcích sady Media Encoder Standard v rozhraních API v2 aktuálně nejsou k dispozici ve verzi 3, jako například:
    * Omezení (pro scénáře na vyžádání a živé)
    * Spojování prostředků
    * Překrytí
    * Oříznutí
    * Prvky, které budou miniatur
* Živé události s překódování aktuálně nepodporují břidlicová kurzor umístíte dovnitř datového proudu a ad vkládáním značek pro reklamy prostřednictvím volání rozhraní API. 

> [!NOTE]
> Tento článek (záložky) a zachovat, vyhledávají se aktualizace.
 
## <a name="code-differences"></a>Rozdílů v kódu

V následující tabulce jsou uvedeny rozdíly v kódu mezi v2 a v3 pro běžné scénáře.

|Scénář|V2 API|V3 API|
|---|---|---|
|Vytvoření assetu a nahrání souboru |[Příklad .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[Příklad .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Odeslání úlohy|[Příklad .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[Příklad .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Předvádí postup při prvním vytvoření transformace a odešlete úlohu.|
|Publikování assetu pomocí šifrování AES |1. Create ContentKeyAuthorizationPolicyOption<br/>2. Create ContentKeyAuthorizationPolicy<br/>3. Create AssetDeliveryPolicy<br/>4. Vytvoření prostředku a nahrát obsah nebo odeslat úlohu a použití prostředku výstupu<br/>5. Přiřazení k AssetDeliveryPolicy Assetu<br/>6. Vytvoření ContentKey<br/>7. Připojit ContentKey k Assetu<br/>8. Vytvoření AccessPolicy<br/>9. Vytvoření lokátoru<br/><br/>[Příklad .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Vytvoření obsahu klíče zásad<br/>2. Vytvoření prostředku<br/>3. Nahrát obsah nebo Asset používat jako JobOutput<br/>4. Vytvořit lokátor streamování<br/><br/>[Příklad .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|

## <a name="known-issues"></a>Známé problémy

* V současné době nelze použít na webu Azure portal ke správě prostředků v3. Použití [rozhraní REST API](https://aka.ms/ams-v3-rest-sdk), rozhraní příkazového řádku, nebo jeden z podporovaných sad SDK.
* Potřebujete zřizovat rezervované jednotky médií (použité položky) ve svém účtu k řízení souběžnosti a výkonu vašich úloh, zejména těch, které jsou zahrnující Video nebo zvuk analýzy. Další informace najdete v článku o [škálování zpracování médií](../previous/media-services-scale-media-processing-overview.md). Můžete spravovat pomocí často používané cesty [CLI 2.0 pro Media Services v3](media-reserved-units-cli-how-to.md), použije [webu Azure portal](../previous/media-services-portal-scale-media-processing.md), nebo pomocí [rozhraní API v2](../previous/media-services-dotnet-encoding-units.md). Potřebujete zřizovat použité položky, ať už jsou pomocí Media Services v2 nebo v3 rozhraní API.
* Služba Media Services entity vytvořené pomocí v3, které rozhraní API nejde spravovat pomocí rozhraní API v2.  
* Není doporučeno spravovat entity, které byly vytvořeny s rozhraními API v2 přes rozhraní API v3. Následuje několik příkladů, které usnadňují entity, které ve dvou verzích nekompatibilní rozdíly:   
    * Úlohy a úkoly vytvořené ve verzi v2 není uveden ve verzi 3 nejsou spojeny s transformace. Doporučuje se přepnout na v3 transformace a úlohy. Bude existovat poměrně krátké časové období museli monitorování v2 probíhající úlohy při přepnutí.
    * Kanály a programy vytvořené pomocí v2 (které jsou mapovány na živé události a Live výstupy ve verzi 3) nemůže pokračovat, spravován pomocí v3. Doporučuje se přepnout na v3 živé události a Live výstupů na vhodné zastavit kanál.<br/>V současné době nemůžete migrovat průběžně běžícím kanálům se půjde.  

> [!NOTE]
> Tato stránka se zachová, a tým Media Services umožňuje další vylepšení rozhraní API v3 se zaměřuje na mezery mezi verzemi.

## <a name="next-steps"></a>Další postup

Projděte si článek o [streamování souborů](stream-files-dotnet-quickstart.md), kde se dozvíte, jak je snadné začít s kódováním a streamováním videosouborů. 


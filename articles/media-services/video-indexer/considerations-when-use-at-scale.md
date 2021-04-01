---
title: Co je potřeba vzít v úvahu při použití Video Indexer ve škále – Azure
titleSuffix: Azure Media Services
description: Toto téma vysvětluje, co je potřeba vzít v úvahu při použití Video Indexer ve velkém měřítku.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: b955c0f494b757fd29c400194ef8b11314a89a03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96483606"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>Co je potřeba zvážit při použití Video Indexer ve velkém měřítku

Při použití nástroje Azure Media Services video indexer k indexování videí a zvětšování archivu videí zvažte možnost škálování. 

V tomto článku najdete odpovědi na otázky:

* Je potřeba vzít v úvahu nějaká technologická omezení?
* Máte inteligentní a efektivní způsob, jak to provést?
* Můžu v procesu zabránit nadměrnému množství peněz?

Tento článek nabízí šest osvědčených postupů použití Video Indexer se škálováním.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>Při nahrávání videí zvažte použití adresy URL v bajtovém poli.

Video Indexer vám dá možnost nahrávat videa z adresy URL nebo přímo odesláním souboru jako pole bajtů. ta ale obsahuje omezení. Další informace najdete v tématu [odeslání důležitých informací a omezení.)](upload-index-videos.md#uploading-considerations-and-limitations)

Za prvé má omezení velikosti souborů. Velikost souboru bajtového pole je v porovnání s omezením velikosti nahrávání na 30 GB při použití adresy URL omezená na 2 GB.

Za druhé zvažte jenom některé z problémů, které mohou ovlivnit váš výkon, a tudíž možnost škálovat:

* Posílání souborů pomocí více částí znamená vysokou závislost v síti, 
* spolehlivost služby, 
* komunikační 
* rychlost nahrávání, 
* ztracené pakety někam na webu.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="První aspekt použití Video Indexer se škálováním":::

Když odesíláte videa pomocí adresy URL, stačí zadat cestu k umístění mediálního souboru a Video Indexer se postará o zbytek (viz `videoUrl` pole v rozhraní [nahrávat video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload) API).

> [!TIP]
> Použijte `videoUrl` volitelný parametr rozhraní API pro nahrání videa.

Pokud chcete zobrazit příklad nahrávání videí pomocí adresy URL, podívejte se na [Tento příklad](upload-index-videos.md#code-sample). Nebo můžete použít [AzCopy](../../storage/common/storage-use-azcopy-v10.md) pro rychlý a spolehlivý způsob, jak získat obsah do účtu úložiště, ze kterého ho můžete odeslat video indexer pomocí [adresy URL SAS](../../storage/common/storage-sas-overview.md).

## <a name="increase-media-reserved-units-if-needed"></a>V případě potřeby zvýšit rezervované jednotky médií

Ve fázi ověření konceptu se obvykle používá Video Indexer nepotřebujete spoustu výpočetní síly. Když začnete s větším počtem videí, které potřebujete indexovat a chcete, aby byl proces na tempu, které vyhovuje vašemu případu použití, je třeba škálovat využití Video Indexer. Proto byste se měli domnívat o zvýšení počtu výpočetních prostředků, které použijete v případě, že aktuální množství výpočetního výkonu není dostatečné.

Pokud chcete zvýšit výpočetní výkon a paralelismu, musíte v Azure Media Services věnovat pozornost [jednotkám rezervovaným](../latest/concept-media-reserved-units.md)pro média (ru). Ru jsou výpočetní jednotky, které určují parametry pro úlohy zpracování multimédií. Počet ru má vliv na počet úloh médií, které mohou být souběžně zpracovány v každém účtu, a jejich typ určuje rychlost zpracování a jedno video může vyžadovat více než jedno RU, pokud je jeho indexování složité. Když je váš ru zaneprázdněný, budou se ve frontě uchovávat nové úlohy, dokud nebude k dispozici jiný prostředek.

K efektivnímu fungování a k tomu, aby nedocházelo k nečinným prostředkům, Video Indexer nabízí systém automatického škálování, který se ru dolů, když je potřeba méně zpracování, a ru nahoru, když jste ve svých nespěcháte hodinách (abyste mohli plně využívat všechna vaše ru). Tuto funkci můžete povolit [zapnutím automatického škálování](manage-account-connected-to-azure.md#autoscale-reserved-units) v nastavení účtu nebo pomocí služby [Azure-Place-Account rozhraní API Azure-Media-Services](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&pattern=update).

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="Druhé zvážení při použití Video Indexer se škálováním":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="Jednotky rezervované pro AMS":::

Pro minimalizaci doby trvání indexování a nízké propustnosti doporučujeme začít s 10 ru typu S3. Později Pokud budete škálovat až na podporu většího obsahu nebo vyšší souběžnosti a potřebujete další prostředky, můžete [nás kontaktovat pomocí systému podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) (jenom u placených účtů) a požádat o další přidělení ru.

## <a name="respect-throttling"></a>Respektování omezení

Video Indexer je navržená tak, aby se zabývat škálováním ve velkém měřítku, a pokud chcete využít jeho maximum, měli byste také vědět, jaké jsou možnosti systému, a odpovídajícím způsobem navrhovat integraci. Nechcete odeslat žádost o nahrání pro dávku videí jenom ke zjištění, že některé filmy se neodeslaly a že přijímáte kód odpovědi HTTP 429 (příliš mnoho požadavků). Může to být způsobeno tím, že jste odeslali více požadavků, než je počet [filmů za minutu, který podporujeme](upload-index-videos.md#uploading-considerations-and-limitations). Video Indexer přidá `retry-after` hlavičku v odpovědi HTTP, záhlaví určuje, kdy se má pokusit o další opakování. Před vyzkoušením dalšího požadavku se ujistěte, že jste to provedli.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Dobře Navrhněte svou integraci a omezení respektování":::

## <a name="use-callback-url"></a>Použít adresu URL zpětného volání

Doporučujeme, abyste nemuseli dotazovat stav vaší žádosti nepřetržitě od druhé odeslané žádosti o nahrání, můžete přidat [adresu URL zpětného volání](upload-index-videos.md#callbackurl)a počkat na video indexer, abyste si ji mohli aktualizovat. Jakmile v žádosti o nahrání dojde ke změně stavu, dostanete oznámení příspěvku na zadanou adresu URL.

Adresu URL zpětného volání můžete přidat jako jeden z parametrů [rozhraní API pro nahrání videa](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload). Podívejte se na ukázky kódu v [úložišti GitHub](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/). 

Pro adresu URL zpětného volání můžete také použít Azure Functions platformou řízenou událostmi, která se dá aktivovat protokolem HTTP a implementovat následující tok.

### <a name="callback-url-definition"></a>definice URL zpětného volání

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>Použití správných parametrů indexování

Při rozhodování, které souvisí s používáním Video Indexer ve velkém měřítku, se podívejte na to, jak se dá využít co nejlépe s správnými parametry podle vašich potřeb. Zamyslete se na svém případu použití definováním různých parametrů, které můžete ušetřit, a urychlit tak proces indexování vašich videí.

Před nahráním a indexováním videa si přečtěte tuto krátkou [dokumentaci](upload-index-videos.md), podívejte se na [indexingPreset](upload-index-videos.md#indexingpreset) a [streamingPreset](upload-index-videos.md#streamingpreset) , abyste získali lepší představu o tom, jaké možnosti jsou.

Například pokud nechcete sledovat video, nemusíte nastavovat předvolby pro streamování. Pokud potřebujete jenom zvukové poznatky, nevytvářejte indexové poznatky.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>Index v optimálním rozlišení, ne na nejvyšší rozlišení

Možná si vyžádáte, jakou kvalitu videa potřebujete k indexování videí? 

V mnoha případech není výkon indexování skoro žádný rozdíl mezi videem HD (720P) a 4K videími. Nakonec získáte téměř stejné přehledy se stejnou jistotou. Čím vyšší kvalita videa nahráváte, znamená to větší velikost souboru a to vede k vyššímu výpočetnímu výkonu a času potřebnému k nahrání videa.

Například pro funkci detekce obličeje může vyšší rozlišení pomáhat s scénářem, kdy existuje velký počet malých, ale kontextových důležitých ploch. To však povede ke kvadratickému nárůstu za běhu a zvýšené riziko falešně pozitivních hodnot.

Proto doporučujeme, abyste ověřili, že získáte správné výsledky pro váš případ použití a chcete ho nejdřív otestovat místně. Nahrajte stejné video v 720P a v 4K a porovnejte přehledy, které získáte.

## <a name="next-steps"></a>Další kroky

[Projděte si výstup Azure Video Indexer vytvořený pomocí rozhraní API](video-indexer-output-json-v2.md)
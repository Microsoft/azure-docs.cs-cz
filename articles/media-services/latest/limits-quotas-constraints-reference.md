---
title: Kvóty a omezení v Azure Media Services
description: Toto téma popisuje kvóty a omezení v Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: 9b1e43968569fb7c185043e1dd249c65fcadddfb
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278656"
---
<!-- If you update limits in this topic, make sure to also update https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#media-services-limits -->
# <a name="azure-media-services-quotas-and-limits"></a>Azure Media Services kvóty a omezení

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V tomto článku jsou uvedené některé z nejběžnějších omezení Microsoft Azure Media Services, které se taky někdy označují jako kvóty.

> [!NOTE]
> U prostředků, které nejsou opraveny, otevřete lístek podpory, který se zeptá na zvýšení kvót. Při pokusu o získání vyšších limitů nevytvářejte další účty Azure Media Services.

## <a name="account-limits"></a>Omezení účtu

| Prostředek | Výchozí omezení |
| --- | --- |
| [Účty Media Services](account-move-account-how-to.md) v jednom předplatném | 100 (fixní) |

## <a name="asset-limits"></a>Omezení assetu

| Prostředek | Výchozí omezení |
| --- | --- |
| [Prostředky](assets-concept.md) na účet Media Services | 1 000 000|

## <a name="storage-limits"></a>Omezení úložiště

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Velikost souboru| V některých scénářích je omezení maximální velikosti souboru podporovanou pro zpracování v Media Services. <sup>první</sup> |
| [Účty úložiště](storage-account-concept.md) | 100<sup>(2)</sup> (fixní) |

<sup>1</sup> maximální velikost podporovaná pro jeden objekt BLOB je v Azure Blob Storage v současnosti až o 5 TB. V Media Services na základě velikostí virtuálních počítačů, které služba používá, platí další omezení. Omezení velikosti se vztahuje na soubory, které nahráváte, a také soubory, které se generují v důsledku zpracování Media Services (kódování nebo analýza). Pokud je zdrojový soubor větší než 260 GB, bude pravděpodobně selhání úlohy. 

V následující tabulce jsou uvedena omezení pro rezervované jednotky médií S1, S2 a S3. Pokud je zdrojový soubor větší než omezení definovaná v tabulce, vaše úloha kódování se nezdařila. Pokud kódujete zdroje 4K rozlišení dlouhé doby trvání, budete muset použít jednotky rezervované pro médium S3 k dosažení potřebného výkonu. Pokud jste 4K obsah, který je větší než limit 260 GB na rezervované jednotky médií S3, otevřete lístek podpory.

|Typ rezervované jednotky médií|Maximální velikost vstupu (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> účty úložiště musí být ze stejného předplatného Azure.

## <a name="jobs-encoding--analyzing-limits"></a>Úlohy (analýza & analýza) omezení

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Počet [úloh](transform-jobs-concept.md) na účet Media Services | 500 000 <sup>(3)</sup> (fixní)|
| Vstupy úloh na úlohu | 50 (fixní)|
| Výstupy úloh na úlohu | 20 (fixní) |
| [Transformuje](transform-jobs-concept.md) účet za Media Services. | 100 (fixní)|
| Transformace výstupů v transformaci | 20 (fixní) |
| Vstup souborů na úlohu|10 (pevné)|

<sup>3</sup> toto číslo zahrnuje úlohy ve frontě, dokončených, aktivních a zrušených úlohách. Nezahrnuje odstraněné úlohy. 

Všechny záznamy úloh ve vašem účtu starším než 90 dnů budou automaticky odstraněny, i když celkový počet záznamů je nižší než maximální kvóta. 

## <a name="live-streaming-limits"></a>Omezení živého streamování

| Prostředek | Výchozí omezení | 
| --- | --- | 
| [Živé události](live-event-outputs-concept.md) <sup>(4)</sup> na účet Media Services |5|
| Živé výstupy na živou událost |3 <sup>(5)</sup> |
| Maximální doba trvání živého výstupu | [Velikost okna DVR](live-event-cloud-dvr-time-how-to.md) |

<sup>4</sup> podrobné informace o omezeních událostí za provozu najdete v tématu [porovnání a omezení typů událostí typu Live](live-event-types-comparison-reference.md).

<sup>5</sup> živých výstupů začíná při vytváření a při odstraňování je zastavování.

## <a name="packaging--delivery-limits"></a>Zabalení & omezení doručení

| Prostředek | Výchozí omezení |
| --- | --- |
| [Koncové body streamování](stream-streaming-endpoint-concept.md) (zastavené nebo spuštěné) na účet Media Services | 2 |
| Jednotky streamování Premium | 10 |
| [Filtry dynamických manifestů](filters-dynamic-manifest-concept.md)|100|
| [Zásady streamování](stream-streaming-policy-concept.md) | 100 <sup>(6)</sup> |
| Jedinečné [Lokátory streamování](stream-streaming-locators-concept.md) přidružené k prostředku v jednom okamžiku | 100<sup>(7)</sup> (fixní) |

<sup>6</sup> Pokud používáte vlastní [zásadu streamování](/rest/api/media/streamingpolicies), měli byste navrhnout určitou sadu takových zásad pro svůj účet Media Service a znovu je použít pro své StreamingLocators, kdykoli budete potřebovat stejné možnosti šifrování a protokoly. Pro každý Lokátor streamování byste neměli vytvářet nové zásady streamování.

<sup>7</sup> Lokátory streamování nejsou navržené pro správu řízení přístupu pro jednotlivé uživatele. Pokud chcete konkrétním uživatelům poskytnout různá přístupová práva, použijte řešení pro správu digitálních práv (DRM).

## <a name="protection-limits"></a>Omezení ochrany

| Prostředek | Výchozí omezení |
| --- | --- |
| Možnosti pro [zásady klíče obsahu](drm-content-key-policy-concept.md) |30 |
| Licence za měsíc pro každý typ DRM při Media Services Služba doručení klíčů na účet|1 000 000|

## <a name="support-ticket"></a>Lístek podpory

U nevyřešených prostředků můžete požádat o aktivaci kvót otevřením [lístku podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Do žádosti uveďte podrobné informace o požadovaných změnách kvóty, případech použití a požadovaných oblastech. <br/>**Nevytvářejte** další účty služby Azure Media Services ve snaze zajistit vyšší limity.

## <a name="next-steps"></a>Další kroky

[Přehled](media-services-overview.md)

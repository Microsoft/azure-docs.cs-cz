---
title: Kvóty a limity ve službě Azure Media Services
description: Toto téma popisuje kvóty a limity ve službě Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/01/2020
ms.author: juliako
ms.openlocfilehash: 2d4f5f83335f6f115362bcf66cf69d7f9de7eaa5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582326"
---
# <a name="azure-media-services-quotas-and-limits"></a>Kvóty a limity mediálních služeb Azure

Tento článek uvádí některé z nejběžnějších omezení Microsoft Azure Media Services, které se také někdy nazývají kvóty.

> [!NOTE]
> U prostředků, které nejsou opraveny, otevřete lístek podpory a požádejte o zvýšení kvót. Nevytvářejte další účty Azure Media Services ve snaze získat vyšší limity.

## <a name="account-limits"></a>Limity účtu

| Prostředek | Výchozí omezení | 
| --- | --- | 
| [Účty mediálních služeb](media-services-account-concept.md) v jednom předplatném | 25 (pevné) |

## <a name="asset-limits"></a>Limity majetku

| Prostředek | Výchozí omezení | 
| --- | --- | 
| [Datové zdroje](assets-concept.md) na účet mediálních služeb | 1 000 000|

## <a name="storage-limits"></a>Limity úložiště

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Velikost souboru| V některých případech je maximální velikost souboru podporovaná pro zpracování ve službě Media Services omezena. <sup>(1)</sup> |
| [Účty úložiště](storage-account-concept.md) | 100<sup>(2)</sup> (pevná) |

<sup>1</sup> Maximální velikost podporovaná pro jeden objekt blob je aktuálně až 5 TB v úložišti objektů blob Azure. Další omezení platí ve službách Media Services na základě velikostí virtuálních počítače, které služba používá. Omezení velikosti platí pro soubory, které nahrajete, a také na soubory, které se generují v důsledku zpracování mediálních služeb (kódování nebo analýza). Pokud je zdrojový soubor větší než 260 GB, úloha pravděpodobně selže. 

V následující tabulce jsou uvedena omezení rezervovaných médií Jednotek S1, S2 a S3. Pokud je zdrojový soubor větší než limity definované v tabulce, úloha kódování se nezdaří. Pokud kódujete zdroje rozlišení 4K s dlouhou dobou trvání, musíte k dosažení potřebného výkonu použít rezervované jednotky médií S3. Pokud máte obsah 4K, který je větší než limit 260 GB na rezervovaných jednotkách médií S3, otevřete lístek podpory.

|Typ rezervované jednotky média|Maximální vstupní velikost (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> Účty úložiště musí pojít ze stejného předplatného Azure.

## <a name="jobs-encoding--analyzing-limits"></a>Omezení úloh (kódování & analýzy)

| Prostředek | Výchozí omezení | 
| --- | --- | 
| [Úlohy](transforms-jobs-concept.md) na účet mediálních služeb | 500 000 <sup>(3)</sup> (pevná)|
| Vstupy úloh na úlohu | 50 (pevná)|
| Výstupy úloh na úlohu | 20 (pevná) |
| [Transformace](transforms-jobs-concept.md) na účet mediálních služeb | 100 (pevná)|
| Transformace výstupů v transformaci | 20 (pevná) |
| Soubory na vstup úlohy|10 (pevná)|

<sup>3</sup> Toto číslo zahrnuje úlohy zařazené do fronty, dokončené, aktivní a zrušené. Nezahrnuje odstraněné úlohy. 

Všechny záznamy úloh y ve vašem účtu starší než 90 dní budou automaticky odstraněny, a to i v případě, že celkový počet záznamů je nižší než maximální kvóta. 

## <a name="live-streaming-limits"></a>Limity živého streamování

| Prostředek | Výchozí omezení | 
| --- | --- | 
| [Živé události](live-events-outputs-concept.md) <sup>(4)</sup> na účet mediálních služeb |5|
| Živé výstupy na živou událost |3 <sup>(5)</sup> |
| Maximální doba trvání živého výstupu | 25 hodin |

<sup>4</sup> Podrobné informace o limitech živých událostí najdete v tématu [Porovnání a omezení typů živých událostí](live-event-types-comparison.md).

<sup>5</sup> Živé výstupy se spustí při vytváření a zastaví se při odstranění.

## <a name="packaging--delivery-limits"></a>Limity pro balení & dodávek

| Prostředek | Výchozí omezení | 
| --- | --- | 
| [Streamování koncových bodů](streaming-endpoint-concept.md) (zastaveno nebo spuštěno) na účet Mediální služby|2 |
| [Filtry dynamických manifestů](filters-dynamic-manifest-overview.md)|100|
| [Zásady streamování](streaming-policy-concept.md) | 100 <sup>(6)</sup> |
| Jedinečné [lokátory streamování](streaming-locators-concept.md) přidružené k datovému zdroji najednou | 100<sup>(7)</sup> (pevná) |

<sup>6</sup> Při použití [vlastních zásad streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies)byste měli pro svůj účet služby Media Service navrhnout omezenou sadu těchto zásad a znovu je použít pro streamovací lokátory, kdykoli jsou potřeba stejné možnosti šifrování a protokoly. Pro každý lokátor streamování byste neměli vytvářet nové zásady streamování.

<sup>7</sup> Lokátory streamování nejsou určeny pro správu řízení přístupu pro jednotlivé uživatele. Pokud chcete konkrétním uživatelům poskytnout různá přístupová práva, použijte řešení pro správu digitálních práv (DRM).

## <a name="protection-limits"></a>Limity ochrany

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Možnosti podle [zásad klíče obsahu](content-key-policy-concept.md) |30 | 
| Licence za měsíc pro každý typ DRM v doručovací službě media services na účet|1 000 000|

## <a name="support-ticket"></a>Lístek podpory

U prostředků, které nejsou pevné, můžete požádat o zvýšení kvót otevřením [lístku podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Do požadavku uveďte podrobné informace o požadovaných změnách kvót, scénářích případu použití a požadovaných oblastech. <br/>**Nevytvářejte** další účty služby Azure Media Services ve snaze zajistit vyšší limity.

## <a name="next-steps"></a>Další kroky

[Přehled](media-services-overview.md)

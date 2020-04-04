---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 03/31/2020
ms.author: juliako
ms.openlocfilehash: 99aeb5384b317d1b4d291c769b5402e829247b30
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656210"
---
> [!NOTE]
> U prostředků, které nejsou opraveny, otevřete lístek podpory a požádejte o zvýšení kvót. Nevytvářejte další účty Azure Media Services ve snaze získat vyšší limity.

### <a name="account-limits"></a>Limity účtu

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Účty mediálních služeb v jednom předplatném | 25 (pevné) |

### <a name="asset-limits"></a>Limity majetku

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Datové zdroje na účet mediálních služeb | 1 000 000|

### <a name="storage-media-limits"></a>Limity pro ukládání (média)

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Velikost souboru| V některých případech je maximální velikost souboru podporovaná pro zpracování ve službě Media Services omezena. <sup>(1)</sup> |
| Účty úložiště | 100<sup>(2)</sup> (pevná) |

<sup>1</sup> Maximální velikost podporovaná pro jeden objekt blob je aktuálně až 5 TB v úložišti objektů blob Azure. Další omezení platí ve službách Media Services na základě velikostí virtuálních počítače, které služba používá. Omezení velikosti platí pro soubory, které nahrajete, a také na soubory, které se generují v důsledku zpracování mediálních služeb (kódování nebo analýza). Pokud je zdrojový soubor větší než 260 GB, úloha pravděpodobně selže. 

V následující tabulce jsou uvedena omezení rezervovaných médií Jednotek S1, S2 a S3. Pokud je zdrojový soubor větší než limity definované v tabulce, úloha kódování se nezdaří. Pokud kódujete zdroje rozlišení 4K s dlouhou dobou trvání, musíte k dosažení potřebného výkonu použít rezervované jednotky médií S3. Pokud máte obsah 4K, který je větší než limit 260 GB na rezervovaných jednotkách médií S3, otevřete lístek podpory.

|Typ rezervované jednotky média|Maximální vstupní velikost (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> Účty úložiště musí pojít ze stejného předplatného Azure.

### <a name="jobs-encoding--analyzing-limits"></a>Omezení úloh (kódování & analýzy)

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Úlohy na účet mediálních služeb | 500 000 <sup>(3)</sup> (pevná)|
| Vstupy úloh na úlohu | 50 (pevná)|
| Výstupy úloh na úlohu | 20 (pevná) |
| Transformace na účet mediálních služeb | 100 (pevná)|
| Transformace výstupů v transformaci | 20 (pevná) |
| Soubory na vstup úlohy|10 (pevná)|

<sup>3</sup> Toto číslo zahrnuje úlohy zařazené do fronty, dokončené, aktivní a zrušené. Nezahrnuje odstraněné úlohy. 

Všechny záznamy úloh y ve vašem účtu starší než 90 dní budou automaticky odstraněny, a to i v případě, že celkový počet záznamů je nižší než maximální kvóta. 

### <a name="live-streaming-limits"></a>Limity živého streamování

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Živé události <sup>(4)</sup> na účet mediálních služeb |5|
| Živé výstupy na živou událost |3 <sup>(5)</sup> |
| Maximální doba trvání živého výstupu | 25 hodin |

<sup>4</sup> Podrobné informace o omezeních živých událostí naleznete v tématu [Porovnání a omezení typů živých událostí](../articles/media-services/latest/live-event-types-comparison.md).

<sup>5</sup> Živé výstupy se spustí při vytváření a zastaví se při odstranění.

### <a name="packaging--delivery-limits"></a>Limity pro balení & dodávek

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Streamování koncových bodů (zastaveno nebo spuštěno) na účet Mediální služby|2 (pevná)|
| Filtry dynamických manifestů|100|
| Zásady streamování | 100 <sup>(6)</sup> |
| Jedinečné lokátory streamování přidružené k datovému zdroji najednou | 100<sup>(7)</sup> (pevná) |

<sup>6</sup> Při použití [vlastních zásad streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies)byste měli pro svůj účet služby Media Service navrhnout omezenou sadu těchto zásad a znovu je použít pro streamovací lokátory, kdykoli jsou potřeba stejné možnosti šifrování a protokoly. Pro každý lokátor streamování byste neměli vytvářet nové zásady streamování.

<sup>7</sup> Lokátory streamování nejsou určeny pro správu řízení přístupu pro jednotlivé uživatele. Pokud chcete konkrétním uživatelům poskytnout různá přístupová práva, použijte řešení pro správu digitálních práv (DRM).

### <a name="protection-limits"></a>Limity ochrany

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Možnosti podle zásad klíče obsahu |30 | 
| Licence za měsíc pro každý typ DRM v doručovací službě media services na účet|1 000 000|

### <a name="support-ticket"></a>Lístek podpory

U prostředků, které nejsou pevné, můžete požádat o zvýšení kvót otevřením [lístku podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Do požadavku uveďte podrobné informace o požadovaných změnách kvót, scénářích případu použití a požadovaných oblastech. <br/>**Nevytvářejte** další účty služby Azure Media Services ve snaze zajistit vyšší limity.

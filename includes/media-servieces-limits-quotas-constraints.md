---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 03/31/2020
ms.author: juliako
ms.openlocfilehash: d6ff823951e6474a35a514036f02c0aabb17bf01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82997737"
---
> [!NOTE]
> U prostředků, které nejsou opraveny, otevřete lístek podpory, který se zeptá na zvýšení kvót. Při pokusu o získání vyšších limitů nevytvářejte další účty Azure Media Services.

### <a name="account-limits"></a>Omezení účtu

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Účty Media Services v jednom předplatném | 25 (pevné) |

### <a name="asset-limits"></a>Omezení assetu

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Prostředky na účet Media Services | 1 000 000|

### <a name="storage-media-limits"></a>Omezení úložiště (média)

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Velikost souboru| V některých scénářích je omezení maximální velikosti souboru podporovanou pro zpracování v Media Services. <sup>první</sup> |
| Účty úložiště | 100<sup>(2)</sup> (fixní) |

<sup>1</sup> maximální velikost podporovaná pro jeden objekt BLOB je v Azure Blob Storage v současnosti až o 5 TB. V Media Services na základě velikostí virtuálních počítačů, které služba používá, platí další omezení. Omezení velikosti se vztahuje na soubory, které nahráváte, a také soubory, které se generují v důsledku zpracování Media Services (kódování nebo analýza). Pokud je zdrojový soubor větší než 260 GB, bude pravděpodobně selhání úlohy. 

V následující tabulce jsou uvedena omezení pro rezervované jednotky médií S1, S2 a S3. Pokud je zdrojový soubor větší než omezení definovaná v tabulce, vaše úloha kódování se nezdařila. Pokud kódujete zdroje 4K rozlišení dlouhé doby trvání, budete muset použít jednotky rezervované pro médium S3 k dosažení potřebného výkonu. Pokud jste 4K obsah, který je větší než limit 260 GB na rezervované jednotky médií S3, otevřete lístek podpory.

|Typ rezervované jednotky médií|Maximální velikost vstupu (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> účty úložiště musí být ze stejného předplatného Azure.

### <a name="jobs-encoding--analyzing-limits"></a>Úlohy (analýza & analýza) omezení

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Počet úloh na účet Media Services | 500 000 <sup>(3)</sup> (fixní)|
| Vstupy úloh na úlohu | 50 (fixní)|
| Výstupy úloh na úlohu | 20 (fixní) |
| Transformuje účet za Media Services. | 100 (fixní)|
| Transformace výstupů v transformaci | 20 (fixní) |
| Vstup souborů na úlohu|10 (pevné)|

<sup>3</sup> toto číslo zahrnuje úlohy ve frontě, dokončených, aktivních a zrušených úlohách. Nezahrnuje odstraněné úlohy. 

Všechny záznamy úloh ve vašem účtu starším než 90 dnů budou automaticky odstraněny, i když celkový počet záznamů je nižší než maximální kvóta. 

### <a name="live-streaming-limits"></a>Omezení živého streamování

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Živé události <sup>(4)</sup> na účet Media Services |5|
| Živé výstupy na živou událost |3 <sup>(5)</sup> |
| Maximální doba trvání živého výstupu | [Velikost okna DVR](../articles/media-services/latest/live-event-cloud-dvr.md) |

<sup>4</sup> podrobné informace o omezeních pro živá událost najdete v tématu [porovnání a omezení typů událostí typu Live](../articles/media-services/latest/live-event-types-comparison.md).

<sup>5</sup> živých výstupů začíná při vytváření a při odstraňování je zastavování.

### <a name="packaging--delivery-limits"></a>Zabalení & omezení doručení

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Koncové body streamování (zastavené nebo spuštěné) na účet Media Services|2 (pevné)|
| Filtry dynamických manifestů|100|
| Zásady streamování | 100 <sup>(6)</sup> |
| Jedinečné Lokátory streamování přidružené k prostředku v jednom okamžiku | 100<sup>(7)</sup> (fixní) |

<sup>6</sup> Pokud používáte vlastní [zásadu streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies), měli byste navrhnout určitou sadu takových zásad pro svůj účet Media Service a znovu je použít pro své StreamingLocators, kdykoli budete potřebovat stejné možnosti šifrování a protokoly. Pro každý Lokátor streamování byste neměli vytvářet nové zásady streamování.

<sup>7</sup> Lokátory streamování nejsou navržené pro správu řízení přístupu pro jednotlivé uživatele. Pokud chcete konkrétním uživatelům poskytnout různá přístupová práva, použijte řešení pro správu digitálních práv (DRM).

### <a name="protection-limits"></a>Omezení ochrany

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Možnosti pro zásady klíče obsahu |30 | 
| Licence za měsíc pro každý typ DRM při Media Services Služba doručení klíčů na účet|1 000 000|

### <a name="support-ticket"></a>Lístek podpory

U nevyřešených prostředků můžete požádat o aktivaci kvót otevřením [lístku podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Do žádosti uveďte podrobné informace o požadovaných změnách kvóty, případech použití a požadovaných oblastech. <br/>**Nevytvářejte** další účty služby Azure Media Services ve snaze zajistit vyšší limity.

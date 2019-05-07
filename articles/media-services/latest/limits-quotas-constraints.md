---
title: Kvóty a omezení v Azure Media Services v3 | Dokumentace Microsoftu
description: Toto téma popisuje kvóty a omezení v Azure Media Services v3
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/02/2019
ms.author: juliako
ms.openlocfilehash: 42b8c4caa53ffa6b3bc1148544c75602597ac452
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153832"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvóty a omezení v Azure Media Services v3

Tento článek popisuje kvóty a omezení v Azure Media Services v3.

| Resource | Výchozí omezení | 
| --- | --- | 
| Prostředky na účet Azure Media Services | 1 000 000|
| Filtry dynamických manifestů|100|
| JobInputs na úlohu | 50 (fixní)|
| JobOutputs na úlohu | 20 (fixní) |
| TransformOutputs v transformace | 20 (fixní) |
| Soubory za JobInput|10 (fixní)|
| Velikost souboru| V některých scénářích platí omezení na maximální velikost souboru podporovaná při zpracování ve službě Media Services. <sup>(1)</sup> |
| Úloh na účet Media Services | 500 000 <sup>(2)</sup> (fixní)|
| Výpis transformací|Stránkování odpovědi s 1 000 transformací na stránku|
| Výpis úloh|Stránkování odpovědi s 500 úlohami na stránku|
| Živé události na účet Media Services |5|
| Účty Media Services v rámci jednoho předplatného | 25 (pevné) |
| Live výstupů na živou událost |3 <sup>(3)</sup> |
| Výstup živého maximální doba trvání | 25 hodin |
| Účty úložiště | 100<sup>(4)</sup> (fixní) |
| Koncové body streamování (zastavení nebo spuštění) na účtu Azure Media Services|2 (fixní)|
| Zásady streamování | 100 <sup>(5)</sup> |
| Transformuje na účtu Azure Media Services | 100 (fixní)|
| Jedinečné lokátory streamování přidružené k prostředku najednou | 100<sup>(6)</sup> (fixní) |
| Zásady symetrických klíčů |30 | 

<sup>1</sup> maximální velikost podporovaná pro jeden objekt blob je aktuálně ve službě Azure Blob Storage až 5 TB. Další omezení platí v závislosti na velikosti virtuálních počítačů, které se používají ve službě Media Services. Omezení velikosti platí pro soubory, které nahrajete a také soubory, které se vygeneruje jako výsledek zpracování (kódování nebo analýza) Media Services. Pokud zdrojový soubor je větší než 260 GB, vaše úloha se pravděpodobně nezdaří. 

Následující tabulka uvádí omezení na média rezervované jednotky S1, S2 a S3. Pokud zdrojový soubor je větší než omezení v tabulce, vaše úlohy kódování se nezdaří. Pokud kódujete zdroje rozlišení 4K dlouhá doba trvání, budete muset použít S3 rezervovaných jednotek médií k zajištění výkonu potřeba. Pokud máte 4 kB obsah, který je větší než limit 260 GB S3 rezervovaných jednotek médií, kontaktujte nás na adrese amshelp@microsoft.com pro zmírnit na podporu vašeho scénáře.

|Typ jednotky rezervované pro média   |Maximální velikost vstupu (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> toto číslo zahrnuje zařazených do fronty, dokončené, aktivní a zrušené úlohy. Nezahrnuje odstraněné úlohy. 

Libovolný záznam úlohy ve vašem účtu, který je starší než 90 dní automaticky odstraní, i v případě, že celkový počet záznamů je nižší než maximální kvóta. 

<sup>3</sup> live výstupů spuštění při vytvoření a zastavení při odstranění.

<sup>4</sup> účtů úložiště musí být ve stejném předplatném Azure.

<sup>5</sup> při použití vlastního [streamování zásad](https://docs.microsoft.com/rest/api/media/streamingpolicies), by měly omezenou sadu zásad návrhu pro svůj účet Media Service a znovu je použít pro vaše StreamingLocators pokaždé, když se na stejné šifrování možnosti a protokoly jsou potřeba. By neměl být vytváření nových zásad streamování pro každý Lokátor streamování.

<sup>6</sup> streamování lokátory nejsou určené pro správu řízení přístupu na uživatele. Pokud chcete konkrétním uživatelům poskytnout různá přístupová práva, použijte řešení pro správu digitálních práv (DRM).

## <a name="support-ticket"></a>Lístek podpory

Pro prostředky, které nejsou pevné, můžete požádat o zvýšení kvóty být vyvolána, otevřením [lístek podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). V žádosti o změny požadované kvóty a scénáře použití a oblastech je potřeba zahrňte podrobné informace. <br/>**Nevytvářejte** další účty služby Azure Media Services ve snaze zajistit vyšší limity.

## <a name="next-steps"></a>Další postup

[Přehled](media-services-overview.md)

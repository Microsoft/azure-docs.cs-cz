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
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: d5fc14adab956fae23aad24fa7bc488c8c2041e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322562"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvóty a omezení v Azure Media Services v3

Tento článek popisuje kvóty a omezení v Azure Media Services v3.

| Prostředek | Výchozí omezení | 
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
| Živé výstupy ve spuštěném stavu na živá událost |3|
| Výstup živého maximální doba trvání | 25 hodin |
| Účty úložiště | 100<sup>(4)</sup> (fixní) |
| Koncové body streamování (zastavení nebo spuštění) na účtu Azure Media Services|2 (fixní)|
| Zásady streamování | 100 <sup>(3)</sup> |
| Transformuje na účtu Azure Media Services | 100 (fixní)|
| Jedinečné lokátory streamování přidružené k prostředku najednou | 100<sup>(5)</sup> (fixní) |
| Zásady symetrických klíčů |30 | 

<sup>1</sup> maximální velikost podporovaná pro jeden objekt blob je aktuálně ve službě Azure Blob Storage až 5 TB. Další omezení platí ale, v závislosti na velikosti virtuálních počítačů, které používají službu Azure Media Services. Pokud zdrojový soubor je větší než 260 GB, vaše úloha se pravděpodobně nezdaří. Pokud máte 4 kB obsah, který je větší než limit 260 GB, kontaktujte nás na adrese amshelp@microsoft.com pro zmírnit na podporu vašeho scénáře.

<sup>2</sup> toto číslo zahrnuje zařazených do fronty, dokončené, aktivní a zrušené úlohy. Nezahrnuje odstraněné úlohy. 

Libovolný záznam úlohy ve vašem účtu, který je starší než 90 dní automaticky odstraní, i v případě, že celkový počet záznamů je nižší než maximální kvóta. 

<sup>3</sup> při použití vlastního [streamování zásad](https://docs.microsoft.com/rest/api/media/streamingpolicies), by měly omezenou sadu zásad návrhu pro svůj účet Media Service a znovu je použít pro vaše StreamingLocators pokaždé, když se na stejné šifrování možnosti a protokoly jsou potřeba. By neměl být vytváření nových zásad streamování pro každý Lokátor streamování.

<sup>4</sup> účtů úložiště musí být ve stejném předplatném Azure.

<sup>5</sup> streamování lokátory nejsou určené pro správu řízení přístupu na uživatele. Pokud chcete konkrétním uživatelům poskytnout různá přístupová práva, použijte řešení pro správu digitálních práv (DRM).

## <a name="support-ticket"></a>Lístek podpory

Pro prostředky, které nejsou pevné, můžete požádat o zvýšení kvóty být vyvolána, otevřením [lístek podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). V žádosti o změny požadované kvóty a scénáře použití a oblastech je potřeba zahrňte podrobné informace. <br/>**Nevytvářejte** další účty služby Azure Media Services ve snaze zajistit vyšší limity.

## <a name="next-steps"></a>Další postup

[Přehled](media-services-overview.md)

---
title: Kvóty a omezení v Azure Media Services v3 | Dokumentace Microsoftu
description: Toto téma popisuje kvóty a omezení v Azure Media Services v3
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/26/2018
ms.author: juliako
ms.openlocfilehash: 49b834325ce819f20978e06d85ee308955510ac1
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051105"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvóty a omezení v Azure Media Services v3

Tento článek popisuje kvóty a omezení v Azure Media Services v3.

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Prostředky na účtu Azure Media Services | 1 000 000|
| Filtry dynamických manifestů|100|
| JobInputs na úlohu | 50 (fixní)|
| JobOutputs za úlohu/TransformOutputs v transformace | 20 (fixní) |
| Soubory za JobInput|10 (fixní)|
| Velikost souboru| V některých scénářích platí omezení na maximální velikost souboru podporovaná při zpracování ve službě Media Services. <sup>(1)</sup> |
| Úloh na účet Media Services | 500 000 <sup>(2)</sup> (fixní)|
| Seznam transformací|Stránkování odpověď, a to s 1 000 transformace na jedné stránce|
| Seznam úloh|Stránkování odpověď, a to s 500 úloh na stránce|
| LiveEvents na účtu Azure Media Services |5|
| Účty Media Services v rámci jednoho předplatného | 25 (pevné) |
| LiveOutputs ve spuštěném stavu na Livestream |3|
| Účty úložiště | 100<sup>(4)</sup> (fixní) |
| Koncové body streamování ve spuštěném stavu na účtu Azure Media Services|2|
| StreamingPolicies | 100 <sup>(3)</sup> |
| Transformuje na účtu Azure Media Services | 100 (fixní)|
| Jedinečné StreamingLocators přidružené k prostředku najednou | 100<sup>(5)</sup> (fixní) |

<sup>1</sup> maximální velikost podporovaná pro jeden objekt blob je aktuálně ve službě Azure Blob Storage až 5 TB. Další omezení platí ale, v závislosti na velikosti virtuálních počítačů, které používají službu Azure Media Services. Pokud zdrojový soubor je větší než 260 GB, vaše úloha se pravděpodobně nezdaří. Pokud máte 4 kB obsah, který je větší než limit 260 GB, kontaktujte nás na adrese amshelp@microsoft.com pro zmírnit na podporu vašeho scénáře.

<sup>2</sup> toto číslo zahrnuje zařazených do fronty, dokončené, aktivní a zrušené úlohy. Nezahrnuje odstraněné úlohy. 

Libovolný záznam úlohy ve vašem účtu, který je starší než 90 dní automaticky odstraní, i v případě, že celkový počet záznamů je nižší než maximální kvóta. 

<sup>3</sup> při použití vlastního [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies), by měly omezenou sadu zásad návrhu pro svůj účet Media Service a znovu je použít pro vaše StreamingLocators pokaždé, když se na stejné šifrování možnosti a protokoly jsou potřeba. Neměli byste vytvářet samostatnou zásadu streamování pro každý streamovací lokátor.

<sup>4</sup> účtů úložiště musí být ve stejném předplatném Azure.

<sup>5</sup> StreamingLocators nejsou určeny pro správu řízení přístupu na uživatele. Pokud chcete konkrétním uživatelům poskytnout různá přístupová práva, použijte řešení pro správu digitálních práv (DRM).

## <a name="support-ticket"></a>Lístek podpory

Pro prostředky, které nejsou pevné, můžete požádat o zvýšení kvóty být vyvolána, otevřením [lístek podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). V žádosti o změny požadované kvóty a scénáře použití a oblastech je potřeba zahrňte podrobné informace. <br/>**Nevytvářejte** další účty služby Azure Media Services ve snaze zajistit vyšší limity.

## <a name="next-steps"></a>Další postup

[Přehled](media-services-overview.md)

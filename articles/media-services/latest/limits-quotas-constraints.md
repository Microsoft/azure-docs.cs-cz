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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 285f4f4aad18943399511cbcac711933f23f7cb2
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004787"
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
| Události v reálném čase na účtu Azure Media Services |5|
| Účty Media Services v rámci jednoho předplatného | 25 (pevné) |
| Live výstupy ve spuštěném stavu na Livestream |3|
| Účty úložiště | 100<sup>(4)</sup> (fixní) |
| Koncové body streamování ve spuštěném stavu na účtu Azure Media Services|2|
| Zásady streamování | 100 <sup>(3)</sup> |
| Transformuje na účtu Azure Media Services | 100 (fixní)|
| Jedinečné lokátory streamování přidružené k prostředku najednou | 100<sup>(5)</sup> (fixní) |

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

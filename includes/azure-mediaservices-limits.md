---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: de3fd8dc0d45ea10e64af8e2258682a9e98639dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66116337"
---
>[!NOTE]
>Pro prostředky, které nejsou pevné otevřete lístek podpory a požádejte o zvýšení kvóty. Nevytvářejte další účty Azure Media Services ve snaze zajistit vyšší limity.

| Resource | Výchozí omezení | 
| --- | --- | 
| Účty Azure Media Services v rámci jednoho předplatného | 25 (pevné) |
| Každý účet Media Services jednotky rezervované pro média |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Úloh na účet Media Services | 50 000<sup>2</sup> |
| Zřetězené úkoly na jednu úlohu | 30 (pevné) |
| Prostředky na účtu Azure Media Services | 1 000 000|
| Prostředky na jeden úkol | 50 |
| Prostředky na jednu úlohu | 100 |
| Jedinečné lokátory přidružené k prostředku najednou | 5<sup>4</sup> |
| Živé kanály na účtu Azure Media Services |5|
| Programy v zastaveném stavu na jeden kanál |50|
| Programy ve spuštěném stavu na jeden kanál |3|
| Streamování koncové body, které se zastaví nebo spuštěné na účtu Azure Media Services|2|
| Jednotky streamování na jeden koncový bod streamování |10 |
| Účty úložiště | 1 000<sup>5</sup> (pevné) |
| Zásady | 1 000 000<sup>6</sup> |
| Velikost souboru| V některých scénářích platí omezení na maximální velikost souboru podporovaná při zpracování ve službě Media Services. <sup>7</sup> |

<sup>1</sup>Pokud změníte typ, třeba z S2 na S1, omezení maximální rezervované jednotky se obnoví.

<sup>2</sup>toto číslo zahrnuje zařazených do fronty, dokončené, aktivní a zrušené úlohy. Nezahrnuje odstraněné úlohy. Staré úlohy můžete odstranit pomocí **IJob.Delete** nebo **odstranit** požadavku HTTP.

Od 1. dubna 2017 libovolný záznam úlohy ve vašem účtu, který je starší než 90 dní automaticky odstraní, spolu s jeho související záznamy. Automatické odstranění dojde i v případě, že celkový počet záznamů je nižší než maximální kvóta. Chcete-li tyto informace archivovat, úloh a úkolů, použít kód popsaný v [Správa prostředků pomocí sady Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup>když provedete žádost o seznam úloh entity, maximálně 1 000 úloh se vrátí na žádost. Mějte přehled o všechny odeslané úlohy, horní nebo přeskočit dotazy, jak je popsáno v [možností dotazu systému OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup>lokátory nejsou určené pro správu řízení přístupu na uživatele. Chcete-li poskytnout různá přístupová práva pro jednotlivé uživatele, použijte řešení digital rights management (DRM). Další informace najdete v tématu [chránit obsah pomocí služby Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup>účtů úložiště musí být ve stejném předplatném Azure.

<sup>6</sup>je stanovený limit 1 000 000 různých zásad Media Services. Příkladem je pro zásady lokátoru nebo ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Pokud vždycky používáte stejné dny a přístupová oprávnění, použijte stejné ID zásad. Informace a příklad najdete v tématu [Správa prostředků pomocí sady Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup>maximální velikost podporovaná pro jeden objekt blob je aktuálně ve službě Azure Blob Storage až 5 TB. Další omezení platí v závislosti na velikosti virtuálních počítačů, které se používají ve službě Media Services. Omezení velikosti platí pro soubory, které nahrajete a také soubory, které se vygeneruje jako výsledek zpracování (kódování nebo analýza) Media Services. Pokud zdrojový soubor je větší než 260 GB, vaše úloha se pravděpodobně nezdaří. 

Následující tabulka uvádí omezení na média rezervované jednotky S1, S2 a S3. Pokud zdrojový soubor je větší než omezení v tabulce, vaše úlohy kódování se nezdaří. Pokud kódujete zdroje rozlišení 4K dlouhá doba trvání, budete muset použít S3 rezervovaných jednotek médií k zajištění výkonu potřeba. Pokud máte 4 kB obsah, který je větší než limit 260 GB S3 rezervovaných jednotek médií, kontaktujte nás na adrese amshelp@microsoft.com pro zmírnit na podporu vašeho scénáře.

|Typ jednotky rezervované pro média   |Maximální velikost vstupu (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

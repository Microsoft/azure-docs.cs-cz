---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2fe091a4ff0295ecadfd69ba3f2d4ca59e9612e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334740"
---
>[!NOTE]
>U prostředků, které nejsou opraveny, otevřete lístek podpory a požádejte o zvýšení kvót. Nevytvářejte další účty Azure Media Services ve snaze získat vyšší limity.

| Prostředek | Omezení | 
| --- | --- | 
| Účty Mediálních služeb Azure v jednom předplatném | 25 (pevné) |
| Rezervované jednotky médií na účet mediálních služeb |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Úlohy na účet mediálních služeb | 50 000<sup>2</sup> |
| Zřetězené úkoly na jednu úlohu | 30 (pevné) |
| Datové zdroje na účet mediálních služeb | 1 000 000|
| Prostředky na jeden úkol | 50 |
| Prostředky na jednu úlohu | 100 |
| Jedinečné lokátory přidružené k prostředku najednou | 5<sup>4.</sup> |
| Živé kanály na účet Mediálních služeb |5|
| Programy v zastaveném stavu na jeden kanál |50|
| Programy ve spuštěném stavu na jeden kanál |3|
| Streamování koncových bodů, které jsou zastavené nebo spuštěné podle účtu Služby Media Services|2|
| Jednotky streamování na jeden koncový bod streamování |10 |
| Účty úložiště | 1 000<sup>5</sup> (pevná) |
| Zásady | 1 000 000<sup>6</sup> |
| Velikost souboru| V některých případech je maximální velikost souboru podporovaná pro zpracování ve službě Media Services omezena. <sup>7.</sup> |

<sup>1.</sup> Pokud změníte typ, například z S2 na S1, maximální limity rezervované jednotky jsou resetovány.

<sup>2.</sup> Toto číslo zahrnuje úlohy zařazené do fronty, dokončené, aktivní a zrušené. Neobsahuje odstraněné úlohy. Staré úlohy můžete odstranit pomocí **iJob.Delete** nebo požadavku **DELETE** HTTP.

dubna 2017 se automaticky odstraní všechny záznamy o úloze ve vašem účtu starší než 90 dní spolu s přidruženými záznamy úkolů. K automatickému odstranění dochází i v případě, že celkový počet záznamů je nižší než maximální kvóta. Chcete-li archivovat informace o úloze a úloze, použijte kód popsaný v části Správa datových zdrojů pomocí sady [Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3.</sup> Při požadavku na seznam entit úlohy je vráceno maximálně 1 000 úloh na jeden požadavek. Chcete-li sledovat všechny odeslané úlohy, použijte horní nebo přeskočení dotazů, jak je popsáno v [možnostech systémových dotazů OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4.</sup> Lokátory nejsou určeny pro správu řízení přístupu pro jednotlivé uživatele. Chcete-li jednotlivým uživatelům udělit různá přístupová práva, použijte řešení správy digitálních práv (DRM). Další informace najdete [v tématu Ochrana obsahu pomocí Mediálních služeb Azure](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5.</sup> Účty úložiště musí být ze stejného předplatného Azure.

<sup>6.</sup> Pro různé zásady mediálních služeb je limit 1 000 000 zásad. Příkladem je zásada Lokátoru nebo ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Pokud vždy používáte stejné dny a přístupová oprávnění, použijte stejné ID zásad. Informace a příklad naleznete v tématu [Správa datových zdrojů pomocí sady Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7.</sup> Maximální velikost podporovaná pro jeden objekt blob je aktuálně až 5 TB v úložišti objektů blob Azure. Další omezení platí ve službách Media Services na základě velikostí virtuálních počítače, které služba používá. Omezení velikosti platí pro soubory, které nahrajete, a také na soubory, které se generují v důsledku zpracování mediálních služeb (kódování nebo analýza). Pokud je zdrojový soubor větší než 260 GB, úloha pravděpodobně selže. 

V následující tabulce jsou uvedena omezení rezervovaných médií Jednotek S1, S2 a S3. Pokud je zdrojový soubor větší než limity definované v tabulce, úloha kódování se nezdaří. Pokud kódujete zdroje rozlišení 4K s dlouhou dobou trvání, musíte k dosažení potřebného výkonu použít rezervované jednotky médií S3. Pokud máte obsah 4K, který je větší než limit 260 GB na rezervovaných jednotkách médií S3, otevřete lístek podpory.

|Typ rezervované jednotky média    |Maximální vstupní velikost (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

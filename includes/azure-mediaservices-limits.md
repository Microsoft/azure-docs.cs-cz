---
author: rothja
ms.service: media-services
ms.topic: include
ms.date: 09/16/2020
ms.author: jroth
ms.openlocfilehash: c315c071aeb36eea0bd1af84b1344cf2fef5f703
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329651"
---
>[!NOTE]
>U prostředků, které nejsou opraveny, otevřete lístek podpory, který se zeptá na zvýšení kvót. Při pokusu o získání vyšších limitů nevytvářejte další účty Azure Media Services.

| Prostředek | Omezení | 
| --- | --- | 
| Účty Azure Media Services v jednom předplatném | 25 (pevné) |
| Rezervované jednotky médií na účet Media Services |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Počet úloh na účet Media Services | 50 000<sup>2</sup> |
| Zřetězené úkoly na jednu úlohu | 30 (pevné) |
| Prostředky na účet Media Services | 1 000 000|
| Prostředky na jeden úkol | 50 |
| Prostředky na jednu úlohu | 100 |
| Jedinečné lokátory přidružené k prostředku najednou | 5<sup>4</sup> |
| Živé kanály na účet Media Services |5|
| Programy v zastaveném stavu na jeden kanál |50|
| Programy ve spuštěném stavu na jeden kanál |3|
| Koncové body streamování, které se zastavily nebo spouštějí na účet Media Services|2|
| Jednotky streamování na jeden koncový bod streamování |10 |
| Účty úložiště | 100<sup>5</sup> (fixní) |
| Zásady | 1 000 000<sup>6</sup> |
| Velikost souboru| V některých scénářích je omezení maximální velikosti souboru podporovanou pro zpracování v Media Services. <sup>7</sup> |

<sup>1</sup> Změníte-li typ, například z hodnoty S2 na S1, budou vynulovány maximální rezervované jednotky.

<sup>2</sup> . Toto číslo zahrnuje úlohy ve frontě, dokončených, aktivních a zrušených úlohách. Neobsahuje odstraněné úlohy. Staré úlohy můžete odstranit pomocí **IJob. Delete** nebo **odstraňte** požadavek HTTP.

Od 1. dubna 2017 se automaticky odstraní libovolný záznam úlohy ve vašem účtu, který je starší než 90 dní, spolu s přidruženými záznamy úkolů. Automatické odstranění probíhá i v případě, že celkový počet záznamů je nižší než maximální kvóta. K archivaci informací o úlohách a úlohách použijte kód popsaný v tématu [Správa prostředků pomocí sady Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup> . Když vytvoříte požadavek na výpis entit úloh, vrátí se maximálně 1 000 úloh na jednu žádost. Pokud chcete sledovat všechny odeslané úlohy, použijte dotazy TOP nebo Skip, jak je popsáno v tématu [Možnosti dotazování systému OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup> . Lokátory nejsou navržené pro správu řízení přístupu pro jednotlivé uživatele. K udělení různých přístupových práv jednotlivým uživatelům použijte řešení DRM (Správa digitálních práv). Další informace najdete v tématu [Ochrana obsahu pomocí Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup> . Účty úložiště musí být ze stejného předplatného Azure.

<sup>6</sup> K dispozici je omezení 1 000 000 zásad pro různé zásady Media Services. Příkladem je zásada lokátoru nebo ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Pokud vždycky používáte stejné dny a přístupová oprávnění, použijte stejné ID zásad. Informace a příklad najdete v tématu [Správa assetů pomocí sady Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup> Maximální velikost podporovaná pro jeden objekt BLOB je v Azure Blob Storage v současnosti až o 5 TB. V Media Services na základě velikostí virtuálních počítačů, které služba používá, platí další omezení. Omezení velikosti se vztahuje na soubory, které nahráváte, a také soubory, které se generují v důsledku zpracování Media Services (kódování nebo analýza). Pokud je zdrojový soubor větší než 260 GB, bude pravděpodobně selhání úlohy. 

V následující tabulce jsou uvedena omezení pro rezervované jednotky médií S1, S2 a S3. Pokud je zdrojový soubor větší než omezení definovaná v tabulce, vaše úloha kódování se nezdařila. Pokud kódujete zdroje 4K rozlišení dlouhé doby trvání, budete muset použít jednotky rezervované pro médium S3 k dosažení potřebného výkonu. Pokud jste 4K obsah, který je větší než limit 260 GB na rezervované jednotky médií S3, otevřete lístek podpory.

|Typ rezervované jednotky médií    |Maximální velikost vstupu (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

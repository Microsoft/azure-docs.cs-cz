---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 1e1316ef568cbc6409a8653022d9acff9837b59d
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279537"
---
>[!NOTE]
>U prostředků, které nejsou pevné, je možné prostřednictvím lístku podpory požádat o zvýšení kvóty. **Nevytvářejte** další účty služby Azure Media Services ve snaze zajistit vyšší limity.

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Účty Azure Media Services (AMS) v rámci jednoho předplatného | 25 (pevné) |
| Rezervované jednotky médií (RU) na jeden účet AMS |25 (S1)<br/>10 (S2, S3) <sup>(1)</sup> | 
| Úlohy na jeden účet AMS | 50 000<sup>(2)</sup> |
| Zřetězené úkoly na jednu úlohu | 30 (pevné) |
| Prostředky na jeden účet AMS | 1 000 000|
| Prostředky na jeden úkol | 50 |
| Prostředky na jednu úlohu | 100 |
| Jedinečné lokátory přidružené k prostředku najednou | 5<sup>(4)</sup> |
| Živé kanály na jeden účet AMS |5|
| Programy v zastaveném stavu na jeden kanál |50|
| Programy ve spuštěném stavu na jeden kanál |3|
| Koncové body streamování ve spuštěném stavu na jeden účet AMS|2|
| Jednotky streamování na jeden koncový bod streamování |10 |
| Účty úložiště | 1 000<sup>(5)</sup> (pevné) |
| Zásady | 1 000 000<sup>(6)</sup> |
| Velikost souboru| V některých scénářích platí omezení na maximální velikost souboru podporovaná při zpracování ve službě Media Services. <sup>7</sup> |
  
<sup>1</sup> Pokud změníte typ (třeba z S2 na S1), se resetují maximální limity RU.

<sup>2</sup> Tato hodnota zahrnuje úlohy zařazené do fronty a dokončené, aktivní a zrušené úlohy. Nezahrnuje odstraněné úlohy. K odstranění starých úloh můžete použít **IJob.Delete** nebo požadavek HTTP **DELETE**.

Od 1. dubna 2017 libovolný záznam úlohy ve vašem účtu, který je starší než 90 dní automaticky odstraní, spolu s jeho přidružené záznamy úkolů, i v případě, že celkový počet záznamů je nižší než maximální kvóta. Pokud potřebujete informace o úlohách/úkolech archivovat, můžete použít kód popsaný [tady](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup> při požadavku na seznam úloh entity, maximálně 1 000 úloh se vrátí na žádost. Pokud potřebujete sledovat všechny odeslané úlohy, můžete použít parametry top/skip popsané v tématu věnovaném [možnostem dotazů pro systém OData](https://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Lokátory nejsou určené ke správě řízení přístupu pro jednotlivé uživatele. Pokud chcete konkrétním uživatelům poskytnout různá přístupová práva, použijte řešení pro správu digitálních práv (DRM). Další informace najdete v [tomto](../articles/media-services/previous/media-services-content-protection-overview.md) oddílu.

<sup>5</sup> Účty úložiště musí být ze stejného předplatného Azure.

<sup>6</sup> Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Pokud vždycky používáte stejné dny, přístupová oprávnění atd., měli byste používat stejné ID zásad. Informace a příklad najdete v [tomto](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies) oddílu.

<sup>7</sup>Pokud nahráváte obsah pro prostředek služby Azure Media Services zpracovat ho pomocí některého z procesorů médií služby (to znamená, kodérů jako Media Encoder Standard a Media Encoderu Premium Workflow nebo analytických modulů jako je Face Detector), potom byste měli vědět, omezení na maximální velikosti podporované. 

Maximální velikost podporovaná pro jeden objekt blob je aktuálně ve službě Azure Blob Storage až 5 TB. Další omezení platí ale, v závislosti na velikosti virtuálních počítačů, které používají službu Azure Media Services. Následující tabulka uvádí omezení na všech rezervované jednotky médií (S1, S2 a S3.) Pokud zdrojový soubor je větší než omezení v tabulce, vaše úlohy kódování se nezdaří. Pokud jsou kódování zdroje rozlišení 4K dlouhá doba trvání, musíte používat rezervované jednotky médií S3 k zajištění výkonu potřeba. Pokud máte 4 kB obsah, který je větší než limit 260 GB na rezervované jednotky médií S3, kontaktujte nás na adrese amshelp@microsoft.com pro zmírnit na podporu vašeho scénáře.

| Typ rezervovaných jednotek médií | Maximální velikost vstupu (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|

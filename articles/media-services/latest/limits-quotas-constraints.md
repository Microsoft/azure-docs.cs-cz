---
title: Kvóty a omezení v Azure Media Services V3 | Microsoft Docs
description: Toto téma popisuje kvóty a omezení v Azure Media Services V3
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 2a530d6a1a447ccde448259623da6faeaa6a4d72
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598367"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvóty a omezení v Azure Media Services V3

Tento článek popisuje kvóty a omezení v Azure Media Services V3.

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Prostředky na účet Azure Media Services | 1 000 000|
| Filtry dynamických manifestů|100|
| JobInputs na úlohu | 50 (fixní)|
| JobOutputs na úlohu | 20 (fixní) |
| TransformOutputs v transformaci | 20 (fixní) |
| Soubory na JobInput|10 (pevné)|
| Velikost souboru| V některých scénářích je omezení maximální velikosti souboru podporovanou pro zpracování v Media Services. <sup>první</sup> |
| Počet úloh na účet Media Services | 500 000 <sup>(2)</sup> (fixní)|
| Živé události na účet Media Services |5|
| Účty Media Services v jednom předplatném | 25 (pevné) |
| Živé výstupy na živou událost |3 <sup>(3)</sup> |
| Maximální doba trvání živého výstupu | 25 hodin |
| Účty úložiště | 100<sup>(4)</sup> (fixní) |
| Koncové body streamování (zastavené nebo spuštěné) na účet Media Services|2 (pevné)|
| Zásady streamování | 100 <sup>(5)</sup> |
| Transformuje účet za Media Services. | 100 (fixní)|
| Jedinečné Lokátory streamování přidružené k prostředku v jednom okamžiku | 100<sup>(6)</sup> (fixní) |
| Možnosti pro zásady klíče obsahu |30 | 
| Licence za měsíc pro každý typ DRM při Media Services Služba doručení klíčů na účet|1 000 000|

<sup>1</sup> maximální velikost podporovaná pro jeden objekt BLOB je v Azure Blob Storage v současnosti až o 5 TB. V Media Services na základě velikostí virtuálních počítačů, které služba používá, platí další omezení. Omezení velikosti se vztahuje na soubory, které nahráváte, a také soubory, které se generují v důsledku zpracování Media Services (kódování nebo analýza). Pokud je zdrojový soubor větší než 260 GB, bude pravděpodobně selhání úlohy. 

V následující tabulce jsou uvedena omezení pro rezervované jednotky médií S1, S2 a S3. Pokud je zdrojový soubor větší než omezení definovaná v tabulce, vaše úloha kódování se nezdařila. Pokud kódujete zdroje 4K rozlišení dlouhé doby trvání, budete muset použít jednotky rezervované pro médium S3 k dosažení potřebného výkonu. Pokud jste 4K obsah, který je větší než limit 260 GB na rezervované jednotky médií S3, otevřete lístek podpory.

|Typ rezervované jednotky médií   |Maximální velikost vstupu (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> toto číslo zahrnuje úlohy ve frontě, dokončených, aktivních a zrušených úlohách. Nezahrnuje odstraněné úlohy. 

Všechny záznamy úloh ve vašem účtu starším než 90 dnů budou automaticky odstraněny, i když celkový počet záznamů je nižší než maximální kvóta. 

<sup>3</sup> živé výstupy začínají při vytváření a při odstranění se zastaví.

<sup>4</sup> účty úložiště musí být ze stejného předplatného Azure.

<sup>5</sup> Pokud používáte vlastní [zásady streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies), měli byste navrhnout určitou sadu takových zásad pro svůj účet Media Service a znovu je použít pro své StreamingLocators, kdykoli budete potřebovat stejné možnosti šifrování a protokoly. Pro každý Lokátor streamování byste neměli vytvářet nové zásady streamování.

<sup>6</sup> lokátorů streamování není navržené pro správu řízení přístupu pro jednotlivé uživatele. Pokud chcete konkrétním uživatelům poskytnout různá přístupová práva, použijte řešení pro správu digitálních práv (DRM).

## <a name="support-ticket"></a>Lístek podpory

U nevyřešených prostředků můžete požádat o aktivaci kvót otevřením [lístku podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Do žádosti uveďte podrobné informace o požadovaných změnách kvóty, případech použití a požadovaných oblastech. <br/>**Nevytvářejte** další účty služby Azure Media Services ve snaze zajistit vyšší limity.

## <a name="next-steps"></a>Další kroky

[Přehled](media-services-overview.md)

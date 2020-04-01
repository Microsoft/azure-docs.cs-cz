---
title: Kvóty a omezení ve službě Azure Media Services v3 | Dokumenty společnosti Microsoft
description: Toto téma popisuje kvóty a omezení ve službě Microsoft Azure Media Services v3.
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
ms.openlocfilehash: 514c1466bc1a686adfc3e07e1f19bd566e979dc1
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420879"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvóty a omezení ve službě Azure Media Services v3

Tento článek popisuje kvóty a omezení ve službě Azure Media Services v3.

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Prostředky na účet Azure Media Services | 1 000 000|
| Filtry dynamických manifestů|100|
| Vstupy úloh na úlohu | 50 (pevná)|
| Výstupy úloh na úlohu | 20 (pevná) |
| Transformace výstupů v transformaci | 20 (pevná) |
| Soubory na vstup úlohy|10 (pevná)|
| Velikost souboru| V některých případech je maximální velikost souboru podporovaná pro zpracování ve službě Media Services omezena. <sup>(1)</sup> |
| Úlohy na účet mediálních služeb | 500 000 <sup>(2)</sup> (pevná)|
| Živé události na účet Media Services |5|
| Účty mediálních služeb v jednom předplatném | 25 (pevné) |
| Živé výstupy na živou událost |3 <sup>(3)</sup> |
| Maximální doba trvání živého výstupu | 25 hodin |
| Účty úložiště | 100<sup>(4)</sup> (pevná) |
| Streamování koncových bodů (zastaveno nebo spuštěno) na účet Mediální služby|2 (pevná)|
| Zásady streamování | 100 <sup>(5)</sup> |
| Transformace na účet mediálních služeb | 100 (pevná)|
| Jedinečné lokátory streamování přidružené k datovému zdroji najednou | 100<sup>(6)</sup> (pevná) |
| Možnosti podle zásad klíče obsahu |30 | 
| Licence za měsíc pro každý typ DRM v doručovací službě media services na účet|1 000 000|

<sup>1</sup> Maximální velikost podporovaná pro jeden objekt blob je aktuálně až 5 TB v úložišti objektů blob Azure. Další omezení platí ve službách Media Services na základě velikostí virtuálních počítače, které služba používá. Omezení velikosti platí pro soubory, které nahrajete, a také na soubory, které se generují v důsledku zpracování mediálních služeb (kódování nebo analýza). Pokud je zdrojový soubor větší než 260 GB, úloha pravděpodobně selže. 

V následující tabulce jsou uvedena omezení rezervovaných médií Jednotek S1, S2 a S3. Pokud je zdrojový soubor větší než limity definované v tabulce, úloha kódování se nezdaří. Pokud kódujete zdroje rozlišení 4K s dlouhou dobou trvání, musíte k dosažení potřebného výkonu použít rezervované jednotky médií S3. Pokud máte obsah 4K, který je větší než limit 260 GB na rezervovaných jednotkách médií S3, otevřete lístek podpory.

|Typ rezervované jednotky média   |Maximální vstupní velikost (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> Toto číslo zahrnuje úlohy zařazené do fronty, dokončené, aktivní a zrušené. Nezahrnuje odstraněné úlohy. 

Všechny záznamy úloh y ve vašem účtu starší než 90 dní budou automaticky odstraněny, a to i v případě, že celkový počet záznamů je nižší než maximální kvóta. 

<sup>3</sup> Živé výstupy se spustí při vytváření a zastaví se při odstranění.

<sup>4</sup> Účty úložiště musí být ze stejného předplatného Azure.

<sup>5</sup> Při použití [vlastních zásad streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies)byste měli pro svůj účet služby Media Service navrhnout omezenou sadu těchto zásad a znovu je použít pro streamovací lokátory, kdykoli jsou potřeba stejné možnosti šifrování a protokoly. Pro každý lokátor streamování byste neměli vytvářet nové zásady streamování.

<sup>6</sup> Lokátory streamování nejsou určeny pro správu řízení přístupu pro jednotlivé uživatele. Pokud chcete konkrétním uživatelům poskytnout různá přístupová práva, použijte řešení pro správu digitálních práv (DRM).

## <a name="support-ticket"></a>Lístek podpory

U prostředků, které nejsou pevné, můžete požádat o zvýšení kvót otevřením [lístku podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Do požadavku uveďte podrobné informace o požadovaných změnách kvót, scénářích případu použití a požadovaných oblastech. <br/>**Nevytvářejte** další účty služby Azure Media Services ve snaze zajistit vyšší limity.

## <a name="next-steps"></a>Další kroky

[Přehled](media-services-overview.md)

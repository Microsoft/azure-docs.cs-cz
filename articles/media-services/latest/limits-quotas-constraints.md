---
title: Kvóty a omezení v Azure Media Services v3 | Microsoft Docs
description: Toto téma popisuje kvóty a omezení v Azure Media Services v3
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 21fc80d7cb274197ae75d2fd5524e76e1e6288d9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvóty a omezení v Azure Media Services v3

Toto téma popisuje kvóty a omezení v Azure Media Services v3.

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Prostředky na účtu Azure Media Services | 1 000 000|
| JobInputs na úlohu | 100 |
| JobOutputs na úlohu | 30 (pevné) |
| Velikost souboru| V některých případech je limit na maximální velikost souboru pro zpracování ve službě Media Services podporována. <sup>(1)</sup> |
| Úloh na účtu Media Services | 50 000<sup>(2)</sup> |
| LiveEvents za účtu Media Services |5|
| Účty služby Media Services v rámci jednoho předplatného | 25 (pevné) |
| StreamingPolicies | 1 000 000<sup>(3)</sup> |
| LiveOutputs v běžícím stavu za LiveEvent |3|
| LiveOutputs v zastaveném stavu za LiveEvent |50|
| Účty úložiště | 1000<sup>(4)</sup> (pevná) |
| Koncové body streamování v běžícím stavu na účtu Media Services|2|
| Transformuje na účtu Media Services | 20 |
| Jedinečné StreamingLocators přidružený prostředek v jednom okamžiku | 20<sup>(5)</sup> |
  
<sup>1</sup>maximální velikost podporovaná pro jeden objekt blob je momentálně až 5 TB ve službě Azure Blob Storage. Další omezení však použít v závislosti na velikosti virtuálních počítačů, které používají službu Azure Media Services. Pokud váš zdrojový soubor je větší než 260 GB úlohu se pravděpodobně nezdaří. Pokud máte 4K obsahu, který je větší než limit 260 GB, kontaktujte nás na adrese amshelp@microsoft.com pro potenciální jejich zmírnění pro podporu váš scénář.

<sup>2</sup> toto číslo zahrnuje zařazených do fronty, dokončení, aktivní a zrušené úlohy. Neobsahuje odstraněné úlohy. 

Záznam všechny úlohy ve vašem účtu, který je starší než 90 dní se automaticky odstraní, i v případě, že celkový počet záznamů je nižší než maximální kvótu. 

<sup>3</sup> existuje omezení 1 000 000 položek StreamingPolicy pro různé zásady Media Services (například pro zásady StreamingLocator nebo ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Pokud vždycky používáte stejné dny, přístupová oprávnění atd., měli byste používat stejné ID zásad. 

<sup>4</sup> účtů úložiště musí být ze stejného předplatného Azure.

<sup>5</sup> StreamingLocators nejsou určených pro správu řízení přístupu na uživatele. Pokud chcete konkrétním uživatelům poskytnout různá přístupová práva, použijte řešení pro správu digitálních práv (DRM).

## <a name="support-ticket"></a>Lístku podpory

Pro prostředky, které nejsou opravit, můžete požádat o kvóty, které se vyvolá, otevřením [lístek podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Uveďte podrobné informace v požadavku na změny požadované kvótu, případ použití scénáře a požadované oblasti. <br/>**Nevytvářejte** další účty služby Azure Media Services ve snaze zajistit vyšší limity.

## <a name="next-steps"></a>Další postup

[Přehled](media-services-overview.md)

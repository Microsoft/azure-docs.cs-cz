---
title: Statistiky v reálném čase v Azure CDN | Dokumentace Microsoftu
description: Statistiky v reálném čase poskytuje v reálném čase data o výkonu Azure CDN při doručování obsahu vašim klientům.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: eb20630533735fb46ea7743be75448329281938a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60334563"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Statistiky v reálném čase v Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled
Tento dokument popisuje statistiky v reálném čase v Microsoft Azure CDN.  Tato funkce poskytuje data v reálném čase, jako je například šířky pásma, stavy mezipaměti a souběžná připojení k vašemu profilu CDN při doručování obsahu vašim klientům. To umožňuje nepřetržité monitorování stavu služby v okamžiku, včetně událostí pro uvedení do provozu.

Jsou k dispozici následující grafy:

* [Bandwidth](#bandwidth)
* [Stavové kódy](#status-codes)
* [Stavy mezipaměti](#cache-statuses)
* [Připojení](#connections)

## <a name="accessing-real-time-stats"></a>Přístup k statistiky v reálném čase
1. V [webu Azure Portal](https://portal.azure.com), přejděte na svůj profil CDN.
   
    ![Okno profilu CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. Z okna profil CDN, klikněte na tlačítko **spravovat** tlačítko.
   
    ![Tlačítko Spravovat okno profilu CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
3. Najeďte myší **Analytics** kartu a pak najeďte myší **statistiky v reálném čase** Kontextová nabídka.  Klikněte na **velkého objektu HTTP**.
   
    ![Portál pro správu CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Se zobrazí v grafech statistiky v reálném čase.

Jednotlivé grafy zobrazuje statistiky v reálném čase pro vybraný časový rozsah, spouští se při načtení stránky.  V grafech automaticky aktualizovat každých několik sekund.  **Aktualizovat graf** tlačítko, pokud jsou k dispozici, se vymažou grafu, po jejímž uplynutí se zobrazí jenom vybraná data.

## <a name="bandwidth"></a>Šířka pásma
![Graf šířky pásma](./media/cdn-real-time-stats/cdn-bandwidth.png)

**Šířky pásma** grafu zobrazí šířka pásma použitá pro aktuální platformu za vybrané časové období. Šedá část grafu označuje využití šířky pásma. Přesné množství šířky pásma, které jsou právě používány se zobrazí přímo pod spojnicový graf.

## <a name="status-codes"></a>Stavové kódy
![Stavový kód grafu](./media/cdn-real-time-stats/cdn-status-codes.png)

**Stavové kódy** grafu určuje, jak často se provádí některé kódy odpovědi HTTP přes vybraný časový rozsah.

> [!TIP]
> Popis jednotlivých možností kód stavu HTTP najdete v tématu [stavové kódy HTTP Azure CDN](/previous-versions/azure/mt759238(v=azure.100)).
> 
> 

Přímo nad grafu se zobrazí seznam stavových kódů HTTP. Tento seznam uvádí všech kódů, které mohou být součástí spojnicový graf a aktuální počet opakování za sekundu pro tento kód stavu. Ve výchozím nastavení zobrazí se řádek pro každou z těchto stavů v grafu. Můžete však pouze monitorovat stavové kódy, které mají speciální význam pro vaši konfiguraci CDN. Zkontrolujte požadované stavové kódy a zrušte zaškrtnutí všech ostatních možností a klikněte na **aktualizovat graf**. 

Můžete dočasně skrýt zaznamenaných dat pro konkrétní stavový kód.  V legendě přímo pod grafem klikněte na stavový kód, který chcete skrýt. Stavový kód bude okamžitě skrytá z grafu. Opětovným kliknutím Tento stavový kód způsobí, že tuto možnost, který se má zobrazit znovu.

## <a name="cache-statuses"></a>Stavy mezipaměti
![Graf stavy mezipaměti](./media/cdn-real-time-stats/cdn-cache-status.png)

**Stavy mezipaměti** grafu určuje, jak často jsou určité typy stavy mezipaměti ke kterým dochází za vybrané časové období. 

> [!TIP]
> Popis jednotlivých možností kód stavu mezipaměti najdete v tématu [Azure CDN mezipaměti stavové kódy](/previous-versions/azure/mt759237(v=azure.100)).
> 
> 

Přímo nad grafu se zobrazí seznam stavových kódů mezipaměti. Tento seznam uvádí všech kódů, které mohou být součástí spojnicový graf a aktuální počet opakování za sekundu pro tento kód stavu. Ve výchozím nastavení zobrazí se řádek pro každou z těchto stavů v grafu. Můžete však pouze monitorovat stavové kódy, které mají speciální význam pro vaši konfiguraci CDN. Zkontrolujte požadované stavové kódy a zrušte zaškrtnutí všech ostatních možností a klikněte na **aktualizovat graf**. 

Můžete dočasně skrýt zaznamenaných dat pro konkrétní stavový kód.  V legendě přímo pod grafem klikněte na stavový kód, který chcete skrýt. Stavový kód bude okamžitě skrytá z grafu. Opětovným kliknutím Tento stavový kód způsobí, že tuto možnost, který se má zobrazit znovu.

## <a name="connections"></a>Připojení
![Připojení grafu](./media/cdn-real-time-stats/cdn-connections.png)

Tento graf označuje, kolik připojení byly vytvořeny na hraničních serverech. Každý požadavek pro určitý prostředek, který se předá prostřednictvím našich CDN výsledků v připojení.

## <a name="next-steps"></a>Další kroky
* Dostanete oznámení zprávou [výstrahy v reálném čase v Azure CDN](cdn-real-time-alerts.md)
* Hlubší vhled díky [Rozšířené sestavy HTTP](cdn-advanced-http-reports.md)
* Analýza [vzorů využití](cdn-analyze-usage-patterns.md)


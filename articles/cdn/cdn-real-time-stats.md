---
title: Statistiky v reálném čase v Azure CDN | Dokumenty společnosti Microsoft
description: Statistiky v reálném čase poskytují data v reálném čase o výkonu Azure CDN při doručování obsahu svým klientům.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d56007e5a196a0857f3b69ac51f5e3b5a88c4f6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593494"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Statistiky v reálném čase v microsoft azure cdn
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled
Tento dokument vysvětluje statistiky v reálném čase v microsoft azure cdn.  Tato funkce poskytuje data v reálném čase, jako je šířka pásma, stavy mezipaměti a souběžná připojení k profilu CDN při doručování obsahu klientům. To umožňuje nepřetržité sledování stavu vaší služby kdykoli, včetně živých událostí.

K dispozici jsou následující grafy:

* [Šířka pásma](#bandwidth)
* [Stavové kódy](#status-codes)
* [Stavy mezipaměti](#cache-statuses)
* [Připojení](#connections)

## <a name="accessing-real-time-stats"></a>Přístup ke statistikám v reálném čase
1. Na [webu Azure Portal](https://portal.azure.com)přejděte na svůj profil CDN.
   
    ![CdN profilový nůž](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. V okně profilu CDN klepněte na tlačítko **Spravovat.**
   
    ![Tlačítko pro správu okna profilu CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.
3. Najeďte na kartu **Analytics** a najeďte na informační **rámeček Statistiky v reálném čase.**  Klikněte na **HTTP Velký objekt**.
   
    ![Portál pro správu CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Zobrazí se grafy statistik v reálném čase.

Každý z grafů zobrazuje statistiky v reálném čase pro vybraný časový rozsah, počínaje načtením stránky.  Grafy se aktualizují automaticky každých několik sekund.  Tlačítko **Aktualizovat graf,** pokud je k dispozici, vymaže graf, po kterém se zobrazí pouze vybraná data.

## <a name="bandwidth"></a>Šířka pásma
![Graf šířky pásma](./media/cdn-real-time-stats/cdn-bandwidth.png)

Graf **šířky pásma** zobrazuje šířku pásma použitou pro aktuální platformu ve zvoleném časovém rozpětí. Stínovaná část grafu označuje využití šířky pásma. Přesná šířka pásma, která je právě používána, je zobrazena přímo pod čárkovým grafem.

## <a name="status-codes"></a>Stavové kódy
![Graf stavového kódu](./media/cdn-real-time-stats/cdn-status-codes.png)

Graf **Stavových kódů** označuje, jak často se ve vybraném časovém rozpětí vyskytují určité kódy odpovědí HTTP.

> [!TIP]
> Popis jednotlivých možností stavového kódu HTTP najdete v [tématu Azure CDN HTTP Status Codes](/previous-versions/azure/mt759238(v=azure.100)).
> 
> 

Seznam stavových kódů HTTP se zobrazí přímo nad grafem. Tento seznam označuje každý stavový kód, který může být zahrnut do spojového grafu, a aktuální počet výskytů za sekundu pro tento stavový kód. Ve výchozím nastavení se pro každý z těchto stavových kódů v grafu zobrazí řádek. Můžete však sledovat pouze stavové kódy, které mají zvláštní význam pro konfiguraci CDN. Chcete-li to provést, zkontrolujte požadované stavové kódy a zrušte zaškrtnutí všech ostatních možností a klepněte na tlačítko **Aktualizovat graf**. 

Protokolovaná data můžete dočasně skrýt pro určitý stavový kód.  Z legendy přímo pod grafem klikněte na stavový kód, který chcete skrýt. Stavový kód bude okamžitě skryt v grafu. Opětovným kliknutím na tento stavový kód se tato možnost znovu zobrazí.

## <a name="cache-statuses"></a>Stavy mezipaměti
![Graf Stavů mezipaměti](./media/cdn-real-time-stats/cdn-cache-status.png)

Graf **Stavy mezipaměti** označuje, jak často se ve vybraném časovém rozpětí vyskytují určité typy stavů mezipaměti. 

> [!TIP]
> Popis jednotlivých možností kódu stavu mezipaměti najdete v [tématu Azure CDN cache Stavové kódy](/previous-versions/azure/mt759237(v=azure.100)).
> 
> 

Seznam stavových kódů mezipaměti se zobrazí přímo nad grafem. Tento seznam označuje každý stavový kód, který může být zahrnut do spojového grafu, a aktuální počet výskytů za sekundu pro tento stavový kód. Ve výchozím nastavení se pro každý z těchto stavových kódů v grafu zobrazí řádek. Můžete však sledovat pouze stavové kódy, které mají zvláštní význam pro konfiguraci CDN. Chcete-li to provést, zkontrolujte požadované stavové kódy a zrušte zaškrtnutí všech ostatních možností a klepněte na tlačítko **Aktualizovat graf**. 

Protokolovaná data můžete dočasně skrýt pro určitý stavový kód.  Z legendy přímo pod grafem klikněte na stavový kód, který chcete skrýt. Stavový kód bude okamžitě skryt v grafu. Opětovným kliknutím na tento stavový kód se tato možnost znovu zobrazí.

## <a name="connections"></a>Připojení
![Graf připojení](./media/cdn-real-time-stats/cdn-connections.png)

Tento graf ukazuje, kolik připojení bylo vytvořeno k hraničním serverům. Každá žádost o prostředek, který prochází naší CDN má za následek připojení.

## <a name="next-steps"></a>Další kroky
* Upozornění v [reálném čase v Azure CDN](cdn-real-time-alerts.md)
* Ponořte se hlouběji pomocí [pokročilých zpráv HTTP](cdn-advanced-http-reports.md)
* Analýza [vzorců využití](cdn-analyze-usage-patterns.md)


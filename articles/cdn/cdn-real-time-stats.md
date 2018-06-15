---
title: Statistiky v reálném čase v Azure CDN | Microsoft Docs
description: Statistiky v reálném čase poskytují v reálném čase údaje o výkonu Azure CDN při doručování obsahu vašim klientům.
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
ms.openlocfilehash: e9b9522de6b2c54dc794b00100ffe358296ecfdd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842878"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Statistiky v reálném čase v Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled
Tento dokument popisuje statistiky v reálném čase v Microsoft Azure CDN.  Tato funkce poskytuje data v reálném čase, například šířky pásma, mezipaměti stavy a souběžných připojení na svůj profil CDN při doručování obsahu vašim klientům. To umožňuje nepřetržité monitorování stavu služby v každém okamžiku včetně přejděte živé události.

K dispozici jsou následující grafy:

* [Šířka pásma](#bandwidth)
* [Stavové kódy](#status-codes)
* [Stavy mezipaměti](#cache-statuses)
* [Připojení](#connections)

## <a name="accessing-real-time-stats"></a>Přístup k statistiky v reálném čase
1. V [portálu Azure](https://portal.azure.com), přejděte na svůj profil CDN.
   
    ![Okno profil CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. Okno profil CDN, klikněte **spravovat** tlačítko.
   
    ![Tlačítko Spravovat okno profil CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
3. Najeďte myší **Analytics** a potom přejděte myší **statistiky v reálném čase** plovoucím panelem.  Klikněte na **velkého objektu HTTP**.
   
    ![Portál pro správu CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Se zobrazí v grafech statistiky v reálném čase.

Každý z v grafech zobrazuje v reálném čase statistiku pro vybrané časové rozpětí, spuštění, pokud stránka načte.  V grafech aktualizovat automaticky každých několik sekund.  **Aktualizovat graf** tlačítko, pokud existuje, dojde k vymazání grafu, po které se zobrazí pouze vybraná data.

## <a name="bandwidth"></a>Šířka pásma
![Graf šířky pásma](./media/cdn-real-time-stats/cdn-bandwidth.png)

**Šířky pásma** grafu zobrazí šířku pásma, které používají pro aktuální platforma pro vybrané časové rozpětí. Šedou barvou část grafu označuje využití šířky pásma. Zobrazí se přesný šířku pásma, které jsou právě používány přímo pod spojnicový graf.

## <a name="status-codes"></a>Stavové kódy
![Graf kódu stavu](./media/cdn-real-time-stats/cdn-status-codes.png)

**Stavové kódy** grafu určuje, jak často se provádí některé kódy odpovědi HTTP přes vybrané časové rozpětí.

> [!TIP]
> Popis jednotlivých možností kód stavu HTTP najdete v tématu [stavové kódy HTTP CDN Azure](https://msdn.microsoft.com/library/mt759238.aspx).
> 
> 

Zobrazí se seznam stavové kódy HTTP přímo nad grafu. Tento seznam označuje každý stavový kód, který může být součástí spojnicový graf a aktuální počet výskytů za sekundu pro tento stavový kód. Ve výchozím nastavení zobrazí se řádek pro každou z těchto stavové kódy v grafu. Však můžete monitorovat pouze stavové kódy, které mají zvláštní význam pro konfiguraci CDN. K tomu, zkontrolujte požadované stavové kódy a zrušte zaškrtnutí všech ostatních možností a pak klikněte na **aktualizovat graf**. 

Můžete dočasně skrýt data protokolu pro konkrétní stavový kód.  V legendě přímo pod grafem klikněte na kód stavu, které chcete skrýt. Stavový kód bude okamžitě skrytá z grafu. Kliknutím na tento stavový kód znovu způsobí, že tato možnost, který se má zobrazit znovu.

## <a name="cache-statuses"></a>Stavy mezipaměti
![Stavy graf do mezipaměti](./media/cdn-real-time-stats/cdn-cache-status.png)

**Mezipaměti stavy** grafu určuje, jak často se provádí některé typy stavů mezipaměti přes vybrané časové rozpětí. 

> [!TIP]
> Popis jednotlivých možností kód stavu mezipaměti najdete v tématu [Azure CDN mezipaměti stavové kódy](https://msdn.microsoft.com/library/mt759237.aspx).
> 
> 

Zobrazí se seznam mezipaměti stavové kódy přímo nad grafu. Tento seznam označuje každý stavový kód, který může být součástí spojnicový graf a aktuální počet výskytů za sekundu pro tento stavový kód. Ve výchozím nastavení zobrazí se řádek pro každou z těchto stavové kódy v grafu. Však můžete monitorovat pouze stavové kódy, které mají zvláštní význam pro konfiguraci CDN. K tomu, zkontrolujte požadované stavové kódy a zrušte zaškrtnutí všech ostatních možností a pak klikněte na **aktualizovat graf**. 

Můžete dočasně skrýt data protokolu pro konkrétní stavový kód.  V legendě přímo pod grafem klikněte na kód stavu, které chcete skrýt. Stavový kód bude okamžitě skrytá z grafu. Kliknutím na tento stavový kód znovu způsobí, že tato možnost, který se má zobrazit znovu.

## <a name="connections"></a>Připojení
![Graf připojení](./media/cdn-real-time-stats/cdn-connections.png)

Tento graf Určuje, kolik připojení byly vytvořeny pro vaše servery edge. Každý požadavek pro určitý prostředek, který prochází výsledky našich CDN v připojení.

## <a name="next-steps"></a>Další kroky
* Upozorňování pomocí [výstrah v reálném čase v Azure CDN](cdn-real-time-alerts.md)
* Dig hlubší s [Rozšířené sestavy HTTP](cdn-advanced-http-reports.md)
* Analýza [vzorce používání](cdn-analyze-usage-patterns.md)


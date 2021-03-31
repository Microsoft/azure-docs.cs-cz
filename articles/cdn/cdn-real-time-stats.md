---
title: Statistiky v reálném čase v Azure CDN | Microsoft Docs
description: Statistika v reálném čase poskytuje údaje o výkonu Azure CDN při doručování obsahu vašim klientům v reálném čase.
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
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 3af2e849aa6658e539b0b5bdbda4428cc28e5ce5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84887229"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Statistiky v reálném čase v Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled
Tento dokument vysvětluje statistiku Microsoft Azure CDN v reálném čase.  Tato funkce poskytuje data v reálném čase, jako je šířka pásma, stavy mezipaměti a souběžná připojení k vašemu profilu CDN při doručování obsahu vašim klientům. To umožňuje nepřetržité monitorování stavu služby kdykoli, včetně událostí v reálném čase.

K dispozici jsou následující grafy:

* [Šířka pásma](#bandwidth)
* [Stavové kódy](#status-codes)
* [Stavy mezipaměti](#cache-statuses)
* [Připojení](#connections)

## <a name="accessing-real-time-stats"></a>Přístup k statistikám v reálném čase
1. Na webu [Azure Portal](https://portal.azure.com)přejděte do svého profilu CDN.
   
    ![Okno profilu CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. V okně profilu CDN klikněte na tlačítko **Spravovat** .
   
    ![Tlačítko Spravovat okno profilu CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.
3. Najeďte myší na kartu **Analýza** a potom najeďte myší na informační rámeček **statistiky v reálném čase** .  Klikněte na **large object http**.
   
    ![Portál pro správu CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Zobrazí se grafy statistik v reálném čase.

Každé z grafů zobrazuje statistiku v reálném čase pro vybrané časové období, které začíná při načtení stránky.  Grafy se automaticky aktualizují každých několik sekund.  Tlačítko **Aktualizovat graf** , pokud je k dispozici, vymaže graf, po kterém se zobrazí pouze vybraná data.

## <a name="bandwidth"></a>Šířka pásma
![Graf šířky pásma](./media/cdn-real-time-stats/cdn-bandwidth.png)

Graf **šířky pásma** zobrazuje množství šířky pásma používaného pro aktuální platformu v rámci vybraného časového rozsahu. Šedá část grafu indikuje použití šířky pásma. Přes spojnicový graf se zobrazuje přesná velikost aktuálně využité šířky pásma přímo.

## <a name="status-codes"></a>Stavové kódy
![Graf stavového kódu](./media/cdn-real-time-stats/cdn-status-codes.png)

Graf **stavových kódů** indikuje, jak často se ve vybraném časovém intervalu vyskytují určité kódy odpovědí HTTP.

> [!TIP]
> Popis jednotlivých možností kódu stavu HTTP najdete v tématu [Azure CDN stavových kódů http](/previous-versions/azure/mt759238(v=azure.100)).
> 
> 

Zobrazí se seznam stavových kódů HTTP přímo nad grafem. Tento seznam uvádí všechny stavové kódy, které mohou být zahrnuty do spojnicového grafu a aktuální počet výskytů za sekundu pro daný stavový kód. Ve výchozím nastavení se pro každý z těchto stavových kódů v grafu zobrazí řádek. Můžete se ale rozhodnout jenom monitorovat stavové kódy, které mají zvláštní význam pro vaši konfiguraci CDN. Provedete to tak, že zkontrolujete požadované stavové kódy a vymažete všechny ostatní možnosti a potom kliknete na **Aktualizovat graf**. 

Můžete dočasně skrýt data protokolu pro určitý stavový kód.  Z legendy přímo pod grafem klikněte na stavový kód, který chcete skrýt. Stavový kód bude od grafu okamžitě skrytý. Opětovným kliknutím na Tento stavový kód se tato možnost zobrazí znovu.

## <a name="cache-statuses"></a>Stavy mezipaměti
![Graf stavu mezipaměti](./media/cdn-real-time-stats/cdn-cache-status.png)

Graf **stavů mezipaměti** indikuje, jak často se ve vybraném časovém intervalu vyskytují určité typy stavů mezipaměti. 

> [!TIP]
> Popis jednotlivých možností kódu stavu mezipaměti najdete v tématu [Azure CDN stavových kódů mezipaměti](/previous-versions/azure/mt759237(v=azure.100)).
> 
> 

Seznam stavových kódů mezipaměti se zobrazuje přímo nad grafem. Tento seznam uvádí všechny stavové kódy, které mohou být zahrnuty do spojnicového grafu a aktuální počet výskytů za sekundu pro daný stavový kód. Ve výchozím nastavení se pro každý z těchto stavových kódů v grafu zobrazí řádek. Můžete se ale rozhodnout jenom monitorovat stavové kódy, které mají zvláštní význam pro vaši konfiguraci CDN. Provedete to tak, že zkontrolujete požadované stavové kódy a vymažete všechny ostatní možnosti a potom kliknete na **Aktualizovat graf**. 

Můžete dočasně skrýt data protokolu pro určitý stavový kód.  Z legendy přímo pod grafem klikněte na stavový kód, který chcete skrýt. Stavový kód bude od grafu okamžitě skrytý. Opětovným kliknutím na Tento stavový kód se tato možnost zobrazí znovu.

## <a name="connections"></a>Připojení
![Graf připojení](./media/cdn-real-time-stats/cdn-connections.png)

Tento graf indikuje, kolik připojení k vašim hraničním serverům bylo navázáno. Každý požadavek na prostředek, který projde naším CDN, má za následek připojení.

## <a name="next-steps"></a>Další kroky
* [Oznámení o výstrahách v reálném čase v Azure CDN](cdn-real-time-alerts.md)
* Dig hlouběji pomocí [pokročilých sestav http](cdn-advanced-http-reports.md)
* Analýza [vzorů využití](cdn-analyze-usage-patterns.md)


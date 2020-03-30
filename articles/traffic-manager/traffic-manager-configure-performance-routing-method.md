---
title: Konfigurace metody směrování výkonu pomocí Azure Traffic Manager | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak nakonfigurovat Traffic Manager pro směrování provozu do koncového bodu s nejnižší latencí
services: traffic-manager
manager: twooley
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: f5e9b7690c28793a35c692a6125a6b11c7a140a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938762"
---
# <a name="configure-the-performance-traffic-routing-method"></a>Konfigurace metody směrování provozu výkonu

Metoda směrování provozu výkonu umožňuje směrovat provoz na koncový bod s nejnižší latencí ze sítě klienta. Datové centrum s nejnižší latencí je obvykle nejbližší v geografické vzdálenosti. Tato metoda směrování provozu nemůže zohlednit změny v konfiguraci sítě nebo načtení v reálném čase.

##  <a name="to-configure-performance-routing-method"></a>Konfigurace metody směrování výkonu

1. V prohlížeči se přihlaste k webu [Azure Portal](https://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/). 
2. Na vyhledávacím panelu portálu vyhledejte **profily traffic manageru** a klikněte na název profilu, pro který chcete nakonfigurovat metodu směrování.
3. V okně **profilu Traffic Managerovte** ověřte, zda jsou k dispozici cloudové služby i weby, které chcete zahrnout do konfigurace.
4. V části **Nastavení** klikněte na **Konfigurace**a v okně **Konfigurace** postupujte takto:
    1. Pro **nastavení metody směrování provozu**vyberte **metodu Směrování** v části **Výkon**.
    2. Nastavení **monitoru koncového bodu** je shodná pro všechny koncové hodnoty v rámci tohoto profilu následujícím způsobem:
        1. Vyberte příslušný **protokol**a zadejte číslo **portu.** 
        2. Pro **cestu** zadejte */* lomítko . Chcete-li sledovat koncové body, musíte zadat cestu a název souboru. Lomítko "/" je platná položka pro relativní cestu a znamená, že soubor je v kořenovém adresáři (výchozí).
        3. V horní části stránky klikněte na **Uložit**.
5.  Otestujte změny v konfiguraci následujícím způsobem:
    1.  Na vyhledávacím panelu portálu vyhledejte název profilu Traffic Manageru a ve výsledcích, které se zobrazí, klikněte na profil Traffic Manager.
    2.  V okně **Profil traffic manageru** klepněte na **položku Přehled**.
    3.  Okno **profilu Traffic Manager** zobrazuje název DNS nově vytvořeného profilu traffic manageru. To může být použito všemi klienty (například přechodem na něj pomocí webového prohlížeče) získat směrovány na správný koncový bod, jak je určeno typu směrování. V tomto případě jsou všechny požadavky směrovány do koncového bodu s nejnižší latencí ze sítě klienta.
6. Jakmile profil Traffic Manageru funguje, upravte záznam DNS na autoritativním serveru DNS tak, aby název domény vaší společnosti ukazoval na název domény Traffic Manageru.

![Konfigurace metody směrování provozu výkonu pomocí Traffic Manageru][1]

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [metodě váženého směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Přečtěte si o [metodě prioritního směrování](traffic-manager-configure-priority-routing-method.md).
- Přečtěte si o [metodě geografického směrování](traffic-manager-configure-geographic-routing-method.md).
- Přečtěte si, jak [otestovat nastavení traffic manageru](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png
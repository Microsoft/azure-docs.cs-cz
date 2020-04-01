---
title: Kurz – konfigurace váženého směrování provozu kruhového dotazování pomocí Azure Traffic Manageru
description: Tento kurz vysvětluje, jak vyvažovat provoz pomocí metody kruhového dotazování v Traffic Manageru
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: 8bdc710b36cae70d29d32333f431b8a9dda154cc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76938715"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Kurz: Konfigurace metody směrování vážených dopravních směrů ve Správci provozu

Běžný vzor metody směrování provozu je poskytnout sadu identických koncových bodů, které zahrnují cloudové služby a weby, a odesílat provoz každému stejně. Následující kroky popisují, jak nakonfigurovat tento typ metody směrování provozu.

> [!NOTE]
> Azure Web App už poskytuje funkce vyrovnávání zatížení kruhového dotazování pro weby v rámci oblasti Azure (která může zahrnovat více datových center). Traffic Manager umožňuje distribuovat provoz mezi weby v různých datových centrech.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Konfigurace metody směrování vážených dopravních směrů

1. V prohlížeči se přihlaste k webu [Azure Portal](https://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/). 
2. Na vyhledávacím panelu portálu vyhledejte **profily traffic manageru** a klikněte na název profilu, pro který chcete nakonfigurovat metodu směrování.
3. V okně **profilu Traffic Managerovte** ověřte, zda jsou k dispozici cloudové služby i weby, které chcete zahrnout do konfigurace.
4. V části **Nastavení** klikněte na **Konfigurace**a v okně **Konfigurace** postupujte takto:
    1. V **případě nastavení metody směrování provozu**ověřte, zda je metoda směrování provozu **vážena**. Pokud tomu tak není, klikněte v rozevíracím seznamu na **Vážený.**
    2. Nastavení **monitoru koncového bodu** je shodná pro všechny koncové hodnoty v rámci tohoto profilu následujícím způsobem:
        1. Vyberte příslušný **protokol**a zadejte číslo **portu.** 
        2. Pro **cestu** zadejte */* lomítko . Chcete-li sledovat koncové body, musíte zadat cestu a název souboru. Lomítko "/" je platná položka pro relativní cestu a znamená, že soubor je v kořenovém adresáři (výchozí).
        3. V horní části stránky klikněte na **Uložit**.
5. Otestujte změny v konfiguraci následujícím způsobem:
    1.  Na vyhledávacím panelu portálu vyhledejte název profilu Traffic Manageru a ve výsledcích, které se zobrazí, klikněte na profil Traffic Manager.
    2.  V okně **Profil traffic manageru** klepněte na **položku Přehled**.
    3.  Okno **profilu Traffic Manager** zobrazuje název DNS nově vytvořeného profilu traffic manageru. To může být použito všemi klienty (například přechodem na něj pomocí webového prohlížeče) získat směrovány na správný koncový bod, jak je určeno typu směrování. V tomto případě jsou všechny požadavky směrovány každý koncový bod způsobem kruhovédotazování.
6. Jakmile profil Traffic Manageru funguje, upravte záznam DNS na autoritativním serveru DNS tak, aby název domény vaší společnosti ukazoval na název domény Traffic Manageru.

![Konfigurace metody směrování vážených dopravních směrů pomocí Traffic Manageru][1]

## <a name="next-steps"></a>Další kroky

- Informace o [metodě směrování prioritního provozu](traffic-manager-configure-priority-routing-method.md).
- Informace o [metodě směrování provozu výkonu](traffic-manager-configure-performance-routing-method.md).
- Přečtěte si o [metodě geografického směrování](traffic-manager-configure-geographic-routing-method.md).
- Přečtěte si, jak [otestovat nastavení traffic manageru](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png

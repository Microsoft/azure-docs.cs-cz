---
title: Kurz – konfigurace směrování přenosu s váženým cyklem v kruhovém dotazování pomocí Azure Traffic Manager
description: Tento kurz vysvětluje, jak vyrovnávat zatížení provozu pomocí metody kruhového dotazování v Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: duau
ms.openlocfilehash: dff7d4ec02c5a17b51d73b9d81f93984b95a7d22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401346"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Kurz: Konfigurace metody váženého směrování provozu v Traffic Manager

Běžným způsobem směrování provozu je poskytnutí sady stejných koncových bodů, mezi které patří cloudové služby a weby a posílání provozu do obou. Následující kroky popisují, jak nakonfigurovat tento typ metody směrování provozu.

> [!NOTE]
> Webová aplikace Azure už poskytuje funkce Vyrovnávání zatížení kruhového dotazování pro weby v oblasti Azure (která může zahrnovat několik datových Center). Traffic Manager umožňuje distribuci provozu mezi weby v různých datových centrech.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Konfigurace metody směrování váženého provozu

1. V prohlížeči se přihlaste k webu [Azure Portal](https://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/). 
2. Na panelu hledání na portálu vyhledejte **Traffic Manager profily** a potom klikněte na název profilu, pro který chcete nakonfigurovat metodu směrování.
3. V okně **profil Traffic Manager** ověřte, zda jsou k dispozici jak cloudové služby, tak i weby, které chcete zahrnout do vaší konfigurace.
4. V části **Nastavení** klikněte na možnost **Konfigurace**a v okně **Konfigurace** proveďte následující kroky:
    1. V části **nastavení metody směrování provozu**ověřte, zda je metoda směrování provozu **váže**. Pokud není, klikněte v rozevíracím seznamu na **váže** .
    2. **Nastavení monitorování koncového bodu** nastavte u všech koncových bodů v tomto profilu stejným způsobem:
        1. Vyberte odpovídající **protokol**a zadejte číslo **portu** . 
        2. Jako **cestu** zadejte lomítko */* . Chcete-li monitorovat koncové body, je nutné zadat cestu a název souboru. Lomítko "/" je platná položka relativní cesty a předpokládá, že se soubor nachází v kořenovém adresáři (výchozí).
        3. V horní části stránky klikněte na **Uložit**.
5. Proveďte test změn v konfiguraci následujícím způsobem:
    1.  Na panelu hledání na portálu vyhledejte název profilu Traffic Manager a klikněte na profil Traffic Manager v zobrazených výsledcích.
    2.  V okně profil **Traffic Manager** klikněte na **Přehled**.
    3.  Okno **profil Traffic Manager** zobrazuje název DNS nově vytvořeného profilu Traffic Manager. Můžete je použít u všech klientů (například tak, že na ně přejdete pomocí webového prohlížeče), abyste se dostali ke správnému koncovému bodu, který určuje typ směrování. V tomto případě všechny požadavky jsou směrovány do každého koncového bodu kruhové dotazování.
6. Jakmile profil Traffic Manager funguje, upravte záznam DNS na autoritativním serveru DNS tak, aby odkazoval na název domény vaší společnosti na název domény Traffic Manager.

![Konfigurace metody směrování váženého provozu pomocí Traffic Manager][1]

## <a name="next-steps"></a>Další kroky

- Seznamte se s [metodou směrování provozu s prioritou](traffic-manager-configure-priority-routing-method.md).
- Seznamte se s [metodou směrování provozu výkonu](traffic-manager-configure-performance-routing-method.md).
- Přečtěte si o [metodě geografického směrování](traffic-manager-configure-geographic-routing-method.md).
- Naučte se [Testovat nastavení Traffic Manager](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png

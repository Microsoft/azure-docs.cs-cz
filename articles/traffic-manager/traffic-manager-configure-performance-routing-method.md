---
title: Konfigurace metody směrování provozu s výkonem pomocí Azure Traffic Manager | Microsoft Docs
description: Tento článek vysvětluje, jak nakonfigurovat Traffic Manager pro směrování provozu do koncového bodu s nejnižší latencí.
services: traffic-manager
manager: twooley
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: duau
ms.openlocfilehash: 46d71a38b79f449084f7353527f2dfb05d5b92a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994906"
---
# <a name="configure-the-performance-traffic-routing-method"></a>Konfigurace metody směrování provozu výkonu

Metoda směrování provozu umožňuje směrovat provoz do koncového bodu s nejnižší latencí ze sítě klienta. Obvykle je datové centrum s nejnižší latencí nejblíže geografické vzdálenosti. Tato metoda směrování provozu nemůže v reálném čase brát v úvahu změny v konfiguraci sítě nebo zatížení.

##  <a name="to-configure-performance-routing-method"></a>Konfigurace metody směrování výkonu

1. V prohlížeči se přihlaste k webu [Azure Portal](https://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/). 
2. Na panelu hledání na portálu vyhledejte **Traffic Manager profily** a potom klikněte na název profilu, pro který chcete nakonfigurovat metodu směrování.
3. V okně **profil Traffic Manager** ověřte, zda jsou k dispozici jak cloudové služby, tak i weby, které chcete zahrnout do vaší konfigurace.
4. V části **Nastavení** klikněte na možnost **Konfigurace** a v okně **Konfigurace** proveďte následující kroky:
    1. V případě metody **směrování provozu** vyberte v možnosti **Směrování** možnost **výkon**.
    2. **Nastavení monitorování koncového bodu** nastavte u všech koncových bodů v tomto profilu stejným způsobem:
        1. Vyberte odpovídající **protokol** a zadejte číslo **portu** . 
        2. Jako **cestu** zadejte lomítko */* . Chcete-li monitorovat koncové body, je nutné zadat cestu a název souboru. Lomítko "/" je platná položka relativní cesty a předpokládá, že se soubor nachází v kořenovém adresáři (výchozí).
        3. V horní části stránky klikněte na **Uložit**.
5.  Proveďte test změn v konfiguraci následujícím způsobem:
    1.  Na panelu hledání na portálu vyhledejte název profilu Traffic Manager a klikněte na profil Traffic Manager v zobrazených výsledcích.
    2.  V okně profil **Traffic Manager** klikněte na **Přehled**.
    3.  Okno **profil Traffic Manager** zobrazuje název DNS nově vytvořeného profilu Traffic Manager. Můžete je použít u všech klientů (například tak, že na ně přejdete pomocí webového prohlížeče), abyste se dostali ke správnému koncovému bodu, který určuje typ směrování. V tomto případě jsou všechny požadavky směrovány do koncového bodu s nejnižší latencí ze sítě klienta.
6. Jakmile profil Traffic Manager funguje, upravte záznam DNS na autoritativním serveru DNS tak, aby odkazoval na název domény vaší společnosti na název domény Traffic Manager.

![Konfigurace metody směrování provozu s výkonem pomocí Traffic Manager][1]

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [metodě váženého směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Přečtěte si o [metodě prioritního směrování](traffic-manager-configure-priority-routing-method.md).
- Přečtěte si o [metodě geografického směrování](traffic-manager-configure-geographic-routing-method.md).
- Naučte se [Testovat nastavení Traffic Manager](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png
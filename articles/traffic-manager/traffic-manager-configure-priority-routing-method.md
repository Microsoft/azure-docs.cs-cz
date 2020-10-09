---
title: Kurz – konfigurace směrování provozu s prioritou pomocí Azure Traffic Manager
description: V tomto kurzu se dozvíte, jak nakonfigurovat metodu směrování provozu s prioritou v Traffic Manager
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
ms.openlocfilehash: 404338c3e36216833d39c3551ae2dee0be304d24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401006"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Kurz: Konfigurace metody směrování provozu priority v Traffic Manager

Azure websites už pro weby v rámci datového centra (označované také jako oblast) poskytují funkce převzetí služeb při selhání, a to bez ohledu na režim webu. Traffic Manager poskytuje převzetí služeb při selhání pro weby v různých datových centrech.

Běžným vzorem pro převzetí služeb při selhání je odeslání provozu do primární služby a poskytování sady identických zálohovacích služeb pro převzetí služeb při selhání. Následující kroky vysvětlují, jak nakonfigurovat toto převzetí služeb při selhání s Azure Cloud Services a weby:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Konfigurace metody směrování provozu s prioritou

1. V prohlížeči se přihlaste k webu [Azure Portal](https://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/). 
2. Na panelu hledání na portálu vyhledejte **Traffic Manager profily** a potom klikněte na název profilu, pro který chcete nakonfigurovat metodu směrování.
3. V okně **profil Traffic Manager** ověřte, zda jsou k dispozici jak cloudové služby, tak i weby, které chcete zahrnout do vaší konfigurace.
4. V části **Nastavení** klikněte na možnost **Konfigurace**a v okně **Konfigurace** proveďte následující kroky:
    1. V části **nastavení metody směrování provozu**ověřte, že je metoda směrování provozu **Priorita**. Pokud tomu tak není, v rozevíracím seznamu klikněte na možnost **Priorita** .
    2. **Nastavení monitorování koncového bodu** nastavte u všech koncových bodů v tomto profilu stejným způsobem:
        1. Vyberte odpovídající **protokol**a zadejte číslo **portu** . 
        2. Jako **cestu** zadejte lomítko */* . Chcete-li monitorovat koncové body, je nutné zadat cestu a název souboru. Lomítko "/" je platná položka relativní cesty a předpokládá, že se soubor nachází v kořenovém adresáři (výchozí).
        3. V horní části stránky klikněte na **Uložit**.
5. V části **Nastavení** klikněte na **koncové body**.
6. V okně **koncové body** Zkontrolujte pořadí priority pro koncové body. Když vyberete metodu směrování přenosů **priority** , pořadí vybraných koncových bodů. Ověřte pořadí priorit koncových bodů.  Primární koncový bod je nahoře. Dvakrát ověřte pořadí, ve kterém je zobrazeno. všechny požadavky budou směrovány do prvního koncového bodu a pokud Traffic Manager zjistí, že není v pořádku, přenos dat automaticky převezme další koncový bod. 
7. Pokud chcete změnit pořadí priority koncového bodu, klikněte na koncový bod, v zobrazeném okně **koncového bodu** klikněte na **Upravit** a podle potřeby změňte hodnotu **priority** . 
8. Kliknutím na **Uložit** uložte změny nastavení koncového bodu.
9. Po dokončení změn konfigurace klikněte v dolní části stránky na **Uložit** .
10. Proveďte test změn v konfiguraci následujícím způsobem:
    1.  Na panelu hledání na portálu vyhledejte název profilu Traffic Manager a klikněte na profil Traffic Manager v zobrazených výsledcích.
    2.  V okně profil **Traffic Manager** klikněte na **Přehled**.
    3.  Okno **profil Traffic Manager** zobrazuje název DNS nově vytvořeného profilu Traffic Manager. Můžete je použít u všech klientů (například tak, že na ně přejdete pomocí webového prohlížeče), abyste se dostali ke správnému koncovému bodu, který určuje typ směrování. V tomto případě jsou všechny požadavky směrovány do prvního koncového bodu a pokud Traffic Manager zjistí, že není v pořádku, přenos dat automaticky převezme další koncový bod.
11. Jakmile profil Traffic Manager funguje, upravte záznam DNS na autoritativním serveru DNS tak, aby odkazoval na název domény vaší společnosti na název domény Traffic Manager.

![Konfigurace metody směrování provozu s prioritou pomocí Traffic Manager][1]

## <a name="next-steps"></a>Další kroky


- Přečtěte si o [metodě váženého směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Seznamte se s [metodou směrování výkonu](traffic-manager-configure-performance-routing-method.md).
- Přečtěte si o [metodě geografického směrování](traffic-manager-configure-geographic-routing-method.md).
- Naučte se [Testovat nastavení Traffic Manager](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png
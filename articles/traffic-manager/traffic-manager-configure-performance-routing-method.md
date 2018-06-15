---
title: Konfigurace metody směrování výkonu provozu pomocí Azure Traffic Manageru | Microsoft Docs
description: Tento článek vysvětluje, jak nakonfigurovat Traffic Manager přesměrovat provoz na koncový bod s nejnižší latencí
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 6dca6de1-18f7-4962-bd98-6055771fab22
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 014aa646459cd64fca7c697419324caa3edaeeea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876310"
---
# <a name="configure-the-performance-traffic-routing-method"></a>Konfigurace metody směrování provozu výkonu

Metodu směrování provozu výkonu umožňuje směrovat provoz na koncový bod s nejnižší latencí z klienta sítě. Datového centra s nejnižší latencí je obvykle na nejbližší v zeměpisné vzdálenosti. Tuto metodu směrování provozu nelze účet v reálném čase změny v konfiguraci sítě nebo zatížení.

##  <a name="to-configure-performance-routing-method"></a>Konfigurace metody směrování podle výkonu

1. V prohlížeči se přihlaste k webu [Azure Portal](http://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/). 
2. V panelu vyhledávání na portálu, vyhledejte **profily Traffic Manageru** a pak klikněte na název profilu, který chcete nakonfigurovat metodu směrování.
3. V **profil služby Traffic Manager** okno, ověřte, zda cloudové služby i weby, které chcete zahrnout do vaší konfigurace.
4. V **nastavení** klikněte na tlačítko **konfigurace**a v **konfigurace** okně dokončení následujícím způsobem:
    1. Pro **nastavení metodu směrování provozu**, pro **metoda směrování** vyberte **výkonu**.
    2. Nastavte **nastavení monitorování koncového bodu** stejné pro všechny každý koncový bod v tomto profilu následujícím způsobem:
        1. Vyberte odpovídající **protokol**a zadejte **Port** číslo. 
        2. Pro **cesta** zadejte lomítkem  */* . Chcete-li monitorovat koncových bodů, musíte zadat cestu a název souboru. A dopředné lomítko "/" je platná položka pro relativní cestu a znamená, že soubor je v kořenovém adresáři (výchozí).
        3. V horní části stránky klikněte na tlačítko **Uložit**.
5.  Testovací změny v konfiguraci následujícím způsobem:
    1.  V panelu vyhledávání na portálu, vyhledejte název profilu Traffic Manageru a klikněte na profil služby Traffic Manager ve výsledcích, zobrazené.
    2.  V **Traffic Manager** profilu okno, klikněte na tlačítko **přehled**.
    3.  **Profil služby Traffic Manager** zobrazuje název DNS vašeho nově vytvořený profil služby Traffic Manager. Tímto lze všechny klienty (například tak, že přejdete do ní pomocí webového prohlížeče) k získání směruje na pravý koncový bod jako Určuje typ směrování. V takovém případě všechny požadavky směrují na koncový bod s nejnižší latencí z klienta sítě.
6. Jakmile váš profil služby Traffic Manager funguje, upravte záznam DNS na autoritativního serveru DNS pro název domény vaší společnosti přejděte na název domény Traffic Manageru.

![Konfigurace metody směrování výkonu provozu pomocí Traffic Manager][1]

## <a name="next-steps"></a>Další kroky

- Další informace o [vážené metodu směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Další informace o [metody směrování s prioritou](traffic-manager-configure-priority-routing-method.md).
- Další informace o [geografické metody směrování](traffic-manager-configure-geographic-routing-method.md).
- Zjistěte, jak [Traffic Manager nastavení testů](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png
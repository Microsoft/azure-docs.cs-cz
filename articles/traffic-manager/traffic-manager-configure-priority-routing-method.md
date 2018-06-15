---
title: Konfigurace metody směrování priority provozu pomocí Azure Traffic Manageru | Microsoft Docs
description: Tento článek vysvětluje, jak nakonfigurovat metodu směrování provozu s prioritou v Traffic Manageru
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
ms.openlocfilehash: 0db83cde6facc89b8b8aa72e6419129ec868235c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876632"
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>Konfigurace metody směrování provozu s prioritou v Traffic Manageru

Bez ohledu na režim příslušného webu weby Azure již poskytují funkce převzetí služeb při selhání pro weby v rámci datového centra (označované také jako oblast). Traffic Manager poskytuje převzetí služeb při selhání pro weby v různých datových centrech.

Běžný vzor pro převzetí služeb při selhání služby je a odesílá data na primární služba poskytující sadu identické zálohování služby pro převzetí služeb při selhání. Následující kroky popisují, jak nakonfigurovat tato seřazených podle priorit převzetí služeb při selhání s Azure cloud services a weby:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Nakonfigurovat metodu směrování provozu s prioritou

1. V prohlížeči se přihlaste k webu [Azure Portal](http://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/). 
2. V panelu vyhledávání na portálu, vyhledejte **profily Traffic Manageru** a pak klikněte na název profilu, který chcete nakonfigurovat metodu směrování.
3. V **profil služby Traffic Manager** okno, ověřte, zda cloudové služby i weby, které chcete zahrnout do vaší konfigurace.
4. V **nastavení** klikněte na tlačítko **konfigurace**a v **konfigurace** okně dokončení následujícím způsobem:
    1. Pro **nastavení metodu směrování provozu**, ověřte, zda je metodu směrování provozu **s prioritou**. Pokud není, klikněte na tlačítko **s prioritou** z rozevíracího seznamu.
    2. Nastavte **nastavení monitorování koncového bodu** stejné pro všechny každý koncový bod v tomto profilu následujícím způsobem:
        1. Vyberte odpovídající **protokol**a zadejte **Port** číslo. 
        2. Pro **cesta** zadejte lomítkem  */* . Chcete-li monitorovat koncových bodů, musíte zadat cestu a název souboru. A dopředné lomítko "/" je platná položka pro relativní cestu a znamená, že soubor je v kořenovém adresáři (výchozí).
        3. V horní části stránky klikněte na tlačítko **Uložit**.
5. V **nastavení** klikněte na tlačítko **koncové body**.
6. V **koncové body** okně zkontrolujte pořadí priorit pro koncové body. Když vyberete **s prioritou** metodu směrování provozu, pořadí otázek vybrané koncové body. Zkontrolujte pořadí podle priority koncových bodů.  Primární koncový bod je v horní části. Zkontrolujte v pořadí, ve kterém se zobrazí. všechny požadavky budou směrované na první koncový bod a Traffic Manager zjistí, je-li být není v pořádku, provoz automaticky přejde na další koncový bod. 
7. Chcete-li změnit pořadí priorit koncový bod, klikněte na koncový bod a v **koncový bod** okno, které se zobrazí, klikněte na tlačítko **upravit** a změňte **s prioritou** hodnoty podle potřeby. 
8. Klikněte na tlačítko **Uložit** uložit změnit nastavení koncového bodu.
9. Po dokončení změny konfigurace, klikněte na tlačítko **Uložit** v dolní části stránky.
10. Testovací změny v konfiguraci následujícím způsobem:
    1.  V panelu vyhledávání na portálu, vyhledejte název profilu Traffic Manageru a klikněte na profil služby Traffic Manager ve výsledcích, zobrazené.
    2.  V **Traffic Manager** profilu okno, klikněte na tlačítko **přehled**.
    3.  **Profil služby Traffic Manager** zobrazuje název DNS vašeho nově vytvořený profil služby Traffic Manager. Tímto lze všechny klienty (například tak, že přejdete do ní pomocí webového prohlížeče) k získání směruje na pravý koncový bod jako Určuje typ směrování. V takovém případě všechny požadavky jsou směrovány do první koncový bod a Traffic Manager zjistí, je-li být není v pořádku, provoz automaticky přejde na další koncový bod.
11. Jakmile váš profil služby Traffic Manager funguje, upravte záznam DNS na autoritativního serveru DNS pro název domény vaší společnosti přejděte na název domény Traffic Manageru.

![Konfigurace metody směrování priority provozu pomocí Traffic Manager][1]

## <a name="next-steps"></a>Další kroky


- Další informace o [vážené metodu směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Další informace o [metody směrování podle výkonu](traffic-manager-configure-performance-routing-method.md).
- Další informace o [geografické metody směrování](traffic-manager-configure-geographic-routing-method.md).
- Zjistěte, jak [Traffic Manager nastavení testů](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png
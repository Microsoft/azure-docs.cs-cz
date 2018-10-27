---
title: Konfigurace metody směrování priority provozu pomocí Azure Traffic Manager | Dokumentace Microsoftu
description: Tento článek vysvětluje postup konfigurace metody prioritního směrování provozu Traffic Manageru
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 52bfc73ad586bb3852e30a850d4ba50a113183d3
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140413"
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>Konfigurace metody směrování provozu priority v Traffic Manageru

Bez ohledu na režim příslušného webu již Azure Websites poskytují funkce převzetí služeb při selhání pro weby v rámci datového centra (označované také jako oblast). Traffic Manager poskytuje převzetí služeb při selhání pro weby v různých datových centrech.

Běžným vzorem pro převzetí služeb při selhání je k posílání provozu do primární služby a poskytují sadu identické zálohování služeb pro převzetí služeb při selhání. Následující postup vysvětluje, jak nakonfigurovat tento seřazený podle priority převzetí služeb při selhání s Azure cloud services a weby:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Konfigurace metody prioritního směrování provozu

1. V prohlížeči se přihlaste k webu [Azure Portal](http://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/). 
2. Na panelu hledání na portálu vyhledejte **profily Traffic Manageru** a potom klikněte na název profilu, který chcete konfigurovat metodu směrování pro.
3. V **profil služby Traffic Manager** okno, ověřte, zda jsou k dispozici cloudovými službami a weby, které chcete zahrnout do vaší konfigurace.
4. V **nastavení** klikněte na tlačítko **konfigurace**a **konfigurace** okno vyplňte následujícím způsobem:
    1. Pro **nastavení metodu směrování provozu**, ověřte, zda je metodu směrování provozu **Priority**. Pokud není, klikněte na tlačítko **Priority** z rozevíracího seznamu.
    2. Nastavte **nastavení sledování koncových bodů** shodné pro všechny každý koncový bod v rámci tohoto profilu následujícím způsobem:
        1. Vyberte příslušné **protokol**a zadejte **Port** číslo. 
        2. Pro **cesta** zadejte lomítkem */*. Monitorování koncových bodů, musíte zadat cestu a název souboru. Dopředné lomítko "/" je platná položka pro relativní cestu a znamená, že se soubor nachází v kořenovém adresáři (výchozí).
        3. V horní části stránky klikněte na tlačítko **Uložit**.
5. V **nastavení** klikněte na tlačítko **koncové body**.
6. V **koncové body** okno, zkontrolujte pořadí priorit pro vaše koncové body. Když vyberete **Priority** metodu směrování provozu, pořadí otázek vybraných koncových bodů. Zkontrolujte pořadí podle priority koncových bodů.  Primární koncový bod je v horní části. Zkontrolujte v pořadí, ve kterém se zobrazí. všechny požadavky budou směrovat na první koncový bod a být není v pořádku, pokud se Traffic Manager zjistí, provoz automaticky převezme služby při selhání na další koncový bod. 
7. Chcete-li změnit pořadí podle priority koncových bodů, klikněte na koncový bod a **koncový bod** okno, které se zobrazí, klikněte na tlačítko **upravit** a změňte **Priority** hodnoty podle potřeby. 
8. Klikněte na tlačítko **Uložit** uložit změnit nastavení koncového bodu.
9. Po dokončení změny konfigurace, klikněte na tlačítko **Uložit** v dolní části stránky.
10. Otestujte změny v konfiguraci následujícím způsobem:
    1.  Na panelu hledání na portálu vyhledejte název profilu Traffic Manageru a klikněte na profil Traffic Manageru ve výsledcích, který je zobrazeno.
    2.  V **Traffic Manageru** profilu okna, klikněte na tlačítko **přehled**.
    3.  **Profil služby Traffic Manager** okně se zobrazí název DNS váš nově vytvořený profil Traffic Manageru. Tímto lze všechny klienty (například tak, že přejdete do ní pomocí webového prohlížeče) získat směrovat na správný koncový bod jako Určuje typ směrování. V tomto případě jsou směrovány na první koncový bod všechny požadavky a být není v pořádku, pokud se Traffic Manager zjistí, provoz automaticky převezme služby při selhání na další koncový bod.
11. Jakmile váš profil Traffic Manageru funguje, upravte záznam DNS na autoritativního serveru DNS pro název domény vaší společnosti odkazovat na název domény Traffic Manageru.

![Konfigurace metody směrování priority provozu pomocí Traffic Manageru][1]

## <a name="next-steps"></a>Další postup


- Přečtěte si o [metodě váženého směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Další informace o [metody směrování podle výkonu](traffic-manager-configure-performance-routing-method.md).
- Přečtěte si o [metodě geografického směrování](traffic-manager-configure-geographic-routing-method.md).
- Zjistěte, jak [test nastavení Traffic Manageru](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png
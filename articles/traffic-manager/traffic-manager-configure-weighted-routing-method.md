---
title: Konfigurace metody směrování provozu vážené kruhové dotazování pomocí Azure Traffic Manageru | Microsoft Docs
description: Tento článek vysvětluje, jak načíst vyrovnávání přenosů pomocí jiné metody kruhového dotazování v Traffic Manageru
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
ms.openlocfilehash: 7aa4c9120d44ff1b3e59a57090ea04e3f8021fc4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876541"
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Konfigurace metody směrování vyvážené provoz v Traffic Manageru

Běžné vzor metoda směrování provozu je poskytují sadu identické koncové body, které zahrnují cloudové služby a weby, a odesílá data na každý kruhového dotazování. Následující kroky popisují postup konfigurace tento typ metodu směrování provozu.

> [!NOTE]
> Weby Azure již poskytují funkce pro weby v rámci datového centra (označované také jako oblast) pro vyrovnávání zatížení pomocí kruhového dotazování. Traffic Manager umožňuje určit metodu směrování provozu kruhovým dotazováním pro weby v různých datových centrech.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Nakonfigurovat metodu směrování provozu vyvážené

1. V prohlížeči se přihlaste k webu [Azure Portal](http://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/). 
2. V panelu vyhledávání na portálu, vyhledejte **profily Traffic Manageru** a pak klikněte na název profilu, který chcete nakonfigurovat metodu směrování.
3. V **profil služby Traffic Manager** okno, ověřte, zda cloudové služby i weby, které chcete zahrnout do vaší konfigurace.
4. V **nastavení** klikněte na tlačítko **konfigurace**a v **konfigurace** okně dokončení následujícím způsobem:
    1. Pro **nastavení metodu směrování provozu**, ověřte, zda je metodu směrování provozu **vážená**. Pokud není, klikněte na tlačítko **vážená** z rozevíracího seznamu.
    2. Nastavte **nastavení monitorování koncového bodu** stejné pro všechny každý koncový bod v tomto profilu následujícím způsobem:
        1. Vyberte odpovídající **protokol**a zadejte **Port** číslo. 
        2. Pro **cesta** zadejte lomítkem  */* . Chcete-li monitorovat koncových bodů, musíte zadat cestu a název souboru. A dopředné lomítko "/" je platná položka pro relativní cestu a znamená, že soubor je v kořenovém adresáři (výchozí).
        3. V horní části stránky klikněte na tlačítko **Uložit**.
5. Testovací změny v konfiguraci následujícím způsobem:
    1.  V panelu vyhledávání na portálu, vyhledejte název profilu Traffic Manageru a klikněte na profil služby Traffic Manager ve výsledcích, zobrazené.
    2.  V **Traffic Manager** profilu okno, klikněte na tlačítko **přehled**.
    3.  **Profil služby Traffic Manager** zobrazuje název DNS vašeho nově vytvořený profil služby Traffic Manager. Tímto lze všechny klienty (například tak, že přejdete do ní pomocí webového prohlížeče) k získání směruje na pravý koncový bod jako Určuje typ směrování. V takovém případě jsou směrovat všechny požadavky každý koncový bod v kruhového dotazování.
6. Jakmile váš profil služby Traffic Manager funguje, upravte záznam DNS na autoritativního serveru DNS pro název domény vaší společnosti přejděte na název domény Traffic Manageru.

![Konfigurace metody směrování vyvážené provozu pomocí Traffic Manager][1]

## <a name="next-steps"></a>Další kroky

- Další informace o [metoda směrování provozu s prioritou](traffic-manager-configure-priority-routing-method.md).
- Další informace o [metoda směrování provozu výkonu](traffic-manager-configure-performance-routing-method.md).
- Další informace o [geografické metody směrování](traffic-manager-configure-geographic-routing-method.md).
- Zjistěte, jak [Traffic Manager nastavení testů](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png

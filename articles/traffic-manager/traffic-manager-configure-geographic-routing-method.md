---
title: Konfigurace metody směrování geografické provozu pomocí Azure Traffic Manageru | Microsoft Docs
description: Tento článek vysvětluje, jak nakonfigurovat metodu směrování provozu geografické pomocí Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 7b49e2a4eef5a966f1ef2aa283a3089bb5b73734
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29397799"
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Konfigurace metody směrování provozu geografické pomocí Traffic Manager

Metodu směrování provozu geografické umožňuje směrovat provoz na konkrétní koncových bodů podle zeměpisného umístění, kde mají původ žádosti. V tomto kurzu se dozvíte, jak vytvořit profil Traffic Manageru pomocí této metody směrování a konfiguraci koncových bodů pro příjem dat z konkrétní zeměpisných oblastí.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Správce provozu

1. V prohlížeči se přihlaste k webu [Azure Portal](http://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/).
2. Klikněte na tlačítko **vytvořit prostředek** > **sítě** > **profil služby Traffic Manager** > **vytvořit**.
4. V **profil služby Traffic Manager vytvořit**:
    1. Zadejte název pro svůj profil. Tento název musí být jedinečný v rámci zóny trafficmanager.net. Pro přístup k vašeho profilu Traffic Manageru, použijte název DNS <profilename>. trafficmanager.net.
    2. Vyberte **Geographic** metody směrování.
    3. Vyberte předplatné, které chcete vytvořit tento profil v části.
    4. Použít existující skupinu prostředků nebo vytvořte novou skupinu prostředků umístit pod tímto profilem. Pokud vyberete možnost vytvořit novou skupinu prostředků, použijte **umístění skupiny prostředků** rozevíracího seznamu a určete tak umístění skupiny prostředků. Toto nastavení odkazuje na umístění skupiny prostředků a nemá žádný vliv na profil služby Traffic Manager, která je nasazena globálně.
    5. Po kliknutí na tlačítko **vytvořit**, váš profil služby Traffic Manager se vytvoří a nasadí globálně.

![Vytvoření profilu Traffic Manageru](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Přidat koncové body

1. Vyhledejte název profilu Traffic Manageru, kterou jste vytvořili v panelu vyhledávání na portálu a klikněte na výsledek, když se zobrazí.
2. Přejděte na **nastavení** -> **koncové body** v Traffic Manageru.
3. Klikněte na tlačítko **přidat** zobrazíte **přidat koncový bod**.
3. Klikněte na tlačítko **přidat** a v **přidání koncového bodu** , se zobrazí, dokončení následujícím způsobem:
4. Vyberte **typ** v závislosti na typu přidáváte koncového bodu. Zeměpisná směrování profily, které jsou určené v produkčním prostředí doporučujeme pomocí typy vnořených koncových bodů obsahující profil podřízené s více než jeden koncový bod. Další podrobnosti najdete v tématu [nejčastější dotazy o metodách směrování provozu geografické](traffic-manager-FAQs.md).
5. Zadejte **Název**, podle kterého tento koncový bod rozpoznáte.
6. Některá pole na této stránce závisí na typu koncového bodu, který chcete přidat:
    1. Pokud přidáváte koncový bod Azure, vyberte **cíle typ prostředku** a **cíl** podle prostředků, kterou chcete směrovat provoz na
    2. Chcete-li přidat **externí** koncový bod, zadejte **plně kvalifikovaný název domény (FQDN)** pro svůj koncový bod.
    3. Pokud přidáváte **koncový bod vnořené**, vyberte **cíle prostředků** odpovídající podřízené profil, který chcete použít a zadat **minimální počet koncových bodů podřízené**.
7. V části geografická mapování pomocí rozevíracího dolů můžete přidat oblastech z místo, kam chcete odeslat k tomuto koncovému bodu přenos. Je nutné přidat alespoň jednu oblast a může mít více oblastí namapované.
8. Tento postup opakujte pro všechny koncové body, které chcete přidat pod tento profil

![Přidání koncového bodu Traffic Manager](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Použít profil služby Traffic Manager
1.  V panelu vyhledávání na portálu, vyhledejte **profil služby Traffic Manager** název, který jste vytvořili v předchozí části a klikněte na profil správce provozu ve výsledcích, zobrazené.
2. Klikněte na tlačítko **přehled**.
3. **Profil služby Traffic Manager** zobrazí název DNS vašeho nově vytvořený profil služby Traffic Manager. Tímto lze všechny klienty (například tak, že přejdete do ní pomocí webového prohlížeče) k získání směruje na pravý koncový bod jako Určuje typ směrování.  V případě geografické směrování Traffic Manager vypadá na zdrojové IP příchozích požadavků a určuje oblast, ve kterém je pocházející. Pokud tuto oblast je namapovaná na koncový bod, provoz se směruje k dispozici. Pokud tato oblast není namapovaná na koncový bod, Traffic Manager vrátí odpověď NODATA dotazu.

## <a name="next-steps"></a>Další postup

- Další informace o [metoda směrování provozu Geographic](traffic-manager-routing-methods.md#geographic).
- Zjistěte, jak [Traffic Manager nastavení testů](traffic-manager-testing-settings.md).

---
title: Konfigurovat geografickou metodu směrování provozu pomocí Azure Traffic Manageru
description: Tento článek vysvětluje, jak nakonfigurovat geografickou metodu směrování provozu pomocí Azure Traffic Manageru
services: traffic-manager
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 8b47d57d21043de97e17b256a39ef88d73fbb1c1
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54197603"
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Konfigurace metody směrování provozu na geografické pomocí Traffic Manageru

Geografickou metodu směrování provozu umožňuje směrovat provoz na konkrétní koncové body na základě geografického umístění, kde mají původ požadavky. V tomto kurzu se dozvíte, jak vytvořit profil služby Traffic Manager pomocí této metody směrování a konfigurace koncových bodů pro příjem provozu z konkrétní zeměpisné oblasti.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

1. V prohlížeči se přihlaste k webu [Azure Portal](http://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/).
2. Klikněte na **Vytvořit prostředek** > **Sítě** > **Profil služby Traffic Manager** > **Vytvořit**.
4. V **vytvořit profil Traffic Manageru**:
    1. Zadejte název pro svůj profil. Tento název musí být jedinečný v rámci zóny trafficmanager.net. Pro přístup k vašeho profilu Traffic Manageru, použijte název DNS <profilename>. trafficmanager.net.
    2. Vyberte **Geographic** metodu směrování.
    3. Vyberte předplatné, které chcete vytvořit tento profil v části.
    4. Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků umístit tento profil v části. Pokud se rozhodnete vytvořit novou skupinu prostředků, použijte **umístění skupiny prostředků** rozevíracího seznamu a zadejte umístění skupiny prostředků. Toto nastavení označuje umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manageru, který se nasadí globálně.
    5. Po kliknutí na **vytvořit**, váš profil Traffic Manageru se vytvoří a nasadí globálně.

![Vytvoření profilu Traffic Manageru](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Přidání koncových bodů

1. Vyhledejte název profilu Traffic Manageru, kterou jste vytvořili v panelu hledání na portálu a klikněte na výsledek při zobrazení.
2. Přejděte do **nastavení** -> **koncové body** v Traffic Manageru.
3. Klikněte na tlačítko **přidat** zobrazíte **přidat koncový bod**.
3. Klikněte na tlačítko **přidat** a **přidat koncový bod** , která se zobrazí, proveďte takto:
4. Vyberte **typ** v závislosti na typu koncového bodu, kterou přidáváte. Pro geografické směrování profily použít v produkčním prostředí důrazně doporučujeme používat typy vnořených koncových bodů obsahujících podřízené profil s více než jeden koncový bod. Další podrobnosti najdete v tématu [nejčastější dotazy o metodách směrování provozu na geografické](traffic-manager-FAQs.md).
5. Zadejte **Název**, podle kterého tento koncový bod rozpoznáte.
6. Některá pole na této stránce jsou závislé na typ koncového bodu, který chcete přidat:
    1. Pokud chcete přidat koncový bod Azure, vyberte **typ cílového prostředku** a **cílové** podle prostředků, které chcete směrovat provoz do
    2. Pokud chcete přidat **externí** koncový bod, zadejte **plně kvalifikovaný název domény (FQDN)** pro koncový bod služby.
    3. Pokud přidáváte **vnořené koncový bod**, vyberte **cílový prostředek** , který odpovídá podřízené profil, který chcete použít a zadat **minimální počet koncových bodů podřízené**.
7. V části geomapping pomocí rozevírací dolů můžete přidat v oblastech z místo, kam chcete, aby se provoz odesílal do tohoto koncového bodu. Je nutné přidat alespoň jedné oblasti a může mít více oblastí namapována.
8. Tento postup opakujte pro všechny koncové body, které chcete přidat pod tento profil

![Přidání koncového bodu služby Traffic Manager](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Použít profil Traffic Manageru
1.  Na panelu hledání na portálu vyhledejte **profil služby Traffic Manager** název, který jste vytvořili v předchozí části a klikněte na profil traffic Manageru ve výsledcích, který je zobrazeno.
2. Klikněte na **Přehled**.
3. V části **Profil služby Traffic Manager** se zobrazí název DNS nově vytvořeného profilu služby Traffic Manager. Tímto lze všechny klienty (například tak, že přejdete do ní pomocí webového prohlížeče) získat směrovat na správný koncový bod jako Určuje typ směrování.  V případě geografické směrování Traffic Manageru zabývá zdrojovou IP adresu příchozího požadavku a určuje oblast, ze kterého pocházejí. Pokud tuto oblast je namapována na koncový bod, provoz se směruje do něj. Pokud tato oblast se nemapuje na koncový bod, Traffic Manager vrátí odpověď na dotaz NODATA.

## <a name="next-steps"></a>Další postup

- Další informace o [metodu směrování provozu Geographic](traffic-manager-routing-methods.md#geographic).
- Zjistěte, jak [test nastavení Traffic Manageru](traffic-manager-testing-settings.md).

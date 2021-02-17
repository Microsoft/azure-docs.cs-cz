---
title: Kurz – partnerské místní prostředí do privátního cloudu
description: Naučte se, jak vytvořit partnerský vztah ExpressRoute Global Reach k privátnímu cloudu v řešení Azure VMware.
ms.topic: tutorial
ms.date: 01/27/2021
ms.openlocfilehash: 04be94849f1cd78ff915ca271845b283c8cfc64c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558789"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Kurz: partnerských místních prostředí do privátního cloudu

ExpressRoute Global Reach připojuje vaše místní prostředí k vašemu privátnímu cloudu řešení Azure VMware. Připojení ExpressRoute Global Reach se naváže mezi okruhem ExpressRoute privátního cloudu a existujícím připojením ExpressRoute k místním prostředím. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Azure Portal můžete použít k povolení partnerského vztahu mezi místními a privátními cloudy ExpressRoute Global Reach.


## <a name="before-you-begin"></a>Než začnete

Než povolíte připojení mezi dvěma okruhy ExpressRoute pomocí ExpressRoute Global Reach, přečtěte si dokumentaci, jak [Povolit připojení v různých předplatných Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Požadavky

- Samostatný fungující okruh ExpressRoute, který slouží k připojení místních prostředí k Azure.
- Zajistěte, aby všechny brány, včetně služby poskytovatele ExpressRoute, podporovaly číslo autonomního systému (ASN) na 4 bajtech. Řešení Azure VMware používá pro reklamní trasy 4 čísla ASN veřejné služby.


## <a name="create-an-expressroute-authorization-key-in-the-on-premises-expressroute-circuit"></a>Vytvoření autorizačního klíče ExpressRoute v místním okruhu ExpressRoute

1. V okně **okruhy ExpressRoute** v části nastavení vyberte **autorizace**.

2. Zadejte název autorizačního klíče a vyberte **Uložit**.

    :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="Vyberte autorizace a zadejte název autorizačního klíče.":::
  
     Po vytvoření se nový klíč zobrazí v seznamu autorizačních klíčů pro daný okruh.
 
 4. Poznamenejte si autorizační klíč a ID ExpressRoute. Použijete je v dalším kroku k dokončení partnerského vztahu.
 
 
 ## <a name="peer-private-cloud-to-on-premises"></a>Privátní cloud peer-to-premises

1. V **přehledu** privátní cloud vyberte v části spravovat možnost **připojení > ExpressRoute Global REACH > přidat**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="V nabídce vyberte připojení, kartu Global Reach ExpressRoute a pak přidejte.":::

2. Zadejte ID ExpressRoute a autorizační klíč vytvořený v předchozí části.

    :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Zadejte ID ExpressRoute a autorizační klíč a pak vyberte vytvořit.":::

3. Vyberte **Vytvořit**. Nové připojení se zobrazí v seznamu místní cloudová připojení.  

>[!TIP]
>Připojení můžete ze seznamu odstranit nebo odpojit výběrem možnosti **Další**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Odpojení nebo odstranění místního připojení":::


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak povolit partnerské vztahy mezi místními a ExpressRoute cloudem Global Reach. 

Přejděte k dalšímu kurzu, kde se dozvíte, jak nasadit a nakonfigurovat řešení VMware HCX pro privátní cloud řešení Azure VMware.

> [!div class="nextstepaction"]
> [Nasazení a konfigurace VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->

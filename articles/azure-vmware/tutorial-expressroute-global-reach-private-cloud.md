---
title: Kurz – partnerské místní prostředí do privátního cloudu
description: Naučte se, jak vytvořit partnerský vztah ExpressRoute Global Reach k privátnímu cloudu v řešení Azure VMware.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 07b7e1c2636f3754eda56af574586a1027403d3e
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576723"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Kurz: partnerských místních prostředí do privátního cloudu

ExpressRoute Global Reach připojuje vaše místní prostředí k vašemu privátnímu cloudu řešení Azure VMware. Připojení ExpressRoute Global Reach se naváže mezi okruhem ExpressRoute privátního cloudu a existujícím připojením ExpressRoute k místním prostředím. 

Okruh ExpressRoute, který použijete při [konfiguraci cloudových sítí Azure-to-Private](tutorial-configure-networking.md) , vyžaduje vytvoření a používání autorizačních klíčů při zapojení do bran ExpressRoute nebo k jiným okruhům ExpressRoute pomocí Global REACH. Z okruhu ExpressRoute už budete používat jeden autorizační klíč a v tomto kurzu vytvoříte druhý pro partnerský vztah s vaším místním ExpressRoute okruhem.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte druhý autorizační klíč pro _okruh 2_, ExpressRoute okruh privátního cloudu.
> * Použijte [Azure Portal](#azure-portal-method) nebo rozhraní příkazového [řádku Azure CLI v Cloud Shell](#azure-cli-in-a-cloud-shell-method) v rámci předplatného _okruhu 1_ , aby bylo možné místní Cloud ExpressRoute Global REACH partnerského vztahu typu peer-to-Private.


## <a name="before-you-begin"></a>Než začnete

Než povolíte připojení mezi dvěma okruhy ExpressRoute pomocí ExpressRoute Global Reach, přečtěte si dokumentaci, jak [Povolit připojení v různých předplatných Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Požadavky

1. Navázalo se připojení k privátnímu cloudu řešení Azure VMware pomocí okruhu ExpressRoute s použitím brány ExpressRoute ve službě Azure Virtual Network (VNet) – což je _okruh 2_ od procedur partnerských vztahů.  
1. Samostatný fungující okruh ExpressRoute, který slouží k připojení místních prostředí k Azure – což je _okruh 1_ z perspektivy postupy partnerských vztahů.
1. A/29 překrývající se [blok síťových adres](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) pro ExpressRoute Global REACH partnerský vztah.

> [!TIP]
> V souvislosti s těmito požadavky je váš místní okruh ExpressRoute _okruhem 1_a váš ExpressRoute okruh privátního cloudu je v jiném předplatném a _okruhu_s popiskem 2. 


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>Vytvoření autorizačního klíče ExpressRoute v privátním cloudu

1. V **přehledu**privátní cloud vyberte v části spravovat možnost **připojení > ExpressRoute > vyžádejte si autorizační klíč**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Vyberte možnost připojení > ExpressRoute > požádejte o autorizační klíč, aby zahájí nový požadavek.":::

2. Zadejte název autorizačního klíče a vyberte **vytvořit**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Vyberte možnost připojení > ExpressRoute > požádejte o autorizační klíč, aby zahájí nový požadavek.":::

   Po vytvoření se nový klíč zobrazí v seznamu autorizačních klíčů pro privátní cloud. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Vyberte možnost připojení > ExpressRoute > požádejte o autorizační klíč, aby zahájí nový požadavek.":::

3. Poznamenejte si autorizační klíč a ID ExpressRoute spolu s blokem adresy/29. Použijete je v dalším kroku k dokončení partnerského vztahu. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Privátní cloud peer-to-premises pomocí autorizačního klíče

Teď, když jste vytvořili autorizační klíč pro okruh privátního cloudu ExpressRoute, můžete ho poslouchat pomocí místního okruhu ExpressRoute.  Partnerský vztah se provádí z perspektivy místního okruhu ExpressRoute v [Azure Portal](#azure-portal-method) nebo pomocí rozhraní příkazového [řádku Azure CLI ve Cloud Shell](#azure-cli-in-a-cloud-shell-method). V obou metodách použijete ID prostředku a autorizační klíč vašeho okruhu ExpressRoute privátního cloudu, který jste vytvořili v předchozích krocích, abyste mohli partnerský vztah dokončit.

### <a name="azure-portal-method"></a>Azure Portal – metoda

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí stejného předplatného jako místní okruh ExpressRoute.

1. V **přehledu**privátní cloud vyberte v části spravovat možnost **připojení > ExpressRoute Global REACH > přidat**.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Vyberte možnost připojení > ExpressRoute > požádejte o autorizační klíč, aby zahájí nový požadavek.":::

1. Místní cloudové připojení můžete vytvořit jedním z následujících způsobů:

   - V seznamu vyberte okruh ExpressRoute.
   - Pokud máte ID okruhu, zkopírujte ho a vložte ho.

1. Vyberte **Připojit**. Nové připojení se zobrazí v seznamu místní cloudová připojení.  

>[!TIP]
>Připojení můžete ze seznamu odstranit nebo odpojit výběrem možnosti **Další**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Vyberte možnost připojení > ExpressRoute > požádejte o autorizační klíč, aby zahájí nový požadavek.":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Rozhraní příkazového řádku Azure v metodě Cloud Shell

Vylepšili jsme příkazy rozhraní [příkazového řádku](../expressroute/expressroute-howto-set-global-reach-cli.md) s konkrétními podrobnostmi a příklady, které vám pomůžou nakonfigurovat Global REACH partnerský vztah mezi místními prostředími do privátního cloudu řešení Azure VMware.  

> [!TIP]  
> V případě zkrácení ve výstupu příkazu Azure CLI můžou tyto pokyny použít [ `–query` argument ke spuštění dotazu JMESPath, aby se zobrazily jenom požadované výsledky](https://docs.microsoft.com/cli/azure/query-azure-cli).


1. Přihlaste se k Azure Portal pomocí stejného předplatného jako místní okruh ExpressRoute a otevřete Cloud Shell. Nechejte prostředí v bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Vyberte možnost připojení > ExpressRoute > požádejte o autorizační klíč, aby zahájí nový požadavek.":::
 
2. Na příkazovém řádku zadejte příkaz Azure CLI pro vytvoření partnerského vztahu s použitím konkrétních informací a ID prostředku, autorizačního klíče a/29 síťového bloku CIDR. 

   Následuje příklad příkazu, který budete používat, a výstupu indikující úspěšné partnerské vztahy. Vzorový příkaz je založený na příkazu použitém v [kroku 3 tématu "povolení připojení mezi okruhy ExpressRoute v různých předplatných Azure"](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Vyberte možnost připojení > ExpressRoute > požádejte o autorizační klíč, aby zahájí nový požadavek.":::
 
   Nyní byste měli být schopni se připojit z místních prostředí k privátnímu cloudu prostřednictvím partnerského vztahu ExpressRoute Global Reach.

> [!TIP]
> Partnerský vztah, který jste právě vytvořili, můžete odstranit pomocí [možnosti zakázat připojení mezi místními sítěmi](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) .


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit druhý autorizační klíč pro okruh privátního cloudu ExpressRoute a povolit partnerský vztah místního cloudu ExpressRoute Global Reach. 

Přejděte k dalšímu kurzu, kde se dozvíte, jak nasadit a nakonfigurovat řešení VMware HCX pro privátní cloud řešení Azure VMware.

> [!div class="nextstepaction"]
> [Nasazení a konfigurace HCX VMware](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->

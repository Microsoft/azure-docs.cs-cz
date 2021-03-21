---
title: Kurz – partnerské místní prostředí do privátního cloudu
description: Naučte se, jak vytvořit partnerský vztah ExpressRoute Global Reach k privátnímu cloudu v řešení Azure VMware.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: ae92bf89a08c5fade8757e3ee596c4ed4a5e6389
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494145"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Kurz: partnerských místních prostředí do privátního cloudu

ExpressRoute Global Reach připojuje vaše místní prostředí k vašemu privátnímu cloudu řešení Azure VMware. Připojení ExpressRoute Global Reach se naváže mezi okruhem ExpressRoute privátního cloudu a existujícím připojením ExpressRoute k místním prostředím. 

Okruh ExpressRoute, který použijete při [konfiguraci sítě pro privátní cloud VMware v Azure](tutorial-configure-networking.md) , vyžaduje, abyste vytvořili a použili autorizační klíče.  Z okruhu ExpressRoute už budete používat jeden autorizační klíč a v tomto kurzu vytvoříte druhý autorizační klíč pro partnerský vztah k místnímu okruhu ExpressRoute.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte druhý autorizační klíč pro _okruh 2_, ExpressRoute okruh privátního cloudu.
> * Pomocí Azure Portal nebo rozhraní příkazového řádku Azure v rámci Cloud Shell v rámci předplatného _okruhu 1_ povolte partnerské vztahy mezi místními a privátními cloudy ExpressRoute Global REACH.


## <a name="before-you-begin"></a>Než začnete

Než povolíte připojení mezi dvěma okruhy ExpressRoute pomocí ExpressRoute Global Reach, přečtěte si dokumentaci, jak [Povolit připojení v různých předplatných Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  

## <a name="prerequisites"></a>Předpoklady

- Navázalo se připojení k privátnímu cloudu řešení Azure VMware pomocí okruhu ExpressRoute s použitím brány ExpressRoute ve službě Azure Virtual Network (VNet) – což je okruh 2 od procedur partnerských vztahů.
- Samostatný fungující okruh ExpressRoute, který slouží k připojení místních prostředí k Azure – což je okruh 1 z perspektivy postupy partnerských vztahů.
- A/29 překrývající se [blok síťových adres](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) pro ExpressRoute Global REACH partnerský vztah.
- Zajistěte, aby všechny brány, včetně služby poskytovatele ExpressRoute, podporovaly číslo autonomního systému (ASN) na 4 bajtech. Řešení Azure VMware používá pro reklamní trasy 4 čísla ASN veřejné služby.

>[!IMPORTANT]
>V souvislosti s těmito požadavky je váš místní okruh ExpressRoute _okruhem 1_ a váš ExpressRoute okruh privátního cloudu je v jiném předplatném a _okruhu_ s popiskem 2.

## <a name="create-an-expressroute-authorization-key-in-the-on-premises-circuit"></a>Vytvoření autorizačního klíče ExpressRoute v místním okruhu

[!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]
 
## <a name="peer-private-cloud-to-on-premises-with-authorization-key"></a>Privátní cloud peer-to-premises s autorizačním klíčem
Teď, když jste vytvořili autorizační klíč pro okruh privátního cloudu ExpressRoute, můžete ho poslouchat pomocí místního okruhu ExpressRoute. Partnerský vztah se provádí z perspektivy místního okruhu ExpressRoute v **Azure Portal** nebo pomocí rozhraní příkazového **řádku Azure CLI ve Cloud Shell**. Pomocí obou metod můžete k dokončení partnerského vztahu použít ID prostředku a autorizační klíč svého ExpressRoute okruhu privátního cloudu.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)
 
1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí stejného předplatného jako místní okruh ExpressRoute.

1. V **přehledu** privátního cloudu v části Spravovat vyberte možnost **připojení**  >  **ExpressRoute Global REACH**  >  **Přidat**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Snímek obrazovky s kartou ExpressRoute Global Reach v privátním cloudu řešení Azure VMware.":::

1. Vytvořte místní cloudové připojení. Proveďte jednu z následujících akcí a pak vyberte **vytvořit**:

   - V seznamu vyberte **okruh ExpressRoute** nebo
   - Pokud máte ID okruhu, vložte ho do pole a zadejte autorizační klíč.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Zadejte ID ExpressRoute a autorizační klíč a pak vyberte vytvořit.":::   
   
   Nové připojení se zobrazí v seznamu místní cloudová připojení.

>[!TIP]
>Připojení můžete ze seznamu odstranit nebo odpojit výběrem možnosti **Další**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Odpojení nebo odstranění místního připojení":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vylepšili jsme příkazy rozhraní [příkazového řádku](../expressroute/expressroute-howto-set-global-reach-cli.md) s konkrétními podrobnostmi a příklady, které vám pomůžou nakonfigurovat Global REACH partnerský vztah mezi místními prostředími do privátního cloudu řešení Azure VMware.

>[!TIP]
>V případě zkrácení ve výstupu příkazu Azure CLI můžou tyto pokyny použít [ `–query` argument](https://docs.microsoft.com/cli/azure/query-azure-cli) ke spuštění dotazu JMESPath, aby se zobrazily jenom požadované výsledky.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí stejného předplatného jako místní okruh ExpressRoute. 

1. Otevřete Cloud Shell a nechejte prostředí bash.

   :::image type="content" source="media/expressroute-global-reach/azure-portal-cloud-shell.png" alt-text="Snímek obrazovky zobrazující Azure Portal Cloud Shell":::

1. Vytvořte partnerský vztah na okruhu 1 a předejte mu ID prostředku okruhu 2 a autorizační klíč. 

   ```azurecli-interactive
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Snímek obrazovky zobrazující příkaz a výsledky úspěšného partnerského vztahu mezi okruhem 1 a okruhem 2.":::

Z místních prostředí se můžete připojit k privátnímu cloudu prostřednictvím partnerského vztahu ExpressRoute Global Reach.

>[!TIP]
>Vytvoření partnerského vztahu můžete odstranit podle pokynů v tématu [zákaz připojení mezi místními sítěmi](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) .


---

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak povolit partnerské vztahy mezi místními a ExpressRoute cloudem Global Reach. 

Přejděte k dalšímu kurzu, kde se dozvíte, jak nasadit a nakonfigurovat řešení VMware HCX pro privátní cloud řešení Azure VMware.

> [!div class="nextstepaction"]
> [Nasazení a konfigurace VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->

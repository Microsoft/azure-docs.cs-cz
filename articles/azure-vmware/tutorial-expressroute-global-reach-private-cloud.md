---
title: Kurz – partnerské místní prostředí do privátního cloudu
description: Naučte se, jak vytvořit partnerský vztah ExpressRoute Global Reach k privátnímu cloudu v řešení Azure VMware.
ms.topic: tutorial
ms.date: 1/5/2021
ms.openlocfilehash: 613aece6ed548f70840349e017de4416883d6cf3
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97913154"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Kurz: partnerských místních prostředí do privátního cloudu

ExpressRoute Global Reach připojuje vaše místní prostředí k vašemu privátnímu cloudu řešení Azure VMware. Připojení ExpressRoute Global Reach se naváže mezi okruhem ExpressRoute privátního cloudu a existujícím připojením ExpressRoute k místním prostředím. 

Okruh ExpressRoute, který používáte při [konfiguraci cloudových sítí Azure na privátní](tutorial-configure-networking.md) , vyžaduje vytvoření a používání autorizačních klíčů.  Už jste v okruhu ExpressRoute použili jeden autorizační klíč a v tomto kurzu vytvoříte druhý pro partnerský vztah s vaším místním okruhem ExpressRoute.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte druhý autorizační klíč pro _okruh 2_, ExpressRoute okruh privátního cloudu.
> * Použijte [Azure Portal](#azure-portal-method) nebo rozhraní příkazového [řádku Azure CLI v Cloud Shell](#azure-cli-in-a-cloud-shell-method) v rámci předplatného _okruhu 1_ , aby bylo možné místní Cloud ExpressRoute Global REACH partnerského vztahu typu peer-to-Private.


## <a name="before-you-begin"></a>Než začnete

Než povolíte připojení mezi dvěma okruhy ExpressRoute pomocí ExpressRoute Global Reach, přečtěte si dokumentaci, jak [Povolit připojení v různých předplatných Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Požadavky

- Navázalo se připojení k privátnímu cloudu řešení Azure VMware pomocí okruhu ExpressRoute s použitím brány ExpressRoute ve službě Azure Virtual Network (VNet) – což je _okruh 2_ od procedur partnerských vztahů.  
- Samostatný fungující okruh ExpressRoute, který slouží k připojení místních prostředí k Azure – což je _okruh 1_ z perspektivy postupy partnerských vztahů.
- A/29 překrývající se [blok síťových adres](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) pro ExpressRoute Global REACH partnerský vztah.
- Ujistěte se, že všechny směrovače, včetně služby poskytovatele ExpressRoute, podporují 4 bajty autonomního systému (ASN). Řešení Azure VMware využívá ke inzerci tras 4 čísla ASN Public.

> [!TIP]
> V souvislosti s těmito požadavky je váš místní okruh ExpressRoute _okruhem 1_ a váš ExpressRoute okruh privátního cloudu je v jiném předplatném a _okruhu_ s popiskem 2. 


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>Vytvoření autorizačního klíče ExpressRoute v privátním cloudu

1. V **přehledu** privátní cloud vyberte v části spravovat možnost **připojení > ExpressRoute > vyžádejte si autorizační klíč**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Vyberte možnost připojení > ExpressRoute > požádejte o autorizační klíč, aby zahájí nový požadavek.":::

2. Zadejte název autorizačního klíče a vyberte **vytvořit**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Vyberte vytvořit a vytvořte nový autorizační klíč. ":::

   Po vytvoření se nový klíč zobrazí v seznamu autorizačních klíčů pro privátní cloud. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Potvrďte, že se nový autorizační klíč zobrazuje v seznamu klíčů pro privátní cloud. ":::

3. Poznamenejte si autorizační klíč a ID ExpressRoute spolu s blokem adresy/29. Použijete je v dalším kroku k dokončení partnerského vztahu. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Privátní cloud peer-to-premises pomocí autorizačního klíče

Teď, když jste vytvořili autorizační klíč pro okruh privátního cloudu ExpressRoute, můžete ho poslouchat pomocí místního okruhu ExpressRoute.  Partnerský vztah se provádí z perspektivy místního okruhu ExpressRoute v [Azure Portal](#azure-portal-method) nebo pomocí rozhraní příkazového [řádku Azure CLI ve Cloud Shell](#azure-cli-in-a-cloud-shell-method). Pomocí obou metod můžete k dokončení partnerského vztahu použít ID prostředku a autorizační klíč svého ExpressRoute okruhu privátního cloudu.

### <a name="azure-portal-method"></a>Azure Portal – metoda

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí stejného předplatného jako místní okruh ExpressRoute.

1. V **přehledu** privátní cloud vyberte v části spravovat možnost **připojení > ExpressRoute Global REACH > přidat**.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="V nabídce vyberte připojení, kartu Global Reach ExpressRoute a pak přidejte.":::

1. Místní cloudové připojení můžete vytvořit jedním z těchto možností:

   - V seznamu vyberte okruh ExpressRoute.
   - Pokud máte ID okruhu, zkopírujte ho a vložte ho.

1. Vyberte **Connect** (Připojit). Nové připojení se zobrazí v seznamu místní cloudová připojení.  

>[!TIP]
>Připojení můžete ze seznamu odstranit nebo odpojit výběrem možnosti **Další**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Odpojení nebo odstranění místního připojení":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Rozhraní příkazového řádku Azure v metodě Cloud Shell

Vylepšili jsme příkazy rozhraní [příkazového řádku](../expressroute/expressroute-howto-set-global-reach-cli.md) s konkrétními podrobnostmi a příklady, které vám pomůžou nakonfigurovat Global REACH partnerský vztah mezi místními prostředími do privátního cloudu řešení Azure VMware.  

> [!TIP]  
> V případě zkrácení ve výstupu příkazu Azure CLI můžou tyto pokyny použít [ `–query` argument ke spuštění dotazu JMESPath, aby se zobrazily jenom požadované výsledky](/cli/azure/query-azure-cli).


1. Přihlaste se k Azure Portal pomocí stejného předplatného jako místní okruh ExpressRoute a otevřete Cloud Shell. Nechejte prostředí v bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Přihlaste se k Azure Portal a otevřete Cloud Shell.":::
 
2. Zadejte příkaz Azure CLI pro vytvoření partnerského vztahu. Použijte konkrétní informace a ID prostředku, autorizační klíč a/29 síťového bloku CIDR. 

   Obrázek ukazuje příklad příkazu, který budete používat, a výstup indikující úspěšné vytvoření partnerského vztahu. Vzorový příkaz je založený na příkazu použitém v [kroku 3 tématu "povolení připojení mezi okruhy ExpressRoute v různých předplatných Azure"](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Vytvoření partnerského vztahu ExpressRoute Global Reach pomocí příkazu Azure CLI ve Cloud Shell.":::
 
   Z místních prostředí se můžete připojit k privátnímu cloudu prostřednictvím partnerského vztahu ExpressRoute Global Reach.

> [!TIP]
> Partnerský vztah, který jste právě vytvořili, můžete odstranit pomocí [možnosti zakázat připojení mezi místními sítěmi](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) .


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit druhý autorizační klíč pro okruh privátního cloudu ExpressRoute. Zjistili jste také, jak povolit partnerské vztahy mezi místními a ExpressRoute cloudem Global Reach. 

Přejděte k dalšímu kurzu, kde se dozvíte, jak nasadit a nakonfigurovat řešení VMware HCX pro privátní cloud řešení Azure VMware.

> [!div class="nextstepaction"]
> [Nasazení a konfigurace VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
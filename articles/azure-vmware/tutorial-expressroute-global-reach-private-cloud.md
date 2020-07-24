---
title: Partnerských místních prostředí do privátního cloudu
description: V tomto kurzu Azure VMware Solution (AVS) vytvoříte ExpressRoute Global Reach partnerský vztah k privátnímu cloudu v rámci služby AVS.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: a9a002eab3219a0db74062570d31595bfcc0d6a3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101560"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Kurz: partnerských místních prostředí do privátního cloudu

ExpressRoute Global Reach připojuje vaše místní prostředí k vašim privátním cloudům. Připojení ExpressRoute Global Reach je navázáno mezi ExpressRoute okruh privátního cloudu a stávající připojení ExpressRoute k místním prostředím.  K dispozici jsou pokyny pro konfiguraci Global Reach ExpressRoute pomocí rozhraní příkazového řádku Azure CLI a prostředí PowerShell a vylepšili jsme [příkazy rozhraní příkazového řádku](../expressroute/expressroute-howto-set-global-reach-cli.md) s konkrétními podrobnostmi a příklady, které vám pomůžou nakonfigurovat Global REACH ExpressRoute partnerský vztah mezi místními prostředími do privátního cloudu řešení Azure VMware (AVS).   

Než povolíte připojení mezi dvěma okruhy ExpressRoute pomocí ExpressRoute Global Reach, přečtěte si dokumentaci, jak [Povolit připojení v různých předplatných Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  Okruh ExpressRoute, který použijete při [konfiguraci cloudových sítí Azure-to-Private](tutorial-configure-networking.md) , vyžaduje vytvoření a používání autorizačních klíčů při zapojení do bran ExpressRoute nebo k jiným okruhům ExpressRoute pomocí Global REACH. Už budete používat jeden autorizační klíč ze okruhu ExpressRoute a vytvoříte druhý pro partnerský vztah s vaším místním okruhem ExpressRoute.

> [!TIP]
> V souvislosti s těmito pokyny je váš místní okruh ExpressRoute _okruhem 1_a váš ExpressRoute okruh privátního cloudu je v jiném předplatném a _okruhu_s popiskem 2. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Využijte stávající pokyny pro správu okruhů ExpressRoute a partnerských vztahů ExpressRoute Global Reach.
> * Vytvoření autorizačního klíče pro _okruh 2_, ExpressRoute okruh privátního cloudu
> * Použití rozhraní příkazového řádku Azure v Cloud Shell v předplatném _okruhu 1_ pro povolení partnerského vztahu mezi místními a privátními cloudy ExpressRoute Global REACH

## <a name="prerequisites"></a>Předpoklady

Předpoklady pro tento kurz:
- Privátní cloud se svým okruhem ExpressRoute s bránou ExpressRoute ve službě Azure Virtual Network (VNet) – to je _okruh 2_ z perspektivy partnerských postupů.
- Samostatný fungující okruh ExpressRoute, který slouží k připojení místních prostředí k Azure – jedná se o _okruh 1_ z perspektivy partnerských postupů.
- A/29 překrývající se [blok síťových adres](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) pro ExpressRoute Global REACH partnerský vztah.

## <a name="create-an-expressroute-authorization-key-in-the-avs-private-cloud"></a>Vytvoření autorizačního klíče ExpressRoute v privátním cloudu služby AVS

1. V **přehledu**privátní cloud vyberte v části spravovat možnost **připojení > ExpressRoute > vyžádejte si autorizační klíč**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Výběr možnosti připojení > ExpressRoute > vyžádání autorizačního klíče pro spuštění nové žádosti":::

2. Zadejte název autorizačního klíče a vyberte **vytvořit**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Kliknutím na vytvořit vytvořte nový autorizační klíč.":::

   Po vytvoření se nový klíč zobrazí v seznamu autorizačních klíčů pro privátní cloud. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Potvrďte, že se nový autorizační klíč zobrazuje v seznamu klíčů pro privátní cloud.":::

3. Poznamenejte si autorizační klíč a ID ExpressRoute spolu s blokem adresy/29. Použijete je v dalším kroku k dokončení partnerského vztahu. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Privátní cloud peer-to-premises pomocí autorizačního klíče

Teď, když jste vytvořili autorizační klíč pro okruh privátního cloudu ExpressRoute, můžete ho poslouchat pomocí místního okruhu ExpressRoute.  Partnerský vztah se provádí z perspektivy místního okruhu ExpressRoute pomocí rozhraní příkazového řádku Azure CLI v Cloud Shell a ID prostředku a autorizační klíč ExpressRoute okruhu vašeho privátního cloudu, který jste vytvořili v předchozích krocích.

> [!TIP]  
> V případě zkrácení ve výstupu příkazu Azure CLI můžou tyto pokyny použít [ `–query` argument ke spuštění dotazu JMESPath, aby se zobrazily jenom požadované výsledky](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).


1. Přihlaste se k Azure Portal pomocí stejného předplatného jako místní okruh ExpressRoute a otevřete Cloud Shell. Nechejte prostředí v bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Přihlaste se k Azure Portal a otevřete Cloud Shell.":::
 
2. Na příkazovém řádku zadejte příkaz Azure CLI pro vytvoření partnerského vztahu s použitím konkrétních informací a ID prostředku, autorizačního klíče a/29 síťového bloku CIDR. 

   Následuje příklad příkazu, který budete používat, a výstupu indikující úspěšné partnerské vztahy. Vzorový příkaz je založený na příkazu použitém v [kroku 3 tématu "povolení připojení mezi okruhy ExpressRoute v různých předplatných Azure"](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Vytvoření partnerského vztahu ExpressRoute Global Reach pomocí příkazu Azure CLI ve Cloud Shell.":::
 
   Nyní byste měli být schopni se připojit z místních prostředí k privátnímu cloudu prostřednictvím partnerského vztahu ExpressRoute Global Reach.

> [!TIP]
> Partnerský vztah, který jste právě vytvořili, můžete odstranit pomocí [možnosti zakázat připojení mezi místními sítěmi](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) .


## <a name="next-steps"></a>Další kroky

Pokud plánujete použít rozšíření HCX (Hybrid Cloud Extension) k migraci úloh virtuálních počítačů do privátního cloudu, použijte postup [Řešení Azure VMware pro instalaci HCX](hybrid-cloud-extension-installation.md) .


<!-- LINKS - external-->

<!-- LINKS - internal -->

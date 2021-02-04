---
title: 'Azure ExpressRoute: Konfigurace Global Reach pomocí Azure Portal'
description: Tento článek vám pomůže propojit okruhy ExpressRoute dohromady a vytvořit privátní síť mezi místními sítěmi a povolit Global Reach pomocí Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: 8366978d50875389ce872c2d1402f0defa2a7371
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539345"
---
# <a name="configure-expressroute-global-reach-using-the-azure-portal"></a>Konfigurace Global Reach ExpressRoute pomocí Azure Portal

Tento článek vám pomůže nakonfigurovat Global Reach ExpressRoute pomocí prostředí PowerShell. Další informace najdete v tématu [ExpressRouteRoute Global REACH](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Než začnete

Než začnete s konfigurací, potvrďte následující kritéria:

* Rozumíte [pracovním](expressroute-workflows.md)postupům zřizování okruhů ExpressRoute.
* Vaše okruhy ExpressRoute jsou ve stavu zřizování.
* Privátní partnerské vztahy Azure se konfigurují na okruhech ExpressRoute.
* Pokud chcete spustit prostředí PowerShell místně, ověřte, zda je v počítači nainstalována nejnovější verze Azure PowerShell.

## <a name="identify-circuits"></a>Identifikace okruhů

1. V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

2. Identifikujte okruhy ExpressRoute, které chcete použít. Můžete povolit ExpressRoute Global Reach mezi soukromým partnerským vztahem všech dvou okruhů ExpressRoute, pokud se nacházejí v podporovaných zemích nebo oblastech. Okruhy se musí vytvořit v různých umístěních partnerského vztahu. 

   * Pokud vaše předplatné vlastní oba okruhy, můžete zvolit buď okruh, aby se konfigurace spustila v následujících oddílech.
   * Pokud se dva okruhy nacházejí v různých předplatných Azure, budete potřebovat autorizaci z jednoho předplatného Azure. Pak předáte autorizační klíč při spuštění příkazu konfigurace v jiném předplatném Azure.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="Seznam okruhů ExpressRoute":::

## <a name="enable-connectivity"></a>Povolit připojení

Povolte konektivitu mezi místními sítěmi. Existují samostatné sady instrukcí pro okruhy, které jsou ve stejném předplatném Azure, a okruhy, které jsou různými předplatnými.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Okruhy ExpressRoute ve stejném předplatném Azure

1. Vyberte konfiguraci **privátního** partnerského vztahu Azure. 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="ExpressRoute peering – přehled":::

1. Zaškrtněte políčko **povolit Global REACH** a pak vyberte **Přidat Global REACH** . otevře se stránka *Přidat konfiguraci Global REACH* .

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Povolit globální oslovení z privátního partnerského vztahu":::

1. Na stránce *Přidat konfiguraci Global REACH* zadejte název této konfigurace. Vyberte *okruh ExpressRoute* , ke kterému chcete připojit tento okruh, a pro *Global REACH podsíť* zadejte do **/29 IPv4** . IP adresy v této podsíti používáme k navázání připojení mezi dvěma okruhy ExpressRoute. Nepoužívejte adresy v této podsíti ve virtuálních sítích Azure ani ve vaší místní síti. Vyberte **Přidat** a přidejte okruh do konfigurace privátního partnerského vztahu.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="Konfigurační stránka Global Reach":::

1. Výběrem **Uložit** dokončete konfiguraci Global REACH. Po dokončení operace budete mít propojení mezi dvěma místními sítěmi prostřednictvím obou okruhů ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Ukládá se konfigurace privátního partnerského vztahu.":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Okruhy ExpressRoute v různých předplatných Azure

Pokud tyto dva okruhy nejsou ve stejném předplatném Azure, budete potřebovat autorizaci. V následující konfiguraci se autorizace vygenerovala z předplatného okruhu 2. Autorizační klíč se pak předává okruhu 1.

1. Vygenerujte autorizační klíč.

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="Generovat autorizační klíč"::: 

   Poznamenejte si ID prostředku okruhu okruhu 2 a autorizační klíč.

1. Vyberte konfiguraci **privátního** partnerského vztahu Azure. 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Přehled partnerského vztahu okruhu 1":::

1. Zaškrtněte políčko **povolit Global REACH** a pak vyberte **Přidat Global REACH** . otevře se stránka *Přidat konfiguraci Global REACH* .

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Povolit globální přístup ze okruhu 1":::

1. Na stránce *Přidat konfiguraci Global REACH* zadejte název této konfigurace. Zaškrtněte políčko **uplatnit autorizaci** . Zadejte **autorizační klíč** a **ID okruhu ExpressRoute** , který jste vygenerovali a získali v kroku 1. Pak pro *Global REACH podsíť* zadejte **IPv4** . IP adresy v této podsíti používáme k navázání připojení mezi dvěma okruhy ExpressRoute. Nepoužívejte adresy v této podsíti ve virtuálních sítích Azure ani ve vaší místní síti. Vyberte **Přidat** a přidejte okruh do konfigurace privátního partnerského vztahu.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="Přidat Global Reach s autorizačním klíčem":::

1. Výběrem **Uložit** dokončete konfiguraci Global REACH. Po dokončení operace budete mít propojení mezi dvěma místními sítěmi prostřednictvím obou okruhů ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Ukládá se konfigurace privátního partnerského vztahu na okruhu 1.":::

## <a name="verify-the-configuration"></a>Ověření konfigurace

Výběrem *privátního partnerského vztahu* v konfiguraci okruhu ExpressRoute ověřte konfiguraci Global REACH. Pokud je konfigurace správně nakonfigurovaná, měla by vypadat takto:

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="Ověřit konfiguraci Global Reach":::

## <a name="disable-connectivity"></a>Zákaz připojení

Pokud se Global Reach zakážete, máte dvě možnosti. Pokud chcete zakázat připojení mezi všemi okruhy, zrušte zaškrtnuté políčka **povolit Global REACH** , abyste mohli zakázat připojení mezi všemi okruhy. Pokud chcete zakázat připojení mezi jednotlivými okruhy, vyberte tlačítko Odstranit vedle *názvu Global REACH* a odeberte mezi nimi připojení. Pak vyberte **Save (Uložit** ) a dokončete operaci.

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="Zakázat konfiguraci Global Reach":::

Po dokončení operace už nebudete mít připojení mezi vaší místní sítí přes okruhy ExpressRoute.

## <a name="next-steps"></a>Další kroky
1. [Další informace o ExpressRoute Global Reach](expressroute-global-reach.md)
2. [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Propojení okruhu ExpressRoute k virtuální síti Azure](expressroute-howto-linkvnet-arm.md)

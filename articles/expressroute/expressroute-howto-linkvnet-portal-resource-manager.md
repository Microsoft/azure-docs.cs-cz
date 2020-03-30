---
title: 'ExpressRoute: Propojení virtuální sítě s okruhem: Portál Azure'
description: Připojte virtuální síť k okruhu Azure ExpressRoute. Kroky s postupy.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 4c7a24ad692086398059d1afd48c8927e9d18582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272913"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Připojení virtuální sítě k okruhu ExpressRoute pomocí portálu
> [!div class="op_single_selector"]
> * [Portál Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – portál Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
> 

Tento článek vám pomůže vytvořit připojení k propojení virtuální sítě s okruhem Azure ExpressRoute pomocí portálu Azure. Virtuální sítě, které připojíte k okruhu Azure ExpressRoute může být buď ve stejném předplatném, nebo mohou být součástí jiného předplatného.

## <a name="before-you-begin"></a>Než začnete

* Před zahájením konfigurace zkontrolujte [požadavky](expressroute-prerequisites.md)na směrování , [požadavky na směrování](expressroute-routing.md)a pracovní [postupy.](expressroute-workflows.md)

* Musí mít aktivní okruh ExpressRoute.
  * Podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a mějte obvod povolený poskytovatelem připojení.
  * Ujistěte se, že máte azure privátní partnerský vztah nakonfigurovaný pro váš okruh. Informace o pokynech partnerského vztahu a směrování naleznete v článku Vytvořit a upravit partnerský vztah pro okruh [ExpressRoute.](expressroute-howto-routing-portal-resource-manager.md)
  * Ujistěte se, že je nakonfigurovaný soukromý partnerský vztah Azure a partnerský vztah Protokolu BGP mezi vaší sítí a Microsoftem je nahoře, abyste mohli povolit připojení od konce do konce.
  * Ujistěte se, že máte vytvořenou a plně zřízené virtuální síť a bránu virtuální sítě. Podle pokynů [vytvořte bránu virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brána virtuální sítě pro ExpressRoute používá GatewayType 'ExpressRoute', ne VPN.

* Ke standardnímu okruhu ExpressRoute můžete propojit až 10 virtuálních sítí. Všechny virtuální sítě musí být ve stejné geopolitické oblasti při použití standardního okruhu ExpressRoute.

* Jedna virtuální síť může být propojena až ke čtyřem okruhům ExpressRoute. Pomocí níže uvedeného procesu vytvořte nový objekt připojení pro každý okruh ExpressRoute, ke kterému se připojujete. Okruhy ExpressRoute může být ve stejném předplatném, různých předplatných nebo kombinací obou.

* Můžete propojit virtuální síť mimo geopolitickou oblast okruhu ExpressRoute nebo připojit větší počet virtuálních sítí k okruhu ExpressRoute, pokud jste povolili doplněk ExpressRoute premium. Další podrobnosti o prémiovém doplňku najdete v [nejčastějších](expressroute-faqs.md) dotazech.

* Než začnete lépe porozumět krokům, můžete [si prohlédnout video.](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Připojení virtuální sítě k okruhu – stejné předplatné

> [!NOTE]
> Informace o konfiguraci protokolu BGP se nezobrazí, pokud zprostředkovatel vrstvy 3 nakonfiguroval vaše partnerské partnerské partnery. Pokud je váš okruh v zřízeném stavu, měli byste být schopni vytvořit připojení.
>

### <a name="to-create-a-connection"></a>Vytvoření připojení

1. Ujistěte se, že váš okruh ExpressRoute a privátní partnerský vztah Azure byly úspěšně nakonfigurovány. Postupujte podle pokynů v [části Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md) a [Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute](expressroute-howto-routing-arm.md). Okruh ExpressRoute by měl vypadat jako následující obrázek:

   [![Snímek obrazovky okruhu ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Zobrazit obvod")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. Nyní můžete spustit zřizování připojení k propojení brány virtuální sítě s okruhem ExpressRoute. Kliknutím na**Přidat** **připojení** > otevřete stránku **Přidat připojení** a nakonfigurujte hodnoty.

   [![Přidání snímku obrazovky připojení](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "Přidat snímek obrazovky připojení")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
3. Po úspěšné konfiguraci připojení se v objektu připojení zobrazí informace o připojení.

   ![Snímek obrazovky objektu připojení](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Připojení virtuální sítě k okruhu – jiné předplatné

Okruh ExpressRoute můžete sdílet mezi více předplatnými. Následující obrázek ukazuje jednoduché schéma fungování sdílení pro okruhy ExpressRoute napříč více předplatnými.

![Připojení napříč předplatnými](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Každý z menších cloudů v rámci velkého cloudu se používá k reprezentaci předplatných, které patří do různých oddělení v rámci organizace.
- Každé oddělení v rámci organizace může používat své vlastní předplatné pro nasazení svých služeb, ale mohou sdílet jeden okruh ExpressRoute a připojit se zpět k místní síti.
- Okruh ExpressRoute může vlastnit jedno oddělení (v tomto příkladu: IT). Další předplatná v rámci organizace můžete použít okruh ExpressRoute a autorizace přidružené k okruhu, včetně předplatných propojených s jinými tenanty Služby Azure Active Directory a registrace smlouvy Enterprise.

  > [!NOTE]
  > Poplatky za připojení a šířku pásma vyhrazeného okruhu budou použity na vlastníka okruhu ExpressRoute. Všechny virtuální sítě sdílejí stejnou šířku pásma.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Správa - O vlastnících obvodů a uživatelích okruhů

"Vlastník okruhu" je oprávněným uživatelem napájení prostředku okruhu ExpressRoute. Vlastník okruhu může vytvořit autorizace, které mohou být uplatněny 'uživatelé okruhu'. Uživatelé okruhu jsou vlastníky bran virtuální sítě, které nejsou ve stejném předplatném jako okruh ExpressRoute. Uživatelé okruhů mohou uplatnit autorizace (jedna autorizace na virtuální síť).

Vlastník obvodu má pravomoc kdykoli upravit a odvolat autorizace. Odvolání autorizace má za následek odstranění všech připojení propojení z předplatného, jehož přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Vytvoření autorizace připojení**

Vlastník okruhu vytvoří autorizaci. Výsledkem je vytvoření autorizačního klíče, který může uživatel okruhu použít k připojení svých bran virtuální sítě k okruhu ExpressRoute. Autorizace je platná pouze pro jedno připojení.

> [!NOTE]
> Každé připojení vyžaduje samostatnou autorizaci.
>

1. Na stránce ExpressRoute klepněte na položku **Autorizace,** zadejte **název** autorizace a klepněte na tlačítko **Uložit**.

   ![Autorizace](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Po uložení konfigurace zkopírujte **ID prostředku** a **autorizační klíč**.

   ![Autorizační klíč](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Odstranění autorizace připojení**

Připojení můžete odstranit výběrem ikony **Odstranit** na stránce pro připojení.

### <a name="circuit-user-operations"></a>Operace uživatele okruhu

Uživatel okruhu potřebuje ID prostředku a autorizační klíč od vlastníka okruhu.

**Uplatnění autorizace připojení**

1. Klikněte na tlačítko **+Nový.**

   ![Klikněte na Nový](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Vyhledejte na webu Marketplace **možnost Připojení,** vyberte ho a klepněte na tlačítko **Vytvořit**.

   ![Hledat připojení](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Ujistěte se, že **typ připojení** je nastaven na "ExpressRoute".
4. Vyplňte podrobnosti a na stránce Základy klikněte na **OK.**

   ![Stránka Základy](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. Na stránce **Nastavení** vyberte **bránu virtuální sítě** a zaškrtněte políčko Uplatnit **autorizaci.**
6. Zadejte **autorizační klíč** a **identifikátor URI okruhu rovnocenných připojení** a pojmenujte připojení. Klikněte na tlačítko **OK**. **Identifikátor URI okruhu partnera** je ID prostředku okruhu ExpressRoute (které najdete v podokně Nastavení vlastností okruhu ExpressRoute).

   ![Stránka Nastavení](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Zkontrolujte informace na stránce **Souhrn** a klepněte na tlačítko **OK**.

**Uvolnění autorizace připojení**

Autorizaci můžete uvolnit odstraněním připojení, které propojuje okruh ExpressRoute s virtuální sítí.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Odstranění připojení k odpojení virtuální sítě

Můžete odstranit připojení a odpojit virtuální síť k okruhu ExpressRoute výběrem ikony **Odstranit** na stránce pro vaše připojení.

## <a name="next-steps"></a>Další kroky
Další informace o expressroute naleznete v [nejčastějších dotazech k expressroute](expressroute-faqs.md).

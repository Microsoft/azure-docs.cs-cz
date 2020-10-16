---
title: 'Kurz: propojení virtuální sítě s okruhem ExpressRoute-Azure Portal'
description: V tomto kurzu se dozvíte, jak vytvořit připojení k propojení virtuální sítě s okruhem Azure ExpressRoute pomocí Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 0ffc9c2ee17862497d3fd986da8e003f7a497056
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107279"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Kurz: připojení virtuální sítě k okruhu ExpressRoute pomocí portálu

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
> 

Tento kurz vám pomůže vytvořit připojení k propojení virtuální sítě s okruhem Azure ExpressRoute pomocí Azure Portal. Virtuální sítě, které se připojují k okruhu Azure ExpressRoute, můžou být ve stejném předplatném nebo být součástí jiného předplatného.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Připojte virtuální síť k okruhu ve stejném předplatném.
> - Připojení virtuální sítě k okruhu v jiném předplatném.
> - Odstraňte propojení mezi virtuální sítí a okruhem ExpressRoute.

## <a name="prerequisites"></a>Předpoklady

* Než začnete s konfigurací, Projděte si [požadavky,](expressroute-prerequisites.md) [požadavky na směrování](expressroute-routing.md)a [pracovní postupy](expressroute-workflows.md) .

* Musí mít aktivní okruh ExpressRoute.
  * Podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a pořiďte okruh povolený vaším poskytovatelem připojení.
  * Ujistěte se, že máte pro váš okruh nakonfigurovaný privátní partnerský vztah Azure. Pokyny pro vytváření partnerských vztahů a směrování najdete v článku [Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) .
  * Zajistěte, aby se nakonfiguroval privátní partnerský vztah Azure a aby se zajistilo vytvoření partnerského vztahu protokolu BGP mezi vaší sítí a Microsoftem pro koncové připojení.
  * Ujistěte se, že máte virtuální síť a že byla vytvořena a plně zřízena Brána virtuální sítě. Postupujte podle pokynů a [vytvořte bránu virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brána virtuální sítě pro ExpressRoute používá GatewayType "ExpressRoute", ne VPN.

* Na standardní okruh ExpressRoute můžete propojit až 10 virtuálních sítí. Pokud používáte standardní okruh ExpressRoute, musí být všechny virtuální sítě ve stejné geopolitické oblasti.

* Jedna virtuální síť může být propojená s až čtyřmi okruhy ExpressRoute. K vytvoření nového objektu připojení pro každý okruh ExpressRoute, ke kterému se připojujete, použijte následující postup. Okruhy ExpressRoute můžou být ve stejném předplatném, různých předplatných nebo kombinaci obou.

* Pokud povolíte doplněk ExpressRoute Premium, můžete propojit virtuální sítě mimo geopolitickou oblast okruhu ExpressRoute. Doplněk Premium vám také umožní připojit více než 10 virtuálních sítí k okruhu ExpressRoute v závislosti na zvolené šířce pásma. Další podrobnosti o doplňku Premium najdete v [nejčastějších dotazech](expressroute-faqs.md) .

* Než začnete lépe pochopit tento postup, můžete si [prohlédnout video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) .

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Připojení virtuální sítě k předplatnému se stejným okruhem

> [!NOTE]
> Pokud poskytovatel vrstvy 3 nakonfiguroval partnerské vztahy, nezobrazí se informace o konfiguraci protokolu BGP. Pokud je váš okruh ve zřízeném stavu, měli byste být schopni vytvářet připojení.
>

### <a name="to-create-a-connection"></a>Vytvoření připojení

1. Ujistěte se, že váš okruh ExpressRoute a soukromý partnerský vztah Azure jsou úspěšně nakonfigurované. Postupujte podle pokynů v tématu [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md) a [Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute](expressroute-howto-routing-arm.md). Okruh ExpressRoute by měl vypadat jako na následujícím obrázku:

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="Snímek obrazovky okruhu ExpressRoute":::

1. Teď můžete začít zřizovat připojení a propojit bránu virtuální sítě s okruhem ExpressRoute. Vyberte **připojení**  >  **Přidat** a otevřete stránku **Přidat připojení** .

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="Snímek obrazovky okruhu ExpressRoute":::

1. Zadejte název připojení a pak vyberte **Další: nastavení >**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-basic.png" alt-text="Snímek obrazovky okruhu ExpressRoute":::

1. Vyberte bránu, která patří do virtuální sítě, kterou chcete propojit s okruhem, a vyberte **zkontrolovat + vytvořit**. Po dokončení ověření vyberte **vytvořit** .

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-settings.png" alt-text="Snímek obrazovky okruhu ExpressRoute":::

1. Po úspěšné konfiguraci připojení se v objektu připojení zobrazí informace o připojení.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="Snímek obrazovky okruhu ExpressRoute":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Připojení virtuální sítě k okruhu jiné předplatné

Okruh ExpressRoute můžete sdílet mezi několika předplatnými. Následující obrázek ukazuje jednoduché schéma, jak sdílení funguje pro okruhy ExpressRoute napříč několika předplatnými.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="Snímek obrazovky okruhu ExpressRoute":::

Každý z menších cloudů ve velkém cloudu se používá k reprezentaci předplatných, která patří do různých oddělení v rámci organizace. Každé oddělení v organizaci používá vlastní předplatné pro nasazení svých služeb – ale může sdílet jeden okruh ExpressRoute pro připojení zpět k místní síti. Jeden oddělení (v tomto příkladu: IT) může vlastnit okruh ExpressRoute. Další předplatná v rámci organizace můžou používat okruh ExpressRoute.

  > [!NOTE]
  > Pro vlastníka okruhu ExpressRoute se použijí poplatky za připojení a šířku pásma pro vyhrazený okruh. Všechny virtuální sítě mají stejnou šířku pásma.
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Správa – informace o vlastníkech okruhů a uživatelích okruhů

' Okruh Owner ' je autorizovaným uživatelem prostředku okruhu ExpressRoute. Vlastník okruhu může vytvořit autorizaci, které může uplatnit "okruh uživatelů". Uživatelé okruhu jsou vlastníky bran virtuální sítě, které nejsou v rámci stejného předplatného jako okruh ExpressRoute. Uživatelé okruhu můžou uplatnit autorizaci (jednu autorizaci na virtuální síť).

Vlastník okruhu má kdykoliv oprávnění upravovat a odvolat. Odvolání výsledků autorizace ve všech připojeních propojení, která se odstraňují z předplatného, jehož přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Vytvoření autorizace připojení**

Vlastník okruhu vytvoří autorizaci, která vytvoří autorizační klíč, který bude používat uživatel okruhu k připojení bran virtuální sítě k okruhu ExpressRoute. Autorizace je platná jenom pro jedno připojení.

> [!NOTE]
> Každé připojení vyžaduje samostatnou autorizaci.
>

1. Na stránce ExpressRoute vyberte **autorizaci** a potom zadejte **název** autorizace a vyberte **Uložit**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="Snímek obrazovky okruhu ExpressRoute":::

2. Po uložení konfigurace zkopírujte **ID prostředku** a **autorizační klíč**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="Snímek obrazovky okruhu ExpressRoute":::

**Odstranění autorizace připojení**

Připojení můžete odstranit tak, že vyberete ikonu **Odstranit** pro autorizační klíč pro vaše připojení.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-authorization-key.png" alt-text="Snímek obrazovky okruhu ExpressRoute":::

Pokud chcete připojení odstranit, ale zachovat autorizační klíč, můžete připojení odstranit ze stránky připojení okruhu.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection-owning-circuit.png" alt-text="Snímek obrazovky okruhu ExpressRoute":::

### <a name="circuit-user-operations"></a>Operace uživatele obvodu

Uživatel okruhu potřebuje ID prostředku a autorizační klíč od vlastníka okruhu.

**Uplatnění autorizace připojení**

1. Vyberte tlačítko **+ vytvořit prostředek** . Vyhledejte **připojení** a vyberte **vytvořit**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="Snímek obrazovky okruhu ExpressRoute":::

1. Ujistěte se, že je *Typ připojení* nastavený na **ExpressRoute**. Vyberte *skupinu prostředků* a *umístění*a pak na stránce základy vyberte **OK** .

    > [!NOTE]
    > Umístění se *musí* shodovat s umístěním brány virtuální sítě, pro který vytváříte připojení.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="Snímek obrazovky okruhu ExpressRoute":::

1. Na stránce **Nastavení** vyberte *bránu virtuální sítě* a zaškrtněte políčko **uplatnit autorizaci** . Zadejte *autorizační klíč* a *identifikátor URI rovnocenného okruhu* a zadejte název připojení. Vyberte **OK**. 
 
    > [!NOTE]
    > *Identifikátor URI partnerského okruhu* je ID prostředku okruhu ExpressRoute (který najdete v podokně nastavení vlastností okruhu ExpressRoute).

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="Snímek obrazovky okruhu ExpressRoute":::

1. Zkontrolujte informace na stránce **Souhrn** a vyberte **OK**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-summary.png" alt-text="Snímek obrazovky okruhu ExpressRoute":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Připojení a odpojení virtuální sítě k okruhu ExpressRoute můžete odstranit tak, že na stránce vyberete ikonu **Odstranit** .

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection.png" alt-text="Snímek obrazovky okruhu ExpressRoute":::

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak připojit virtuální síť k okruhu ve stejném předplatném a v jiném předplatném. Další informace o bráně ExpressRoute najdete v těchto tématech: 

> [!div class="nextstepaction"]
> [O branách virtuální sítě ExpressRoute](expressroute-about-virtual-network-gateways.md)

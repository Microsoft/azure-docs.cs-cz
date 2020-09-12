---
title: 'ExpressRoute: propojení virtuální sítě s okruhem: Azure Portal'
description: Naučte se vytvořit připojení k propojení virtuální sítě s okruhem Azure ExpressRoute pomocí Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/17/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 56508503c199b1f822ce8f181689a236f3a0af18
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89395821"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Připojení virtuální sítě k okruhu ExpressRoute pomocí portálu
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
> 

Tento článek vám pomůže vytvořit připojení k propojení virtuální sítě s okruhem Azure ExpressRoute pomocí Azure Portal. Virtuální sítě, které se připojují k okruhu Azure ExpressRoute, můžou být ve stejném předplatném, nebo můžou být součástí jiného předplatného.

## <a name="before-you-begin"></a>Než začnete

* Než začnete s konfigurací, Projděte si [požadavky,](expressroute-prerequisites.md) [požadavky na směrování](expressroute-routing.md)a [pracovní postupy](expressroute-workflows.md) .

* Musí mít aktivní okruh ExpressRoute.
  * Podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a pořiďte okruh povolený vaším poskytovatelem připojení.
  * Ujistěte se, že máte pro váš okruh nakonfigurovaný privátní partnerský vztah Azure. Pokyny pro vytváření partnerských vztahů a směrování najdete v článku [Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) .
  * Ujistěte se, že je nakonfigurovaný privátní partnerský vztah Azure a že partnerský vztah protokolu BGP mezi vaší sítí a Microsoftem je tak, aby bylo možné povolit kompletní připojení.
  * Ujistěte se, že máte virtuální síť a že byla vytvořena a plně zřízena Brána virtuální sítě. Postupujte podle pokynů a [vytvořte bránu virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brána virtuální sítě pro ExpressRoute používá GatewayType "ExpressRoute", ne VPN.

* Na standardní okruh ExpressRoute můžete propojit až 10 virtuálních sítí. Pokud používáte standardní okruh ExpressRoute, musí být všechny virtuální sítě ve stejné geopolitické oblasti.

* Jedna virtuální síť může být propojená s až čtyřmi okruhy ExpressRoute. Pomocí níže uvedeného postupu vytvořte nový objekt připojení pro každý okruh ExpressRoute, ke kterému se připojujete. Okruhy ExpressRoute můžou být ve stejném předplatném, různých předplatných nebo kombinaci obou.

* Pokud jste povolili doplněk ExpressRoute Premium, můžete propojit virtuální síť mimo geopolitickou oblast okruhu ExpressRoute nebo připojit větší počet virtuálních sítí k okruhu ExpressRoute. Další podrobnosti o doplňku Premium najdete v [nejčastějších dotazech](expressroute-faqs.md) .

* Než začnete lépe pochopit tento postup, můžete si [prohlédnout video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) .

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Připojení virtuální sítě k předplatnému se stejným okruhem

> [!NOTE]
> Pokud poskytovatel vrstvy 3 nakonfiguroval partnerské vztahy, nezobrazí se informace o konfiguraci protokolu BGP. Pokud je váš okruh ve zřízeném stavu, měli byste být schopni vytvářet připojení.
>

### <a name="to-create-a-connection"></a>Vytvoření připojení

1. Ujistěte se, že váš okruh ExpressRoute a soukromý partnerský vztah Azure jsou úspěšně nakonfigurované. Postupujte podle pokynů v tématu [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md) a [Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute](expressroute-howto-routing-arm.md). Okruh ExpressRoute by měl vypadat jako na následujícím obrázku:

   [![Snímek obrazovky okruhu ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Zobrazit okruh")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. Teď můžete začít zřizovat připojení a propojit bránu virtuální sítě s okruhem ExpressRoute. Kliknutím na **připojit**  >  **Přidat** otevřete stránku **Přidat připojení** a pak nakonfigurujte tyto hodnoty.

   [![Obrázek přidání připojení](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "Obrázek přidání připojení")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
3. Po úspěšné konfiguraci připojení se v objektu připojení zobrazí informace o připojení.

   ![Snímek obrazovky objektu připojení](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Připojení virtuální sítě k okruhu jiné předplatné

Okruh ExpressRoute můžete sdílet mezi několika předplatnými. Následující obrázek ukazuje jednoduché schéma, jak sdílení funguje pro okruhy ExpressRoute napříč několika předplatnými.

![Připojení mezi předplatnými](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Každý z menších cloudů ve velkém cloudu se používá k reprezentaci předplatných, která patří do různých oddělení v rámci organizace.
- Každé oddělení v organizaci může používat vlastní předplatné pro nasazení svých služeb, ale může sdílet jeden okruh ExpressRoute pro připojení zpět k místní síti.
- Jeden oddělení (v tomto příkladu: IT) může vlastnit okruh ExpressRoute. Další předplatná v rámci organizace můžou používat okruh ExpressRoute a autorizaci přidruženou k okruhu, včetně předplatných propojených s ostatními Azure Active Directory klienty a registrace smlouva Enterprise.

  > [!NOTE]
  > Pro vlastníka okruhu ExpressRoute se použijí poplatky za připojení a šířku pásma pro vyhrazený okruh. Všechny virtuální sítě mají stejnou šířku pásma.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Správa – informace o vlastníkech okruhů a uživatelích okruhů

' Okruh Owner ' je autorizovaným uživatelem prostředku okruhu ExpressRoute. Vlastník okruhu může vytvořit autorizaci, které může uplatnit "okruh uživatelů". Uživatelé okruhu jsou vlastníky bran virtuální sítě, které nejsou v rámci stejného předplatného jako okruh ExpressRoute. Uživatelé okruhu můžou uplatnit autorizaci (jednu autorizaci na virtuální síť).

Vlastník okruhu má kdykoliv oprávnění upravovat a odvolat. Odvolání výsledků autorizace ve všech připojeních propojení, která se odstraňují z předplatného, jehož přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Vytvoření autorizace připojení**

Vlastník okruhu vytvoří autorizaci. Výsledkem je vytvoření autorizačního klíče, který může uživatel okruhu použít k připojení bran virtuální sítě k okruhu ExpressRoute. Autorizace je platná jenom pro jedno připojení.

> [!NOTE]
> Každé připojení vyžaduje samostatnou autorizaci.
>

1. Na stránce ExpressRoute klikněte na **autorizace** a potom zadejte **název** autorizace a klikněte na **Uložit**.

   ![Autorizace](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Po uložení konfigurace zkopírujte **ID prostředku** a **autorizační klíč**.

   ![Autorizační klíč](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Odstranění autorizace připojení**

Připojení můžete odstranit výběrem ikony **Odstranit** na stránce pro vaše připojení.

### <a name="circuit-user-operations"></a>Operace uživatele obvodu

Uživatel okruhu potřebuje ID prostředku a autorizační klíč od vlastníka okruhu.

**Uplatnění autorizace připojení**

1. Klikněte na tlačítko **+ Nový** .

   ![Klikněte na nový.](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Na webu Marketplace vyhledejte **"připojení"** , vyberte ho a klikněte na **vytvořit**.

   ![Hledat připojení](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Ujistěte se, že je **Typ připojení** nastavený na "ExpressRoute".
4. Vyplňte podrobnosti a klikněte na tlačítko **OK** na stránce základy.

   ![Stránka základy](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. Na stránce **Nastavení** vyberte **bránu virtuální sítě** a zaškrtněte políčko **uplatnit autorizaci** .
6. Zadejte **autorizační klíč** a **identifikátor URI rovnocenného okruhu** a zadejte název připojení. Klikněte na **OK**. **Identifikátor URI partnerského okruhu** je ID prostředku okruhu ExpressRoute (který najdete v podokně nastavení vlastností okruhu ExpressRoute).

   ![Stránka Nastavení](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Zkontrolujte informace na stránce **Souhrn** a klikněte na tlačítko **OK**.

**Uvolnění autorizace připojení**

Autorizaci můžete uvolnit odstraněním připojení, které propojuje okruh ExpressRoute s virtuální sítí.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Odstranění připojení pro odpojení virtuální sítě

Připojení a odpojení virtuální sítě k okruhu ExpressRoute můžete odstranit tak, že na stránce vyberete ikonu **Odstranit** .

## <a name="next-steps"></a>Další kroky
Další informace o ExpressRoute najdete v tématu [ExpressRoute – Nejčastější dotazy](expressroute-faqs.md).

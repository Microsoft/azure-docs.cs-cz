---
title: 'ExpressRoute: propojení virtuální sítě s okruhem: Azure Portal'
description: 'Připojte virtuální síť k okruhu Azure ExpressRoute. Postupy: kroky.'
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 4c7a24ad692086398059d1afd48c8927e9d18582
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272913"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Připojení virtuální sítě k okruhu ExpressRoute pomocí portálu
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
> 

Tento článek vám pomůže vytvořit připojení k propojení virtuální sítě k okruhu Azure ExpressRoute pomocí webu Azure portal. Virtuální sítě, které se připojují k váš okruh Azure ExpressRoute může být ve stejném předplatném nebo můžou být součástí jiné předplatné.

## <a name="before-you-begin"></a>Před zahájením

* Než začnete s konfigurací, Projděte si [požadavky,](expressroute-prerequisites.md) [požadavky na směrování](expressroute-routing.md)a [pracovní postupy](expressroute-workflows.md) .

* Musí mít aktivní okruh ExpressRoute.
  * Podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a pořiďte okruh povolený vaším poskytovatelem připojení.
  * Ujistěte se, že máte soukromého partnerského vztahu Azure nakonfigurovaný pro váš okruh. Pokyny pro vytváření partnerských vztahů a směrování najdete v článku [Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) .
  * Ověřte, že je nakonfigurovaný soukromého partnerského vztahu Azure a partnerského vztahu protokolu BGP mezi vaší sítí a Microsoftem nahoru tak, že povolíte připojení k začátku do konce.
  * Ujistěte se, že máte virtuální síť a Brána virtuální sítě vytvořené a plně zřízený. Postupujte podle pokynů a [vytvořte bránu virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brána virtuální sítě pro ExpressRoute používá GatewayType "ExpressRoute", ne VPN.

* Až 10 virtuálních sítí můžete propojit standardní okruh ExpressRoute. Všechny virtuální sítě musí být ve stejné geopolitické oblasti, při použití standardní okruh ExpressRoute.

* Jedné virtuální sítě nesmí být propojení až čtyři okruhy ExpressRoute. Můžete vytvořit nový objekt připojení pro každý okruh ExpressRoute, ke kterému se připojujete pomocí procesu níže. Okruhy ExpressRoute může být ve stejném předplatném, různá předplatná nebo kombinaci obou.

* Můžete propojit virtuální síť mimo geopolitické oblasti okruh ExpressRoute nebo připojení větší počet virtuálních sítí pro váš okruh ExpressRoute, pokud jste povolili doplněk ExpressRoute premium. Další podrobnosti o doplňku Premium najdete v [nejčastějších dotazech](expressroute-faqs.md) .

* Než začnete lépe pochopit tento postup, můžete si [prohlédnout video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) .

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Připojení virtuální sítě k okruhu - stejné předplatné

> [!NOTE]
> Informace o konfiguraci protokolu BGP nezobrazí, pokud zprostředkovatel vrstvy 3 nakonfigurován váš partnerské vztahy. Pokud váš okruh je ve stavu zřízeno, by měl být možné vytvořit připojení.
>

### <a name="to-create-a-connection"></a>Vytvoření připojení

1. Ujistěte se, že váš okruh ExpressRoute a soukromého partnerského vztahu Azure byl úspěšně nakonfigurován. Postupujte podle pokynů v tématu [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md) a [Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute](expressroute-howto-routing-arm.md). Váš okruh ExpressRoute by měl vypadat jako na následujícím obrázku:

   [![Snímek obrazovky okruhu ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Zobrazit okruh")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. Teď můžete začít zřizování připojení k propojení vaší brány virtuální sítě pro váš okruh ExpressRoute. Kliknutím na **připojení** > **Přidat** otevřete stránku **Přidat připojení** a pak nakonfigurujte tyto hodnoty.

   [![Obrázek přidání připojení](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "Obrázek přidání připojení")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
3. Po připojení se úspěšně nakonfigurovalo, objekt připojení se zobrazí informace o připojení.

   ![Snímek obrazovky připojení objektu](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Připojení virtuální sítě k okruhu - jiného předplatného

Okruh ExpressRoute můžete sdílet napříč několika předplatnými. Následující obrázek znázorňuje jednoduchý způsob sdílení prací pro okruhy ExpressRoute schéma napříč několika předplatnými.

![Připojením mezi předplatnými](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Každá z menších cloudy v rámci velkých cloudových se používá k reprezentování odběry, které patří k různým oblastem v rámci organizace.
- Vlastní předplatné každé oddělení v rámci organizace můžete použít k nasazení svých služeb, ale můžou sdílet jeden okruh ExpressRoute pro připojení zpět k místní síti.
- Jednoho oddělení (v tomto příkladu: IT) může vlastnit okruh ExpressRoute. Okruh ExpressRoute a povolení přidružené k okruhu, včetně předplatných přidružených k jiných tenantů Azure Active Directory a registrace smlouvy Enterprise, můžete použít jiných předplatných v rámci organizace.

  > [!NOTE]
  > Poplatky za připojení a šířku pásma pro vyhrazeného okruhu uplatní se na vlastníka okruhu ExpressRoute. Všechny virtuální sítě sdílejí stejnou šířku pásma.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Správa – o okruh uživatelů a vlastníků okruh

Vlastníka okruhu je autorizovaný uživatel Power prostředku okruhu ExpressRoute. Vlastníka okruhu můžete vytvářet autorizace, které můžou uplatnit: uživatelé okruhu". Uživatelé okruhu jsou vlastníky bran virtuální sítě, které nejsou ve stejném předplatném jako okruh ExpressRoute. Uživatelé okruhu můžete uplatnění autorizací (jedním autorizačním jednu virtuální síť).

Vlastníka okruhu má schopnost upravovat dokumentů a odvolání přístupu autorizace kdykoli. Odvolává se autorizace výsledkem spojení odstraňuje z předplatného, jehož přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Vytvoření autorizace připojení**

Vlastníka okruhu vytvoří autorizaci. Výsledkem je vytvoření autorizačního klíče, který je možné připojit své brány virtuální sítě k okruhu ExpressRoute uživatelem okruhu. Povolení platí jenom jedno připojení.

> [!NOTE]
> Každé připojení vyžaduje samostatnou autorizaci.
>

1. Na stránce ExpressRoute klikněte na **autorizace** a potom zadejte **název** autorizace a klikněte na **Uložit**.

   ![Autorizace](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Po uložení konfigurace zkopírujte **ID prostředku** a **autorizační klíč**.

   ![Autorizační klíč](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Odstranění autorizace připojení**

Připojení můžete odstranit výběrem ikony **Odstranit** na stránce pro vaše připojení.

### <a name="circuit-user-operations"></a>Operace pro uživatele okruhu

ID prostředku a autorizační klíč z vlastníka okruhu, musí uživatel okruhu.

**Uplatnění autorizace připojení**

1. Klikněte na tlačítko **+ Nový** .

   ![Klikněte na tlačítko Nový](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Na webu Marketplace vyhledejte **"připojení"** , vyberte ho a klikněte na **vytvořit**.

   ![Hledat připojení](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Ujistěte se, že je **Typ připojení** nastavený na "ExpressRoute".
4. Vyplňte podrobnosti a klikněte na tlačítko **OK** na stránce základy.

   ![Základní informace o stránce](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. Na stránce **Nastavení** vyberte **bránu virtuální sítě** a zaškrtněte políčko **uplatnit autorizaci** .
6. Zadejte **autorizační klíč** a **identifikátor URI rovnocenného okruhu** a zadejte název připojení. Klikněte na tlačítko **OK**. **Identifikátor URI partnerského okruhu** je ID prostředku okruhu ExpressRoute (který najdete v podokně nastavení vlastností okruhu ExpressRoute).

   ![Stránka nastavení](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Zkontrolujte informace na stránce **Souhrn** a klikněte na tlačítko **OK**.

**Uvolnění autorizace připojení**

Povolení můžete uvolnit tak, že odstraníte okruh ExpressRoute k virtuální síti, která odkazuje připojení.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Odstranit připojení k propojení virtuální sítě

Připojení a odpojení virtuální sítě k okruhu ExpressRoute můžete odstranit tak, že na stránce vyberete ikonu **Odstranit** .

## <a name="next-steps"></a>Další kroky
Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

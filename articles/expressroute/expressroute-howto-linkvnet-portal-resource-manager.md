---
title: 'Propojení virtuální sítě k okruhu ExpressRoute: Azure portal | Dokumentace Microsoftu'
description: 'Připojení virtuální sítě k okruhu Azure ExpressRoute. Postupy: kroky.'
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: f5cb5441-2fba-46d9-99a5-d1d586e7bda4
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: cherylmc
ms.openlocfilehash: 47c0db66889dae5d74e2fcf46a27ec1397930e53
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256778"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Připojení virtuální sítě k okruhu ExpressRoute pomocí portálu
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
> 

Tento článek vám pomůže vytvořit připojení k propojení virtuální sítě k okruhu Azure ExpressRoute pomocí webu Azure portal. Virtuální sítě, které se připojují k váš okruh Azure ExpressRoute může být ve stejném předplatném nebo můžou být součástí jiné předplatné.

## <a name="before-you-begin"></a>Než začnete

* Zkontrolujte [požadavky](expressroute-prerequisites.md), [požadavky směrování](expressroute-routing.md), a [pracovních postupů](expressroute-workflows.md) předtím, než začnete s konfigurací.

* Musí mít aktivní okruh ExpressRoute.
  * Postupujte podle pokynů a [vytvořit okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a mějte ho povolený poskytovatel připojení.
  * Ujistěte se, že máte soukromého partnerského vztahu Azure nakonfigurovaný pro váš okruh. Zobrazit [konfigurace směrování](expressroute-howto-routing-portal-resource-manager.md) najdete pokyny pro směrování.
  * Ověřte, že je nakonfigurovaný soukromého partnerského vztahu Azure a partnerského vztahu protokolu BGP mezi vaší sítí a Microsoftem nahoru tak, že povolíte připojení k začátku do konce.
  * Ujistěte se, že máte virtuální síť a Brána virtuální sítě vytvořené a plně zřízený. Postupujte podle pokynů a [vytvořit bránu virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brána virtuální sítě pro ExpressRoute používá GatewayType "ExpressRoute", ne VPN.

* Až 10 virtuálních sítí můžete propojit standardní okruh ExpressRoute. Všechny virtuální sítě musí být ve stejné geopolitické oblasti, při použití standardní okruh ExpressRoute.

* Jedné virtuální sítě nesmí být propojení až čtyři okruhy ExpressRoute. Můžete vytvořit nový objekt připojení pro každý okruh ExpressRoute, ke kterému se připojujete pomocí procesu níže. Okruhy ExpressRoute může být ve stejném předplatném, různá předplatná nebo kombinaci obou.

* Můžete propojit virtuální síť mimo geopolitické oblasti okruh ExpressRoute nebo připojení větší počet virtuálních sítí pro váš okruh ExpressRoute, pokud jste povolili doplněk ExpressRoute premium. Zkontrolujte, [nejčastější dotazy k](expressroute-faqs.md) podrobné informace o doplněk premium.

* Je možné [zobrazení videa](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) před zahájením pro lepší pochopení kroků.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Připojení virtuální sítě k okruhu - stejné předplatné

> [!NOTE]
> Informace o konfiguraci protokolu BGP nezobrazí, pokud zprostředkovatel vrstvy 3 nakonfigurován váš partnerské vztahy. Pokud váš okruh je ve stavu zřízeno, by měl být možné vytvořit připojení.
>

### <a name="to-create-a-connection"></a>Vytvoření připojení

1. Ujistěte se, že váš okruh ExpressRoute a soukromého partnerského vztahu Azure byl úspěšně nakonfigurován. Postupujte podle pokynů v [vytvořit okruh ExpressRoute](expressroute-howto-circuit-arm.md) a [konfigurace směrování](expressroute-howto-routing-arm.md). Váš okruh ExpressRoute by měl vypadat jako na následujícím obrázku:

  ![Snímek obrazovky okruhu ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
2. Teď můžete začít zřizování připojení k propojení vaší brány virtuální sítě pro váš okruh ExpressRoute. Klikněte na tlačítko **připojení** > **přidat** otevřít **přidat připojení** stránce a pak proveďte konfiguraci hodnot.

  ![Přidat snímek obrazovky připojení](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)
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

**Chcete-li vytvořit připojení autorizace**

Vlastníka okruhu vytvoří autorizaci. Výsledkem je vytvoření autorizačního klíče, který je možné připojit své brány virtuální sítě k okruhu ExpressRoute uživatelem okruhu. Povolení platí jenom jedno připojení.

1. Na stránce ExpressRoute, klikněte na **povolení** a potom zadejte **název** autorizace a klikněte na **Uložit**.

  ![Autorizace](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Po uložení konfigurace zkopírovat **ID prostředku** a **autorizační klíč**.

  ![Autorizační klíč](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Chcete-li odstranit autorizaci připojení**

Připojení můžete odstranit tak, že vyberete **odstranit** ikonu na stránce pro připojení.

### <a name="circuit-user-operations"></a>Operace pro uživatele okruhu

ID prostředku a autorizační klíč z vlastníka okruhu, musí uživatel okruhu.

**Chcete-li uplatnit autorizaci připojení**

1. Klikněte na tlačítko **+ nová** tlačítko.

  ![Klikněte na tlačítko Nový](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Vyhledejte **"Připojení"** na webu Marketplace, vyberte ho a klikněte na tlačítko **vytvořit**.

  ![Hledat připojení](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Ujistěte se, **typ připojení** je nastavena na "ExpressRoute".
4. Vyplňte podrobnosti a pak klikněte na **OK** na základní stránce.

  ![Základní informace o stránce](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. V **nastavení** stránky, vyberte **Brána virtuální sítě** a zkontrolujte **uplatnit autorizaci** zaškrtávací políčko.
6. Zadejte **autorizační klíč** a **identifikátor URI okruhu partnerských uzlů** a zadejte název připojení. Klikněte na **OK**.

  ![Stránka Nastavení](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Zkontrolujte informace **Souhrn** stránky a klikněte na tlačítko **OK**.

**K uvolnění ověření připojení**

Povolení můžete uvolnit tak, že odstraníte okruh ExpressRoute k virtuální síti, která odkazuje připojení.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Odstranit připojení k propojení virtuální sítě

Můžete odstranit připojení a zrušit propojení virtuální sítě k okruhu ExpressRoute, tak, že vyberete **odstranit** ikonu na stránce pro připojení.

## <a name="next-steps"></a>Další postup
Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
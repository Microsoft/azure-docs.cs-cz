---
title: "Propojení virtuální sítě k okruhu ExpressRoute: portálu Azure | Microsoft Docs"
description: "Připojte virtuální sítě k okruhu Azure ExpressRoute. Postupy: kroky."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f5cb5441-2fba-46d9-99a5-d1d586e7bda4
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: cherylmc
ms.openlocfilehash: c2bef1d79d3133ea6306928a8c917e1bc3000a58
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Připojit virtuální sítě k okruhu ExpressRoute pomocí portálu
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – portál Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
> 

Tento článek vám pomůže vytvořit připojení k propojení virtuální sítě k okruhu Azure ExpressRoute pomocí webu Azure portal. Virtuální sítě, která se připojují k okruhu Azure ExpressRoute může být v rámci stejného předplatného, nebo mohou být součástí jiné předplatné.

## <a name="before-you-begin"></a>Než začnete

* Zkontrolujte [požadavky](expressroute-prerequisites.md), [požadavky na směrování](expressroute-routing.md), a [pracovních](expressroute-workflows.md) před zahájením konfigurace.

* Musí mít aktivní okruh ExpressRoute.
  * Postupujte podle pokynů a [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a mějte ho povolený vaším poskytovatelem připojení.
  * Ujistěte se, abyste měli soukromého partnerského vztahu Azure nakonfigurovaný pro váš okruh. Najdete v článku [konfigurace směrování](expressroute-howto-routing-portal-resource-manager.md) směrování pokyny najdete v článku.
  * Zajistěte, aby soukromý partnerský vztah Azure je nakonfigurován a partnerském vztahu protokolu BGP mezi vaší sítí a Microsoftem zapnutý tak, že povolíte připojení klient server.
  * Ujistěte se, že máte virtuální sítě a brány virtuální sítě vytvoří a plně zřízený. Postupujte podle pokynů a [vytvořit bránu virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brána virtuální sítě pro ExpressRoute používá GatewayType 'ExpressRoute' není síť VPN.

* Až 10 virtuálních sítí můžete propojit standardní okruh ExpressRoute. Všechny virtuální sítě musí být ve stejné geopolitické oblasti, při použití standardní okruh ExpressRoute.

* Jeden virtuální sítě může být propojený až čtyři okruhy ExpressRoute. Můžete vytvořit nový objekt připojení pro každý okruh ExpressRoute, ke kterému se připojujete pomocí procesu níže. Okruhy ExpressRoute může být v rámci stejného předplatného, různých předplatných nebo kombinaci obou.

* Můžete propojit virtuální sítě mimo geopolitické oblasti okruhu ExpressRoute nebo větší počet virtuálních sítí připojit k okruhu ExpressRoute, pokud jste povolili doplněk ExpressRoute premium. Zkontrolujte [– nejčastější dotazy](expressroute-faqs.md) další podrobnosti o doplněk premium.

* Můžete [zobrazit video](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) před zahájením pro lepší pochopení kroků.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Připojit virtuální sítě k okruhu - stejného předplatného.

> [!NOTE]
> Informace o konfiguraci protokolu BGP nezobrazí, pokud zprostředkovatel služby vrstvy 3 nakonfigurovaný vaší partnerských vztahů. Pokud váš okruh je ve stavu, zřízené, byste měli k vytvoření připojení.
>

### <a name="to-create-a-connection"></a>Chcete-li vytvořit připojení

1. Zkontrolujte, že stav okruhu ExpressRoute a soukromý partnerský vztah Azure úspěšně nakonfigurovaný. Postupujte podle pokynů v [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md) a [konfigurace směrování](expressroute-howto-routing-arm.md). Váš okruh ExpressRoute by měl vypadat podobně jako na následujícím obrázku:

  ![Snímek obrazovky okruhu ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
2. Nyní můžete spustit zřizování připojení k propojení vaší brány virtuální sítě k okruhu ExpressRoute. Klikněte na tlačítko **připojení** > **přidat** otevřete **přidat připojení** stránky a pak proveďte konfiguraci hodnot.

  ![Přidat připojení – snímek obrazovky](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)
3. Po úspěšném dokončení konfigurace připojení k objektu připojení se zobrazí informace o připojení.

  ![Snímek obrazovky připojení objektu](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Připojit virtuální sítě k okruhu - jiném předplatném.

Okruh ExpressRoute můžete sdílet mezi více předplatných. Následující obrázek znázorňuje jednoduchý schéma o tom, jak sdílení funguje pro okruhy ExpressRoute napříč více předplatných.

![Připojení mezi předplatnými](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Každý z menší cloudy v rámci velké cloudu se používá k reprezentování odběry, které patří k různým oblastem v rámci organizace.
- Každé oddělení v organizaci můžete použít vlastní předplatné nasazování služeb, ale můžou sdílet jeden okruh ExpressRoute zpětné připojení k síti na pracovišti.
- Jednoho oddělení (v tomto příkladu: IT) můžete vlastní okruh ExpressRoute. Odběr v rámci organizace pomocí okruh ExpressRoute a povolení přidružené k okruhu, včetně odběry propojen ostatní tenanty Azure Active Directory a smlouva Enterprise Agreement registrace.

  > [!NOTE]
  > Připojení a šířku pásma poplatky za vyhrazené okruh uplatní na vlastníka okruhu ExpressRoute. Všechny virtuální sítě sdílejí stejné šířky pásma.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Správa - o vlastníky okruhu a okruh uživatelů

Vlastníka okruhu je oprávněný uživatel Power prostředku okruhu ExpressRoute. Vlastníka okruhu můžete vytvořit autorizací, které lze uplatnit podle 'okruh uživatele'. Okruh uživatelů se vlastníci brány virtuální sítě, které nejsou v rámci stejného předplatného jako okruh ExpressRoute. Uživatelé okruhu můžete uplatnit autorizací (jedním autorizačním na jednu virtuální síť).

Vlastníka okruhu má právo upravit a kdykoli odvolat oprávnění. Odvolání autorizaci má za následek všechna připojení odkaz odstraňuje z předplatného, jejichž přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Chcete-li vytvořit připojení autorizace**

Vlastníka okruhu vytvoří povolení. Výsledkem je vytvoření autorizační klíč, které je možné se připojit své brány virtuální sítě k okruhu ExpressRoute uživatelem okruh. Povolení je platný pro jenom jedno připojení.

1. Na stránce ExpressRoute, klikněte na tlačítko **autorizací** a potom zadejte **název** pro ověřování a klikněte na tlačítko **Uložit**.

  ![Autorizace](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Po uložení konfigurace zkopírovat **ID prostředku** a **autorizační klíč**.

  ![Autorizační klíč](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Chcete-li odstranit autorizace připojení**

Připojení můžete odstranit výběrem **odstranit** ikonu na stránce pro připojení.

### <a name="circuit-user-operations"></a>Operace okruh uživatele

ID prostředku a autorizační klíč z vlastníka okruhu, musí uživatel okruh.

**Pokud chcete uplatnit autorizace připojení**

1. Klikněte **+ nový** tlačítko.

  ![Klikněte na tlačítko Nový](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Vyhledejte **"Připojení"** na webu Marketplace, vyberte ho a klikněte na tlačítko **vytvořit**.

  ![Hledání připojení](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Zajistěte, aby **typ připojení** je nastaven na "ExpressRoute".
4. Zadejte podrobnosti a pak klikněte na **OK** na stránce základy.

  ![Základy stránky](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. V **nastavení** stránky, vyberte **Brána virtuální sítě** a zkontrolujte **uplatnit autorizace** zaškrtávací políčko.
6. Zadejte **autorizační klíč** a **Peer okruh URI** a pojmenujte připojení. Klikněte na **OK**.

  ![Stránka Nastavení](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Informace v **Souhrn** a klikněte na tlačítko **OK**.

**Chcete-li uvolnit autorizace připojení**

Povolení můžete vydat odstraněním připojení, který odkazuje okruhu ExpressRoute do virtuální sítě.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Odstranit připojení k zrušení propojení virtuální sítě

Můžete odstranit připojení a výběrem zrušit propojení virtuální sítě k okruhu ExpressRoute **odstranit** ikonu na stránce pro připojení.

## <a name="next-steps"></a>Další postup
Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
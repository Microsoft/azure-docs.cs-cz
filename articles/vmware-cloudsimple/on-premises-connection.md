---
title: Řešení Azure VMware (AVS) – místní připojení pomocí ExpressRoute
description: Popisuje, jak požádat o místní připojení pomocí ExpressRoute ze sítě funkce AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 10a21faf2790b4c7a26d80e46bf44c8bffabf27f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019617"
---
# <a name="connect-from-on-premises-to-avs-using-expressroute"></a>Připojení z místního prostředí k funkci AVS pomocí ExpressRoute

Pokud už máte připojení Azure ExpressRoute z externího umístění (třeba místně) do Azure, můžete ho připojit k vašemu prostředí služby AVS. Můžete to provést prostřednictvím funkce Azure, která umožňuje vzájemně propojit dva okruhy ExpressRoute. Tato metoda vytváří zabezpečenou, soukromou a vysokou šířku pásma připojení s nízkou latencí mezi dvěma prostředími.

[![místní připojení ExpressRoute – Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Než začnete

Pro navázání Global Reachho připojení z místního prostředí se vyžaduje blok adres sítě a **/29** . Adresní prostor/29 se používá pro tranzitní síť mezi okruhy ExpressRoute. Tranzitní síť by se neměla překrývat s žádnou z vašich virtuálních sítí Azure, místních sítí ani sítí privátního cloudu služby AVS.

## <a name="prerequisites"></a>Požadavky

* Aby bylo možné navázat spojení mezi okruhem a sítěmi privátního cloudu služby AVS, je vyžadován okruh Azure ExpressRoute.
* Uživatel musí mít oprávnění k vytváření autorizačních klíčů na okruhu ExpressRoute.

## <a name="scenarios"></a>Scénáře

Připojení místní sítě k síti privátního cloudu služby AVS umožňuje používat privátní cloud služby AVS různými způsoby, včetně následujících scénářů:

* Přístup k síti privátního cloudu služby AVS bez vytvoření připojení VPN typu Site-to-site
* Použijte místní službu Active Directory jako zdroj identity v privátním cloudu služby AVS.
* Migrujte virtuální počítače běžící místně do privátního cloudu služby AVS.
* Využijte privátní cloud služby AVS jako součást řešení zotavení po havárii.
* Využijte místní prostředky na virtuálních počítačích s úlohami privátního cloudu služby AVS.

## <a name="connecting-expressroute-circuits"></a>Propojení okruhů ExpressRoute

K navázání připojení ExpressRoute musíte vytvořit autorizaci na okruhu ExpressRoute a poskytnout autorizační informace pro funkci AVS.


### <a name="create-expressroute-authorization"></a>Vytvořit autorizaci ExpressRoute

1. Přihlaste se k portálu Azure.

2. Na horním panelu hledání vyhledejte **okruh ExpressRoute** a klikněte na **okruhy ExpressRoute** pod položkou **služby**.
    [![okruhy ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Vyberte okruh ExpressRoute, ke kterému se chcete připojit k síti služby AVS.

4. Na stránce ExpressRoute klikněte na **autorizace**, zadejte název autorizace a klikněte na **Uložit**.
    [![autorizace okruhu ExpressRoute](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Kliknutím na ikonu Kopírovat zkopírujte ID prostředku a autorizační klíč. Vložte ID a klíč do textového souboru.
    [![autorizační kopii okruhu ExpressRoute](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **ID prostředku** se musí zkopírovat z uživatelského rozhraní a mělo by být ve formátu ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>```, když ho poskytnete pro podporu.

6. Zaregistrujte lístek s <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">podporou</a> připojení, které se má vytvořit.
    * Typ problému: **technický**
    * Předplatné: **předplatné, ve kterém je nasazená služba AVS**
    * Služba: **řešení VMware (AVS)**
    * Typ problému: **žádost o službu**
    * Podtyp problému: **vytvoření připojení ExpressRoute k místnímu** prostředí
    * Zadejte ID prostředku a autorizační klíč, který jste zkopírovali a uložili v podokně podrobností.
    * Zadejte adresní prostor sítě/29 pro tranzitní síť.
    * Odesíláte výchozí trasu prostřednictvím ExpressRoute?
    * Má provoz privátního cloudu pro funkci AVS používat výchozí trasu odeslanou prostřednictvím ExpressRoute?

    > [!IMPORTANT]
    > Odesílání výchozí trasy vám umožní odesílat veškerý internetový provoz z privátního cloudu služby AVS pomocí místního připojení k Internetu. Pokud chcete zakázat výchozí trasu nakonfigurovanou v privátním cloudu pro funkci AVS a použít výchozí trasu místního připojení, zadejte podrobnosti v lístku podpory.

## <a name="next-steps"></a>Další kroky

* [Další informace o síťových připojeních Azure](cloudsimple-azure-network-connection.md)  

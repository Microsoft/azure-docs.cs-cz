---
title: Řešení Azure VMware podle CloudSimple – místní připojení pomocí ExpressRoute
description: Popisuje, jak požádat o místní připojení pomocí ExpressRoute ze sítě CloudSimple region.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 27d1d9e54838d9f45a28d634fa1c24fb8785aee1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899231"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Připojení z místního prostředí k CloudSimple pomocí ExpressRoute

Pokud už máte připojení Azure ExpressRoute z externího umístění (třeba místně) do Azure, můžete ho připojit ke svému prostředí CloudSimple. Můžete to provést prostřednictvím funkce Azure, která umožňuje vzájemně propojit dva okruhy ExpressRoute. Tato metoda vytváří zabezpečenou, soukromou a vysokou šířku pásma připojení s nízkou latencí mezi dvěma prostředími.

[![Místní ExpressRoute připojení – Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Než začnete

Pro navázání Global Reachho připojení z místního prostředí se vyžaduje blok adres sítě a **/29** .  Adresní prostor/29 se používá pro tranzitní síť mezi okruhy ExpressRoute.  Tranzitní síť by se neměla překrývat s žádnou z vašich virtuálních sítí Azure, místními sítěmi ani CloudSimple privátních cloudových sítí.

## <a name="prerequisites"></a>Předpoklady

* Aby bylo možné navázat spojení mezi okruhem a sítěmi privátního cloudu CloudSimple, je vyžadován okruh Azure ExpressRoute.
* Uživatel musí mít oprávnění k vytváření autorizačních klíčů na okruhu ExpressRoute.

## <a name="scenarios"></a>Scénáře

Připojení místní sítě k síti privátního cloudu umožňuje používat privátní cloud různými způsoby, včetně následujících scénářů:

* Přístup k síti privátního cloudu bez vytvoření připojení VPN typu Site-to-site.
* Použijte místní službu Active Directory jako zdroj identity v privátním cloudu.
* Migrujte virtuální počítače, které jsou spuštěné místně, do svého privátního cloudu.
* Využijte svůj privátní cloud jako součást řešení zotavení po havárii.
* Využijte místní prostředky na virtuálních počítačích s úlohami privátního cloudu.

## <a name="connecting-expressroute-circuits"></a>Propojení okruhů ExpressRoute

K navázání připojení ExpressRoute musíte vytvořit autorizaci na okruhu ExpressRoute a poskytnout informace o autorizaci CloudSimple.


### <a name="create-expressroute-authorization"></a>Vytvořit autorizaci ExpressRoute

1. Přihlaste se k webu Azure Portal.

2. Na horním panelu hledání vyhledejte **okruh ExpressRoute** a klikněte na **okruhy ExpressRoute** pod položkou **služby**.
    [![Okruhy ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Vyberte okruh ExpressRoute, který máte v úmyslu připojit se k vaší CloudSimple síti.

4. Na stránce ExpressRoute klikněte na **autorizace**, zadejte název autorizace a klikněte na **Uložit**.
    [![Autorizace okruhu ExpressRoute](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Kliknutím na ikonu Kopírovat zkopírujte ID prostředku a autorizační klíč. Vložte ID a klíč do textového souboru.
    [![Autorizační kopie okruhu ExpressRoute](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **ID prostředku** se musí zkopírovat z uživatelského rozhraní a měl by být ve formátu, ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` když ho poskytnete pro podporu.

6. Zaregistrujte lístek s <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">podporou</a> připojení, které se má vytvořit.
    * Typ problému: **technický**
    * Předplatné: **předplatné, ve kterém je nasazená služba CloudSimple**
    * Služba: **řešení VMware podle CloudSimple**
    * Typ problému: **žádost o službu**
    * Podtyp problému: **vytvoření připojení ExpressRoute k místnímu** prostředí
    * Zadejte ID prostředku a autorizační klíč, který jste zkopírovali a uložili v podokně podrobností.
    * Zadejte adresní prostor sítě/29 pro tranzitní síť.
    * Odesíláte výchozí trasu prostřednictvím ExpressRoute?
    * Má provoz privátního cloudu používat výchozí trasu odeslanou prostřednictvím ExpressRoute?

    > [!IMPORTANT]
    > Odesílání výchozí trasy vám umožní odesílat veškerý internetový provoz z privátního cloudu pomocí místního připojení k Internetu.  Pokud chcete zakázat výchozí trasu nakonfigurovanou v privátním cloudu a použít výchozí trasu místní připojení, zadejte podrobnosti v lístku podpory.

## <a name="next-steps"></a>Další kroky

* [Další informace o síťových připojeních Azure](cloudsimple-azure-network-connection.md)  

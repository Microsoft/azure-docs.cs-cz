---
title: Řešení Azure VMware by CloudSimple – místní připojení pomocí ExpressRoute
description: Popisuje, jak požádat o místní připojení pomocí ExpressRoute ze sítě cloudsimple region
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0dd5ede110255b6e53bbc397e683e66b3beffc65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019617"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Připojení z místního prostředí ke CloudSimple pomocí ExpressRoute

Pokud už máte připojení Azure ExpressRoute z externího umístění (například místně) do Azure, můžete ho připojit k prostředí CloudSimple. Můžete tak učinit prostřednictvím funkce Azure, která umožňuje dva okruhy ExpressRoute pro připojení k sobě navzájem. Tato metoda vytváří zabezpečené, soukromé, s vysokou šířkou pásma, s nízkou latencí připojení mezi dvěma prostředími.

[![Místní připojení ExpressRoute – globální dosah](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Než začnete

Pro navázání připojení Global Reach z místního prostředí je vyžadován blok síťové adresy **/29.**  Adresní prostor /29 se používá pro přenosovou síť mezi okruhy ExpressRoute.  Tranzitní síť by se neměla překrývat s žádnou z vašich virtuálních sítí Azure, místních sítí nebo sítí CloudSimple Privátní hocloud.

## <a name="prerequisites"></a>Požadavky

* Před navázáním připojení mezi okruhem a privátnímcloudovým sítím CloudSimple je vyžadován okruh Azure ExpressRoute.
* Uživatel je povinen s oprávněními k vytvoření autorizačních klíčů na okruhu ExpressRoute.

## <a name="scenarios"></a>Scénáře

Připojení místní sítě k privátní cloudové síti umožňuje používat privátní cloud různými způsoby, včetně následujících scénářů:

* Získejte přístup k privátní cloudové síti bez vytvoření připojení VPN site-to-site.
* Použijte místní službu Active Directory jako zdroj identit y v privátním cloudu.
* Migrujte virtuální počítače spuštěné místně do privátního cloudu.
* Použijte svůj privátní cloud jako součást řešení zotavení po havárii.
* Využijte místní prostředky na virtuálních počítačích s úlohami privátního cloudu.

## <a name="connecting-expressroute-circuits"></a>Připojení obvodů ExpressRoute

Chcete-li navázat připojení ExpressRoute, musíte vytvořit autorizaci v okruhu ExpressRoute a poskytnout informace o autorizaci CloudSimple.


### <a name="create-expressroute-authorization"></a>Vytvořit autorizaci ExpressRoute

1. Přihlaste se k portálu Azure.

2. Na horním vyhledávacím panelu vyhledejte **okruh ExpressRoute** a v části **Služby**klepněte na **okruhExpressRoute** .
    [![Obvody ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Vyberte okruh ExpressRoute, který chcete připojit k síti CloudSimple.

4. Na stránce ExpressRoute klepněte na položku **Autorizace**, zadejte název autorizace a klepněte na tlačítko **Uložit**.
    [![Autorizace okruhu ExpressRoute](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Zkopírujte ID prostředku a autorizační klíč kliknutím na ikonu kopírování. Vložte ID a zadejte klíč do textového souboru.
    [![Kopie autorizace okruhu ExpressRoute](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **ID prostředku** musí být zkopírováno z ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` ui a mělo by být ve formátu, když ho poskytnete pro podporu.

6. Soubor lístek s <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">podporou</a> pro připojení, které mají být vytvořeny.
    * Typ problému: **Technické**
    * Předplatné: **Předplatné, kde se nasadí služba CloudSimple**
    * Služba: **Řešení VMware od CloudSimple**
    * Typ problému: **Požadavek na službu**
    * Podtyp problému: **Vytvoření připojení ExpressRoute k místnímu prostředí**
    * Zadejte ID prostředku a autorizační klíč, který jste zkopírovali a uložili v podokně podrobností.
    * Zadejte síťový adresní prostor /29 pro tranzitní síť.
    * Posíláte výchozí trasu přes ExpressRoute?
    * Má provoz privátního cloudu používat výchozí trasu odeslanou přes ExpressRoute?

    > [!IMPORTANT]
    > Odesílání výchozí trasy umožňuje odesílat veškerý internetový provoz z privátního cloudu pomocí místního připojení k internetu.  Chcete-li zakázat výchozí trasu nakonfigurovanou v privátním cloudu a použít výchozí trasu místního připojení, zadejte podrobnosti v lístku podpory.

## <a name="next-steps"></a>Další kroky

* [Další informace o síťových připojeních Azure](cloudsimple-azure-network-connection.md)  

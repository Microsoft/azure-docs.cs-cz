---
title: "Propojení virtuální sítě k okruhu ExpressRoute: prostředí PowerShell: classic: Azure | Microsoft Docs"
description: "Tento dokument obsahuje přehled o tom, jak propojit virtuální sítě (virtuální sítě) pro okruhy ExpressRoute pomocí modelu nasazení classic a prostředí PowerShell."
services: expressroute
documentationcenter: na
author: ganesr
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 9b53fd72-9b6b-4844-80b9-4e1d54fd0c17
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: ganesr
ms.openlocfilehash: 2f0fed77a676bc46e8daa9c41efd533dffe72d8b
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Připojit virtuální sítě k okruhu ExpressRoute pomocí prostředí PowerShell (klasické)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – portál Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
>

Tento článek vám pomůže propojení virtuální sítě (virtuální sítě) pro okruhy Azure ExpressRoute pomocí modelu nasazení classic a prostředí PowerShell. Virtuální sítě může být ve stejném předplatném nebo můžou být součástí jiné předplatné.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace
1. Je třeba nejnovější verzi modulů prostředí Azure PowerShell. Nejnovější moduly Powershellu si můžete stáhnout z části prostředí PowerShell [Azure stáhne stránky](https://azure.microsoft.com/downloads/). Postupujte podle pokynů v [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) podrobné pokyny ke konfiguraci počítače pro používání modulů prostředí Azure PowerShell.
2. Je potřeba posoudit [požadavky](expressroute-prerequisites.md), [požadavky na směrování](expressroute-routing.md), a [pracovních](expressroute-workflows.md) před zahájením konfigurace.
3. Musí mít aktivní okruh ExpressRoute.
   * Postupujte podle pokynů a [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-classic.md) a váš poskytovatel připojení povolte okruh.
   * Ujistěte se, abyste měli soukromého partnerského vztahu Azure nakonfigurovaný pro váš okruh. Najdete v článku [konfigurace směrování](expressroute-howto-routing-classic.md) směrování pokyny najdete v článku.
   * Zajistěte, aby soukromý partnerský vztah Azure je nakonfigurován a partnerském vztahu protokolu BGP mezi vaší sítí a Microsoftem zapnutý tak, že povolíte připojení klient server.
   * Musí mít virtuální sítě a brány virtuální sítě vytvoří a plně zřízený. Postupujte podle pokynů a [konfigurace virtuální sítě pro ExpressRoute](expressroute-howto-vnet-portal-classic.md).

Můžete se propojit až 10 virtuální sítě k okruhu ExpressRoute. Všechny virtuální sítě musí být ve stejné geopolitické oblasti. Můžete propojit větší počet virtuální sítě k okruhu ExpressRoute nebo propojení virtuálních sítí, které jsou v dalších geopolitických oblastí, pokud jste povolili doplněk ExpressRoute premium. Zkontrolujte [– nejčastější dotazy](expressroute-faqs.md) další podrobnosti o doplněk premium.

Jeden virtuální sítě může být propojený až čtyři okruhy ExpressRoute. Chcete-li vytvořit nové propojení na každý okruh ExpressRoute, ke kterému se připojujete pomocí procesu níže. Okruhy ExpressRoute může být v rámci stejného předplatného, různých předplatných nebo kombinaci obou.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Připojit k okruhu virtuální sítě v rámci stejného předplatného
Pomocí následující rutiny můžete propojit virtuální sítě k okruhu ExpressRoute. Ujistěte se, že bránu virtuální sítě se vytvoří a je připravený pro propojování předtím, než spustíte rutinu.

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
    Provisioned

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Připojení virtuální sítě jiného předplatného k okruhu
Okruh ExpressRoute můžete sdílet mezi více předplatných. Následující obrázek znázorňuje jednoduchý schéma o tom, jak sdílení funguje pro okruhy ExpressRoute napříč více předplatných.

Každý z menší cloudy v rámci velké cloudu se používá k reprezentování odběry, které patří k různým oblastem v rámci organizace. Každé oddělení v organizaci můžete použít vlastní předplatné pro nasazení služeb – ale jako vodítko použijte oddělení může sdílet jeden okruh ExpressRoute zpětné připojení k síti na pracovišti. Jednoho oddělení (v tomto příkladu: IT) můžete vlastní okruh ExpressRoute. Okruh ExpressRoute můžete použít jiné odběry v rámci organizace.

> [!NOTE]
> Připojení a šířku pásma poplatky za vyhrazené okruh uplatní na vlastníka okruhu ExpressRoute. Všechny virtuální sítě sdílejí stejné šířky pásma.
> 
> 

![Připojení mezi předplatnými](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Správa
*Vlastníka okruhu* je správce nebo spolusprávce předplatného, ve kterém se vytvoří okruh ExpressRoute. Vlastníka okruhu může autorizovat správci nebo coadministrators další odběry, označuje jako *okruhů uživatelů*, používat vyhrazené okruh, kterou vlastní. Okruh uživatelů, kteří mají oprávnění využívat organizace okruh ExpressRoute můžete propojit virtuální sítě v rámci svého předplatného okruh ExpressRoute po jejich oprávnění.

Vlastníka okruhu má právo upravit a kdykoli odvolat oprávnění. Odvolání povolení bude výsledkem všechny odkazy odstraňuje z předplatného, jejichž přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Vytvoření ověření**

Autorizuje vlastníka okruhu správcům další odběry použít určený okruh. V následujícím příkladu správce okruhu (Contoso IT) umožňuje správci jiné předplatné (Dev-Test) propojit až dvě virtuální sítě k okruhu. Správce Contoso IT umožňuje to tak, že zadáte ID Microsoft Dev-Test. Rutina neodešle e-mailu zadané ID společnosti Microsoft. Vlastníka okruhu je potřeba explicitně oznámit jiných vlastník předplatného, registrace je dokončena.

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

**Kontrola povolení**

Vlastníka okruhu můžete zkontrolovat všechny autorizací, které jsou vydány na konkrétní okruhu spuštěním následující rutiny:

    Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

    Description         : EngineeringTeam
    Limit               : 3
    LinkAuthorizationId : ####################################
    MicrosoftIds        : engadmin@contoso.com
    Used                : 1

    Description         : MarketingTeam
    Limit               : 1
    LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    MicrosoftIds        : marketingadmin@contoso.com
    Used                : 0

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : salesadmin@contoso.com
    Used                : 2


**Aktualizace oprávnění**

Vlastníka okruhu můžete upravit autorizací pomocí následující rutiny:

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


**Odstranění autorizací**

Vlastníka okruhu můžete odvolání nebo odstranění autorizací pro uživatele tak, že spustíte následující rutinu:

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>Operace okruh uživatele

**Kontrola povolení**

Uživatele okruhu můžete zkontrolovat autorizací pomocí následující rutiny:

    Get-AzureAuthorizedDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

**Uplatňuje autorizací propojení**

Okruh může spouštět následující rutiny můžete uplatnit odkaz autorizace:

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

Spusťte tento příkaz v nově propojené předplatné virtuální sítě:

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"

## <a name="next-steps"></a>Další postup
Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).


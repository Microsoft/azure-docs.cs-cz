---
title: 'Úprava okruhu ExpressRoute: prostředí PowerShell: portál Azure classic | Microsoft Docs'
description: Tento článek vás provede kroky pro kontrolu stavu, aktualizace nebo odstranění a zrušení zřízení okruhu ExpressRoute modelu nasazení classic.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 0134d242-6459-4dec-a2f1-4657c3bc8b23
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 457bb74fa15d31fecbf668038ac880cafb8a897d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "24102830"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Úprava okruhu ExpressRoute pomocí prostředí PowerShell (klasické)

> [!div class="op_single_selector"]
> * [portál Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video – portál Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

Tento článek také ukazuje, jak zkontrolovat stav, aktualizace nebo odstranění a zrušení zřízení okruhu ExpressRoute.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Než začnete

Nainstalujte nejnovější verzi modulů prostředí PowerShell Azure Service Management (SM) postupujte podle pokynů v [Začínáme s rutinami prostředí Azure PowerShell](/powershell/azure/overview) podrobné pokyny ke konfiguraci počítače pro používání Moduly prostředí Azure PowerShell.

## <a name="get-the-status-of-a-circuit"></a>Načíst stav okruhu

Tyto informace kdykoli můžete načíst pomocí `Get-AzureCircuit` rutiny. Provádění volání bez parametrů jsou uvedeny všechny okruhů.

```powershell
Get-AzureDedicatedCircuit

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled

Bandwidth                        : 1000
CircuitName                      : MyAsiaCircuit
Location                         : Singapore
ServiceKey                       : #################################
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Informace o konkrétní okruh ExpressRoute můžete získat pomocí předání klíče služby jako parametr pro volání.

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Podrobný popis všech parametrů můžete získat spuštěním v následujícím příkladu:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Úprava okruhu

Bez dopadu na připojení můžete upravit některé vlastnosti okruhu ExpressRoute.

Můžete provést následující úlohy bez výpadků:

* Povolit nebo zakázat doplněk ExpressRoute premium pro váš okruh ExpressRoute.
* Zadaný na tomto portu je dostupná kapacita, zvětšete šířku pásma okruhu ExpressRoute. Přechod na starší verzi šířku pásma okruhu není podporována. 
* Změňte plán měření z – měření podle objemu dat na neomezená Data na úrovni. Změna plánu měření z neomezená Data – měření podle objemu dat není podporována.
* Můžete povolit nebo zakázat *povolit klasické operace*.

Odkazovat [ExpressRoute – nejčastější dotazy](expressroute-faqs.md) Další informace o omezení.

### <a name="enable-the-expressroute-premium-add-on"></a>Povolit doplněk ExpressRoute premium

Doplněk ExpressRoute premium pro váš okruh. existující můžete povolit pomocí následující rutiny prostředí PowerShell:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

Funkce doplněk ExpressRoute premium povoleno bude mít teď váš okruh. Jakmile úspěšně spuštěn příkaz začnou fakturace pro funkci rozšíření premium.

### <a name="disable-the-expressroute-premium-add-on"></a>Zakázat doplněk ExpressRoute premium

> [!IMPORTANT]
> Tato operace může selhat, pokud používáte prostředky, které jsou větší než co je povoleno pro standardní okruh.
> 
> 

#### <a name="considerations"></a>Požadavky

* Ujistěte se, že počet virtuální sítě propojené ke okruhu je menší než 10 před downgradovat z úrovně premium na standard. Pokud to neuděláte, vaše žádost o aktualizaci se nezdaří a se účtují zvýhodněné sazby.
* Je nutné zrušit všechny virtuální sítě v jiných geopolitické oblasti. Pokud ne, vaše žádost o aktualizaci se nezdaří a se účtují zvýhodněné sazby.
* Směrovací tabulka musí být menší než 4 000 tras pro soukromý partnerský vztah. Pokud vaše velikost tabulky trasy je větší než 4 000 tras, relace BGP zahodí a nebude možné opětovně povolena dokud počet předpon inzerovaných přejde nižší než 4 000.

#### <a name="to-disable-the-premium-add-on"></a>Chcete-li zakázat doplněk premium

Doplněk ExpressRoute premium pro váš okruh. existující můžete zakázat pomocí následující rutiny prostředí PowerShell:

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>Aktualizace šířku pásma okruhu ExpressRoute

Zkontrolujte [ExpressRoute – nejčastější dotazy](expressroute-faqs.md) pro podporované možnosti šířky pásma pro poskytovatele. Můžete vybrat libovolnou velikost, která je větší než velikost existujícího okruhu tak dlouho, dokud umožňuje fyzického portu (na kterém je vytvořený váš okruh).

> [!IMPORTANT]
> Možná budete muset znovu vytvořit okruh ExpressRoute, pokud je nedostatečné kapacity na existující port. Pokud v tomto umístění není k dispozici žádné další kapacitu, nelze upgradovat okruh.
>
> Nejde snížit šířku pásma okruhu ExpressRoute bez přerušení. Přechod na starší verzi šířky pásma vyžaduje zrušení zřízení okruh ExpressRoute a pak znova nezajistíte nové okruh ExpressRoute.
> 
> 

#### <a name="resize-a-circuit"></a>Změna velikosti okruhu

Jakmile se rozhodnete, jakou velikost potřebujete, můžete ke změně velikosti okruhu následující příkaz:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Jakmile se váš okruh má byla velikosti na straně společnosti Microsoft, obraťte se na svého poskytovatele připojení k aktualizaci konfigurace na jejich straně tak, aby odpovídaly tuto změnu. Fakturace začíná v pro možnost aktualizované šířky pásma z tohoto bodu.

Pokud se zobrazí chybová zpráva při zvýšení šířku pásma okruhu, znamená to, existuje ponecháno žádné dostatečnou šířku pásma na fyzického portu, kde se má vytvořit existujícím okruhem. Musíte odstranit tento okruh a vytvořit nové okruh velikosti, které potřebujete.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Zrušení zřízení a odstranit okruh

### <a name="considerations"></a>Požadavky

* Je nutné zrušit všechny virtuální sítě od okruhu ExpressRoute pro tato operace proběhla úspěšně. Zkontrolujte, zda jsou všechny virtuální sítě, které jsou propojeny s okruh, pokud se tato operace nezdaří.
* Pokud je poskytovatel služby okruh ExpressRoute Stav zřizování **zřizování** nebo **zajištěno** , musíte pracovat se svým poskytovatelem služeb pro zrušení zřízení okruhu na jejich straně. Abychom mohli pokračovat a rezervovat prostředky vám účtovat, dokud poskytovatele služeb dokončení zrušení okruhu a upozorní nás.
* Pokud má poskytovatel služeb zrušit okruhu (poskytovatele služeb Stav zřizování je nastavena na **není zajišťováno**), pak můžete odstranit okruh. To zastaví fakturace pro okruh.

#### <a name="delete-a-circuit"></a>Odstranit okruh

Váš okruh ExpressRoute můžete odstranit tak, že spustíte následující příkaz:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
---
title: 'Úprava okruhu ExpressRoute: prostředí PowerShell: Azure classic | Dokumentace Microsoftu'
description: Tento článek vás provede kroky ke kontrole stavu, aktualizace nebo odstranění a zrušení zřízení modelu okruhů ExpressRoute modelu nasazení classic.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: ganesr;cherylmc
ms.custom: seodec18
ms.openlocfilehash: 0d6d8af9456f5f943eb70b5a63b69e2f7f16a4cb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104233"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Úprava okruhu ExpressRoute pomocí prostředí PowerShell (classic)

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

Tento článek vás provede kroky ke kontrole stavu, aktualizace nebo odstranění a zrušení zřízení modelu okruhů ExpressRoute modelu nasazení classic. Tento článek se týká modelu nasazení Classic.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Před zahájením

Nainstalujte nejnovější verze modulů Azure Service Management Powershellu a modul ExpressRoute.  Při použití v následujícím příkladu, mějte na paměti, že číslo verze (v tomto příkladu 5.1.1) se změní vydané novější verze rutin.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Pokud potřebujete další informace o Azure Powershellu, přečtěte si [Začínáme s rutinami Azure Powershellu](/powershell/azure/overview) podrobné pokyny o tom, jak nakonfigurovat počítač pomocí modulů Azure Powershellu.

Přihlásit se ke svému účtu Azure, použijte následující příklad:

1. Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

  ```powershell
  Connect-AzureRmAccount
  ```
2. Zkontrolujte předplatná pro příslušný účet.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Máte-li více předplatných, vyberte předplatné, které chcete použít.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

4. V dalším kroku použijte následující rutinu k vašemu předplatnému Azure přidat do prostředí PowerShell pro model nasazení classic.

  ```powershell
  Add-AzureAccount
  ```

## <a name="get-the-status-of-a-circuit"></a>Získat stav okruhu

Tyto informace kdykoli můžete načíst pomocí `Get-AzureCircuit` rutiny. Volání bez parametrů jsou uvedeny všechny okruhy.

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

Informace o konkrétní okruh ExpressRoute můžete získat pomocí předání klíče služby jako parametr volání.

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

Podrobný popis všech parametrů získáte spuštěním následujícího příkladu:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Úprava okruhu

Můžete upravit některé vlastnosti okruhu ExpressRoute bez dopadu na připojení.

Můžete provádět následující úlohy došlo k výpadku:

* Povolit nebo zakázat doplněk ExpressRoute premium pro váš okruh ExpressRoute.
* Zvětšete šířku pásma váš okruh ExpressRoute zadaný na portu je k dispozici kapacita. Šířku pásma okruhu downgradu není podporován. 
* Změňte plán monitorování míry využití měření podle objemu dat na neomezená Data. Změna plánu měření z neomezená Data na měření podle objemu dat se nepodporuje.
* Můžete povolit nebo zakázat *povolit klasické operace*.

Odkazovat [ExpressRoute – nejčastější dotazy](expressroute-faqs.md) Další informace o omezení a omezení.

### <a name="enable-the-expressroute-premium-add-on"></a>Povolit doplněk ExpressRoute premium

Doplněk ExpressRoute premium pro existující okruh můžete povolit pomocí následující rutiny Powershellu:

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

Váš okruh teď budou mít funkce doplněk ExpressRoute premium povolené. Co nejdříve po úspěšném spuštění příkazu začne fakturace pro funkci doplněk premium.

### <a name="disable-the-expressroute-premium-add-on"></a>Zakázat doplněk ExpressRoute premium

> [!IMPORTANT]
> Tato operace může selhat, pokud používáte prostředky, které jsou větší než co je povolený pro standardní okruh.
> 
> 

#### <a name="considerations"></a>Požadavky

* Ujistěte se, že počet virtuálních sítí propojených s okruhem je menší než 10 předtím, než spustíte downgrade z úrovně premium na standard. Pokud to neuděláte, vaši žádost o aktualizaci se nezdaří a bude se vám účtovat sazby za úrovně premium.
* Je nutné zrušit všechny virtuální sítě v dalších geopolitických oblastí. Pokud ne, vaši žádost o aktualizaci se nezdaří a bude se vám účtovat sazby za úrovně premium.
* Směrovací tabulky musí být menší než 4 000 tras pro soukromý partnerský vztah. Pokud se velikost vašeho směrovací tabulku je větší než 4 000 tras, relace protokolu BGP zahodí a nebude možné opětovně povolena dokud počet předpon inzerovaných klesne pod 4000.

#### <a name="to-disable-the-premium-add-on"></a>Chcete-li zakázat doplněk premium

Doplněk ExpressRoute premium pro existující okruh můžete zakázat pomocí následující rutiny Powershellu:

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

Zkontrolujte, [ExpressRoute – nejčastější dotazy](expressroute-faqs.md) pro podporované možnosti šířky pásma pro vašeho poskytovatele. Můžete si vybrat libovolné velikosti, která je větší než velikost stávajících okruhů tak dlouho, dokud umožňuje fyzického portu (na kterém je vytvořený váš okruh).

> [!IMPORTANT]
> Bude pravděpodobně nutné znovu vytvořit okruh ExpressRoute, pokud je nedostatečné kapacity na existující port. Pokud v tomto umístění není k dispozici žádné další kapacitu, nemůže upgradovat okruh.
>
> Nejde snížit šířku pásma okruhu ExpressRoute bez přerušení. Downgrade šířky pásma je potřeba zrušit zřízení okruhu ExpressRoute a pak znova nezajistíte nového okruhu ExpressRoute.
> 
> 

#### <a name="resize-a-circuit"></a>Změna velikosti okruhu

Až se rozhodnete, jaké velikosti budete potřebovat, můžete změnit velikost váš okruh následující příkaz:

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

Jakmile váš okruh má byla velikost na straně Microsoftu, obraťte se na svého poskytovatele připojení aktualizovat konfiguraci na své straně tak, aby odpovídaly tuto změnu. Fakturace začíná v parametru aktualizované šířky pásma od této chvíle.

Pokud se zobrazí chybová zpráva při zvýšení šířky pásma okruhu, to znamená, že existuje na fyzický port, ve kterém je vytvořený váš existující okruh zůstane bez dostatečnou šířku pásma. Musíte odstranit tento okruh a vytvořit nový okruh velikosti, které potřebujete.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Zrušení zřízení a odstranění okruhu

### <a name="considerations"></a>Požadavky

* Je nutné zrušit všechny virtuální sítě z okruhu ExpressRoute k úspěšnému provedení této operace. Zkontrolujte, zda máte žádné virtuální sítě, které jsou propojeny k okruhu, pokud se tato operace se nezdaří.
* Pokud je stav zřizování poskytovatele služeb okruh ExpressRoute **zřizování** nebo **zřízená** , musíte pracovat se svým poskytovatelem služeb zrušit zřízení okruhu na své straně. Pokračujeme v rezervovat prostředky a účtovat až do dokončení zrušení zřízení okruhu a informuje nás poskytovatelem služeb.
* Pokud poskytovatel služeb okruh zruší (poskytovatel služeb Stav zřizování je nastavena na **nezřízeno**), pak můžete odstranit okruh. Tím se zastaví účtování okruhu.

#### <a name="delete-a-circuit"></a>Odstranit okruh

Váš okruh ExpressRoute můžete odstranit spuštěním následujícího příkazu:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```

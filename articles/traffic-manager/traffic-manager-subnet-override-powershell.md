---
title: Přepsání podsítě Azure Traffic Manager pomocí Azure PowerShell | Microsoft Docs
description: Tento článek vám pomůže porozumět tomu, jak se Traffic Manager přepsání podsítě používá k přepsání metody směrování Traffic Manager profilu pro směrování provozu do koncového bodu na základě IP adresy koncového uživatele prostřednictvím předdefinovaného rozsahu IP adres pro mapování koncových bodů pomocí Azure PowerShell.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 01bd3b1e945ee7c9ac16af7048536c0a9e2d731a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401584"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Přepsání podsítě Traffic Manager pomocí Azure PowerShellu

Přepsání podsítě Traffic Manager umožňuje změnit metodu směrování profilu.  Přidání přepsání bude směrovat provoz na základě IP adresy koncového uživatele s předdefinovaným rozsahem IP adres na mapování koncových bodů. 

## <a name="how-subnet-override-works"></a>Jak funguje přepsání podsítě

Při přidání přepsání podsítě do profilu Traffic Manageru se Traffic Manager nejdřív zkontroluje, jestli pro IP adresu koncového uživatele existuje přepsání podsítě. Je-li nalezen, bude dotaz DNS uživatele směrován do odpovídajícího koncového bodu.  Pokud mapování nenalezne, Traffic Manager se vrátí do původní metody směrování tohoto profilu. 

Rozsahy IP adres se dají zadat buď jako rozsahy CIDR (například 1.2.3.0/24), nebo jako rozsah adres (například 1.2.3.4-5.6.7.8). Rozsahy IP adres přidružené ke každému koncovému bodu musí být pro tento koncový bod jedinečné. V případě překrytí rozsahů IP adres mezi různými koncovými body dojde k odmítnutí profilu Traffic Manager.

Existují dva typy profilů směrování, které podporují přepsání podsítí:

* **Geografické** – Pokud Traffic Manager najde přepsání podsítě pro IP adresu dotazu DNS, bude směrovat dotaz na koncový bod bez ohledu na stav koncového bodu.
* **Výkon** – Pokud Traffic Manager nalezne přepsání podsítě pro IP adresu dotazu DNS, bude směrovat provoz pouze do koncového bodu, pokud je v pořádku.  Traffic Manager se vrátí do heuristiky směrování výkonu, pokud koncový bod přepsání podsítě není v pořádku.

## <a name="create-a-traffic-manager-subnet-override"></a>Vytvoření přepsání podsítě Traffic Manager

Pokud chcete vytvořit přepsání podsítě Traffic Manager, můžete pomocí Azure PowerShell přidat podsítě pro přepsání do koncového bodu Traffic Manager.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete spustit příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte PowerShell z počítače, budete potřebovat modul Azure PowerShell, 1.0.0 nebo novější. `Get-Module -ListAvailable Az`Nainstalovanou verzi můžete najít spuštěním. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit `Login-AzAccount` pro přihlášení k Azure.


1. **Načíst koncový bod Traffic Manager:**

    Pokud chcete povolit přepsání podsítě, načtěte koncový bod, do kterého chcete přepsání přidat, a uložte ho do proměnné pomocí [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Nahraďte název, název souboru a ResourceGroupName hodnotami koncového bodu, který se chystáte měnit.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Přidejte do koncového bodu rozsah IP adres:**
    
    Pokud chcete přidat rozsah IP adres ke koncovému bodu, použijte k přidání rozsahu [příkaz Add-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) .

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    Po přidání rozsahů aktualizujte koncový bod pomocí [set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) .

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
Odebrání rozsahu IP adres se dá dokončit pomocí [Remove-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0).

1.  **Načíst koncový bod Traffic Manager:**

    Pokud chcete povolit přepsání podsítě, načtěte koncový bod, do kterého chcete přepsání přidat, a uložte ho do proměnné pomocí [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Nahraďte název, název souboru a ResourceGroupName hodnotami koncového bodu, který se chystáte měnit.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Odeberte rozsah IP adres z koncového bodu:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     Po odebrání rozsahů aktualizujte koncový bod pomocí [set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) .

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [metodách směrování provozu](traffic-manager-routing-methods.md)Traffic Manager.

Přečtěte si o [způsobu směrování provozu podsítě](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)

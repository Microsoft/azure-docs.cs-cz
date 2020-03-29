---
title: Přepsání podsítě Azure Traffic Manager pomocí Azure PowerShellu | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže pochopit, jak se přepsání podsítě Traffic Manager používá k přepsání metody směrování profilu Traffic Manageru k přesměrování provozu na koncový bod na základě IP adresy koncového uživatele prostřednictvím předdefinovaného mapování rozsahu IP adres koncových bodů pomocí Azure Powershell.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 323093ec78a9486d19496b0ee90e37cb42eea341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938423"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Přepsání podsítě Traffic Manager pomocí Azure Powershellu

Přepsání podsítě Traffic Manager umožňuje změnit metodu směrování profilu.  Přidání přepsání bude směrovat provoz na základě IP adresy koncového uživatele s předdefinovaným rozsahem IP adres na mapování koncového bodu. 

## <a name="how-subnet-override-works"></a>Jak funguje přepsání podsítě

Když jsou do profilu správce provozu přidána lokální změny podsítě, Traffic Manager nejprve zkontroluje, zda pro IP adresu koncového uživatele neexistuje přepsání podsítě. Pokud je nalezen, dotaz DNS uživatele bude přesměrován na odpovídající koncový bod.  Pokud mapování není nalezeno, Traffic Manager se vrátí k původní metodě směrování profilu. 

Rozsahy IP adres lze zadat buď jako rozsahy CIDR (například 1.2.3.0/24), nebo jako rozsahy adres (například 1.2.3.4-5.6.7.8). Rozsahy IP přidružené ke každému koncovému bodu musí být pro tento koncový bod jedinečné. Jakékoli překrytí rozsahů IP mezi různými koncovými body způsobí, že profil bude odmítnut Traffic Managerem.

Existují dva typy směrovacích profilů, které podporují přepsání podsítě:

* **Geografické** – Pokud Traffic Manager najde přepsání podsítě pro IP adresu dotazu DNS, bude směrovat dotaz do koncového bodu bez ohledu na stav koncového bodu.
* **Výkon** – Pokud Traffic Manager najde přepsání podsítě pro IP adresu dotazu DNS, bude směrovat provoz pouze do koncového bodu, pokud je v pořádku.  Traffic Manager se vrátí k výkonu směrování heuristické, pokud koncový bod přepsání podsítě není v pořádku.

## <a name="create-a-traffic-manager-subnet-override"></a>Vytvoření přepsání podsítě Traffic Manager

Chcete-li vytvořit přepsání podsítě Traffic Manager, můžete pomocí prostředí Azure PowerShell přidat podsítě pro přepsání do koncového bodu Traffic Manager.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete spustit příkazy, které následují v [Prostředí Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním Prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatná interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte PowerShell z počítače, budete potřebovat modul Azure PowerShell, 1.0.0 nebo novější. Můžete spustit `Get-Module -ListAvailable Az` a najít nainstalovanou verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, musíte taky `Login-AzAccount` spustit pro přihlášení k Azure.


1. **Načíst koncový bod Traffic Manageru:**

    Chcete-li povolit přepsání podsítě, načtěte koncový bod, do který chcete přepsání přidat, a uložte jej do proměnné pomocí [get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Nahraďte název, název profilea a název_skupiny ResourceGroupName hodnotami koncového bodu, který měníte.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Přidejte rozsah IP adres do koncového bodu:**
    
    Chcete-li přidat rozsah IP adres do koncového bodu, přidejte rozsah pomocí [funkce Add-AzTrafficManagerIpAddressRange.](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0)

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    Po přidání rozsahů aktualizujte koncový bod pomocí [parametru Set-AzTrafficManagerEndpoint.](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0)

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
Odebrání rozsahu IP adres lze dokončit pomocí [remove-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0).

1.  **Načíst koncový bod Traffic Manageru:**

    Chcete-li povolit přepsání podsítě, načtěte koncový bod, do který chcete přepsání přidat, a uložte jej do proměnné pomocí [get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Nahraďte název, název profilea a název_skupiny ResourceGroupName hodnotami koncového bodu, který měníte.

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
     Po odebrání rozsahů aktualizujte koncový bod pomocí [parametru Set-AzTrafficManagerEndpoint.](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0)

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Další kroky
Další informace o [metodách směrování provozu](traffic-manager-routing-methods.md)traffic manageru .

Informace o [metodě směrování provozu podsítě](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)

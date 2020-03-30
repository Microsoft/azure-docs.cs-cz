---
title: Přepsání podsítě Azure Traffic Manager pomocí rozhraní příkazového příkazu Azure | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže pochopit, jak lze přepsat přepsání podsítě Traffic Manager k přepsání metody směrování profilu Traffic Manageru k přesměrování provozu na koncový bod na základě ip adresy koncového uživatele prostřednictvím předdefinovaného mapování rozsahu IP adres na koncový bod.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 818b692884bd9d31efd08663a582ebcfec2032e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938474"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Přepsání podsítě Traffic Manager pomocí rozhraní příkazového příkazu Azure

Přepsání podsítě Traffic Manager umožňuje změnit metodu směrování profilu.  Přidání přepsání bude směrovat provoz na základě IP adresy koncového uživatele s předdefinovaným rozsahem IP adres na mapování koncového bodu. 

## <a name="how-subnet-override-works"></a>Jak funguje přepsání podsítě

Když jsou do profilu správce provozu přidána lokální změny podsítě, Traffic Manager nejprve zkontroluje, zda pro IP adresu koncového uživatele neexistuje přepsání podsítě. Pokud je nalezen, dotaz DNS uživatele bude přesměrován na odpovídající koncový bod.  Pokud mapování není nalezeno, Traffic Manager se vrátí k původní metodě směrování profilu. 

Rozsahy IP adres lze zadat buď jako rozsahy CIDR (například 1.2.3.0/24), nebo jako rozsahy adres (například 1.2.3.4-5.6.7.8). Rozsahy IP přidružené ke každému koncovému bodu musí být pro tento koncový bod jedinečné. Jakékoli překrytí rozsahů IP mezi různými koncovými body způsobí, že profil bude odmítnut Traffic Managerem.

Existují dva typy směrovacích profilů, které podporují přepsání podsítě:

* **Geografické** – Pokud Traffic Manager najde přepsání podsítě pro IP adresu dotazu DNS, bude směrovat dotaz do koncového bodu bez ohledu na stav koncového bodu.
* **Výkon** – Pokud Traffic Manager najde přepsání podsítě pro IP adresu dotazu DNS, bude směrovat provoz pouze do koncového bodu, pokud je v pořádku.  Traffic Manager se vrátí k výkonu směrování heuristické, pokud koncový bod přepsání podsítě není v pořádku.

## <a name="create-a-traffic-manager-subnet-override"></a>Vytvoření přepsání podsítě Traffic Manager

Chcete-li vytvořit přepsání podsítě Traffic Manager, můžete pomocí rozhraní příkazového příkazu Azure přidat podsítě pro přepsání do koncového bodu Traffic Manager.

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Aktualizujte koncový bod Traffic Manageru přepsáním podsítě.
Pomocí azure cli aktualizovat koncový bod s [az sítě traffic-manager aktualizace koncového bodu](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

```azurecli

### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints

```

Rozsahy ADRES IP můžete odebrat spuštěním [aktualizace koncového bodu správce síťových přenosů az](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) s možností **--remove.**

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>Další kroky
Další informace o [metodách směrování provozu](traffic-manager-routing-methods.md)traffic manageru .

Informace o [metodě směrování provozu podsítě](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
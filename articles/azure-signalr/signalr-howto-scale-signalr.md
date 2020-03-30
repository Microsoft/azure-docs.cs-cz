---
title: Škálování instance služby Azure SignalR
description: Zjistěte, jak škálovat instanci služby Azure SignalR a přidat nebo snížit kapacitu, a to prostřednictvím portálu Azure nebo vytelatého příkazového příkazu Azure.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: zhshang
ms.openlocfilehash: c8d74342e624b837c7ee803a2bcdcc12a3fb814b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659283"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Jak škálovat instanci služby Azure SignalR?
Tento článek ukazuje, jak škálovat instanci služby Azure SignalR. Existují dva scénáře pro škálování, škálování nakapacitu a horizontální navýšení kapacity.

* [Škálování navýšit](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)kapacitu: Získejte další jednotky, připojení, zprávy a další. Můžete vertikálně navýšit tím, že změníte cenovou úroveň z free na standard.
* [Horizontální navýšení kapacity](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Zvýšení počtu jednotek SignalR. Můžete škálovat až na 100 jednotek.

Nastavení měřítka trvá několik minut. Ve vzácných případech může trvat přibližně 30 minut. Nevyžadují, abyste změnili kód nebo znovu nasadili serverové aplikace.

Informace o cenách a kapacitách jednotlivých služeb SignalR najdete [v tématu Podrobnosti o cenách služby Azure SignalR](https://azure.microsoft.com/pricing/details/signalr-service/).  

> [!NOTE]
> Změna služby SignalR z úrovně **Free** na **úroveň Standard** nebo naopak, IP adresa veřejné služby se změní a obvykle trvá 30-60 minut, než se změna na servery DNS rozšíří na servery DNS po celém internetu. Vaše služba může být nedostupná před aktualizací služby DNS. Obecně se nedoporučuje měnit cenovou úroveň příliš často.


## <a name="scale-on-azure-portal"></a>Škálování na webu Azure Portal

1. Otevřete v prohlížeči portál [Azure Portal](https://portal.azure.com).

2. Na stránce SignalR Service v yberte v levé nabídce **měřítko**.
   
3. Zvolte cenovou úroveň a klikněte na **Vybrat**. Nastavte počet jednotek pro **úroveň Standard.**
   
    ![Škálování na portálu](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Klikněte na **Uložit**.

## <a name="scale-using-azure-cli"></a>Škálování pomocí azure cli

Tento skript vytvoří nový prostředek služby SignalR na úroveň **Free** tier a novou skupinu prostředků a škáluje ji až na **úroveň Standard.** 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

Poznamenejte si vygenerovaný název pro novou skupinu prostředků. Tento název skupiny prostředků použijete, když budete chtít odstranit všechny prostředky skupiny.

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>Porovnání cenových úrovní

Podrobné informace, jako jsou zahrnuté zprávy a připojení pro každou cenovou úroveň, naleznete v [tématu SignalR Service Pricing Details](https://azure.microsoft.com/pricing/details/signalr-service/).

Tabulka omezení služeb, kvót a omezení v každé vrstvě naleznete v tématu [Omezení služby SignalR](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits).

## <a name="next-steps"></a>Další kroky

V této příručce jste se dozvěděli o tom, jak škálovat jednu instanci služby SignalR.

Více koncových bodů jsou také podporovány pro škálování, horizontálního oddílu a mezi oblastmi scénáře.

> [!div class="nextstepaction"]
> [škálování služby SignalR s více instancemi](./signalr-howto-scale-multi-instances.md)

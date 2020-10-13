---
title: Škálování instance služby signalizace Azure
description: Naučte se škálovat instance služby signalizace Azure za účelem přidání nebo snížení kapacity, prostřednictvím Azure Portal nebo Azure CLI.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 9/9/2020
ms.author: zhshang
ms.custom: devx-track-azurecli
ms.openlocfilehash: bb01f2f96e5db4c94e759b114818360e6084255f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89595763"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Jak škálovat instanci služby signalizace Azure?
V tomto článku se dozvíte, jak škálovat instanci služby signalizace Azure. Existují dva scénáře pro škálování, horizontální navýšení kapacity a horizontální navýšení kapacity.

* [Horizontální navýšení kapacity](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Získejte další jednotky, připojení, zprávy a další. Škálujte nahoru změnou cenové úrovně od bezplatné na standard.
* [Horizontální](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)navýšení kapacity: zvyšte počet jednotek signalizace. Můžete škálovat až na až 100 jednotek. K dispozici jsou omezené možnosti jednotek pro škálování: 1, 2, 5, 10, 20, 50 a 100 jednotek pro jednu instanci služby signalizace.

Použití nastavení škálování trvá několik minut. Ve výjimečných případech může trvat přibližně 30 minut, než se použije. Nevyžadují, abyste změnili kód nebo znovu nasadili serverovou aplikaci.

Informace o cenách a kapacitě jednotlivých služeb signalizace najdete v [podrobnostech o cenách služby Azure Signal Service](https://azure.microsoft.com/pricing/details/signalr-service/).  

> [!NOTE]
> Změna služby signalizace z úrovně **Free** na úroveň **Standard** nebo naopak dojde ke změně IP adresy veřejné služby a obvykle zabere 30-60 minut na rozšíření změn na servery DNS v celém internetu. Vaše služba může být nedosažitelná, než se aktualizuje služba DNS. Obecně se nedoporučuje měnit cenovou úroveň příliš často.


## <a name="scale-on-azure-portal"></a>Škálování podle Azure Portal

1. Otevřete v prohlížeči portál [Azure Portal](https://portal.azure.com).

2. Na stránce služby signalizace v nabídce vlevo vyberte možnost **škálovat**.
   
3. Zvolte cenovou úroveň a pak klikněte na **Vybrat**. Nastavte počet jednotek pro úroveň **Standard** .
   
    ![Škálování na portálu](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Klikněte na **Uložit**.

## <a name="scale-using-azure-cli"></a>Škálování pomocí Azure CLI

Tento skript vytvoří nový prostředek služby signálu úrovně **Free** a novou skupinu prostředků a škáluje ji na úroveň **Standard** . 

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

## <a name="compare-pricing-tiers"></a>Porovnat cenové úrovně

Podrobné informace, jako jsou zahrnuté zprávy a připojení pro jednotlivé cenové úrovně, najdete v tématu [Podrobnosti o cenách služby signaler](https://azure.microsoft.com/pricing/details/signalr-service/).

Tabulka omezení služby, kvót a omezení v každé úrovni najdete v tématu [omezení služby Signal Service](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits).

## <a name="next-steps"></a>Další kroky

V této příručce jste se dozvěděli, jak škálovat instanci služby jednoho signálu.

Pro scénáře škálování, horizontálního dělení a mezi oblastmi se podporuje i více koncových bodů.

> [!div class="nextstepaction"]
> [škálování služby signalizace s víc instancemi](./signalr-howto-scale-multi-instances.md)

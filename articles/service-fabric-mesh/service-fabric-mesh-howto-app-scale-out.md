---
title: Škálování služeb v aplikaci Azure Service Fabric mřížky | Dokumentace Microsoftu
description: Zjistěte, jak nezávisle na sobě škálovat služby v rámci aplikace běžící na služby prostředků infrastruktury sítě pomocí Azure CLI.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: c0350b767b65aee0c4611bb8fa6f635a651d33dc
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076005"
---
# <a name="scale-services-within-an-application-running-on-service-fabric-mesh"></a>Škálování služeb v rámci aplikace běžící na služby prostředků infrastruktury sítě

Tento článek popisuje, jak nezávisle na sobě škálovat mikroslužeb v rámci aplikace. V tomto příkladu vizuálních objektů aplikace se skládá ze dvou mikroslužeb; `web` a `worker`. 

`web` Služba je aplikace ASP.NET Core s webovou stránku, která ukazuje trojúhelníky v prohlížeči. Prohlížeč zobrazí jeden trojúhelník u každé instance `worker` služby. 

`worker` Služby v prostoru přesune na trojúhelník v nastaveném intervalu a odešle umístění na trojúhelník `web` služby. Používá DNS pro překlad adres `web` služby.

## <a name="set-up-service-fabric-mesh-cli"></a>Nastavení rozhraní příkazového řádku mřížky na Service Fabric 
K dokončení této úlohy můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Instalace modulu Azure Service Fabric mřížky CLI rozšíření pomocí těchto [pokyny](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k Azure a nastavení předplatného.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků k nasazení aplikace. Můžete použít existující skupinu prostředků a tento krok přeskočit. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application-with-one-worker-service"></a>Nasazení aplikace se službou jeden pracovní proces.
Vytvoření aplikace pomocí skupiny prostředků `deployment create` příkazu.

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
Ve výstupu předchozího příkazu nasadí Linux pomocí [mesh_rp.base.linux.json šablony](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json). Pokud chcete nasadit aplikace pro Windows, použijte [mesh_rp.base.windows.json šablony](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json). Image kontejnerů Windows jsou větší než Image kontejnerů s Linuxem a může trvat delší dobu na nasazení.

Za pár minut by měla vrátit příkazu pomocí:

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Otevřete aplikaci
Po úspěšném nasazení aplikace, získejte veřejnou IP adresu pro koncový bod služby a otevřít v prohlížeči. Ji by měl zobrazení webové stránky s jeden trojúhelník procházení místo.

Nasazení příkazu vrátí veřejnou IP adresu koncového bodu služby. Můžete také zadávat dotazy sítě prostředek, který chcete zjistit veřejnou IP adresu koncového bodu služby.
 
Název sítě prostředků pro tuto aplikaci je `visualObjectsNetwork`, načíst informace o něm pomocí následujícího příkazu. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>Škálování `worker` služby

Škálování `worker` service tři instance pomocí následujícího příkazu. 

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
Ve výstupu předchozího příkazu nasadí Linux pomocí [mesh_rp.scaleout.linux.json šablony](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json). Pokud chcete nasadit aplikace pro Windows, použijte [mesh_rp.scaleout.windows.json šablony](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json). Image kontejnerů Windows jsou větší než Image kontejnerů s Linuxem a může trvat delší dobu na nasazení.

Po úspěšně nasazení aplikace v prohlížeči by měl být zobrazení na webové stránce pomocí tři trojúhelníky procházení místo.

## <a name="delete-the-resources"></a>Odstranit prostředky

Pokud chcete ušetřit omezené prostředky přiřazené do programu preview, odstraňte prostředky často. Odstranit prostředky vztahující se na tomto příkladu, odstraňte skupinu prostředků, ve které se nasadily.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Další postup
- Zobrazit ukázkovou aplikaci vizuální objekty v [Githubu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects).
- Další informace o modelu prostředků služby Service Fabric najdete v tématu [modelu služby Service Fabric mřížky prostředků](service-fabric-mesh-service-fabric-resources.md).
- Další informace o Service Fabric sítě najdete v článku [sítě pro Service Fabric přehled](service-fabric-mesh-overview.md).
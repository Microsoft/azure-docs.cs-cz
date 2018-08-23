---
title: Škálování služeb v aplikaci Azure Service Fabric mřížky | Dokumentace Microsoftu
description: Zjistěte, jak nezávisle na sobě škálovat služby v rámci aplikace běžící na služby prostředků infrastruktury sítě pomocí Azure CLI.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/08/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: e68bcd135c33c7fd83908b8fed0fd098a698fd36
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054004"
---
# <a name="scale-services-within-an-application-running-on-service-fabric-mesh"></a>Škálování služeb v rámci aplikace běžící na služby prostředků infrastruktury sítě

Tento článek popisuje, jak nezávisle na sobě škálovat mikroslužeb v rámci aplikace. V tomto příkladu vizuálních objektů aplikace se skládá ze dvou mikroslužeb: `web` a `worker`.

`web` Služba je aplikace ASP.NET Core s webovou stránku, která ukazuje trojúhelníky v prohlížeči. Prohlížeč zobrazí jeden trojúhelník u každé instance `worker` služby.

`worker` Služby v prostoru přesune na trojúhelník v nastaveném intervalu a odešle umístění na trojúhelník `web` služby. Používá DNS pro překlad adres `web` služby.

## <a name="set-up-service-fabric-mesh-cli"></a>Nastavení rozhraní příkazového řádku služby Service Fabric Mesh

K dokončení této úlohy můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Nainstalujte rozšiřující modul Azure Service Fabric Mesh CLI pomocí těchto [pokynů](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure a nastavte své předplatné.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pro nasazení aplikace.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application-with-one-worker-service"></a>Nasazení aplikace se službou jeden pracovní proces.

Vytvoření aplikace pomocí skupiny prostředků `deployment create` příkazu.

Následující příklad nasadí aplikaci Linux pomocí [mesh_rp.base.linux.json šablony](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json). Chcete-li nasadit aplikaci s Windows, použijte [[mesh_rp.base.windows.json šablony](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json). Image kontejnerů Windows jsou větší než image kontejnerů Linuxu a jejich nasazení může trvat delší dobu.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

V několika minutách se příkaz by měl vrátit s:

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Otevření aplikace

Nasazení příkazu vrátí veřejnou IP adresu koncového bodu služby. Po úspěšně nasazení aplikace, získejte veřejnou IP adresu pro koncový bod služby a otevřete ji v prohlížeči. Zobrazí se webová stránka s přesunutí trojúhelník.

Název sítě prostředků pro tuto aplikaci je `visualObjectsNetwork`. Informace o aplikaci, jako je například její popis, umístění, skupinu prostředků, atd. můžete zobrazit pomocí následujícího příkazu:

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>Škálování `worker` služby

Škálování `worker` service tři instance pomocí následujícího příkazu. Následující příklad nasadí aplikaci Linux pomocí [mesh_rp.scaleout.linux.json šablony](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json). Chcete-li nasadit aplikaci s Windows, použijte [mesh_rp.scaleout.windows.json šablony](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json). Mějte na paměti, že může trvat delší dobu nasazení větších imagí kontejnerů.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```

Po úspěšně nasazení aplikace v prohlížeči zobrazit webovou stránku pomocí tří přesunutí trojúhelníky.

## <a name="delete-the-resources"></a>Odstranit prostředky

Často odstraníte prostředky, které už používáte v Azure. Pokud chcete odstranit prostředky související se v tomto příkladu, odstraňte skupinu prostředků ve které se nasadily (čímž odstraníte všechno, co spojený s vybranou skupinou prostředků) pomocí následujícího příkazu:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Další postup

- Zobrazit ukázkovou aplikaci vizuální objekty v [Githubu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects).
- Další informace o modelu prostředků služby Service Fabric najdete v tématu [modelu služby Service Fabric mřížky prostředků](service-fabric-mesh-service-fabric-resources.md).
- Další informace o Service Fabric sítě najdete v článku [sítě pro Service Fabric přehled](service-fabric-mesh-overview.md).
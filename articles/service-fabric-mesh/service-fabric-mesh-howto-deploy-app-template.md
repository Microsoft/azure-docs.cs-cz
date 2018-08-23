---
title: Nasazení aplikace do služby Azure Service Fabric sítě pomocí šablony | Dokumentace Microsoftu
description: Zjistěte, jak nasadit aplikaci .NET Core pro Service Fabric sítě ze šablony pomocí rozhraní příkazového řádku Azure.
services: service-fabric-mesh
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
ms.date: 07/12/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 356e8019f9a4a64cb1c1c113d0f44990aa4e0f95
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42056793"
---
# <a name="deploy-a-service-fabric-mesh-application-to-service-fabric-mesh-using-a-template"></a>Nasazení aplikace Service Fabric sítě se Service Fabric pomocí šablony
Tento článek ukazuje, jak nasadit aplikaci .NET Core se Service Fabric pomocí šablony. Jakmile budete hotovi, budete mít hlasovací aplikaci s front-endu, která ukládá výsledky hlasování do back-end služby v clusteru webového rozhraní ASP.NET Core. Front-endu používá službu DNS pro překlad adres back-end služby.

## <a name="set-up-service-fabric-mesh-cli"></a>Nastavení rozhraní příkazového řádku služby Service Fabric Mesh 
K dokončení této úlohy můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Nainstalujte rozšiřující modul Azure Service Fabric Mesh CLI pomocí těchto [pokynů](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k Azure a nastavte své předplatné.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků pro nasazení aplikace. Můžete použít některou ze stávajících skupin prostředků a tento krok přeskočit. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Nasazení aplikace
Vytvoření aplikace pomocí skupiny prostředků `deployment create` příkazu.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

Ve výstupu předchozího příkazu nasadí aplikace Windows pomocí [mesh_rp.windows.json šablony](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json). Pokud chcete nasadit aplikace pro Linux, použijte [mesh_rp.linux.json šablony](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.linux.json). Image kontejnerů Windows jsou větší než image kontejnerů Linuxu a jejich nasazení může trvat delší dobu.

Za pár minut by měla vrátit příkazu pomocí:

`VotingApp has been deployed successfully on VotingAppNetwork with public ip address <IP address>` 

## <a name="open-the-application"></a>Otevření aplikace
Po úspěšném nasazení aplikace, získejte veřejnou IP adresu pro koncový bod služby a otevřít v prohlížeči. Zobrazí se následující webové stránky. 

![Hlasovací aplikace](./media/service-fabric-mesh-howto-deploy-app-template/VotingApplication.png)

Teď můžete přidat do aplikace Možnosti hlasování a Hlasujte o nich ho nebo odstranit Možnosti hlasování.

Nasazení příkazu vrátí veřejnou IP adresu koncového bodu služby. Volitelně můžete také zadávat dotazy sítě prostředek, který chcete zjistit veřejnou IP adresu koncového bodu služby. 

Název sítě prostředků pro tuto aplikaci je `VotingAppNetwork`, načíst informace o něm pomocí následujícího příkazu. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name VotingAppNetwork
```

## <a name="check-the-application-details"></a>Kontrola podrobností aplikace
Stav aplikace můžete zkontrolovat pomocí příkazu `app show`. Název aplikace pro nasazenou aplikaci je "VotingApp", tedy načíst jeho podrobnosti. 

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name VotingApp
```

## <a name="list-the-deployed-applications"></a>Výpis nasazených aplikací
Seznam aplikací, které jste nasadili v předplatném, zobrazíte pomocí příkazu app list. 

```azurecli-interactive
az mesh app list -o table
```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už nepotřebujete aplikace a s ní související prostředky, odstraňte skupinu prostředků, které je obsahují. 

```azurecli-interactive
az group delete --resource-group myResourceGroup  
``` 

## <a name="next-steps"></a>Další postup
- Zobrazit ukázkové hlasovací aplikace na [Githubu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp).
- Další informace o Service Fabric sítě najdete v článku [sítě pro Service Fabric přehled](service-fabric-mesh-overview.md).



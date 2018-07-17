---
title: Nasazení aplikace do služby Azure Service Fabric sítě pomocí šablony | Dokumentace Microsoftu
description: Zjistěte, jak nasadit aplikaci .NET Core pro Service Fabric sítě ze šablony pomocí rozhraní příkazového řádku Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceputal
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 11b3ad6bf83eebfc94ead6f8a730b8e6a6b8de2f
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076007"
---
# <a name="deploy-a-service-fabric-mesh-application-to-service-fabric-mesh-using-a-template"></a>Nasazení aplikace Service Fabric sítě se Service Fabric pomocí šablony
Tento článek ukazuje, jak nasadit aplikaci .NET Core se Service Fabric pomocí šablony. Jakmile budete hotovi, budete mít hlasovací aplikaci s front-endu, která ukládá výsledky hlasování do back-end služby v clusteru webového rozhraní ASP.NET Core. Front-endu používá službu DNS pro překlad adres back-end služby.

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

## <a name="deploy-the-application"></a>Nasazení aplikace
Vytvoření aplikace pomocí skupiny prostředků `deployment create` příkazu.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

Ve výstupu předchozího příkazu nasadí aplikace Windows pomocí [mesh_rp.windows.json šablony](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json). Pokud chcete nasadit aplikace pro Linux, použijte [mesh_rp.linux.json šablony](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.linux.json). Image kontejnerů Windows jsou větší než Image kontejnerů s Linuxem a může trvat delší dobu na nasazení.

Za pár minut by měla vrátit příkazu pomocí:

`VotingApp has been deployed successfully on VotingAppNetwork with public ip address <IP address>.` 

Například IP adresa je 13.68.129.22.

## <a name="open-the-application"></a>Otevřete aplikaci
Po úspěšném nasazení aplikace, připojení ke koncovému bodu služby (z předchozího příkladu 13.68.129.22) v prohlížeči.  

![Hlasovací aplikace](./media/service-fabric-mesh-howto-deploy-app-template/VotingApplication.png)

Teď můžete přidat do aplikace Možnosti hlasování a Hlasujte o nich ho nebo odstranit Možnosti hlasování.

## <a name="check-the-application-details"></a>Zkontrolujte podrobnosti o aplikaci
Můžete zkontrolovat stav vaší aplikace pomocí `app show` příkazu. Název aplikace pro nasazenou aplikaci je "VotingApp", tedy načíst jeho podrobnosti. 

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name VotingApp
```

Můžete také zadávat dotazy síťovému prostředku, pokud chcete zjistit IP adresu kontejneru, ve kterém je služba nasazená spuštěním příkazu "az mřížky network show":

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name VotingAppNetwork
```

## <a name="list-the-deployed-applications"></a>Výpis nasazených aplikací
Příkazem "seznamu aplikací" zobrazíte seznam aplikací, které jste nasadili do vašeho předplatného. 

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



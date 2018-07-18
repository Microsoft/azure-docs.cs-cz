---
title: Store stavu tak, že připojí svazek soubory Azure na základě uvnitř kontejneru v aplikaci Service Fabric mřížky | Dokumentace Microsoftu
description: Zjistěte, jak k uložení stavu tak, že připojí svazek soubory Azure na základě uvnitř kontejneru v aplikaci Service Fabric sítě pomocí Azure CLI.
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
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 94a4e17e6285893520a2f6482b32a69b1229e2fa
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090628"
---
# <a name="store-state-by-mounting-azure-files-based-volume-in-service-fabric-mesh-application"></a>Store stavu tak, že připojí svazek soubory Azure na základě aplikace Service Fabric mřížky

Tento článek popisuje, jak k uložení stavu ve službě soubory Azure tak, že připojí svazek uvnitř kontejneru aplikace Service Fabric mřížky. V tomto příkladu má aplikace čítač služby ASP.NET Core s webovou stránkou, která zobrazuje hodnota čítače v prohlížeči. 

`counterService` Perodically přečte hodnotu čítače ze souboru, zvýší jeho a zápis zpátky do souboru. Soubor je uložen ve složce, která je připojena v svazku se opírá o sdílenou složku služby soubory Azure. 

## <a name="set-up-service-fabric-mesh-cli"></a>Nastavení rozhraní příkazového řádku mřížky na Service Fabric 
K dokončení této úlohy můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Instalace modulu Azure Service Fabric mřížky CLI rozšíření pomocí těchto [pokyny](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k Azure a nastavení předplatného.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-file-share"></a>Vytvoření sdílené složky 
Vytvoření sdílené složky Azure pomocí těchto [pokyny](/azure/storage/files/storage-how-to-create-file-share). Název účtu úložiště a klíč účtu úložiště, název sdílené složky souborů jsou odkazovány jako `<storageAccountName>`, `<storageAccountKey>`, a `<fileShareName>` v následujících pokynech.

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků k nasazení aplikace. Můžete použít existující skupinu prostředků a tento krok přeskočit. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>Nasazení šablony

Vytvoření aplikace a související prostředky, pomocí následujícího příkazu a zadejte hodnoty pro `storageAccountName`, `storageAccountKey` a `fileShareName` z předchozího kroku.

`storageAccountKey` Parametr v šabloně `securestring`. Se nebude zobrazovat ve stavu nasazení a `az mesh service show` příkazy. Ujistěte se, že je správně zadán v následujícím příkazu.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

Ve výstupu předchozího příkazu nasadí aplikaci Linux pomocí [mesh_rp.linux.json šablony](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json). Pokud chcete nasadit aplikace pro Windows, použijte [mesh_rp.windows.json šablony](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json). Image kontejnerů Windows jsou větší než Image kontejnerů s Linuxem a může trvat delší dobu na nasazení.

Za pár minut by měla vrátit příkazu pomocí:

`counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Otevřete aplikaci
Po úspěšném nasazení aplikace, získejte veřejnou IP adresu pro koncový bod služby a otevřít v prohlížeči. Na webové stránce se zobrazí s hodnotou čítače aktualizuje každou sekundu.

Nasazení příkazu vrátí veřejnou IP adresu koncového bodu služby. Volitelně můžete také zadávat dotazy sítě prostředek, který chcete zjistit veřejnou IP adresu koncového bodu služby. 
 
Název sítě prostředků pro tuto aplikaci je `counterAppNetwork`, načíst informace o něm pomocí následujícího příkazu. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Ověřte, že aplikace je možné použít svazku
Aplikace vytvoří soubor s názvem `counter.txt` v souboru sdílet uvnitř `counter/counterService` složky. Obsah tohoto souboru je hodnota čítače se zobrazuje na webové stránce.

Stáhnout soubor pomocí libovolného nástroje, který umožňuje procházení sdílenou složku Azure Files. [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) je příkladem tento nástroj.

## <a name="delete-the-resources"></a>Odstranit prostředky

Pokud chcete ušetřit omezené prostředky přiřazené do programu preview, odstraňte prostředky často. Odstranit prostředky vztahující se na tomto příkladu, odstraňte skupinu prostředků, ve které se nasadily.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Další postup

- Zobrazit soubory Azure svazku ukázkovou aplikaci na [Githubu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Další informace o modelu prostředků služby Service Fabric najdete v tématu [modelu služby Service Fabric mřížky prostředků](service-fabric-mesh-service-fabric-resources.md).
- Další informace o Service Fabric sítě najdete v článku [sítě pro Service Fabric přehled](service-fabric-mesh-overview.md).

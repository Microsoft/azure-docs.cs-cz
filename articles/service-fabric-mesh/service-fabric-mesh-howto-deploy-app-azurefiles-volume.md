---
title: Store stavu v aplikaci Azure Service Fabric sítě pomocí připojení svazku soubory Azure na základě uvnitř kontejneru | Dokumentace Microsoftu
description: Zjistěte, jak ukládat stavu v aplikaci Azure Service Fabric sítě tak, že připojení svazku soubory Azure na základě uvnitř kontejnerů pomocí Azure CLI.
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
ms.date: 08/09/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 3b350deff2883761af6a3a2b3c5c9ef22235bde0
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054616"
---
# <a name="store-state-in-an-azure-service-fabric-mesh-application-by-mounting-an-azure-files-based-volume-inside-the-container"></a>Stav Store v aplikaci Azure Service Fabric sítě pomocí připojení soubory Azure na základě objemu uvnitř kontejneru

Tento článek popisuje, jak k uložení stavu ve službě soubory Azure tak, že připojí svazek uvnitř kontejneru aplikace Service Fabric mřížky. V tomto příkladu má aplikace čítač služby ASP.NET Core s webovou stránkou, která zobrazuje hodnota čítače v prohlížeči. 

`counterService` Pravidelně načítá hodnotu čítače ze souboru, zvýší jeho a zápis zpátky do souboru. Soubor je uložen ve složce, která je připojena v svazku se opírá o sdílenou složku služby soubory Azure.

## <a name="prerequisites"></a>Požadavky

K dokončení této úlohy můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Pokud chcete použít rozhraní příkazového řádku Azure v tomto článku, ujistěte se, že `az --version` vrátí alespoň `azure-cli (2.0.43)`.  Nainstalovat (nebo aktualizovat) rozšiřující modul Azure mřížky příkazového řádku Service Fabric pomocí těchto [pokyny](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure a nastavte své předplatné.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-file-share"></a>Vytvoření sdílené složky

Vytvoření sdílené složky Azure pomocí těchto [pokyny](/azure/storage/files/storage-how-to-create-file-share). Název účtu úložiště a klíč účtu úložiště, název sdílené složky souborů jsou odkazovány jako `<storageAccountName>`, `<storageAccountKey>`, a `<fileShareName>` v následujících pokynech. Tyto hodnoty jsou k dispozici na portálu Azure:
* <storageAccountName> – V části **účty úložiště**, jedná se o název účtu úložiště, který jste použili při vytváření sdílené složky.
* <storageAccountKey> -Výběr účtu úložiště v rámci **účty úložiště** a pak vyberte **přístupové klíče** a použijte hodnoty v rámci **key1**.
* <fileShareName> -Výběr účtu úložiště v rámci **účty úložiště** a pak vyberte **soubory**. Název, který má používat je název sdílené složky, kterou jste právě vytvořili.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pro nasazení aplikace. Následující příkaz vytvoří skupinu prostředků s názvem `myResourceGroup` v umístění východní USA.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>Nasazení šablony

Vytvoření aplikace a související prostředky, pomocí následujícího příkazu a zadejte hodnoty pro `storageAccountName`, `storageAccountKey` a `fileShareName` z dříve [vytvoření sdílené složky](#create-a-file-share) kroku.

`storageAccountKey` Parametr v šabloně je zabezpečený řetězec. Se nebude zobrazovat ve stavu nasazení a `az mesh service show` příkazy. Ujistěte se, že je správně zadán v následujícím příkazu.

Následující příkaz nasadí aplikaci Linux pomocí [mesh_rp.linux.json šablony](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json). Chcete-li nasadit aplikaci s Windows, použijte [mesh_rp.windows.json šablony](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json). Mějte na paměti, že může trvat delší dobu nasazení větších imagí kontejnerů.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

V několika minutách se příkaz by měl vrátit s `counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>`

## <a name="open-the-application"></a>Otevření aplikace

Nasazení příkazu vrátí veřejnou IP adresu koncového bodu služby. Po úspěšně nasazení aplikace, získejte veřejnou IP adresu pro koncový bod služby a otevřete ji v prohlížeči. Zobrazí se na webové stránce s hodnotou čítače aktualizuje každou sekundu.

Název sítě prostředků pro tuto aplikaci je `counterAppNetwork`. Informace o aplikaci, jako je například její popis, umístění, skupinu prostředků, atd. můžete zobrazit pomocí následujícího příkazu:

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Ověřte, že aplikace je možné použít svazku

Aplikace vytvoří soubor s názvem `counter.txt` v souboru sdílet uvnitř `counter/counterService` složky. Obsah tohoto souboru je hodnota čítače se zobrazuje na webové stránce.

Soubor stáhnout využít libovolný nástroj, který umožňuje procházení sdílené složky služby soubory Azure, jako [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="delete-the-resources"></a>Odstranit prostředky

Často odstraníte prostředky, které už používáte v Azure. Pokud chcete odstranit prostředky související se v tomto příkladu, odstraňte skupinu prostředků ve které se nasadily (čímž odstraníte všechno, co spojený s vybranou skupinou prostředků) pomocí následujícího příkazu:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Další postup

- Zobrazit soubory Azure svazku ukázkovou aplikaci na [Githubu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Další informace o modelu prostředků služby Service Fabric najdete v tématu [modelu služby Service Fabric mřížky prostředků](service-fabric-mesh-service-fabric-resources.md).
- Další informace o Service Fabric sítě najdete v článku [sítě pro Service Fabric přehled](service-fabric-mesh-overview.md).
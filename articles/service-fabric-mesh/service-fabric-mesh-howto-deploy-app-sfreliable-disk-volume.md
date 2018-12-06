---
title: Použití s vysokou dostupností Service Fabric Reliable diskový svazek v aplikaci Azure Service Fabric mřížky | Dokumentace Microsoftu
description: Zjistěte, jak k uložení stavu v aplikaci Azure Service Fabric sítě tak, že připojí svazek Service Fabric Reliable založené na disku uvnitř kontejnerů pomocí Azure CLI.
services: service-fabric-mesh
documentationcenter: .net
author: ashishnegi
manager: raunakpandya
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: 2ed64bbf0da252285184e2ca6fef0555a85ce149
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52955317"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Připojit svazek s vysokou dostupností Service Fabric Reliable založené na disku v aplikaci Service Fabric mřížky 
Běžnou metodu zachování stavu s využitím kontejnerové aplikace je použití vzdáleného úložiště, jako je Azure File Storage nebo databáze, jako je Azure Cosmos DB. Tím se spojují významné pro čtení a zápisu latence sítě do vzdáleného úložiště.

Tento článek popisuje, jak k uložení stavu s vysokou dostupností Service Fabric Reliable disku tak, že připojí svazek uvnitř kontejneru aplikace Service Fabric mřížky.
Service Fabric Reliable disku poskytuje svazky pro místní čtení zápisů replikují v rámci clusteru Service Fabric k zajištění vysoké dostupnosti. To odebere volání sítě pro čtení a zápisy, snižuje latence sítě. Pokud kontejner restartuje nebo přesune do jiného uzlu, nové instance kontejneru se zobrazí stejném svazku jako starší verzi. Proto je efektivní a vysoce dostupné.

V tomto příkladu má aplikace čítač služby ASP.NET Core s webovou stránkou, která zobrazuje hodnota čítače v prohlížeči.

`counterService` Pravidelně načítá hodnotu čítače ze souboru, zvýší jeho a zápis zpátky do souboru. Soubor je uložen ve složce, která je připojena v Service Fabric Reliable disku se opírá o svazku.

## <a name="prerequisites"></a>Požadavky

K dokončení této úlohy můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Pokud chcete použít rozhraní příkazového řádku Azure v tomto článku, ujistěte se, že `az --version` vrátí alespoň `azure-cli (2.0.43)`.  Nainstalovat (nebo aktualizovat) rozšiřující modul Azure mřížky příkazového řádku Service Fabric pomocí těchto [pokyny](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure a nastavte své předplatné.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pro nasazení aplikace. Následující příkaz vytvoří skupinu prostředků s názvem `myResourceGroup` v umístění východní USA. Pokud změníte název skupiny prostředků v následující příkaz, nezapomeňte změnit nastavení v všechny příkazy, které následují.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Nasazení šablony

Následující příkaz nasadí aplikaci Linux pomocí [counter.sfreliablevolume.linux.json šablony](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Chcete-li nasadit aplikaci s Windows, použijte [counter.sfreliablevolume.windows.json šablony](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Mějte na paměti, že může trvat delší dobu nasazení větších imagí kontejnerů.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

Můžete také zjistit stav nasazení pomocí příkazu

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Všimněte si názvu prostředku brány, která má jako typ prostředku `Microsoft.ServiceFabricMesh/gateways`. Ten se použije při získávání veřejnou IP adresu aplikace.

## <a name="open-the-application"></a>Otevření aplikace

Po úspěšném nasazení aplikace, získejte IP adresu prostředku brány pro aplikaci. Použijte název brány, kterou jste si všimli v výše uvedené části.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --gateway-name counterGateway
```

Výstup by měl mít zadanou vlastnost `ipAddress` tedy veřejnou IP adresu pro koncový bod služby. Otevřete v prohlížeči. Zobrazí se na webové stránce s hodnotou čítače aktualizuje každou sekundu.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Ověřte, že aplikace je možné použít svazku

Aplikace vytvoří soubor s názvem `counter.txt` hromadně uvnitř `counter/counterService` složky. Obsah tohoto souboru je hodnota čítače se zobrazuje na webové stránce.

## <a name="delete-the-resources"></a>Odstranit prostředky

Často odstraníte prostředky, které už používáte v Azure. Pokud chcete odstranit prostředky související se v tomto příkladu, odstraňte skupinu prostředků ve které se nasadily (čímž odstraníte všechno, co spojený s vybranou skupinou prostředků) pomocí následujícího příkazu:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Další postup

- Zobrazit ukázkové aplikace Service Fabric Reliable svazku disku na [Githubu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Další informace o modelu prostředků Service Fabric najdete v článku o [modelu prostředků Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Další informace o službě Service Fabric Mesh najdete v článku s [přehledem služby Service Fabric Mesh](service-fabric-mesh-overview.md).

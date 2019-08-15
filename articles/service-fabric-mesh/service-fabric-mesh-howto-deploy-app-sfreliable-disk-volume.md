---
title: Použití vysoce dostupného Service Fabric spolehlivého svazku disku v aplikaci pro síť Azure Service Fabric Microsoft Docs
description: Naučte se ukládat stav do aplikace sítě Azure Service Fabric pomocí rozhraní příkazového řádku Azure, a to připojením Service Fabric spolehlivých svazků na disku uvnitř kontejneru.
services: service-fabric-mesh
documentationcenter: .net
author: ashishnegi
manager: raunakpandya
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: 25bd298c412db38ec4d3b7859580d58ac9b151fb
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036160"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Připojení vysoce dostupného Service Fabric spolehlivého svazku založeného na disku v aplikaci Service Fabric sítě 
Běžnou metodou trvalého stavu s aplikacemi typu kontejner je použití vzdáleného úložiště, jako je Azure File Storage nebo databáze jako Azure Cosmos DB. To má za následek značnou latenci čtení a zápisu v síti do vzdáleného úložiště.

Tento článek ukazuje, jak uložit stav na vysoce dostupný Service Fabric spolehlivý disk připojením svazku do kontejneru Service Fabric aplikace sítě.
Service Fabric Reliable disk poskytuje svazky pro místní čtení s zápisy replikovanými v rámci clusteru Service Fabric pro zajištění vysoké dostupnosti. Tím dojde k odebrání síťových volání pro čtení a omezení latence sítě pro zápis. Pokud se kontejner restartuje nebo přesune na jiný uzel, nová instance kontejneru uvidí stejný svazek jako starší. Je tak efektivní i vysoce dostupný.

V tomto příkladu má aplikace čítače ASP.NET Core službu s webovou stránkou, která v prohlížeči zobrazuje hodnotu čítače.

`counterService` Pravidelně čte hodnotu čítače ze souboru, zvětšuje ho a zapisuje ho zpátky do souboru. Soubor je uložený ve složce, která je připojená na svazek, který je zálohovaný Service Fabric spolehlivým diskem.

## <a name="prerequisites"></a>Požadavky

K dokončení této úlohy můžete použít Azure Cloud Shell nebo místní instalaci rozhraní příkazového řádku Azure CLI. Pokud chcete používat Azure CLI s tímto článkem, ujistěte se `az --version` , že se `azure-cli (2.0.43)`vrátí aspoň.  Pomocí těchto [pokynů](service-fabric-mesh-howto-setup-cli.md)nainstalujte (nebo aktualizujte) modul rozšíření CLI pro Azure Service Fabric.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure a nastavte své předplatné.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pro nasazení aplikace. Následující příkaz vytvoří skupinu prostředků s názvem `myResourceGroup` v umístění ve východní USA. Pokud změníte název skupiny prostředků v následujícím příkazu, nezapomeňte změnit ji ve všech příkazech, které následují.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Nasazení šablony

Následující příkaz nasadí aplikaci pro Linux pomocí [šablony Counter. sfreliablevolume. Linux. JSON](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). K nasazení aplikace pro Windows použijte [šablonu Counter. sfreliablevolume. Windows. JSON](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Mějte na paměti, že nasazení větších imagí kontejneru může trvat delší dobu.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

Stav nasazení můžete zobrazit také pomocí příkazu.

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Všimněte si názvu prostředku brány, který má typ `Microsoft.ServiceFabricMesh/gateways`prostředku. Použije se při získávání veřejné IP adresy aplikace.

## <a name="open-the-application"></a>Otevření aplikace

Po úspěšném nasazení aplikace Získejte adresu ipAddress prostředku brány pro aplikaci. Použijte název brány, který jste si všimli v části výše.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

Výstup by měl mít vlastnost `ipAddress` , která je veřejnou IP adresou pro koncový bod služby. Otevřete ho z prohlížeče. Zobrazí se webová stránka s hodnotou čítače, která se aktualizuje každou sekundu.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Ověřte, že aplikace dokáže použít svazek.

Aplikace vytvoří ve složce Volume umístěný `counter.txt` `counter/counterService` soubor s názvem. Obsah tohoto souboru je hodnota čítače zobrazená na webové stránce.

## <a name="delete-the-resources"></a>Odstranit prostředky

Často odstraňujte prostředky, které už nepoužíváte v Azure. Pokud chcete odstranit prostředky, které souvisejí s tímto příkladem, odstraňte skupinu prostředků, ve které byly nasazené (což odstraní vše spojené se skupinou prostředků) pomocí následujícího příkazu:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Další postup

- Podívejte se na na GitHubu ukázkovou aplikaci [](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter)Service Fabric Reliable Volume disk.
- Další informace o modelu prostředků Service Fabric najdete v článku o [modelu prostředků Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Další informace o službě Service Fabric Mesh najdete v článku s [přehledem služby Service Fabric Mesh](service-fabric-mesh-overview.md).

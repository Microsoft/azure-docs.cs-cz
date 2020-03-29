---
title: Service Fabric Spolehlivý diskový svazek s mřížkou service fabric
description: Zjistěte, jak ukládat stav v aplikaci Azure Service Fabric Mesh připojením svazku založeného na service fabric reliable disk uvnitř kontejneru pomocí azure cli.
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: f26fe70afe7d9e2872f06ac6da7143556278b1b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497966"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Připojení vysoce dostupného svazku založeného na spolehlivém disku service fabric v aplikaci Service Fabric Mesh 
Běžnou metodou uchování stavu s aplikacemi kontejnerů je použití vzdáleného úložiště, jako je Azure File Storage nebo databáze, jako je Azure Cosmos DB. To má za následek významné čtení a zápis latence sítě do vzdáleného úložiště.

Tento článek ukazuje, jak ukládat stav ve vysoce dostupné service fabric spolehlivý disk připojením svazku uvnitř kontejneru aplikace Service Fabric Mesh.
Service Fabric Reliable Disk poskytuje svazky pro místní čtení s zápisy replikované v clusteru Service Fabric pro vysokou dostupnost. Tím odeberete síťová volání pro čtení a snižuje latenci sítě pro zápisy. Pokud se kontejner restartuje nebo přesune do jiného uzlu, nová instance kontejneru se zobrazí stejný svazek jako starší. Je tedy efektivní a vysoce dostupný.

V tomto příkladu má aplikace Counter službu ASP.NET Core s webovou stránkou, která zobrazuje hodnotu čítače v prohlížeči.

Pravidelně `counterService` čte hodnotu čítače ze souboru, increments a zapsat zpět do souboru. Soubor je uložen ve složce, která je připojena na svazku podporovaném service fabric reliable disk.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto úkolu můžete použít Prostředí Azure Cloud Shell nebo místní instalaci příkazového příkazu k řešení Azure. Chcete-li použít azure cli s `az --version` tímto `azure-cli (2.0.43)`článkem, ujistěte se, že vrátí alespoň .  Nainstalujte (nebo aktualizujte) rozšiřující modul rozšíření Azure Service Fabric Mesh CLI podle následujících [pokynů](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure a nastavte své předplatné.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pro nasazení aplikace. Následující příkaz vytvoří skupinu `myResourceGroup` prostředků pojmenovanou v umístění ve východních Spojených státech. Pokud změníte název skupiny prostředků v příkazu níže, nezapomeňte jej změnit ve všech následujících příkazech.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Nasazení šablony

Následující příkaz nasadí linuxovou aplikaci pomocí [šablony counter.sfreliablevolume.linux.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Chcete-li nasadit aplikaci systému Windows, použijte [šablonu counter.sfreliablevolume.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Uvědomte si, že větší image kontejneru může trvat déle nasazení.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

Můžete také zobrazit stav nasazení pomocí příkazu

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Všimněte si názvu prostředku brány, `Microsoft.ServiceFabricMesh/gateways`který má typ prostředku jako . To bude použito při získávání veřejné IP adresy aplikace.

## <a name="open-the-application"></a>Otevření aplikace

Jakmile se aplikace úspěšně nasadí, získejte ipAddress prostředku brány pro aplikaci. Použijte název brány, který jste si všimli ve výše uvedené části.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

Výstup by měl `ipAddress` mít vlastnost, která je veřejná IP adresa pro koncový bod služby. Otevřete ji z prohlížeče. Zobrazí se webová stránka s hodnotou čítače, která je aktualizována každou sekundu.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Ověřte, zda je aplikace schopna používat svazek

Aplikace vytvoří soubor `counter.txt` pojmenovaný ve `counter/counterService` složce svazku uvnitř. Obsah tohoto souboru je hodnota čítače zobrazená na webové stránce.

## <a name="delete-the-resources"></a>Odstranění prostředků

Často odstraňte prostředky, které už v Azure nepoužíváte. Chcete-li odstranit prostředky související s tímto příkladem, odstraňte skupinu prostředků, ve které byly nasazeny (která odstraní vše, co je přidruženo ke skupině prostředků) pomocí následujícího příkazu:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Další kroky

- Zobrazení ukázkové aplikace Service Fabric Reliable Volume Disk na [GitHubu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Další informace o modelu prostředků Service Fabric najdete v článku o [modelu prostředků Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Další informace o službě Service Fabric Mesh najdete v článku s [přehledem služby Service Fabric Mesh](service-fabric-mesh-overview.md).

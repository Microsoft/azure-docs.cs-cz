---
title: Kurz – Škálování aplikace spuštěné ve službě Azure Service Fabric Mesh | Microsoft Docs
description: V tomto kurzu se naučíte škálovat služby v aplikaci spuštěné ve službě Service Fabric Mesh.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 4d6839fea3ce0eb74cdf87396716cdc69c0cd1a0
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165977"
---
# <a name="tutorial-scale-an-application-running-in-service-fabric-mesh"></a>Kurz: Škálování aplikace spuštěné ve službě Service Fabric Mesh

Tento kurz je druhá část série. Zjistěte, jak můžete ručně škálovat počet instancí služby aplikace, která byla [předtím nasazena do služby Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md). Až budete hotovi, budete mít front-endovou službu se třemi instancemi a datovou službu se dvěma instancemi.

Ve druhé části této série se naučíte:

> [!div class="checklist"]
> * Konfigurovat požadovaný počet instancí služby
> * Provést upgrade

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Nasazení aplikace do služby Service Fabric Mesh pomocí šablony](service-fabric-mesh-tutorial-template-deploy-app.md)
> * Škálovat aplikaci spuštěnou ve službě Service Fabric Mesh
> * [Upgrade aplikace spuštěné ve službě Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Odebrání aplikace](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* [Nainstalujte si místně Azure CLI a Service Fabric Mesh CLI](service-fabric-mesh-howto-setup-cli.md#install-the-service-fabric-mesh-cli-locally).

## <a name="manually-scale-your-services-in-or-out"></a>Ruční horizontální snížení nebo navýšení kapacity služeb

Jednou z hlavních výhod nasazení aplikací do služby Service Fabric Mesh je možnost snadného horizontálního snížení nebo navýšení kapacity služeb. Tuto možnost byste měli používat ke zvládnutí různých objemů zatížení vašich služeb nebo ke zlepšení dostupnosti.

Tento kurz používá ukázku To Do List jako příklad, který byl [dříve nasazen](service-fabric-mesh-tutorial-template-deploy-app.md) a teď by měl běžet. Aplikace má dvě služby: WebFrontEnd a ToDoService. Každá z těchto služeb byla původně nasazena s jednou replikou.  Pokud chcete zobrazit počet spuštěných replik pro službu WebFrontEnd, spusťte následující příkaz:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

Pokud chcete zobrazit počet spuštěných replik pro službu ToDoService, spusťte následující příkaz:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

V šabloně nasazení pro prostředek aplikace má každá služba vlastnost *replicaCount*, kterou můžete použít k nastavení toho, kolikrát chcete tuto službu nasadit. Aplikace se může skládat z několika služeb, z nichž každá má jedinečný počet *replicaCount* a které jsou společně nasazovány a spravovány. Pokud chcete škálovat počet replik služby, upravte hodnotu *replicaCount* pro každou službu, kterou chcete škálovat, v šabloně nasazení nebo souboru parametrů.  Potom aplikaci upgradujte.

### <a name="modify-the-deployment-template-parameters"></a>Úprava parametrů šablony nasazení

Pokud jsou v šabloně hodnoty, které plánujete po nasazení aplikace změnit, nebo které byste chtěli mít možnost změnit pro každé nasazení (pokud plánujete opakované použití této šablony pro další nasazení), je vhodné tyto hodnoty parametrizovat.

Aplikace byla dříve nasazena pomocí [šablony nasazení mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) a souboru [parametrů mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

Soubor [parametrů mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) otevřete místně a nastavte hodnotu *frontEndReplicaCount* na 3 a hodnotu *serviceReplicaCount* na 2:

```json
      "frontEndReplicaCount":{
        "value": "3"
      },
      "serviceReplicaCount":{
        "value": "2"
      }
```

Změny uložte do souboru parametrů.  Parametry *frontEndReplicaCount* a *serviceReplicaCount* jsou deklarovány v části *parametrů* [šablony nasazení mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json):

```json
"frontEndReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    },
    "serviceReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    }
```

Vlastnost *replicaCount* služby WebFrontEnd odkazuje na parametr *frontEndReplicaCount* a vlastnost *replicaCount* služby ToDoService odkazuje na parametr *serviceReplicaCount*:

```json
    "services": [
    {
    "name": "WebFrontEnd",
    "properties": {
        "description": "WebFrontEnd description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('frontEndReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    },
    {
    "name": "ToDoService",
    "properties": {
        "description": "ToDoService description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('serviceReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    }
],
```

Jakmile šablonu upravíte, upgradujte aplikaci.

### <a name="upgrade-your-application"></a>Upgrade aplikace

Když je aplikace spuštěná, můžete ji upgradovat opětovným nasazením šablony a aktualizovaného souboru s parametry:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Spustí se tím upgrade aplikace se zajištěním provozu a během několika minut byste měli vidět zvýšení počtu instancí služeb.  Pokud chcete zobrazit počet spuštěných replik pro službu WebFrontEnd, spusťte následující příkaz:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

Pokud chcete zobrazit počet spuštěných replik pro službu ToDoService, spusťte následující příkaz:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurovat požadovaný počet instancí služby
> * Provést upgrade

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Upgrade aplikace spuštěné ve službě Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)

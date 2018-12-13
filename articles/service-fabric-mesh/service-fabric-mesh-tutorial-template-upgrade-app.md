---
title: Kurz – Upgrade aplikace spuštěné ve službě Azure Service Fabric Mesh | Microsoft Docs
description: V tomto kurzu zjistíte, jak upgradovat aplikaci Service Fabric spuštěnou ve službě Service Fabric Mesh.
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
ms.openlocfilehash: f617be79cb61932f79728feef76f056ce72ae0ab
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891125"
---
# <a name="tutorial-upgrade-a-service-fabric-application-running-in-service-fabric-mesh"></a>Kurz: Upgrade aplikace Service Fabric spuštěné ve službě Service Fabric Mesh

Tento kurz je třetí částí série. Dozvíte se, jak upgradovat aplikaci Service Fabric, která se [předtím nasadila do služby Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md), a to zvýšením množství přidělených prostředků CPU.  Jakmile budete hotovi, budete mít webová front-end služba spuštěná s vyšší prostředků procesoru.

Ve třetí části této série se naučíte:

> [!div class="checklist"]
> * Změna konfigurace aplikace
> * Upgrade aplikace spuštěné ve službě Service Fabric Mesh

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Nasazení aplikace do služby Service Fabric Mesh pomocí šablony](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Škálování aplikace spuštěné ve službě Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * Upgrade aplikace spuštěné ve službě Service Fabric Mesh
> * [Odebrání aplikace](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Otevřete [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) nebo si [nainstalujte Azure CLI a Service Fabric Mesh CLI místně](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="upgrade-application-configurations"></a>Upgrade konfigurace aplikace

Jednou z hlavních výhod nasazování aplikací do služby Service Fabric Mesh je možnost snadné aktualizace konfigurace aplikace.  Jedná se například o prostředky CPU nebo paměti pro vaše služby.

Tento kurz jako příklad používá ukázku Seznam úkolů, která se [nasadila dříve](service-fabric-mesh-tutorial-template-deploy-app.md) a teď by měla být spuštěná. Aplikace obsahuje dvě služby: WebFrontEnd a ToDoService. Každá z těchto služeb byla původně nasazená s prostředky CPU nastavenými na hodnotu 0,5.  Pokud chcete zobrazit prostředky CPU pro službu WebFrontEnd, spusťte následující příkaz:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

V šabloně nasazení pro prostředek aplikace má každá služba vlastnost *cpu*, kterou můžete použít k nastavení požadovaného množství prostředků CPU. Aplikace se může skládat z několika služeb, z nichž každá má jedinečné nastavení *cpu* a které se nasazují a spravují společně. Pokud chcete zvýšit prostředků procesoru webovou front-end službu, upravte *cpue* hodnota v souboru šablony nebo parametrů nasazení.  Potom aplikaci upgradujte.

### <a name="modify-the-deployment-template-parameters"></a>Úprava parametrů šablony nasazení

Pokud jsou v šabloně hodnoty, které plánujete po nasazení aplikace změnit, nebo které byste chtěli mít možnost změnit pro každé nasazení (pokud plánujete opakované použití této šablony pro další nasazení), je vhodné tyto hodnoty parametrizovat.

Aplikace se dříve nasadila pomocí [šablony nasazení mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) a souboru [parametrů mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

Místně otevřete soubor [parametrů mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) a nastavte hodnotu *frontEndCpu* na 1:

```json
      "frontEndCpu":{
        "value": "1"
      }
```

Uložte provedené změny souboru parametrů.  

Parametr *frontEndCpu* se deklaruje v části *parameters* [šablony nasazení mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json):

```json
"frontEndCpu": {
    "defaultValue": "0.5",
    "type": "string",
    "metadata": {
        "description": "The CPU resources for the front end web service."
    }
}
```

Na parametr *frontEndCpu* odkazuje vlastnost *codePackages->resources->requests->cpu* služby WebFrontEnd:

```json
    "services": [
          {
            "name": "WebFrontEnd",
            "properties": {
              "description": "WebFrontEnd description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "WebFrontEnd",
                  "image": "[parameters('frontEndImage')]",
                  ...
                  "resources": {
                    "requests": {
                      "cpu": "[parameters('frontEndCpu')]",
                      "memoryInGB": "[parameters('frontEndMemory')]"
                    }
                  },
                  "imageRegistryCredential": {
                    "server": "[parameters('registryServer')]",
                    "username": "[parameters('registryUserName')]",
                    "password": "[parameters('registryPassword')]"
                  }
                }
              ],
              ...
```

### <a name="upgrade-your-application"></a>Upgrade aplikace

Když je aplikace spuštěná, můžete ji upgradovat opětovným nasazením šablony a aktualizovaného souboru s parametry:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Spustí se tím upgrade aplikace se zajištěním provozu a během několika minut byste měli vidět zvýšení množství prostředků CPU.  Pokud chcete zobrazit prostředky CPU pro službu WebFrontEnd, spusťte následující příkaz:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

## <a name="next-steps"></a>Další postup

V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Změna konfigurace aplikace
> * Upgrade aplikace spuštěné ve službě Service Fabric Mesh

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Odebrání aplikace Service Fabric Mesh](service-fabric-mesh-tutorial-template-remove-app.md)

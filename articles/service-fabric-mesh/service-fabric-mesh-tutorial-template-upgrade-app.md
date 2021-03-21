---
title: Kurz – upgrade aplikace běžící v Azure Service Fabric sítě
description: V tomto kurzu zjistíte, jak upgradovat aplikaci Service Fabric spuštěnou ve službě Service Fabric Mesh.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 8a71e854f03bee75b757e0a0aa02e7aa2c24469b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626557"
---
# <a name="tutorial-upgrade-a-service-fabric-application-running-in-service-fabric-mesh"></a>Kurz: Upgrade aplikace Service Fabric spuštěné ve službě Service Fabric Mesh

> [!IMPORTANT]
> Náhled sítě Azure Service Fabric je vyřazený. Nová nasazení již nebudou povolena prostřednictvím rozhraní API pro Service Fabric sítě. Podpora stávajících nasazení bude pokračovat do 28. dubna 2021.
> 
> Podrobnosti najdete v tématu [vyřazení náhledu do sítě Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Tento kurz je třetí částí série. Dozvíte se, jak upgradovat aplikaci Service Fabric, která se [předtím nasadila do služby Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md), a to zvýšením množství přidělených prostředků CPU.  Až budete hotovi, budete mít webovou front-end službu spuštěnou s vyššími prostředky procesoru.

Ve třetí části této série se naučíte:

> [!div class="checklist"]
> * Změna konfigurace aplikace
> * Upgrade aplikace spuštěné ve službě Service Fabric Mesh

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Nasadit aplikaci do služby Service Fabric Mesh pomocí šablony](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Škálování aplikace spuštěné ve službě Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * Upgrade aplikace spuštěné ve službě Service Fabric Mesh
> * [Odebrání aplikace](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto kurzem:

* Pokud nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Otevřete [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) nebo si [nainstalujte Azure CLI a Service Fabric Mesh CLI místně](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="upgrade-application-configurations"></a>Upgrade konfigurace aplikace

Jednou z hlavních výhod nasazování aplikací do služby Service Fabric Mesh je možnost snadné aktualizace konfigurace aplikace.  Jedná se například o prostředky CPU nebo paměti pro vaše služby.

Tento kurz jako příklad používá ukázku Seznam úkolů, která se [nasadila dříve](service-fabric-mesh-tutorial-template-deploy-app.md) a teď by měla být spuštěná. Aplikace obsahuje dvě služby: WebFrontEnd a ToDoService. Každá z těchto služeb byla původně nasazená s prostředky CPU nastavenými na hodnotu 0,5.  Pokud chcete zobrazit prostředky CPU pro službu WebFrontEnd, spusťte následující příkaz:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

V šabloně nasazení pro prostředek aplikace má každá služba vlastnost *cpu*, kterou můžete použít k nastavení požadovaného množství prostředků CPU. Aplikace se může skládat z několika služeb, z nichž každá má jedinečné nastavení *cpu* a které se nasazují a spravují společně. Chcete-li zvýšit prostředky procesoru webové front-end služby, upravte hodnotu *cpue* v šabloně nasazení nebo souboru parametrů.  Potom aplikaci upgradujte.

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

Parametr *frontEndCpu* se deklaruje v části *parameters*[šablony nasazení mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json):

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

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Změna konfigurace aplikace
> * Upgrade aplikace spuštěné ve službě Service Fabric Mesh

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Odebrání aplikace Service Fabric Mesh](service-fabric-mesh-tutorial-template-remove-app.md)

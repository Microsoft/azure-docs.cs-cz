---
title: Rychlý start – nasazení aplikace Hello World do služby Azure Service Fabric Mesh | Microsoft Docs
description: V tomto rychlém startu se dozvíte, jak nasadit aplikaci Service Fabric Mesh do služby Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: Nepřidávejte ani neupravujte klíčová slova, aniž byste se poradili se svým odborníkem na SEO.
author: rwike77
ms.author: ryanwi
ms.date: 08/24/2018
ms.topic: quickstart
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: f5b834f92b2a126f68780a7647fda4d8b35dfe43
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886440"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>Rychlý start: Nasazení aplikace Hello World do služby Service Fabric Mesh

[Service Fabric Mesh](service-fabric-mesh-overview.md) umožňuje snadno vytvářet a spravovat aplikace mikroslužeb v Azure, aniž byste museli zřizovat virtuální počítače. V tomto rychlém startu vytvoříte aplikaci Hello World v Azure a zveřejníte ji na internetu. K dokončení této operace stačí jediný příkaz. Během pár minut se v prohlížeči zobrazí toto zobrazení:

![Aplikace Hello World v prohlížeči][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Pokud ještě nemáte účet Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="set-up-service-fabric-mesh-cli"></a>Nastavení rozhraní příkazového řádku služby Service Fabric Mesh 
K dokončení tohoto rychlého startu můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Nainstalujte rozšiřující modul Azure Service Fabric Mesh CLI pomocí těchto [pokynů](service-fabric-mesh-howto-setup-cli.md).

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
Vytvořte aplikaci ve skupině prostředků pomocí příkazu `az mesh deployment create`.  Pokud používáte konzolu Bash, spusťte následující příkaz:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}" 
```

Pokud používáte konzolu PowerShellu, spusťte následující příkaz:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.linux.json --parameters "{'location': {'value': 'eastus'}}"
```

Předchozí příkaz nasadí aplikaci pro Linux pomocí [šablony mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.linux.json). Pokud chcete nasadit aplikaci pro Windows, použijte [šablonu mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.windows.json). Image kontejnerů Windows jsou větší než image kontejnerů Linuxu a jejich nasazení může trvat delší dobu.

Po několika minutách příkaz vrátí následující:

`helloWorldApp has been deployed successfully on helloWorldNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Otevření aplikace
Po úspěšném nasazení aplikace zkopírujte veřejnou IP adresu pro koncový bod služby z výstupu rozhraní příkazového řádku. IP adresu otevřete ve webovém prohlížeči. Zobrazí se webová stránka s logem Azure Service Fabric Mesh.

## <a name="check-the-application-details"></a>Kontrola podrobností aplikace
Stav aplikace můžete zkontrolovat pomocí příkazu `az mesh app show`. Tento příkaz poskytne užitečné informace, podle kterých můžete postupovat dál.

Název aplikace v tomto rychlém startu je `helloWorldApp`. Abyste získali informace o aplikaci, spusťte následující příkaz:

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>Zobrazení aplikačních protokolů

Projděte si protokoly nasazené aplikace pomocí příkazu `az mesh code-package-log get`:

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete chtít aplikaci odstranit, spuštěním příkazu [az group delete][az-group-delete] odeberte skupinu prostředků včetně aplikace a síťových prostředků, které obsahuje.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o vytváření a nasazování aplikací Service Fabric Mesh, pokračujte v tomto kurzu.
> [!div class="nextstepaction"]
> [Vytvoření a nasazení webové aplikace s více službami](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest

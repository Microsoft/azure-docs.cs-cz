---
title: Povolení Azure Dev Spaces v AKS & instalaci nástrojů na straně klienta
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Naučte se, jak povolit Azure Dev Spaces v clusteru AKS a nainstalovat nástroje na straně klienta.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
ms.openlocfilehash: eb7c9b4d3d03b6f4f1f21e6fb2b2a60aa303b181
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212428"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Povolení Azure Dev Spaces v clusteru AKS a instalace nástrojů na straně klienta

Tento článek ukazuje několik způsobů povolení Azure Dev Spaces v clusteru AKS a instalaci nástrojů na straně klienta.

## <a name="enable-azure-dev-spaces-using-the-cli"></a>Povolení Azure Dev Spaces pomocí rozhraní příkazového řádku

Než budete moci povolit vývojové prostory pomocí rozhraní příkazového řádku, budete potřebovat:
* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet][az-portal-create-account].
* [Rozhraní příkazového řádku Azure je nainstalované][install-cli].
* [Cluster AKS][create-aks-cli] v [podporované oblasti][supported-regions].

Pomocí `use-dev-spaces` příkazu povolte v clusteru AKS vývojářské prostory a postupujte podle pokynů.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Výše uvedený příkaz povolí v clusteru *myAKSCluster* ve skupině *myResourceGroup* vývojářské prostory a vytvoří *výchozí* prostor pro vývoj.

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

`use-dev-spaces`Příkaz také nainstaluje Azure dev Spaces CLI.

## <a name="install-the-client-side-tools"></a>Instalace nástrojů na straně klienta

Pomocí Azure Dev Spaces nástrojů na straně klienta můžete pracovat s vývojovým prostorem v clusteru AKS z místního počítače. K dispozici je několik způsobů, jak nainstalovat nástroje na straně klienta:

* V [Visual Studio Code][vscode]nainstalujte [Azure dev Spaces rozšíření][vscode-extension].
* V [aplikaci Visual Studio 2019][visual-studio]nainstalujte úlohu vývoj pro Azure.
* Stáhněte a nainstalujte rozhraní příkazového řádku pro [Windows][cli-win], [Mac][cli-mac]nebo [Linux][cli-linux] .

## <a name="remove-azure-dev-spaces-using-the-cli"></a>Odebrání Azure Dev Spaces pomocí rozhraní příkazového řádku

K odebrání Azure Dev Spaces z clusteru AKS použijte `azds remove` příkaz.

```azurecli
azds remove -g MyResourceGroup -n MyAKS
```

Následující příklad výstupu ukazuje odebrání Azure Dev Spaces z clusteru *MyAKS* .

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Všechny obory názvů, které jste vytvořili pomocí Azure Dev Spaces, zůstanou spolu s jejich úlohami, ale nové úlohy v těchto oborech názvů nebudou s Azure Dev Spaces instrumentovat. Kromě toho, Pokud restartujete jakékoli existující lusky instrumentované pomocí Azure Dev Spaces, můžou se zobrazit chyby. Tyto lusky je potřeba znovu nasadit bez nástrojů Azure Dev Spaces. Pokud chcete z clusteru úplně odebrat Azure Dev Spaces, odstraňte všechny lusky ve všech oborech názvů, kde byla povolená Azure Dev Spaces.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak Azure Dev Spaces funguje.

> [!div class="nextstepaction"]
> [Jak Azure Dev Spaces funguje](../how-dev-spaces-works.md)

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds

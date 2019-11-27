---
title: Instalace Azure Dev Spaces na AKS & nástrojů na straně klienta
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Naučte se, jak nainstalovat Azure Dev Spaces do clusteru AKS a nainstalovat nástroje na straně klienta.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
ms.openlocfilehash: 2649b36c96313d4a7d878a1c72c3b175ad0f4d30
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325783"
---
# <a name="install-azure-dev-spaces-on-aks-and-the-client-side-tooling"></a>Instalace Azure Dev Spaces v AKS a nástrojích na straně klienta

Tento článek ukazuje několik způsobů, jak nainstalovat Azure Dev Spaces do clusteru AKS a jak nainstalovat nástroje na straně klienta.

## <a name="install-azure-dev-spaces-using-the-cli"></a>Instalace Azure Dev Spaces pomocí rozhraní příkazového řádku

Než budete moct nainstalovat vývojové prostory pomocí rozhraní příkazového řádku, budete potřebovat:
* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet][az-portal-create-account].
* [Rozhraní příkazového řádku Azure je nainstalované][install-cli].
* [Cluster AKS][create-aks-cli] v [podporované oblasti][supported-regions].

Pomocí příkazu `use-dev-spaces` povolte v clusteru AKS vývojářské prostory a postupujte podle pokynů.

```cmd
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Výše uvedený příkaz povolí v clusteru *myAKSCluster* ve skupině *myResourceGroup* vývojářské prostory a vytvoří *výchozí* prostor pro vývoj.

```cmd
$ az aks use-dev-spaces -g myResourceGroup -n myAKSCluster

'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

Příkaz `use-dev-spaces` také nainstaluje Azure Dev Spaces CLI.

## <a name="install-azure-dev-spaces-using-the-azure-portal"></a>Instalace Azure Dev Spaces pomocí Azure Portal

Než budete moct nainstalovat vývojářské prostory pomocí Azure Portal, budete potřebovat:
* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet][az-portal-create-account].
* [Cluster AKS][create-aks-portal] v [podporované oblasti][supported-regions].

Instalace Azure Dev Spaces pomocí Azure Portal:
1. Přihlaste se na web [Azure Portal ][az-portal].
1. Přejděte do clusteru AKS.
1. Klikněte na možnost *vývojové prostory*.
1. Změňte *Povolit vývojové prostory* na *Ano* a klikněte na *Uložit*.

![Povolit vývojové prostory v Azure Portal](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

**Instalace Azure dev Spaces pomocí Azure Portal nenainstaluje** žádné nástroje na straně klienta pro Azure dev Spaces.

## <a name="install-the-client-side-tooling"></a>Instalace nástrojů na straně klienta

Pomocí Azure Dev Spaces nástrojů na straně klienta můžete komunikovat s vývojovým prostorem v clusteru AKS z místního počítače. Existuje několik způsobů, jak nainstalovat nástroje na straně klienta:

* V [Visual Studio Code][vscode]nainstalujte [Azure dev Spaces rozšíření][vscode-extension].
* V [aplikaci Visual Studio 2019][visual-studio]nainstalujte úlohu vývoj pro Azure.
* V aplikaci Visual Studio 2017 nainstalujte úlohu vývoj pro web a [Visual Studio Tools for Kubernetes][visual-studio-k8s-tools].
* Stáhněte a nainstalujte rozhraní příkazového řádku pro [Windows][cli-win], [Mac][cli-mac]nebo [Linux][cli-linux] .

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak zjednodušit vývoj díky práci s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Vývoj pro tým v Azure Dev Spaces][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds

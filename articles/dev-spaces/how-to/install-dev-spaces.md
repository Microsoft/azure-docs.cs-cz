---
title: Povolení Azure Dev Spaces na AKS & instalace nástrojů na straně klienta
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Zjistěte, jak povolit Azure Dev Spaces v clusteru AKS a nainstalovat nástroje na straně klienta.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s
ms.openlocfilehash: a6b3be5ceba5e60b99b2f75e060f3321cd3151f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898947"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Povolení Azure Dev Spaces v clusteru AKS a instalace nástrojů na straně klienta

Tento článek ukazuje několik způsobů, jak povolit Azure Dev Spaces v clusteru AKS a také nainstalovat nástroje na straně klienta.

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>Povolení nebo odebrání azure dev spaces pomocí příkazového příkazového příkazu

Před povolením funkce Dev Spaces pomocí funkce velné podpořiní konto potřebujete:
* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet][az-portal-create-account].
* [Nainstalované vyčitapou Azure][install-cli].
* [Cluster AKS][create-aks-cli] v [podporované oblasti][supported-regions].

Pomocí `use-dev-spaces` příkazu povolte funkci Dev Spaces v clusteru AKS a postupujte podle pokynů.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Výše uvedený příkaz povolí funkce Dev Spaces v clusteru *myAKSCluster* ve skupině *myResourceGroup* a vytvoří *výchozí* prostor pro dev.

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

Příkaz `use-dev-spaces` také nainstaluje příkaz CLI Azure Dev Spaces.

Chcete-li odebrat Azure Dev Spaces z `azds remove` clusteru AKS, použijte příkaz. Například:

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Výše uvedený příkaz odebere Azure Dev Spaces z clusteru *MyAKS* v *MyResourceGroup*. Všechny obory názvů, které jste vytvořili pomocí Azure Dev Spaces, zůstanou spolu s jejich úlohami, ale nové úlohy v těchto oborech názvů nebudou instrumentované pomocí Azure Dev Spaces. Kromě toho pokud restartujete všechny existující pody instrumentované s Azure Dev Spaces, může se zobrazit chyby. Tyto pody musí být znovu nasazeny bez nástrojů Azure Dev Spaces. Pokud chcete úplně odebrat Azure Dev Spaces z clusteru, odstraňte všechny pody ve všech oborech názvů, kde byla povolena Azure Dev Spaces.

## <a name="enable-or-remove-azure-dev-spaces-using-the-azure-portal"></a>Povolení nebo odebrání Azure Dev Spaces pomocí webu Azure Portal

Než povolíte funkci Dev Spaces pomocí portálu Azure, potřebujete:
* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet][az-portal-create-account].
* [Cluster AKS][create-aks-portal] v [podporované oblasti][supported-regions].

Povolení Azure Dev Spaces pomocí portálu Azure:
1. Přihlaste se k [portálu Azure][az-portal].
1. Přejděte do clusteru AKS.
1. Vyberte položku nabídky *Dev Spaces.*
1. Změňte *možnost Povolit dev prostory* na *ano* a klepněte na tlačítko *Uložit*.

![Povolení dev spaces na webu Azure Portal](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Povolení azure dev spaces pomocí portálu Azure **nenainstaluje** žádné nástroje na straně klienta pro Azure Dev Spaces.

Pokud chcete z clusteru AKS odebrat Azure Dev Spaces, změňte *možnost Povolit dev spaces* na *Ne* a klikněte na *Uložit*. Všechny obory názvů, které jste vytvořili pomocí Azure Dev Spaces, zůstanou spolu s jejich úlohami, ale nové úlohy v těchto oborech názvů nebudou instrumentované pomocí Azure Dev Spaces. Kromě toho pokud restartujete všechny existující pody instrumentované s Azure Dev Spaces, může se zobrazit chyby. Tyto pody musí být znovu nasazeny bez nástrojů Azure Dev Spaces. Pokud chcete úplně odebrat Azure Dev Spaces z clusteru, odstraňte všechny pody ve všech oborech názvů, kde byla povolena Azure Dev Spaces.

## <a name="install-the-client-side-tools"></a>Instalace nástrojů na straně klienta

Nástroje Azure Dev Spaces na straně klienta můžete použít k interakci s dev prostory v clusteru AKS z místního počítače. Nástroje na straně klienta lze nainstalovat několika způsoby:

* V [kódu Visual Studia][vscode]nainstalujte rozšíření Azure [Dev Spaces][vscode-extension].
* Ve [Visual Studiu 2019][visual-studio]nainstalujte úlohu vývoje Azure.
* V sadě Visual Studio 2017 nainstalujte úlohy pro vývoj webu a [nástroje sady Visual Studio pro Kubernetes][visual-studio-k8s-tools].
* Stáhněte a nainstalujte rozhraní se systémem [Windows][cli-win], [Mac][cli-mac]nebo [Linux][cli-linux] CLI.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak můžete zjednodušit vývoj spolupráce pomocí práce s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Vývoj týmu v Azure Dev Spaces][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
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

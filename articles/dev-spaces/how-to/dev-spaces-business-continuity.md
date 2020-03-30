---
title: Provozní kontinuita a zotavení po havárii
services: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Naučte se používat Azure Dev Spaces a Služby Azure Kubernetes k zajištění kontinuity podnikání a přípravě na zotavení po havárii
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 37c0048bfa7e72b25eb56603fc027045eba25cea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295823"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Kontinuita podnikání a zotavení po havárii v Azure Dev Spaces

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Projděte si pokyny k zotavení po havárii pro službu Azure Kubernetes Service (AKS)

Azure Dev Spaces je funkce služby Azure Kubernetes Service (AKS). Měli byste znát pokyny pro zotavení po havárii v AKS a zvážit, zda se vztahují na clustery AKS, které používáte pro dev Spaces. Další informace najdete [v doporučených postupech pro kontinuitu podnikání a zotavení po havárii ve službě Azure Kubernetes Service (AKS).](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Povolení dev prostorů v clusterech AKS v různých oblastech

Povolení dev spaces na clusterech AKS v různých oblastech umožňuje pokračovat v používání funkce Dev Spaces ihned po selhání oblasti Azure.

Obecné informace o nasazení AKS ve více oblastech najdete v tématu [Plán pro nasazení ve více oblastech.](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment)

### <a name="enable-dev-spaces-via-the-azure-portal"></a>Povolení dev spaces přes portál Azure

Vyberte položku nabídky **Dev Spaces** pod nastavením každého clusteru na webu Azure Portal. Pak zvolte možnost povolit dev spaces a uložit.

![Povolení dev spaces přes portál Azure](../media/common/enable-dev-spaces.jpg)

Tento postup opakujte pro každý cluster.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Povolení dev prostorů prostřednictvím příkazového příkazového příkazového příkazu Azure

Na příkazovém řádku můžete také povolit funkci Dev Spaces:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Nasazení směrného plánu týmu do každého clusteru

Při práci s Dev Spaces obvykle nasazujete celou aplikaci do nadřazeného dev prostoru v clusteru Kubernetes. Ve výchozím `default` nastavení je místo použito. Počáteční nasazení zahrnuje všechny služby, stejně jako externí prostředky, které tyto služby závisí na, jako jsou databáze nebo fronty. Tento stav se označuje jako *výchozí bod*. Po nastavení směrného plánu v nadřazeném dev prostoru itetujete a ladíte jednotlivé služby uvnitř podřízených dev prostorů.

Nejnovější verze základní sady služeb byste měli nasadit do clusterů ve více oblastech. Aktualizace směrných služeb tímto způsobem zajistí, že můžete i nadále používat Dev Spaces, pokud dojde k selhání oblasti Azure. Pokud například nasadíte směrný plán prostřednictvím kanálu CI/CD, upravte kanál tak, aby se nasazuje do více clusterů v různých oblastech.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Vyberte správný cluster AKS, který se má použít pro funkce Dev Spaces.

Po správné konfiguraci záložního clusteru se směrným plánem vašeho týmu můžete kdykoli rychle přepnout do zálohovacího clusteru. Potom můžete znovu spustit jednotlivé služby, na kterých pracujete, v podřízených prostorech pro vývoj.

Vyberte jiný cluster s následujícím příkazem příkazu příkazu příkazu příkazu:

```azurecli
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Dostupné dev prostory v novém clusteru můžete uvést pomocí následujícího příkazu:

```cmd
azds space list
```

Můžete vytvořit novou dev prostor pro práci, nebo vybrat existující dev prostor, s následujícím příkazem:

```cmd
azds space select -n <space name>
```

Po spuštění těchto příkazů se vybraný cluster a dev prostor použijí pro následné operace příkazového příkazu a pro ladění projektů pomocí rozšíření kódu Visual Studio pro Azure Dev Spaces.

Pokud používáte Visual Studio, můžete přepnout cluster používaný existujícím projektem pomocí následujících kroků:

1. Otevřete projekt v sadě Visual Studio.
1. Klikněte pravým tlačítkem myši na název projektu v Průzkumníku řešení a klikněte na **Vlastnosti.**
1. V levém podokně klikněte na **Ladění.**
1. Na stránce Vlastnosti ladění klikněte na rozevírací seznam **Profil** a zvolte **Azure Dev Spaces**.
1. Klikněte na tlačítko **Změnit.**
1. V zobrazeném dialogovém okně vyberte cluster AKS, který chcete použít. V případě potřeby zvolte jiný prostor pro vytvoření pro práci nebo vytvořte novou dev prostor, výběrem příslušné volby z rozevíracího seznamu **Prostor.**

Jakmile vyberete správný cluster a prostor, můžete službu spustit stisknutím klávesy F5 v části Funkce Provspaces.

Opakujte tyto kroky pro všechny ostatní projekty nakonfigurované pro použití původního clusteru.

## <a name="access-a-service-on-a-backup-cluster"></a>Přístup ke službě v zálohovacím clusteru

Pokud jste službu nakonfigurovali tak, aby používala veřejný název DNS, bude mít jinou adresu URL, pokud ji spustíte v zálohovacím clusteru. Veřejné názvy DNS jsou `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io`vždy ve formátu . Pokud přepnete do jiného clusteru, změní se identifikátor GUID clusteru a případně oblast.

Dev Spaces vždy zobrazuje správnou adresu `azds up`URL služby při spuštění nebo v okně Výstup v sadě Visual Studio v části **Azure Dev Spaces**.

Adresu URL můžete také najít `azds list-uris` spuštěním příkazu:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Tuto adresu URL použijte při přístupu ke službě.

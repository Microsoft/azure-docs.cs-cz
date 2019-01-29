---
title: Obchodní kontinuity podnikových procesů a zotavení po havárii v Azure Dev prostorů | Dokumentace Microsoftu
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: article
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
manager: jeconnoc
ms.openlocfilehash: 7ecb792e45aeab90f9ae315e5b31247966bcd184
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55199025"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Obchodní kontinuity podnikových procesů a zotavení po havárii v Azure Dev mezery

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Přečtěte si pokyny pro zotavení po havárii pro Azure Kubernetes Service (AKS)

Azure Dev prostory je funkce Azure Kubernetes Service (AKS). By měl vědět pokyny pro zotavení po havárii ve službě AKS a zvažte, jestli se vztahují na AKS clustery, které používáte pro vývoj mezery. Další informace, použijte odkaz [osvědčené postupy pro obchodní kontinuity podnikových procesů a zotavení po havárii ve službě Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Povolit vývoje prostorů u clusterů AKS v různých oblastech

Povolení vývoje prostorů u clusterů AKS v různých oblastech, můžete pokračovat v použití prostorů Dev ihned po selhání oblasti Azure.

Obecné informace o nasazení ve více oblastech aks najdete v tématu [plánování pro nasazení v různých oblastech](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multi-region-deployment)

Informace o nasazování clusteru AKS, který je kompatibilní s Azure Dev prostory najdete v tématu [vytvořit cluster Kubernetes pomocí Azure Cloud Shell](https://docs.microsoft.com/azure/dev-spaces/how-to/create-cluster-cloud-shell)

### <a name="enable-dev-spaces-via-the-azure-portal"></a>Povolit prostory Dev prostřednictvím webu Azure portal

Klikněte na tlačítko **Dev prostory** navigační položka ve vlastnostech každý cluster na webu Azure Portal. Zvolte možnost povolit prostory vývoj.

![Povolení prostorů Dev prostřednictvím webu Azure portal](../media/common/enable-dev-spaces.jpg)

Tento postup opakujte pro každý cluster.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Povolit prostory vývoj pomocí rozhraní příkazového řádku Azure

Můžete také povolit prostory Dev na příkazovém řádku:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Nasaďte standardní hodnoty pro váš tým pro každý cluster

Při práci s mezerami Dev, obvykle nasazení celé aplikace do prostoru dev nadřazené ve vašem clusteru Kubernetes. Ve výchozím nastavení `default` používá prostor. Počáteční nasazení obsahuje všechny služby, jakož i externí prostředky, které tyto služby závisí na, jako jsou databáze nebo fronty. To se označuje jako *směrného plánu*. Po nastavení směrný plán v oboru nadřazené dev budete procházet hotový a ladit jednotlivé služby uvnitř podřízených dev mezery.

Nejnovější verze sady směrného plánu služeb byste měli nasadit do clusterů v několika oblastech. Aktualizace směrného plánu služeb tímto způsobem zajišťuje, že můžete nadále používat Dev mezery, pokud dojde k selhání oblasti Azure. Pokud nasadíte standardní hodnoty prostřednictvím kanálu CI/CD, upravte kanál, tak, aby se nasadí do více clusterů v různých oblastech.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Vyberte správný cluster AKS pro účely vývoje prostorů

Po konfiguraci zálohování clusteru se systémem směrný plán týmu správně, můžete rychle přepínat k zálohování clusteru kdykoli. Potom můžete znovu spustit jednotlivých služeb, které pracujete v prostorách vývoj.

Vyberte jiný cluster pomocí následujícího příkazu rozhraní příkazového řádku:

```cmd
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Můžete zobrazit seznam dostupných dev prostory na nový cluster pomocí následujícího příkazu:

```cmd
azds space list
```

Můžete vytvořit nový vývoj místa pro práci, nebo vybrat existující prostor dev, pomocí následujícího příkazu:

```cmd
azds space select -n <space name>
```

Po spuštění těchto příkazů, vybraný cluster a místo vývoj se použije pro následné operace rozhraní příkazového řádku a ladění pomocí Visual Studio Code příponou Azure Dev prostory projektů.

Pokud používáte Visual Studio, můžete přepnout cluster používá existující projekt pomocí následujících kroků:

1. Otevřete svůj projekt v sadě Visual Studio.
1. Název projektu v Průzkumníku řešení klikněte pravým tlačítkem myši a klikněte na tlačítko **vlastnosti**
1. V levém podokně klikněte na tlačítko **ladění**
1. Na stránce vlastností ladění, klikněte na tlačítko **profilu** rozevírací seznam a zvolte **prostory Azure Dev**.
1. Klikněte na tlačítko **změnu** tlačítko.
1. V zobrazeném dialogovém okně vyberte cluster AKS, který chcete použít. V případě potřeby jiné dev místo, kde se práce v zvolte nebo vytvořte nový prostor dev, tak, že vyberete příslušnou možnost z **místo** rozevíracího seznamu.

Až zvolíte správné cluster a místo, stisknete klávesu F5 ke spuštění služby v prostorách vývoj.

Tento postup opakujte pro všechny projekty, které umožňují použít k původnímu clusteru.

## <a name="access-a-service-on-a-backup-cluster"></a>Přístup ke službě zálohování clusteru

Pokud jste nakonfigurovali služby pro použití veřejného názvu DNS, pak služba bude mít jinou adresu URL při spuštění v clusteru zálohování. Veřejné názvy DNS jsou vždy ve formátu `<space name>.s.<service name>.<cluster GUID>.<region>.aksapp.io`. Pokud přejdete do jiného clusteru, se změní GUID clusteru a případně oblast.

Při spuštění vývoje prostorů vždy zobrazovat správnou adresu URL pro službu `azds up`, nebo v okně Výstup v sadě Visual Studio v části **Azure Dev prostory**.

Můžete také vyhledat adresu URL spuštěním `azds list-uris` příkaz:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://mywebapi.d05afe7e006a4fddb73c.eastus.aksapp.io/  Available
```

Pomocí této adresy URL při přístupu ke službě.
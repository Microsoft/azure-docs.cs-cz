---
title: Provozní kontinuita a zotavení po havárii
services: azure-dev-spaces
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 01/28/2019
ms.topic: conceptual
description: Naučte se používat Azure Dev Spaces a služby Azure Kubernetes k zajištění kontinuity podnikových aplikací a příprava na zotavení po havárii.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 77425d294dcea0546259b152b793cec291940c53
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929595"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Provozní kontinuita a zotavení po havárii v Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Přečtěte si pokyny pro zotavení po havárii pro službu Azure Kubernetes Service (AKS).

Azure Dev Spaces je funkce služby Azure Kubernetes Service (AKS). Měli byste si být vědomi pokynů pro zotavení po havárii v AKS a zvážit, zda se vztahují na clustery AKS, které používáte pro vývojové prostory. Další informace najdete [v článku osvědčené postupy pro kontinuitu podnikových procesů a zotavení po havárii ve službě Azure Kubernetes Service (AKS)](../../aks/operator-best-practices-multi-region.md) .

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Povolit vývojářské prostory v clusterech AKS v různých oblastech

Povolením vývojových prostorů v clusterech AKS v různých oblastech můžete dál používat vývojové prostory hned po selhání oblasti Azure.

Obecné informace o nasazeních AKS ve více oblastech najdete v tématu [Plánování nasazení ve více oblastech](../../aks/operator-best-practices-multi-region.md#plan-for-multiregion-deployment) .

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Povolit vývojářské prostory prostřednictvím Azure CLI

V příkazovém řádku můžete taky povolit vývojové prostory:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Nasazení standardních hodnot týmu do každého clusteru

Při práci s vývojovým prostorem obvykle nasadíte celou aplikaci do nadřazeného vývojového prostoru v clusteru Kubernetes. Ve výchozím nastavení se `default` používá mezera. Počáteční nasazení zahrnuje všechny služby a také externí prostředky, na kterých tyto služby závisejí, jako jsou databáze nebo fronty. To se označuje jako *standardní* hodnota. Po nastavení směrného plánu v nadřazeném vývojovém prostoru můžete iterovat a ladit jednotlivé služby v podřízených vývojových prostorech.

Nejnovější verze sady služeb můžete nasadit do clusterů ve více oblastech. Aktualizace standardních služeb tímto způsobem zajistí, že budete moct dál používat vývojářské prostory, pokud dojde k selhání oblasti Azure. Pokud například nasadíte svůj směrný plán prostřednictvím kanálu CI/CD, upravte kanál tak, aby se nasadil do několika clusterů v různých oblastech.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Vyberte správný cluster AKS, který se má použít pro vývojové prostory.

Po správném nakonfigurování záložního clusteru se směrným plánem vašeho týmu můžete kdykoli rychle přejít na záložní cluster. Pak můžete znovu spustit jednotlivé služby, na kterých pracujete v podřízených vývojových prostorech.

Vyberte jiný cluster s následujícím příkazem CLI:

```azurecli
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Dostupné vývojové prostory v novém clusteru můžete zobrazit pomocí následujícího příkazu:

```cmd
azds space list
```

Můžete vytvořit nový prostor pro vývoj pro práci, nebo vybrat existující místo pro vývoj pomocí následujícího příkazu:

```cmd
azds space select -n <space name>
```

Po spuštění těchto příkazů se vybraný cluster a místo pro vývoj použijí pro následné operace CLI a pro ladění projektů pomocí rozšíření Visual Studio Code pro Azure Dev Spaces.

Pokud používáte aplikaci Visual Studio, můžete přepínat cluster používaný existujícím projektem pomocí následujících kroků:

1. Otevřete svůj projekt v sadě Visual Studio.
1. Klikněte pravým tlačítkem myši na název projektu v Průzkumník řešení a klikněte na **vlastnosti** .
1. V levém podokně klikněte na **ladění** .
1. Na stránce Vlastnosti ladění klikněte na rozevírací seznam **profil** a vyberte **Azure dev Spaces**.
1. Klikněte na tlačítko **změnit** .
1. V dialogovém okně, které se zobrazí, vyberte cluster AKS, který chcete použít. V případě potřeby zvolte jiný prostor pro vývoj, na kterém chcete pracovat, nebo vytvořte nový prostor pro vývoj tím, že v rozevíracím seznamu **prostor** vyberete příslušnou možnost.

Po výběru správného clusteru a prostoru můžete stisknutím klávesy F5 službu spustit ve vývojových prostorech.

Opakujte tyto kroky pro všechny ostatní projekty nakonfigurované na použití původního clusteru.

## <a name="access-a-service-on-a-backup-cluster"></a>Přístup ke službě na záložním clusteru

Pokud jste službu nakonfigurovali tak, aby používala veřejný název DNS, bude mít služba jinou adresu URL, pokud ji spustíte na záložním clusteru. Veřejné názvy DNS jsou vždycky ve formátu `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io` . Pokud přepnete na jiný cluster, identifikátor GUID clusteru, případně se změna oblasti změní.

Pro vývojové prostory se vždycky zobrazuje správná adresa URL pro službu, pokud je spuštěná `azds up` , nebo v okně výstup v aplikaci Visual Studio v části **Azure dev Spaces**.

Adresu URL můžete najít také spuštěním `azds list-uris` příkazu:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Použijte tuto adresu URL při přístupu ke službě.

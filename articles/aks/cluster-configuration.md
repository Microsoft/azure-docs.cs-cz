---
title: Konfigurace clusteru ve službách Azure Kubernetes Services (AKS)
description: Zjistěte, jak nakonfigurovat cluster ve službě Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479160"
---
# <a name="configure-an-aks-cluster"></a>Konfigurace clusteru AKS

V rámci vytváření clusteru AKS může být nutné přizpůsobit konfiguraci clusteru tak, aby vyhovovala vašim potřebám. Tento článek představuje několik možností pro přizpůsobení clusteru AKS.

## <a name="os-configuration-preview"></a>Konfigurace operačního systému (náhled)

AKS nyní podporuje Ubuntu 18.04 jako operační systém uzlů (OS) ve verzi Preview. Během období náhledu jsou k dispozici jak Ubuntu 16.04, tak Ubuntu 18.04.

Musíte mít nainstalované následující prostředky:

- Nastavení příkazového příkazu Azure verze 2.2.0 nebo novější
- Aks-preview 0.4.35 rozšíření

Chcete-li nainstalovat rozšíření aks-preview 0.4.35 nebo novější, použijte následující příkazy azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

Zaregistrujte `UseCustomizedUbuntuPreview` funkci:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Může trvat několik minut, než se stav zobrazí jako **Registrovaný**. Stav registrace můžete zkontrolovat pomocí příkazu [az feature list:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Pokud se stav zobrazí jako registrovaný, `Microsoft.ContainerService` aktualizujte registraci poskytovatele prostředků pomocí příkazu [registrovat poskytovatele az:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Nakonfigurujte cluster tak, aby při vytváření clusteru používal Ubuntu 18.04. Pomocí `--aks-custom-headers` příznaku nastavte Ubuntu 18.04 jako výchozí operační systém.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Pokud chcete vytvořit pravidelný cluster Ubuntu 16.04, můžete tak učinit `--aks-custom-headers` vynecháním vlastní značky.

## <a name="custom-resource-group-name"></a>Název vlastní skupiny prostředků

Když nasadíte cluster služby Azure Kubernetes v Azure, vytvoří se pro pracovní uzly druhá skupina prostředků. Ve výchozím nastavení aks pojmenuje `MC_resourcegroupname_clustername_location`skupinu prostředků uzlu , ale můžete také zadat vlastní název.

Chcete-li zadat název vlastní skupiny prostředků, nainstalujte rozšíření Azure CLI verze 0.3.2 nebo novější. Pomocí rozhraní příkazového řádku `--node-resource-group` Azure `az aks create` použijte parametr příkazu k určení vlastního názvu pro skupinu prostředků. Pokud k nasazení clusteru AKS použijete šablonu Správce prostředků Azure, `nodeResourceGroup` můžete definovat název skupiny prostředků pomocí této vlastnosti.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Sekundární skupina prostředků se automaticky vytvoří poskytovatelem prostředků Azure ve vašem vlastním předplatném. Všimněte si, že název vlastní skupiny prostředků můžete zadat pouze při vytvoření clusteru. 

Při práci se skupinou prostředků uzlu mějte na paměti, že nemůžete:

- Zadejte existující skupinu prostředků pro skupinu prostředků uzlu.
- Zadejte jiné předplatné pro skupinu prostředků uzlu.
- Po vytvoření clusteru změňte název skupiny prostředků uzlu.
- Zadejte názvy spravovaných prostředků v rámci skupiny prostředků uzlu.
- Upravte nebo odstraňte značky spravovaných prostředků vytvořené azure v rámci skupiny prostředků uzlu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak `Kured` [použít aktualizace zabezpečení a jádra pro linuxové uzly](node-updates-kured.md) v clusteru.
- Informace o [upgradu clusteru služby Azure Kubernetes Service (AKS)](upgrade-cluster.md) najdete v tématu Upgrade clusteru Služby Azure Kubernetes (Azure Kubernetes).
- Odpovědi na některé běžné otázky AKS najdete v seznamu [nejčastějších dotazů týkajících se AKS.](faq.md)
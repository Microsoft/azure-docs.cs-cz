---
title: Konfigurace clusteru ve službě Azure Kubernetes Services (AKS)
description: Informace o tom, jak nakonfigurovat cluster ve službě Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fe5ce13d9db8f2bc2231f87de7e602e63d239bfa
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725142"
---
# <a name="configure-an-aks-cluster"></a>Konfigurace clusteru AKS

V rámci vytváření clusteru AKS možná budete muset přizpůsobit konfiguraci clusteru tak, aby vyhovovala vašim potřebám. Tento článek představuje několik možností přizpůsobení clusteru AKS.

## <a name="os-configuration-preview"></a>Konfigurace operačního systému (Preview)

AKS nyní podporuje Ubuntu 18,04 jako operační systém Node (OS) ve verzi Preview. Během období Preview jsou k dispozici obě Ubuntu 16,04 a Ubuntu 18,04.

Musíte mít nainstalované následující zdroje:

- Rozhraní příkazového řádku Azure, verze 2.2.0 nebo novější
- Rozšíření AKS-Preview 0.4.35

Pokud chcete nainstalovat rozšíření AKS-Preview 0.4.35 nebo novější, použijte následující příkazy rozhraní příkazového řádku Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

Zaregistrujte `UseCustomizedUbuntuPreview` funkci:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Může trvat několik minut, než se stav zobrazí jako **zaregistrované**. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Pokud se stav zobrazuje jako zaregistrované, aktualizujte registraci `Microsoft.ContainerService` poskytovatele prostředků pomocí příkazu [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="new-clusters"></a>Nové clustery

Nakonfigurujte cluster tak, aby při vytvoření clusteru používal Ubuntu 18,04. Pomocí `--aks-custom-headers` příznaku nastavte Ubuntu 18,04 jako výchozí operační systém.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Pokud chcete vytvořit běžný cluster Ubuntu 16,04, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` značku.

### <a name="existing-clusters"></a>Existující clustery

Nakonfigurujte nový fond uzlů tak, aby používal Ubuntu 18,04. Pomocí `--aks-custom-headers` příznaku nastavte Ubuntu 18,04 jako výchozí operační systém pro tento fond uzlů.

```azure-cli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Pokud chcete vytvořit běžné fondy uzlů Ubuntu 16,04, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` značku.


## <a name="custom-resource-group-name"></a>Název vlastní skupiny prostředků

Když v Azure nasadíte cluster služby Azure Kubernetes, vytvoří se pro pracovní uzly druhá skupina prostředků. Ve výchozím nastavení pojmenuje skupinu prostředků uzlu AKS `MC_resourcegroupname_clustername_location` , ale můžete zadat i vlastní název.

Pokud chcete zadat vlastní název skupiny prostředků, nainstalujte rozšíření Azure CLI AKS-Preview verze 0.3.2 nebo novější. Pomocí rozhraní příkazového řádku Azure použijte `--node-resource-group` parametr `az aks create` příkazu k zadání vlastního názvu pro skupinu prostředků. Pokud k nasazení clusteru AKS použijete šablonu Azure Resource Manager, můžete definovat název skupiny prostředků pomocí `nodeResourceGroup` Vlastnosti.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Sekundární skupinu prostředků automaticky vytvoří poskytovatel prostředků Azure ve vlastním předplatném. Název vlastní skupiny prostředků můžete zadat jenom při vytvoření clusteru. 

Při práci s skupinou prostředků uzlu Pamatujte na to, že nemůžete:

- Zadejte existující skupinu prostředků pro skupinu prostředků uzlu.
- Zadejte jiné předplatné pro skupinu prostředků uzlu.
- Po vytvoření clusteru změňte název skupiny prostředků uzlu.
- Zadejte názvy spravovaných prostředků v rámci skupiny prostředků uzlu.
- Upravte nebo odstraňte značky spravovaných prostředků vytvořené v Azure v rámci skupiny prostředků uzlu.

## <a name="next-steps"></a>Další kroky

- Naučte se používat `Kured` k [nasazení zabezpečení a aktualizací jádra na uzly se systémem Linux](node-updates-kured.md) ve vašem clusteru.
- Přečtěte si téma [upgrade clusteru Azure Kubernetes Service (AKS)](upgrade-cluster.md) , kde se dozvíte, jak upgradovat cluster na nejnovější verzi Kubernetes.
- V seznamu [nejčastějších dotazů k AKS](faq.md) najdete odpovědi na některé běžné otázky týkající se AKS.
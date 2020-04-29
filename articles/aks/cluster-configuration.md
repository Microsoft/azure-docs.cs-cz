---
title: Konfigurace clusteru ve službě Azure Kubernetes Services (AKS)
description: Informace o tom, jak nakonfigurovat cluster ve službě Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479160"
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

Nakonfigurujte cluster tak, aby při vytvoření clusteru používal Ubuntu 18,04. Pomocí `--aks-custom-headers` příznaku nastavte Ubuntu 18,04 jako výchozí operační systém.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Pokud chcete vytvořit běžný cluster Ubuntu 16,04, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` značku.

## <a name="custom-resource-group-name"></a>Název vlastní skupiny prostředků

Když v Azure nasadíte cluster služby Azure Kubernetes, vytvoří se pro pracovní uzly druhá skupina prostředků. Ve výchozím nastavení pojmenuje skupinu `MC_resourcegroupname_clustername_location`prostředků uzlu AKS, ale můžete zadat i vlastní název.

Pokud chcete zadat vlastní název skupiny prostředků, nainstalujte rozšíření Azure CLI AKS-Preview verze 0.3.2 nebo novější. Pomocí rozhraní `az aks create` příkazového řádku Azure použijte `--node-resource-group` parametr příkazu k zadání vlastního názvu pro skupinu prostředků. Pokud k nasazení clusteru AKS použijete šablonu Azure Resource Manager, můžete definovat název skupiny prostředků pomocí `nodeResourceGroup` vlastnosti.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Sekundární skupinu prostředků automaticky vytvoří poskytovatel prostředků Azure ve vlastním předplatném. Všimněte si, že při vytváření clusteru můžete zadat jenom název vlastní skupiny prostředků. 

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
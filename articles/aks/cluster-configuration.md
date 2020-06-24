---
title: Konfigurace clusteru ve službě Azure Kubernetes Services (AKS)
description: Informace o tom, jak nakonfigurovat cluster ve službě Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 06/20/2020
ms.openlocfilehash: 43aadd52f17367b488fcec086404caaba9158f33
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85205771"
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

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Pokud chcete vytvořit běžný cluster Ubuntu 16,04, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` značku.

### <a name="existing-clusters"></a>Existující clustery

Nakonfigurujte nový fond uzlů tak, aby používal Ubuntu 18,04. Pomocí `--aks-custom-headers` příznaku nastavte Ubuntu 18,04 jako výchozí operační systém pro tento fond uzlů.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Pokud chcete vytvořit běžné fondy uzlů Ubuntu 16,04, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` značku.

## <a name="generation-2-virtual-machines-preview"></a>Virtuální počítače generace 2 (Preview)
Azure podporuje [virtuální počítače generace 2 (Gen2) (VM](../virtual-machines/windows/generation-2.md)). Virtuální počítače 2. generace podporují klíčové funkce, které nejsou podporované na virtuálních počítačích generace 1 (Gen1). Mezi tyto funkce patří zvýšené množství paměti, rozšíření Intel software Guard (Intel SGX) a virtualizovaná trvalá paměť (vPMEM).

Virtuální počítače generace 2 používají novou architekturu na bázi rozhraní UEFI namísto architektury založené na systému BIOS používané virtuálními počítači 1. generace.
Virtuální počítače Gen2 podporují jenom určité SKU a velikosti. Zkontrolujte [seznam podporovaných velikostí](../virtual-machines/windows/generation-2.md#generation-2-vm-sizes), abyste viděli, jestli vaše SKU podporuje nebo vyžaduje Gen2.

Kromě toho ne všechny image virtuálních počítačů podporují Gen2, na virtuálních počítačích s AKS Gen2 použije nový [Obrázek AKS Ubuntu 18,04](#os-configuration-preview). Tento obrázek podporuje všechny SKU a velikosti Gen2.

Pokud chcete ve verzi Preview používat virtuální počítače s Gen2, budete potřebovat:
- `aks-preview`Rozšíření CLI je nainstalované.
- `Gen2VMPreview`Příznak funkce je zaregistrován.

Zaregistrujte `Gen2VMPreview` funkci:

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

Může trvat několik minut, než se stav zobrazí jako **zaregistrované**. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

Pokud se stav zobrazuje jako zaregistrované, aktualizujte registraci `Microsoft.ContainerService` poskytovatele prostředků pomocí příkazu [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

K instalaci rozšíření AKS-Preview rozhraní příkazového řádku použijte následující příkazy rozhraní příkazového řádku Azure:

```azurecli
az extension add --name aks-preview
```

Pokud chcete aktualizovat rozšíření CLI AKS-Preview, použijte následující příkazy rozhraní příkazového řádku Azure CLI:

```azurecli
az extension update --name aks-preview
```

### <a name="new-clusters"></a>Nové clustery
Nakonfigurujte cluster, aby při vytváření clusteru používal virtuální počítače s Gen2 pro vybranou SKU. Pomocí `--aks-custom-headers` příznaku nastavte Gen2 jako generování virtuálního počítače na novém clusteru.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Pokud chcete vytvořit běžný cluster pomocí virtuálních počítačů generace 1 (Gen1), můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` značku. Můžete také přidat další virtuální počítače s Gen1 nebo Gen2, jak je uvedeno níže.

### <a name="existing-clusters"></a>Existující clustery
Nakonfigurujte nový fond uzlů tak, aby používal virtuální počítače s Gen2. Pomocí `--aks-custom-headers` příznaku nastavte Gen2 jako generování virtuálního počítače pro tento fond uzlů.

```azure-cli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Pokud chcete vytvořit regulární fondy uzlů Gen1, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` značku.

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

---
title: Povolení šifrování založeného na hostiteli ve službě Azure Kubernetes Service (AKS)
description: Naučte se konfigurovat šifrování založené na hostiteli v clusteru AKS (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7eb3215aeb1f7c6508092d18fbebd90f852efe63
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772914"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Šifrování založené na hostiteli ve službě Azure Kubernetes (AKS) (Preview)

Při šifrování založeném na hostiteli jsou data uložená v hostitelích virtuálních počítačů na virtuálních počítačích uzlů agentů AKS v klidovém stavu a toky se šifrují do služby úložiště. To znamená, že dočasné disky jsou v klidovém stavu zašifrované pomocí klíčů spravovaných platformou. Mezipaměť operačního systému a datových disků je v klidovém stavu šifrovaná s použitím klíčů spravovaných platformou nebo klíčů spravovaných zákazníkem v závislosti na typu šifrování nastaveném na těchto discích. Ve výchozím nastavení platí, že pokud používáte AKS, operační systém a datové disky jsou v klidovém stavu šifrované pomocí klíčů spravovaných platformou, což znamená, že mezipaměti pro tyto disky jsou také standardně zašifrované v klidovém stavu pomocí klíčů spravovaných platformou.  [Pomocí disků Azure ve službě Azure Kubernetes](azure-disk-customer-managed-keys.md)můžete zadat vlastní spravované klíče, které následují za sebou vlastní klíče (BYOK). Mezipaměť pro tyto disky se pak zašifruje taky pomocí klíče, který zadáte v tomto kroku.


## <a name="before-you-begin"></a>Než začnete

Tato funkce se dá nastavit jenom při vytváření clusteru nebo při vytváření fondu uzlů.

> [!NOTE]
> Šifrování založené na hostiteli je dostupné v [oblastech Azure][supported-regions] , které podporují šifrování na straně serveru Azure Managed disks a jenom s konkrétními [podporovanými velikostmi virtuálních počítačů][supported-sizes].

### <a name="prerequisites"></a>Požadavky

- Ujistěte se, že máte `aks-preview` nainstalovanou verzi rozhraní příkazového řádku CLI verze 0.4.73 nebo novější.
- Ujistěte se, že je `EnableEncryptionAtHostPreview` v části `Microsoft.ContainerService` povoleno příznak funkce.

Než pro svůj cluster služby Azure Kubernetes použijete vlastnost EncryptionAtHost, musíte povolit funkci pro vaše předplatné. Pokud chcete povolit funkci pro vaše předplatné, postupujte prosím podle následujících kroků:

1. Spusťte následující příkaz, který zaregistruje funkci pro vaše předplatné.

```azurecli-interactive
Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```
2. Před vyzkoušením této funkce zkontrolujte, zda je stav registrace zaregistrován (několik minut trvá) pomocí příkazu níže.

```azurecli-interactive
Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```

### <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření rozhraní příkazového řádku aks-preview

Chcete-li vytvořit cluster AKS, který je hostitelem šifrování, budete potřebovat nejnovější rozšíření rozhraní *příkazového řádku AKS-Preview* . Nainstalujte rozšíření Azure CLI *AKS-Preview* pomocí příkazu [AZ Extension Add][az-extension-add] nebo vyhledejte všechny dostupné aktualizace pomocí příkazu [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Omezení

- Dá se povolit jenom pro nové fondy uzlů.
- Dá se povolit jenom v [oblastech Azure][supported-regions] , které podporují šifrování Azure Managed disks na straně serveru a jenom s konkrétními [podporovanými velikostmi virtuálních počítačů][supported-sizes].
- Vyžaduje cluster AKS a fond uzlů v závislosti na Virtual Machine Scale Sets (VMSS) jako *typ sady virtuálních počítačů*.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Použití šifrování založeného na hostiteli pro nové clustery (Preview)

Nakonfigurujte uzly agenta clusteru tak, aby při vytvoření clusteru používaly šifrování založené na hostiteli. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Pokud chcete vytvořit clustery bez šifrování založeného na hostiteli, můžete to udělat tak, že tento parametr vynecháte `--enable-encryption-at-host` .

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Použití šifrování založeného na hostiteli na existujících clusterech (Preview)

V existujících clusterech můžete povolit šifrování založené na hostiteli přidáním nového fondu uzlů do clusteru. Nakonfigurujte nový fond uzlů pro použití šifrování založeného na hostiteli pomocí `--enable-encryption-at-host` parametru.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Pokud chcete vytvořit nové fondy uzlů bez funkce šifrování založené na hostiteli, můžete to udělat tak, že parametr vynecháte `--enable-encryption-at-host` .

## <a name="next-steps"></a>Další kroky

Projděte si [osvědčené postupy zabezpečení clusteru AKS][best-practices-security] . Další informace o [šifrování na základě hostitele](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register

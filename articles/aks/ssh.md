---
title: SSH do uzlů clusteru Azure Kubernetes Service (AKS)
description: Naučte se vytvořit připojení SSH s uzly clusteru Azure Kubernetes Service (AKS) pro úlohy odstraňování potíží a údržby.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 6ddd1b160110e7a751f54f89b387a62d94e9308e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "67614482"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Připojení k uzlům clusteru SSH a Azure Kubernetes Service (AKS) pro účely údržby nebo řešení potíží

V průběhu životního cyklu clusteru Azure Kubernetes Service (AKS) budete možná potřebovat získat přístup k uzlu AKS. Tento přístup může být pro údržbu, shromažďování protokolů nebo jiné operace řešení potíží. K uzlům AKS můžete přistupovat pomocí SSH, včetně uzlů Windows serveru (aktuálně ve verzi Preview v AKS). [K uzlům Windows serveru se můžete připojit také pomocí připojení protokolem RDP (Remote Desktop Protocol)][aks-windows-rdp]. Pro účely zabezpečení nejsou uzly AKS k dispozici pro Internet.

V tomto článku se dozvíte, jak vytvořit připojení SSH s uzlem AKS pomocí svých privátních IP adres.

## <a name="before-you-begin"></a>Před zahájením

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.64 nebo novější. Verzi `az --version` zjistíte spuštěním. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>Přidat veřejný klíč SSH

Ve výchozím nastavení se klíče SSH získávají nebo generují a pak se do uzlů přidávají při vytváření clusteru AKS. Pokud potřebujete zadat jiné klíče SSH, než jaké jste použili při vytváření clusteru AKS, přidejte svůj veřejný klíč SSH do uzlů AKS pro Linux. V případě potřeby můžete vytvořit klíč SSH pomocí [MacOS nebo Linux][ssh-nix] nebo [Windows][ssh-windows]. Použijete-li pro vytvoření páru klíčů gen, uložte dvojici klíčů ve formátu OpenSSH, nikoli jako výchozí formát privátního klíče pro výstup pro výstup (soubor. ppk).

> [!NOTE]
> Klíče SSH se momentálně dají do uzlů Linux Přidat jenom pomocí Azure CLI. Pokud používáte uzly Windows serveru, použijte klíče SSH, které jste zadali při vytváření clusteru AKS, a přejděte ke kroku o [tom, jak získat adresu uzlu AKS](#get-the-aks-node-address). Nebo se [pomocí připojení protokolu RDP (Remote Desktop Protocol) připojte k uzlům Windows serveru][aks-windows-rdp].

Postup získání privátní IP adresy uzlů AKS se liší v závislosti na typu clusteru AKS, který spustíte:

* Pro většinu clusterů AKS použijte postup pro [získání IP adresy pro běžné clustery AKS](#add-ssh-keys-to-regular-aks-clusters).
* Pokud používáte jakékoli funkce ve verzi Preview v AKS, které používají služby Virtual Machine Scale Sets, jako je více fondů uzlů nebo podpora kontejnerů Windows serveru, [postupujte podle kroků pro clustery AKS založené na škálování služby Virtual Machine Scale-based](#add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="add-ssh-keys-to-regular-aks-clusters"></a>Přidání klíčů SSH do běžných AKS clusterů

Pokud chcete přidat klíč SSH do uzlu AKS pro Linux, proveďte následující kroky:

1. K získání názvu skupiny prostředků pro prostředky clusteru AKS použijte [AZ AKS show][az-aks-show]. Název clusteru je přiřazen k proměnné s názvem *CLUSTER_RESOURCE_GROUP*. Nahraďte *myResourceGroup* názvem vaší skupiny prostředků, ve kterém se nachází cluster AKS:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Seznam virtuálních počítačů ve skupině prostředků clusteru AKS pomocí příkazu [AZ VM list][az-vm-list] . Tyto virtuální počítače jsou vaše uzly AKS:

    ```azurecli-interactive
    az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
    ```

    Následující příklad výstupu ukazuje uzly AKS:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. K přidání klíčů SSH do uzlu použijte příkaz [AZ VM User Update][az-vm-user-update] . Zadejte název skupiny prostředků a potom jeden z uzlů AKS, který jste získali v předchozím kroku. Ve výchozím nastavení je uživatelské jméno pro uzly AKS *azureuser*. Zadejte umístění vlastního umístění veřejného klíče SSH, například *~/.ssh/id_rsa.pub*, nebo vložte obsah svého veřejného klíče SSH:

    ```azurecli-interactive
    az vm user update \
      --resource-group $CLUSTER_RESOURCE_GROUP \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

### <a name="add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters"></a>Přidání klíčů SSH do clusterů AKS založených na škálování sady virtuálních počítačů

Pokud chcete přidat klíč SSH do uzlu AKS pro Linux, který je součástí sady škálování virtuálních počítačů, proveďte následující kroky:

1. K získání názvu skupiny prostředků pro prostředky clusteru AKS použijte [AZ AKS show][az-aks-show]. Název clusteru je přiřazen k proměnné s názvem *CLUSTER_RESOURCE_GROUP*. Nahraďte *myResourceGroup* názvem vaší skupiny prostředků, ve kterém se nachází cluster AKS:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. V dalším kroku Získejte sadu škálování virtuálního počítače pro cluster AKS pomocí příkazu [AZ VMSS list][az-vmss-list] . Název sady škálování virtuálního počítače je přiřazen k proměnné s názvem *SCALE_SET_NAME*:

    ```azurecli-interactive
    SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
    ```

1. Pokud chcete přidat klíče SSH do uzlů v sadě škálování virtuálního počítače, použijte příkaz [AZ VMSS Extension set][az-vmss-extension-set] . V předchozích příkazech jsou uvedené skupiny prostředků clusteru a název sady škálování virtuálního počítače. Ve výchozím nastavení je uživatelské jméno pro uzly AKS *azureuser*. V případě potřeby aktualizujte umístění vlastního umístění veřejného klíče SSH, například *~/.ssh/id_rsa.pub*:

    ```azurecli-interactive
    az vmss extension set  \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --vmss-name $SCALE_SET_NAME \
        --name VMAccessForLinux \
        --publisher Microsoft.OSTCExtensions \
        --version 1.4 \
        --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"
    ```

1. Použijte klíč SSH na uzly pomocí příkazu [AZ VMSS Update-Instances][az-vmss-update-instances] :

    ```azurecli-interactive
    az vmss update-instances --instance-ids '*' \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --name $SCALE_SET_NAME
    ```

## <a name="get-the-aks-node-address"></a>Získat adresu uzlu AKS

Uzly AKS nejsou veřejně zpřístupněny pro Internet. Pro SSH k uzlům AKS použijete privátní IP adresu. V dalším kroku vytvoříte pomocnou nápovědu v clusteru AKS, která umožňuje SSH k této privátní IP adrese uzlu. Postup získání privátní IP adresy uzlů AKS se liší v závislosti na typu clusteru AKS, který spustíte:

* Pro většinu clusterů AKS použijte postup pro [získání IP adresy pro běžné clustery AKS](#ssh-to-regular-aks-clusters).
* Pokud používáte jakékoli funkce ve verzi Preview v AKS, které používají služby Virtual Machine Scale Sets, jako je více fondů uzlů nebo podpora kontejnerů Windows serveru, [postupujte podle kroků pro clustery AKS založené na škálování služby Virtual Machine Scale-based](#ssh-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="ssh-to-regular-aks-clusters"></a>SSH na běžné clustery AKS

Pomocí příkazu [AZ VM list-IP-addresss][az-vm-list-ip-addresses] ZOBRAZTE privátní IP adresu uzlu clusteru AKS. Zadejte vlastní název skupiny prostředků clusteru AKS, který jste získali v předchozím kroku [AZ-AKS-show][az-aks-show] :

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Následující příklad výstupu ukazuje privátní IP adresy uzlů AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

### <a name="ssh-to-virtual-machine-scale-set-based-aks-clusters"></a>SSH do clusterů AKS založených na škálování sady virtuálních počítačů

Vypíšete interní IP adresu uzlů pomocí [příkazu kubectl Get][kubectl-get]:

```console
kubectl get nodes -o wide
```

Následující příklad výstupu ukazuje interní IP adresy všech uzlů v clusteru, včetně uzlu Windows serveru.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Zaznamenejte interní IP adresu uzlu, který chcete řešit. Tuto adresu budete používat v pozdějším kroku.

## <a name="create-the-ssh-connection"></a>Vytvoření připojení SSH

Pokud chcete vytvořit připojení SSH k uzlu AKS, spusťte pomocníka pod clusterem AKS. Tato pomocná Nápověda je k dispozici s přístupem SSH do clusteru a dalším přístupem k uzlu SSH. Pokud chcete vytvořit a použít pomocníka pod, proveďte následující kroky:

1. Spusťte image `debian` kontejneru a připojte k ní relaci terminálu. Tento kontejner se dá použít k vytvoření relace SSH s jakýmkoli uzlem v clusteru AKS:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Pokud používáte uzly Windows serveru (aktuálně ve verzi Preview v AKS), přidejte do příkazu selektor uzlů pro naplánování kontejneru Debian v uzlu Linux následujícím způsobem:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Základní Debian image neobsahuje součásti SSH. Jakmile je relace Terminálové služby připojena ke kontejneru, nainstalujte klienta ssh pomocí `apt-get` následujícího postupu:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. V novém okně terminálu, které není připojené k vašemu kontejneru, zobrazte seznam lusků v clusteru AKS pomocí příkazu [kubectl Get lusks][kubectl-get] . Pod ním vytvořeným v předchozím kroku začíná název *AKS-SSH*, jak je znázorněno v následujícím příkladu:

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. V prvním kroku tohoto článku jste přidali veřejný klíč SSH pro uzel AKS. Teď si nakopírujete soukromý klíč SSH do pod. Tento privátní klíč se používá k vytvoření SSH na uzlech AKS.

    Zadejte vlastní název *AKS* pod jménem, který se získá v předchozím kroku. V případě potřeby změňte *~/.ssh/id_rsa* na umístění vašeho privátního klíče SSH:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Zpátky v relaci Terminálové služby do svého kontejneru aktualizujte oprávnění u zkopírovaného `id_rsa` privátního klíče SSH, aby byl uživatel jen pro čtení:

    ```console
    chmod 0600 id_rsa
    ```

1. Nyní vytvořte připojení SSH k AKS uzlu. Výchozí uživatelské jméno pro uzly AKS je znovu *azureuser*. Přijměte výzvu, aby bylo možné pokračovat v připojení, protože klíč SSH je nejprve důvěryhodný. Pak budete mít k dispozici příkaz bash pro váš uzel AKS:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4
    
    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.
    
    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud
    
    [...]
    
    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>Odebrat přístup přes SSH

Po dokončení `exit` relace SSH a pak `exit` interaktivní relaci kontejneru. Po zavření této relace kontejneru se odstraní pole pod použitým pro přístup SSH z clusteru AKS.

## <a name="next-steps"></a>Další postup

Pokud potřebujete další data pro řešení potíží, můžete [Zobrazit protokoly kubelet][view-kubelet-logs] nebo [Zobrazit protokoly hlavního uzlu Kubernetes][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances

---
title: Připojte se přes SSH uzly clusteru Azure Kubernetes Service (AKS)
description: Zjistěte, jak vytvořit připojení SSH s uzly clusteru Azure Kubernetes Service (AKS) pro řešení potíží a údržby úlohy.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 6ddd1b160110e7a751f54f89b387a62d94e9308e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614482"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Připojení přes SSH do Azure Kubernetes Service (AKS) uzlů clusteru za účelem údržby nebo řešení potíží

V průběhu životního cyklu clusteru Azure Kubernetes Service (AKS) budete muset získat přístup k uzlu AKS. Tento přístup může být pro údržbu, shromažďování protokolů nebo jiné operace odstraňování potíží. Uzly AKS pomocí protokolu SSH, včetně uzly Windows serveru (aktuálně ve verzi preview ve službě AKS) jsou přístupné. Můžete také [připojení k uzlům Windows serveru pomocí připojení vzdálené plochy protocol (RDP)][aks-windows-rdp]. Z bezpečnostních důvodů nejsou uzlů AKS přístupný z Internetu.

Tento článek ukazuje, jak vytvořit připojení SSH k uzlu AKS pomocí jejich privátní IP adresy.

## <a name="before-you-begin"></a>Před zahájením

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

Také nutné mít Azure CLI verze 2.0.64 nebo později nainstalované a nakonfigurované. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>Přidejte veřejný klíč SSH

Ve výchozím nastavení klíče SSH jsou získali, nebo vygeneruje a potom přidat do uzlů při vytváření clusteru AKS. Pokud je třeba zadat různé klíče SSH než ty, které jste použili při vytváření clusteru AKS, přidejte veřejný klíč SSH do uzlů Linux AKS. V případě potřeby můžete vytvořit klíče SSH pomocí [macOS nebo Linux][ssh-nix] or [Windows][ssh-windows]. Pokud použijete k vytvoření páru klíčů PuTTY Obecné, uložte páru klíčů v OpenSSH formátu místo výchozí formát PuTTy privátního klíče (soubor .ppk).

> [!NOTE]
> Klíče můžete SSH aktuálně lze přidat pouze pro uzly s Linuxem pomocí Azure CLI. Pokud používáte uzly Windows serveru, pomocí klíčů SSH zadali při vytváření clusteru AKS a přejděte na krok v [získání adresy uzlů AKS](#get-the-aks-node-address). Nebo, [připojení k uzlům Windows serveru pomocí připojení vzdálené plochy protocol (RDP)][aks-windows-rdp].

Postup získání privátní IP adresy uzlů AKS se liší podle typu cluster AKS, který můžete spustit:

* Většina clusterů AKS, postupujte podle pokynů k [získat IP adresu pro pravidelné AKS clustery](#add-ssh-keys-to-regular-aks-clusters).
* Je-li používat všechny funkce verze preview ve službě AKS, použít škálovací sady virtuálních počítačů, jako je například více fondy uzlů nebo podpora kontejnerů Windows serveru, [postupujte podle kroků pro clustery AKS na základě sady škálování virtuálního počítače](#add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="add-ssh-keys-to-regular-aks-clusters"></a>Přidání klíče SSH k pravidelné clusteru AKS

Chcete-li přidat klíč SSH na Linux AKS uzlu, proveďte následující kroky:

1. Získání názvu skupiny prostředků pro prostředky clusteru AKS pomocí [az aks zobrazit][az-aks-show]. Název clusteru je přiřazen do proměnné s názvem *CLUSTER_RESOURCE_GROUP*. Nahraďte *myResourceGroup* s názvem vaší skupiny prostředků, ve kterém můžete clusteru AKS nachází:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Seznam virtuálních počítačů pomocí AKS clusteru pro skupinu prostředků [az vm list][az-vm-list] příkazu. Tyto virtuální počítače jsou uzly AKS:

    ```azurecli-interactive
    az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
    ```

    Následující příklad výstupu ukazuje uzlů AKS:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. Chcete-li přidat vaše klíče SSH k uzlu, použijte [az vm se aktualizace uživatele][az-vm-user-update] příkazu. Zadejte název skupiny prostředků a potom jeden z uzlů AKS, kterou jste získali v předchozím kroku. Ve výchozím nastavení, uživatelské jméno pro uzly AKS. je *azureuser*. Zadejte umístění vlastní SSH veřejné klíče umístění, jako třeba *~/.ssh/id_rsa.pub*, nebo vložte obsah veřejného klíče SSH:

    ```azurecli-interactive
    az vm user update \
      --resource-group $CLUSTER_RESOURCE_GROUP \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

### <a name="add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters"></a>Přidání klíče SSH do clusterů AKS na základě sady škálování virtuálního počítače

Chcete-li přidat klíč SSH na Linux AKS uzel, který je součástí škálovací sady virtuálních počítačů, proveďte následující kroky:

1. Získání názvu skupiny prostředků pro prostředky clusteru AKS pomocí [az aks zobrazit][az-aks-show]. Název clusteru je přiřazen do proměnné s názvem *CLUSTER_RESOURCE_GROUP*. Nahraďte *myResourceGroup* s názvem vaší skupiny prostředků, ve kterém můžete clusteru AKS nachází:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Potom získejte škálovací sady pro váš cluster AKS pomocí virtuálních počítačů [az vmss list][az-vmss-list] příkazu. Název virtuálního počítače škálovací sady je přiřazen do proměnné s názvem *SCALE_SET_NAME*:

    ```azurecli-interactive
    SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
    ```

1. Chcete-li přidat vaše klíče SSH k uzlům ve škálovací sadě virtuálního počítače, použijte [az vmss extension set][az-vmss-extension-set] příkazu. Skupina prostředků clusteru a název škálovací sady virtuálních počítačů jsou k dispozici ve výstupech předchozích příkazů. Ve výchozím nastavení, uživatelské jméno pro uzly AKS. je *azureuser*. V případě potřeby aktualizujte umístění vlastní SSH veřejné klíče umístění, jako například *~/.ssh/id_rsa.pub*:

    ```azurecli-interactive
    az vmss extension set  \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --vmss-name $SCALE_SET_NAME \
        --name VMAccessForLinux \
        --publisher Microsoft.OSTCExtensions \
        --version 1.4 \
        --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"
    ```

1. Použít klíč SSH k uzlům pomocí [az vmss update-instances][az-vmss-update-instances] příkaz:

    ```azurecli-interactive
    az vmss update-instances --instance-ids '*' \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --name $SCALE_SET_NAME
    ```

## <a name="get-the-aks-node-address"></a>Získání adresy uzlů AKS

Uzlů AKS nejsou veřejně přístupný z Internetu. Pro připojení SSH k uzlů AKS použijte privátní IP adresu. V dalším kroku vytvoříte pod pomocné rutiny ve vašem clusteru AKS, která vám umožní SSH tato privátní IP adresa uzlu. Postup získání privátní IP adresy uzlů AKS se liší podle typu cluster AKS, který můžete spustit:

* Většina clusterů AKS, postupujte podle pokynů k [získat IP adresu pro pravidelné AKS clustery](#ssh-to-regular-aks-clusters).
* Je-li používat všechny funkce verze preview ve službě AKS, použít škálovací sady virtuálních počítačů, jako je například více fondy uzlů nebo podpora kontejnerů Windows serveru, [postupujte podle kroků pro clustery AKS na základě sady škálování virtuálního počítače](#ssh-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="ssh-to-regular-aks-clusters"></a>SSH k pravidelné clusteru AKS

Zobrazit uzel clusteru AKS pomocí privátní IP adresu [az vm list-ip-addresses][az-vm-list-ip-addresses] command. Provide your own AKS cluster resource group name obtained in a previous [az-aks-show][az-aks-show] kroku:

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Následující příklad výstupu ukazuje privátní IP adresy uzlů AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

### <a name="ssh-to-virtual-machine-scale-set-based-aks-clusters"></a>SSH do clusterů AKS na základě sady škálování virtuálního počítače

Seznam uzlů pomocí interní IP adresa [kubectl get příkaz][kubectl-get]:

```console
kubectl get nodes -o wide
```

Následující příklad výstupu ukazuje interních IP adres na všech uzlech v clusteru, včetně uzel Windows serveru.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Záznam interní IP adresa uzlu, který chcete vyřešit. Tuto adresu použijete v pozdějším kroku.

## <a name="create-the-ssh-connection"></a>Vytvořte připojení SSH

Vytvořte připojení SSH k uzlu AKS, je spustit pod pomocné rutiny ve vašem clusteru AKS. Tato pomocná pod vám poskytne přístup přes SSH do clusteru a pak případně pomocí dalších přístup k uzlu SSH. Vytváření a používání tohoto podu pomocné rutiny, proveďte následující kroky:

1. Spuštění `debian` kontejneru obrázků a připojit se k němu Terminálové relaci. Tento kontejner slouží k vytvoření relace SSH pomocí libovolného uzlu v clusteru AKS:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Pokud používáte uzly Windows serveru (aktuálně ve verzi preview ve službě AKS), přidejte k příkazu naplánování Debian kontejner v Linuxu uzlu uzlu selektoru:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Základní image Debian neobsahuje součásti SSH. Po relaci Terminálové služby je připojení ke kontejneru, nainstalovat klienta SSH pomocí `apt-get` následujícím způsobem:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. V novém okně terminálu, nejsou připojené do kontejneru, seznam podů v clusteru AKS pomocí [kubectl get pods][kubectl-get] příkazu. Spustí pod vytvořili v předchozím kroku s názvem *aks-ssh*, jak je znázorněno v následujícím příkladu:

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. V prvním kroku v tomto článku jste přidali veřejný klíč SSH uzlů AKS. Nyní zkopírujte váš privátní klíč SSH do pod. Tento soukromý klíč se používá k vytvoření SSH do uzlů AKS.

    Zadejte vlastní *aks-ssh* názvu podu získaný v předchozím kroku. V případě potřeby změňte *~/.ssh/id_rsa* umístění privátního klíče SSH:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Zpět v Terminálové relaci do kontejneru, aktualizovat oprávnění zkopírovaný `id_rsa` SSH privátní klíč tak, aby se uživatel jen pro čtení:

    ```console
    chmod 0600 id_rsa
    ```

1. Teď vytvořte připojení SSH k uzlu AKS. Znovu, je výchozí uživatelské jméno pro uzly AKS *azureuser*. Přijměte výzvy a pokračujte s připojením, protože klíč SSH je první důvěryhodné. Poté jsou součástí příkazovém řádku bash vaše uzlu AKS:

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

Až budete hotovi, `exit` relaci SSH a pak `exit` kontejneru interaktivní relace. Po zavření této relace kontejneru se odstraní pod používá pro přístup přes SSH z clusteru AKS.

## <a name="next-steps"></a>Další postup

Pokud potřebujete další data pro řešení problémů, můžete [zobrazení protokolů kubelet][view-kubelet-logs] or [view the Kubernetes master node logs][view-master-logs].

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

---
title: Připojte se přes SSH uzly clusteru Azure Kubernetes Service (AKS)
description: Zjistěte, jak vytvořit připojení SSH s uzly clusteru Azure Kubernetes Service (AKS) pro řešení potíží a údržby úlohy.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/05/2019
ms.author: iainfou
ms.openlocfilehash: 680e087e80d3e9891e201e7cb474ccfcf7fcc70b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072636"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Připojení přes SSH do Azure Kubernetes Service (AKS) uzlů clusteru za účelem údržby nebo řešení potíží

V průběhu životního cyklu clusteru Azure Kubernetes Service (AKS) budete muset získat přístup k uzlu AKS. Tento přístup může být pro údržbu, shromažďování protokolů nebo jiné operace odstraňování potíží. Uzlů AKS jsou virtuální počítače s Linuxem, takže můžete k nim přistupovat pomocí protokolu SSH. Z bezpečnostních důvodů nejsou uzlů AKS přístupný z Internetu.

Tento článek ukazuje, jak vytvořit připojení SSH k uzlu AKS pomocí jejich privátní IP adresy.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

Také nutné mít Azure CLI verze 2.0.59 nebo později nainstalované a nakonfigurované. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>Přidejte veřejný klíč SSH

Ve výchozím nastavení vygenerují se klíče SSH při vytváření clusteru AKS. Pokud jste nezadali klíče SSH při vytváření clusteru AKS, přidejte vaše veřejné klíče SSH pro uzly AKS.

Chcete-li přidat klíč SSH k uzlu AKS, proveďte následující kroky:

1. Získání názvu skupiny prostředků pro prostředky clusteru AKS pomocí [az aks zobrazit][az-aks-show]. Zadejte vlastní skupina základních prostředků a název clusteru AKS:

    ```azurecli-interactive
    az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
    ```

1. Seznam virtuálních počítačů pomocí AKS clusteru pro skupinu prostředků [az vm list] [ az-vm-list] příkazu. Tyto virtuální počítače jsou uzly AKS:

    ```azurecli-interactive
    az vm list --resource-group MC_myResourceGroup_myAKSCluster_eastus -o table
    ```

    Následující příklad výstupu ukazuje uzlů AKS:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. Chcete-li přidat vaše klíče SSH k uzlu, použijte [az vm se aktualizace uživatele] [ az-vm-user-update] příkazu. Zadejte název skupiny prostředků a potom jeden z uzlů AKS, kterou jste získali v předchozím kroku. Ve výchozím nastavení, uživatelské jméno pro uzly AKS. je *azureuser*. Zadejte umístění vlastní SSH veřejné klíče umístění, jako třeba *~/.ssh/id_rsa.pub*, nebo vložte obsah veřejného klíče SSH:

    ```azurecli-interactive
    az vm user update \
      --resource-group MC_myResourceGroup_myAKSCluster_eastus \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="get-the-aks-node-address"></a>Získání adresy uzlů AKS

Uzlů AKS nejsou veřejně přístupný z Internetu. Pro připojení SSH k uzlů AKS použijte privátní IP adresu. V dalším kroku vytvoříte pod pomocné rutiny ve vašem clusteru AKS, která vám umožní SSH tato privátní IP adresa uzlu.

Zobrazit uzel clusteru AKS pomocí privátní IP adresu [az vm list-ip-addresses] [ az-vm-list-ip-addresses] příkazu. Zadejte vlastní AKS clusteru název skupiny prostředků získané v předchozím [az-aks-show] [ az-aks-show] kroku:

```azurecli-interactive
az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table
```

Následující příklad výstupu ukazuje privátní IP adresy uzlů AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Vytvořte připojení SSH

Vytvořte připojení SSH k uzlu AKS, je spustit pod pomocné rutiny ve vašem clusteru AKS. Tato pomocná pod vám poskytne přístup přes SSH do clusteru a pak případně pomocí dalších přístup k uzlu SSH. Vytváření a používání tohoto podu pomocné rutiny, proveďte následující kroky:

1. Spuštění `debian` kontejneru obrázků a připojit se k němu Terminálové relaci. Tento kontejner slouží k vytvoření relace SSH pomocí libovolného uzlu v clusteru AKS:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

1. Základní image Debian neobsahuje součásti SSH. Po relaci Terminálové služby je připojení ke kontejneru, nainstalovat klienta SSH pomocí `apt-get` následujícím způsobem:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. V novém okně terminálu, nejsou připojené do kontejneru, seznam podů v clusteru AKS pomocí [kubectl get pods] [ kubectl-get] příkazu. Spustí pod vytvořili v předchozím kroku s názvem *aks-ssh*, jak je znázorněno v následujícím příkladu:

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

Pokud potřebujete další data pro řešení problémů, můžete [zobrazení protokolů kubelet] [ view-kubelet-logs] nebo [zobrazit protokoly hlavní uzel Kubernetes][view-master-logs].

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

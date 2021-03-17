---
title: Připojení SSH k uzlům clusteru Azure Kubernetes Service (AKS)
description: Naučte se vytvořit připojení SSH s uzly clusteru Azure Kubernetes Service (AKS) pro úlohy odstraňování potíží a údržby.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 7455b98348f2b8c40f2ffc125abe1297af88fbd8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034451"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Připojení pomocí SSH k uzlům clusteru Azure Kubernetes Service (AKS) kvůli údržbě nebo řešení potíží

V průběhu životního cyklu clusteru Azure Kubernetes Service (AKS) budete možná potřebovat získat přístup k uzlu AKS. Tento přístup může být pro údržbu, shromažďování protokolů nebo jiné operace řešení potíží. K uzlům AKS můžete přistupovat pomocí SSH, včetně uzlů Windows serveru. [K uzlům Windows serveru se můžete připojit také pomocí připojení protokolem RDP (Remote Desktop Protocol)][aks-windows-rdp]. Pro účely zabezpečení nejsou uzly AKS přístupné z Internetu. Pro SSH k uzlům AKS použijete privátní IP adresu.

V tomto článku se dozvíte, jak vytvořit připojení SSH s uzlem AKS pomocí svých privátních IP adres.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Ve výchozím nastavení se klíče SSH získávají nebo generují a pak se do uzlů přidávají při vytváření clusteru AKS. V tomto článku se dozvíte, jak zadat různé klíče SSH než klíče SSH, které jste použili při vytváření clusteru AKS. Článek také ukazuje, jak určit privátní IP adresu vašeho uzlu a připojit se k němu pomocí SSH. Pokud nepotřebujete zadat jiný klíč SSH, můžete přeskočit krok přidávání veřejného klíče SSH do uzlu.

Tento článek také předpokládá, že máte klíč SSH. Můžete vytvořit klíč SSH pomocí [MacOS nebo Linux][ssh-nix] nebo [Windows][ssh-windows]. Použijete-li pro vytvoření páru klíčů gen, uložte dvojici klíčů ve formátu OpenSSH, nikoli jako výchozí formát privátního klíče pro výstup pro výstup (soubor. ppk).

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Konfigurace clusterů AKS založených na škálování virtuálních počítačů pro přístup přes SSH

Pokud chcete nakonfigurovat přístup SSH na základě sady škálování virtuálních počítačů, vyhledejte název sady virtuálních počítačů v clusteru a přidejte do této sady škálování svůj veřejný klíč SSH.

Pomocí příkazu [AZ AKS show][az-aks-show] Získejte název skupiny prostředků vašeho clusteru AKS a pak použijte příkaz [AZ VMSS list][az-vmss-list] , který získá název vaší sady škálování.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query '[0].name' -o tsv)
```

Výše uvedený příklad přiřadí název skupiny prostředků clusteru pro *myAKSCluster* v *myResourceGroup* pro *CLUSTER_RESOURCE_GROUP*. Příklad následně používá *CLUSTER_RESOURCE_GROUP* k vypsání názvu sady škálování a přiřadí ho do *SCALE_SET_NAME*.

> [!IMPORTANT]
> V tuto chvíli byste měli aktualizovat jenom klíče SSH pro clustery AKS založené na škálování virtuálních počítačů pomocí rozhraní příkazového řádku Azure CLI.
> 
> V případě uzlů se systémem Linux je možné klíče SSH aktuálně přidat jenom pomocí Azure CLI. Pokud se chcete připojit k uzlu Windows serveru pomocí SSH, použijte klíče SSH, které jste zadali při vytváření clusteru AKS, a přeskočte další sadu příkazů pro přidání veřejného klíče SSH. Pořád budete potřebovat IP adresu uzlu, který chcete řešit, který je zobrazený v posledním příkazu této části. Alternativně se můžete [k uzlům Windows serveru připojit pomocí připojení protokolu RDP (Remote Desktop Protocol)][aks-windows-rdp] místo použití SSH.

Pokud chcete přidat klíče SSH do uzlů v sadě škálování virtuálního počítače, použijte příkaz [AZ VMSS Extension set][az-vmss-extension-set] a [AZ VMSS Update-Instances][az-vmss-update-instances] .

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

Výše uvedený příklad používá proměnné *CLUSTER_RESOURCE_GROUP* a *SCALE_SET_NAME* z předchozích příkazů. Výše uvedený příklad také používá *~/.ssh/id_rsa. pub* jako umístění pro veřejný klíč SSH.

> [!NOTE]
> Ve výchozím nastavení je uživatelské jméno pro uzly AKS *azureuser*.

Po přidání veřejného klíče SSH do sady škálování můžete SSH použít na virtuální počítač uzlu v této sadě škálování pomocí jeho IP adresy. Pomocí [příkazu kubectl Get][kubectl-get]ZOBRAZTE soukromé IP adresy uzlů clusteru AKS.

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

Zaznamenejte interní IP adresu uzlu, který chcete řešit.

Pokud chcete získat přístup k uzlu pomocí SSH, postupujte podle kroků v části [vytvoření připojení SSH](#create-the-ssh-connection).

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Konfigurace clusterů AKS založených na sadě dostupnosti virtuálních počítačů pro přístup přes SSH

Chcete-li nakonfigurovat cluster AKS založený na sadě dostupnosti virtuálních počítačů pro přístup SSH, vyhledejte název uzlu systému Linux v clusteru a přidejte do tohoto uzlu veřejný klíč SSH.

Pomocí příkazu [AZ AKS show][az-aks-show] Získejte název skupiny prostředků vašeho clusteru AKS a pak použijte příkaz [AZ VM list][az-vm-list] , který vypíše název virtuálního počítače pro uzel Linux vašeho clusteru.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Výše uvedený příklad přiřadí název skupiny prostředků clusteru pro *myAKSCluster* v *myResourceGroup* pro *CLUSTER_RESOURCE_GROUP*. Příklad následně používá *CLUSTER_RESOURCE_GROUP* k vypsání názvu virtuálního počítače. Ukázkový výstup zobrazuje název virtuálního počítače:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

K přidání klíčů SSH do uzlu použijte příkaz [AZ VM User Update][az-vm-user-update] .

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Výše uvedený příklad používá proměnnou *CLUSTER_RESOURCE_GROUP* a název virtuálního počítače uzlu z předchozích příkazů. Výše uvedený příklad také používá *~/.ssh/id_rsa. pub* jako umístění pro veřejný klíč SSH. Místo zadání cesty můžete také použít obsah svého veřejného klíče SSH.

> [!NOTE]
> Ve výchozím nastavení je uživatelské jméno pro uzly AKS *azureuser*.

Po přidání veřejného klíče SSH do virtuálního počítače uzlu můžete k tomuto virtuálnímu počítači přes SSH použít jeho IP adresu. Pomocí příkazu [AZ VM list-IP-addresss][az-vm-list-ip-addresses] ZOBRAZTE privátní IP adresu uzlu clusteru AKS.

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Výše uvedený příklad používá sadu proměnných *CLUSTER_RESOURCE_GROUP* v předchozích příkazech. Následující příklad výstupu ukazuje privátní IP adresy uzlů AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Vytvoření připojení SSH

Pokud chcete vytvořit připojení SSH k uzlu AKS, spusťte pomocníka pod clusterem AKS. Tato pomocná Nápověda je k dispozici s přístupem SSH do clusteru a dalším přístupem k uzlu SSH. Pokud chcete vytvořit a použít pomocníka pod, proveďte následující kroky:

1. Spusťte `debian` Image kontejneru a připojte k ní relaci terminálu. Tento kontejner se dá použít k vytvoření relace SSH s jakýmkoli uzlem v clusteru AKS:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Pokud používáte uzly Windows serveru, přidejte do příkazu selektor uzlů pro naplánování kontejneru Debian na uzlu se systémem Linux:
    >
    > ```console
    > kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"v1","spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}'
    > ```

1. Jakmile je relace Terminálové služby připojena ke kontejneru, nainstalujte klienta SSH pomocí nástroje `apt-get` :

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Otevřete nové okno terminálu, které není připojené ke kontejneru, a zkopírujte privátní klíč SSH do pomocné rutiny pod. Tento privátní klíč se používá k vytvoření SSH v uzlu AKS. 

   V případě potřeby změňte *~/.ssh/id_rsa* na umístění vašeho privátního klíče SSH:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Vraťte se do svého kontejneru do relace Terminálové služby, aktualizujte oprávnění u zkopírovaného `id_rsa` privátního klíče SSH tak, aby byl uživatel jen pro čtení:

    ```console
    chmod 0400 id_rsa
    ```

1. Vytvořte připojení SSH k AKS uzlu. Výchozí uživatelské jméno pro uzly AKS je znovu *azureuser*. Přijměte výzvu, aby bylo možné pokračovat v připojení, protože klíč SSH je nejprve důvěryhodný. Pak budete mít k dispozici příkaz bash pro váš uzel AKS:

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

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další data pro řešení potíží, můžete [Zobrazit protokoly kubelet][view-kubelet-logs] nebo [Zobrazit protokoly hlavního uzlu Kubernetes][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: ./view-control-plane-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
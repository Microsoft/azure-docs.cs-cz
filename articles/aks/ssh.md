---
title: Připojení SSH k uzlům clusteru Azure Kubernetes Service (AKS)
description: Zjistěte, jak vytvořit připojení SSH s uzly clusteru Azure Kubernetes Service (AKS) pro řešení potíží a úlohy údržby.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: dfdcda40a24142f85bbeb360aacf0971d72d181f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593627"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Připojení pomocí SSH k uzlům clusteru Azure Kubernetes Service (AKS) kvůli údržbě nebo řešení potíží

Během celého životního cyklu clusteru Služby Azure Kubernetes (AKS) může být nutné získat přístup k uzlu AKS. Tento přístup může být pro údržbu, shromažďování protokolů nebo jiné operace řešení potíží. K uzlům AKS můžete přistupovat pomocí SSH, včetně uzlů Windows Server (aktuálně ve verzi Preview v AKS). K uzlům systému Windows Server se můžete připojit také [pomocí připojení protokolu RDP (Remote Desktop Protocol).][aks-windows-rdp] Z bezpečnostních důvodů nejsou uzly AKS vystaveny internetu. Chcete-li SSH na uzly AKS, použijte privátní IP adresu.

Tento článek ukazuje, jak vytvořit připojení SSH s uzlem AKS pomocí jejich privátní IP adresy.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

Ve výchozím nastavení jsou klíče SSH získány nebo generovány a poté přidány do uzlů při vytváření clusteru AKS. Tento článek ukazuje, jak zadat různé klíče SSH než klíče SSH používané při vytváření clusteru AKS. Článek také ukazuje, jak určit privátní IP adresu uzlu a připojit se k ní pomocí SSH. Pokud nepotřebujete zadat jiný klíč SSH, můžete přeskočit krok pro přidání veřejného klíče SSH do uzlu.

Tento článek také předpokládá, že máte klíč SSH. Klíč SSH můžete vytvořit pomocí [macOS nebo Linuxu][ssh-nix] nebo [Windows][ssh-windows]. Pokud k vytvoření dvojice klíčů použijete PuTTY Gen, uložte dvojici klíčů ve formátu OpenSSH, nikoli ve výchozím formátu soukromého klíče PuTTy (soubor.ppk).

Potřebujete také nainstalované a nakonfigurované a nakonfigurované azure CLI verze 2.0.64 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Konfigurace clusterů AKS založených na škálovací sadě virtuálních strojů pro přístup SSH

Chcete-li nakonfigurovat škálovací sadu virtuálních strojů pro přístup SSH, vyhledejte název škálovací sady virtuálních strojů vašeho clusteru a přidejte do této škálovací sady veřejný klíč SSH.

Pomocí příkazu [az aks show][az-aks-show] získáte název skupiny prostředků clusteru AKS a potom příkaz [seznamu az vmss][az-vmss-list] získáte název škálovací sady.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

Výše uvedený příklad přiřadí název skupiny prostředků clusteru pro *myAKSCluster* v *myResourceGroup* k *CLUSTER_RESOURCE_GROUP*. Příklad pak použije *CLUSTER_RESOURCE_GROUP* k vypsat název škálovací sady a přiřadit jej *SCALE_SET_NAME*.

> [!IMPORTANT]
> V tuto chvíli byste měli aktualizovat pouze klíče SSH pro clustery AKS založené na škálovací sadě virtuálních strojů pomocí azure CLI.
> 
> Pro linuxové uzly lze klíče SSH aktuálně přidat jenom pomocí azure cli. Pokud se chcete připojit k uzlu Windows Server pomocí SSH, použijte klíče SSH, které jsou k dispozici při vytváření clusteru AKS, a přeskočte další sadu příkazů pro přidání veřejného klíče SSH. Stále budete potřebovat IP adresu uzlu, který chcete řešit, což je uvedeno v konečném příkazu této části. Případně se můžete [připojit k uzlům systému Windows Server pomocí připojení protokolu RDP (remote desktop)][aks-windows-rdp] namísto použití ssh.

Chcete-li přidat klíče SSH do uzlů ve škálovací sadě virtuálních strojů, použijte [příkazy rozšíření az vmss][az-vmss-extension-set] a [az vmss update-instance.][az-vmss-update-instances]

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

Výše uvedený příklad používá *proměnné CLUSTER_RESOURCE_GROUP* a *SCALE_SET_NAME* z předchozích příkazů. Výše uvedený příklad také používá *~/.ssh/id_rsa.pub* jako umístění pro váš veřejný klíč SSH.

> [!NOTE]
> Ve výchozím nastavení je uživatelské jméno uzlů AKS *azureuser*.

Po přidání veřejného klíče SSH do škálovací sady můžete SSH do virtuálního počítače uzlu v této škálovací sadě pomocí jeho IP adresy. Zobrazení privátních IP adres uzlů clusteru AKS pomocí [příkazu kubectl get][kubectl-get].

```console
kubectl get nodes -o wide
```

Výstup následujícího příkladu zobrazuje interní IP adresy všech uzlů v clusteru, včetně uzlu Windows Server.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Zaznamenejte interní IP adresu uzlu, který chcete řešit.

Chcete-li získat přístup k uzlu pomocí SSH, postupujte podle pokynů v části [Vytvoření připojení SSH](#create-the-ssh-connection).

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Konfigurace clusterů AKS založených na sadě AKS založených na virtuálním počítači pro přístup SSH

Chcete-li nakonfigurovat cluster AKS založený na sadě virtuálních strojů pro přístup SSH, vyhledejte název linuxového uzlu vašeho clusteru a přidejte do tohoto uzlu veřejný klíč SSH.

Pomocí příkazu [az aks show][az-aks-show] získáte název skupiny prostředků clusteru AKS a potom příkaz [az vm seznam][az-vm-list] pro seznam názvů virtuálních strojů linuxového uzlu vašeho clusteru.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Výše uvedený příklad přiřadí název skupiny prostředků clusteru pro *myAKSCluster* v *myResourceGroup* k *CLUSTER_RESOURCE_GROUP*. Příklad pak používá *CLUSTER_RESOURCE_GROUP* k zobrazení seznamu názvu virtuálního počítače. Ukázkový výstup ukazuje název virtuálního počítače:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Chcete-li přidat klíče SSH do uzlu, použijte příkaz [aktualizace uživatele az vm.][az-vm-user-update]

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Výše uvedený příklad používá *proměnnou CLUSTER_RESOURCE_GROUP* a název virtuálního počítače uzlu z předchozích příkazů. Výše uvedený příklad také používá *~/.ssh/id_rsa.pub* jako umístění pro váš veřejný klíč SSH. Můžete také použít obsah veřejného klíče SSH namísto určení cesty.

> [!NOTE]
> Ve výchozím nastavení je uživatelské jméno uzlů AKS *azureuser*.

Po přidání veřejného klíče SSH do virtuálního počítače uzlu můžete SSH do tohoto virtuálního počítače použít jeho IP adresu. Zobrazte privátní IP adresu uzlu clusteru AKS pomocí příkazu [az vm list-ip-addresses.][az-vm-list-ip-addresses]

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Výše uvedený příklad používá *proměnnou CLUSTER_RESOURCE_GROUP* nastavenou v předchozích příkazech. Následující ukázkový výstup ukazuje privátní IP adresy uzlů AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Vytvoření připojení SSH

Chcete-li vytvořit připojení SSH k uzlu AKS, spusťte pomocný modul v clusteru AKS. Tento pomocný modul poskytuje přístup SSH do clusteru a pak další přístup uzlu SSH. Chcete-li vytvořit a použít tento pomocový modul, proveďte následující kroky:

1. Spusťte bitovou `debian` kopii kontejneru a připojte k ní terminálovou relaci. Tento kontejner lze použít k vytvoření relace SSH s libovolným uzlem v clusteru AKS:

    ```console
    kubectl run --generator=run-pod/v1 -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Pokud používáte uzly Windows Server (aktuálně ve verzi preview v AKS), přidejte k příkazu volič uzlů a naplánujte kontejner Debianu na linuxovém uzlu:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Jakmile je relace terminálu připojena ke kontejneru, nainstalujte klienta SSH pomocí `apt-get`:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Otevřete nové okno terminálu, které není připojeno k kontejneru, zkopírujte svůj soukromý klíč SSH do pomocního modulu. Tento soukromý klíč se používá k vytvoření SSH do uzlu AKS. 

   V případě potřeby změňte *~/.ssh/id_rsa* umístění svého soukromého klíče SSH:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Vraťte se do terminálové relace do kontejneru, aktualizujte oprávnění na zkopírovaném soukromém klíči `id_rsa` SSH tak, aby byl jen pro uživatele:

    ```console
    chmod 0600 id_rsa
    ```

1. Vytvořte připojení SSH k uzlu AKS. Opět platí, že výchozí uživatelské jméno pro uzly AKS je *azureuser*. Přijměte výzvu k pokračování v připojení, protože klíč SSH je nejprve důvěryhodný. Poté budete mít k dispozici výzvu bash vašeho uzlu AKS:

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

## <a name="remove-ssh-access"></a>Odebrání přístupu SSH

Po dokončení `exit` relace SSH `exit` a potom interaktivní kontejner u. Když se tato relace kontejneru zavře, pod používaný pro přístup SSH z clusteru AKS se odstraní.

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další data pro řešení potíží, můžete [zobrazit kubelet protokoly][view-kubelet-logs] nebo [zobrazit protokoly hlavního uzlu Kubernetes][view-master-logs].

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

---
title: Připojte se přes RDP uzlech clusteru serveru systému Windows Azure Kubernetes Service (AKS)
description: Zjistěte, jak vytvořit připojení RDP s clusterem Azure Kubernetes Service (AKS) uzlech serveru systému Windows pro řešení potíží a údržby úlohy.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: mlearned
ms.openlocfilehash: 0238278b81255d735f8a950ca307d0e05100cfec
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614563"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Připojení přes RDP k Azure Kubernetes Service (AKS) uzlů clusteru Windows serveru pro údržbu nebo řešení potíží

V průběhu životního cyklu clusteru Azure Kubernetes Service (AKS) budete muset získat přístup k uzlu AKS Windows serveru. Tento přístup může být pro údržbu, shromažďování protokolů nebo jiné operace odstraňování potíží. Můžete přistupovat uzlů AKS Windows serveru pomocí protokolu RDP. Případně, pokud chcete použít SSH pro přístup k uzly AKS Windows serveru a mít přístup ke stejným klíčů, který jste použili při vytváření clusteru, provedením kroků v [SSH do uzlů clusteru Azure Kubernetes Service (AKS)][ssh-steps]. Z bezpečnostních důvodů nejsou uzlů AKS přístupný z Internetu.

Podpora uzel Windows Server je aktuálně ve verzi preview ve službě AKS.

Tento článek ukazuje, jak vytvořit připojení RDP k uzlu AKS pomocí jejich privátní IP adresy.

## <a name="before-you-begin"></a>Před zahájením

Tento článek předpokládá, že máte existující cluster AKS pomocí uzlu Windows serveru. Pokud potřebujete AKS cluster, najdete v článku [vytvoření clusteru AKS pomocí kontejneru Windows pomocí Azure CLI][aks-windows-cli]. You need the Windows administrator username and password for the Windows Server node you want to troubleshoot. You also need an RDP client such as [Microsoft Remote Desktop][rdp-mac].

Také nutné mít Azure CLI verze 2.0.61 nebo později nainstalované a nakonfigurované. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Nasazení virtuálního počítače ve stejné podsíti jako váš cluster

Uzlů Windows Server ve vašem clusteru AKS nemají zvenku přístupný IP adresy. Chcete-li vytvořit připojení RDP, můžete nasadit virtuální počítač s veřejně dostupná IP adresa na stejné podsíti jako uzly Windows serveru.

Následující příklad vytvoří virtuální počítač s názvem *myVM* v *myResourceGroup* skupinu prostředků.

Nejprve získejte podsíť používá váš fond uzlů Windows serveru. Pokud chcete získat id podsítě, budete potřebovat název podsítě. Pokud chcete získat název podsítě, budete potřebovat název virtuální sítě. Získejte název virtuální sítě pomocí dotazu na clusteru pro svůj seznam sítí. K dotazování clusteru, musíte její název. Získáte všechny tyto spuštěním následujícího ve službě Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Teď, když máte SUBNET_ID, spusťte následující příkaz v okně Azure Cloud Shell vytvořte virtuální počítač:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

Následující příklad výstupu ukazuje virtuální počítač byl úspěšně vytvořen a zobrazí veřejnou IP adresu virtuálního počítače.

```console
13.62.204.18
```

Zaznamenejte veřejnou IP adresu virtuálního počítače. Tuto adresu použijete v pozdějším kroku.

## <a name="get-the-node-address"></a>Získání adresy uzlu

Ke správě clusteru Kubernetes použijete [kubectl][kubectl], klienta příkazového řádku Kubernetes. Pokud používáte Azure Cloud Shell, `kubectl` je již nainstalována. Chcete-li nainstalovat `kubectl` místně, použijte [az aks install-cli][az-aks-install-cli] příkaz:
    
```azurecli-interactive
az aks install-cli
```

Ke konfiguraci `kubectl` pro připojení k vašemu clusteru Kubernetes, použijte [az aks get-credentials][az-aks-get-credentials] příkazu. Tento příkaz stáhne přihlašovací údaje a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Seznam uzlů Windows Server pomocí interní IP adresa [kubectl get][kubectl-get] příkaz:

```console
kubectl get nodes -o wide
```

Následující příklad výstupu ukazuje interních IP adres na všech uzlech v clusteru, včetně uzlů Windows serveru.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Zaznamenejte interní IP adresa uzlu Windows Server, který chcete vyřešit. Tuto adresu použijete v pozdějším kroku.

## <a name="connect-to-the-virtual-machine-and-node"></a>Připojte se k virtuálnímu počítači a uzel

Připojte se k veřejnou IP adresu virtuálního počítače, které jste předtím vytvořili pomocí klienta protokolu RDP, jako [Vzdálená plocha od Microsoftu][rdp-mac].

![Obrázek připojení k virtuálnímu počítači pomocí klienta protokolu RDP](media/rdp/vm-rdp.png)

Po připojení k virtuálnímu počítači, připojit k *interní IP adresa* uzlu Windows Server chcete řešení potíží pomocí klienta protokolu RDP z virtuálního počítače.

![Obrázek připojení k uzlu Windows serveru pomocí klienta protokolu RDP](media/rdp/node-rdp.png)

Nyní jste připojeni k uzlu Windows serveru.

![Obrázek zavřete okno příkazového řádku v uzlu Windows Server](media/rdp/node-session.png)

Teď můžete spustit všechny příkazy, řešení problémů ve *cmd* okna. Protože uzly Windows serveru použít Windows Server Core, není úplným grafickým uživatelským rozhraním nebo jiné nástroje grafického uživatelského rozhraní při připojování k uzlu Windows serveru přes protokol RDP.

## <a name="remove-rdp-access"></a>Odebrat přístup protokolu RDP

Až budete hotovi, ukončete připojení RDP k uzlu Windows Server pak ukončete relaci RDP k virtuálnímu počítači. Po ukončení obě relace protokolu RDP, odstraňte virtuální počítač s [az vm delete][az-vm-delete] příkaz:

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další data pro řešení problémů, můžete [zobrazit protokoly hlavní uzel Kubernetes][view-master-logs] or [Azure Monitor][azure-monitor-containers].

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md

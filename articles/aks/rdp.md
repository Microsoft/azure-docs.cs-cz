---
title: RdP na uzly AKS Windows Server
titleSuffix: Azure Kubernetes Service
description: Zjistěte, jak vytvořit připojení RDP s clustery Azure Kubernetes Service (AKS) uzly Windows Server pro řešení potíží a úlohy údržby.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 140d59894b38c7f07f16b0ac3cf99316c201d120
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886785"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Připojení k rdp clusteru Azure Kubernetes Service (AKS) uzly Windows Server pro údržbu nebo řešení potíží

Během celého životního cyklu clusteru Služby Azure Kubernetes (AKS) může být nutné získat přístup k uzlu AKS Windows Server. Tento přístup může být pro údržbu, shromažďování protokolů nebo jiné operace řešení potíží. K uzlům AKS Windows Server můžete přistupovat pomocí programu RDP. Případně pokud chcete použít SSH pro přístup k uzlům AKS Windows Server a máte přístup ke stejnému páru klíčů, který byl použit při vytváření clusteru, můžete postupovat podle kroků v [SSH do uzlů clusteru Služby Azure Kubernetes (AKS).][ssh-steps] Z bezpečnostních důvodů nejsou uzly AKS vystaveny internetu.

Podpora uzlů systému Windows Server je aktuálně ve verzi Preview v AKS.

Tento článek ukazuje, jak vytvořit připojení RDP s uzlem AKS pomocí jejich privátní IP adresy.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS s uzlem Systému Windows Server. Pokud potřebujete cluster AKS, přečtěte si článek o [vytvoření clusteru AKS s kontejnerem Windows pomocí azure cli][aks-windows-cli]. Pro uzel Windows Server, který chcete vyřešit, potřebujete uživatelské jméno a heslo správce systému Windows. Potřebujete také klienta RDP, například [vzdálenou plochu .][rdp-mac]

Potřebujete také nainstalované a nakonfigurované a nakonfigurované azure CLI verze 2.0.61 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Nasazení virtuálního počítače do stejné podsítě jako cluster

Uzly systému Windows Server clusteru AKS nemají externě přístupné adresy IP. Chcete-li vytvořit připojení protokolu RDP, můžete nasadit virtuální počítač s veřejně přístupnou adresou IP do stejné podsítě jako uzly systému Windows Server.

Následující příklad vytvoří virtuální počítač s názvem *myVM* ve skupině prostředků *myResourceGroup.*

Nejprve získejte podsíť používanou fondem uzlů systému Windows Server. Chcete-li získat ID podsítě, potřebujete název podsítě. Chcete-li získat název podsítě, potřebujete název virtuální sítě. Získejte název virtuální sítě dotazem clusteru pro jeho seznam sítí. Chcete-li dotaz clusteru, budete potřebovat jeho název. Všechny tyto informace můžete získat spuštěním následujících položek v prostředí Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Teď, když máte SUBNET_ID, spusťte následující příkaz ve stejném okně Azure Cloud Shell k vytvoření virtuálního počítače:

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

Následující příklad výstupu ukazuje, že virtuální počítač byl úspěšně vytvořen a zobrazí veřejnou IP adresu virtuálního počítače.

```console
13.62.204.18
```

Zaznamenejte veřejnou IP adresu virtuálního počítače. Tuto adresu použijete v pozdějším kroku.

## <a name="allow-access-to-the-virtual-machine"></a>Povolení přístupu k virtuálnímu počítači

Podsítě fondu uzlů AKS jsou ve výchozím nastavení chráněny skupinami zabezpečení sítě (Skupiny zabezpečení sítě). Chcete-li získat přístup k virtuálnímu počítači, budete muset mít povolený přístup v nsg.

> [!NOTE]
> NsG jsou řízeny službou AKS. Každá změna, kterou provedete v souboru nsg, bude kdykoli přepsána řídicí rovinou.
>

Nejprve získejte skupinu prostředků a název nsg nsg přidat pravidlo:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Potom vytvořte pravidlo nsg:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>Získání adresy uzlu

Chcete-li spravovat cluster Kubernetes, použijte [kubectl][kubectl], klientpříkazového řádku Kubernetes. Pokud používáte Azure `kubectl` Cloud Shell, je už nainstalovaný. Chcete-li nainstalovat `kubectl` místně, použijte příkaz [az aks install-cli:][az-aks-install-cli]
    
```azurecli-interactive
az aks install-cli
```

Pomocí příkazu [az aks get-credentials][az-aks-get-credentials] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového příkazu Kubernetes tak, aby je používalo.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Uveďte interní ADRESU IP uzlů systému Windows Server pomocí příkazu [kubectl get:][kubectl-get]

```console
kubectl get nodes -o wide
```

Následující příklad výstup ukazuje interní IP adresy všech uzlů v clusteru, včetně uzlů Windows Server.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Zaznamenejte interní adresu IP uzlu windows serveru, který chcete řešit. Tuto adresu použijete v pozdějším kroku.

## <a name="connect-to-the-virtual-machine-and-node"></a>Připojení k virtuálnímu počítači a uzlu

Připojte se k veřejné IP adrese virtuálního počítače, který jste vytvořili dříve pomocí klienta RDP, například [vzdálené plochy .][rdp-mac]

![Obrázek připojení k virtuálnímu počítači pomocí klienta RDP](media/rdp/vm-rdp.png)

Po připojení k virtuálnímu počítači se připojte k *interní IP adrese* uzlu Windows Server, který chcete řešit pomocí klienta RDP z virtuálního počítače.

![Obrázek připojení k uzlu Windows Server pomocí klienta RDP](media/rdp/node-rdp.png)

Nyní jste připojeni k uzlu Windows Server.

![Obrázek okna cmd v uzlu Windows Server](media/rdp/node-session.png)

Nyní můžete spustit všechny příkazy pro řešení potíží v okně *cmd.* Vzhledem k tomu, že uzly systému Windows Server používají windows server core, neexistuje při připojení k uzlu Windows Server přes rdp žádné úplné grafické uživatelské rozhraní ani jiné nástroje grafického uživatelského rozhraní.

## <a name="remove-rdp-access"></a>Odebrání přístupu k přístupu k přístupu k serveru RDP

Po dokončení ukončete připojení RDP k uzlu Windows Server a pak ukončete relaci RDP do virtuálního počítače. Po ukončení obou relací PROTOKOLU RDP odstraňte virtuální počítač pomocí [příkazu delete az vm:][az-vm-delete]

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

A pravidlo NSG:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další data pro řešení potíží, můžete [zobrazit protokoly hlavního uzlu Kubernetes][view-master-logs] nebo [Azure Monitor][azure-monitor-containers].

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

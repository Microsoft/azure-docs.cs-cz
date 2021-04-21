---
title: AKS uzlů Windows serveru přes protokol RDP
titleSuffix: Azure Kubernetes Service
description: Přečtěte si, jak vytvořit připojení RDP s clustery Windows serveru clusteru Azure Kubernetes Service (AKS) pro úlohy odstraňování potíží a údržby.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 62f29c0550b858e34d888da61f1bd7fbd358f82d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782922"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Připojení pomocí protokolu RDP ke službě Azure Kubernetes (AKS) clustery Windows serveru pro účely údržby nebo řešení potíží

V průběhu životního cyklu clusteru AKS (Azure Kubernetes Service) bude možná potřeba získat přístup k uzlu AKS Windows serveru. Tento přístup může být pro údržbu, shromažďování protokolů nebo jiné operace řešení potíží. K uzlům Windows serveru AKS můžete přistupovat pomocí protokolu RDP. Případně, pokud chcete použít SSH pro přístup k uzlům Windows serveru AKS a máte přístup ke stejnému souboru KeyPair, které jste použili při vytváření clusteru, můžete postupovat podle kroků v [SSH do uzlů clusteru Azure Kubernetes Service (AKS)][ssh-steps]. Pro účely zabezpečení nejsou uzly AKS k dispozici pro Internet.

V tomto článku se dozvíte, jak vytvořit připojení RDP s uzlem AKS pomocí svých privátních IP adres.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS s uzlem Windows serveru. Pokud potřebujete cluster AKS, přečtěte si článek o [Vytvoření clusteru AKS s kontejnerem Windows pomocí Azure CLI][aks-windows-cli]. Pro uzel Windows serveru, pro který chcete řešit potíže, budete potřebovat uživatelské jméno a heslo správce systému Windows. Pokud je neznáte, můžete je resetovat pomocí následujícího [resetování služby Vzdálená plocha nebo jejího hesla správce na virtuálním počítači s Windows ](/troubleshoot/azure/virtual-machines/reset-rdp). Potřebujete také klienta protokolu RDP, například [Vzdálená plocha Microsoft][rdp-mac].

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.61 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Nasazení virtuálního počítače do stejné podsítě jako cluster

Uzly Windows serveru v clusteru AKS nemají IP adresy externě přístupné. Chcete-li vytvořit připojení RDP, můžete nasadit virtuální počítač s veřejně dostupnou IP adresou do stejné podsítě jako uzly Windows serveru.

Následující příklad vytvoří virtuální počítač s názvem *myVM* ve skupině prostředků *myResourceGroup* .

Nejprve získejte podsíť, kterou používá fond uzlů Windows serveru. Chcete-li získat ID podsítě, budete potřebovat název podsítě. Chcete-li získat název podsítě, budete potřebovat název virtuální sítě. Získejte název virtuální sítě tak, že Dotazujte svůj cluster na svůj seznam sítí. K dotazování clusteru budete potřebovat jeho jméno. Všechny z nich můžete získat spuštěním následujícího v Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Teď, když máte SUBNET_ID, spusťte následující příkaz ve stejném okně Azure Cloud Shell a vytvořte virtuální počítač:

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

Následující příklad výstupu ukazuje, že se virtuální počítač úspěšně vytvořil a zobrazuje veřejnou IP adresu virtuálního počítače.

```console
13.62.204.18
```

Poznamenejte si veřejnou IP adresu virtuálního počítače. Tuto adresu budete používat v pozdějším kroku.

## <a name="allow-access-to-the-virtual-machine"></a>Povolení přístupu k virtuálnímu počítači

Podsítě fondu uzlů AKS jsou ve výchozím nastavení chráněné pomocí skupin zabezpečení sítě (skupiny zabezpečení sítě). Pokud chcete získat přístup k virtuálnímu počítači, budete muset povolit přístup v NSG.

> [!NOTE]
> Skupin zabezpečení sítě se řídí službou AKS. Všechny změny, které provedete v NSG, budou kdykoli přepsat rovinou ovládacího prvku.
>

Nejprve získejte skupinu prostředků a název NSG NSG, do kterého chcete přidat pravidlo:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Pak vytvořte pravidlo NSG:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>Získat adresu uzlu

Ke správě clusteru Kubernetes použijete klienta příkazového řádku Kubernetes [kubectl][kubectl]. Pokud používáte Azure Cloud Shell, `kubectl` je již nainstalováno. Pokud chcete nainstalovat `kubectl` místně, použijte příkaz [AZ AKS Install-CLI][az-aks-install-cli] :
    
```azurecli-interactive
az aks install-cli
```

Pomocí příkazu [az aks get-credentials][az-aks-get-credentials] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Výpis interní IP adresy uzlů Windows serveru pomocí příkazu [kubectl Get][kubectl-get] :

```console
kubectl get nodes -o wide
```

Následující příklad výstupu ukazuje interní IP adresy všech uzlů v clusteru, včetně uzlů Windows serveru.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Zaznamenejte interní IP adresu uzlu Windows serveru, na který chcete řešit potíže. Tuto adresu budete používat v pozdějším kroku.

## <a name="connect-to-the-virtual-machine-and-node"></a>Připojení k virtuálnímu počítači a uzlu

Připojte se k veřejné IP adrese virtuálního počítače, který jste vytvořili dříve pomocí klienta RDP, jako je například [Vzdálená plocha Microsoft][rdp-mac].

![Obrázek připojení k virtuálnímu počítači pomocí klienta RDP](media/rdp/vm-rdp.png)

Po připojení k virtuálnímu počítači se připojte k *interní IP adrese* uzlu Windows serveru, který chcete řešit pomocí klienta RDP z virtuálního počítače.

![Obrázek připojení k uzlu Windows serveru pomocí klienta RDP](media/rdp/node-rdp.png)

Nyní jste připojeni k uzlu Windows Server.

![Obrázek okna cmd v uzlu Windows serveru](media/rdp/node-session.png)

Nyní můžete spustit jakékoli příkazy pro řešení potíží v okně *příkazového řádku* . Vzhledem k tomu, že uzly Windows serveru používají jádro Windows serveru, není k dispozici celé grafické rozhraní (GUI) nebo jiné nástroje grafického uživatelského rozhraní, když se k uzlu Windows serveru přes RDP připojíte

## <a name="remove-rdp-access"></a>Odebrání přístupu RDP

Až to budete mít, ukončete připojení RDP k uzlu Windows serveru a potom relaci RDP ukončete s virtuálním počítačem. Po ukončení obou relací RDP odstraňte virtuální počítač pomocí příkazu [AZ VM Delete][az-vm-delete] :

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

Pokud potřebujete další data pro řešení potíží, můžete [Zobrazit protokoly nebo Azure monitor Kubernetes hlavního uzlu][view-master-logs] . [][azure-monitor-containers]

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-vm-delete]: /cli/azure/vm#az_vm_delete
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
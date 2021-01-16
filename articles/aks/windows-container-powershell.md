---
title: Vytvoření kontejneru Windows serveru v clusteru s AKS pomocí PowerShellu
description: Naučte se rychle vytvořit cluster Kubernetes a nasadit aplikaci v kontejneru Windows serveru ve službě Azure Kubernetes Service (AKS) pomocí PowerShellu.
services: container-service
ms.topic: article
ms.date: 05/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 97741423fa8b689a92bd9db78b810e6b86aefcbd
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247059"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-powershell"></a>Vytvoření kontejneru Windows serveru v clusteru služby Azure Kubernetes (AKS) pomocí PowerShellu

Služba Azure Kubernetes Service (AKS) je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. V tomto článku nasadíte cluster AKS pomocí prostředí PowerShell. `ASP.NET`V kontejneru Windows serveru také nasadíte ukázkovou aplikaci do clusteru.

![Obrázek přechodu na ukázkovou aplikaci ASP.NET](media/windows-container-powershell/asp-net-sample-app.png)

V tomto článku se předpokládá základní znalost konceptů Kubernetes. Další informace najdete v tématu [základní koncepty Kubernetes pro Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Pokud se rozhodnete použít prostředí PowerShell místně, Tento článek vyžaduje, abyste nainstalovali modul AZ PowerShell a připojili se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) . Další informace o instalaci modulu AZ PowerShell najdete v tématu [Install Azure PowerShell][install-azure-powershell]. Musíte taky nainstalovat modul PowerShell AZ. aks: 

```azurepowershell-interactive
Install-Module Az.Aks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud máte více předplatných Azure, vyberte příslušné předplatné, ve kterém se prostředky mají fakturovat. Pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext) vyberte konkrétní ID předplatného.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="limitations"></a>Omezení

Při vytváření a správě clusterů AKS, které podporují více fondů uzlů, platí následující omezení:

* Nemůžete odstranit první fond uzlů.

Pro fondy uzlů Windows serveru platí následující další omezení:

* Cluster AKS může mít maximálně 10 fondů uzlů.
* Cluster AKS může mít maximálně 100 uzlů v každém fondu uzlů.
* Název fondu uzlů Windows serveru má maximálně 6 znaků.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[Skupina prostředků Azure](../azure-resource-manager/management/overview.md) je logická skupina, ve které se nasazují a spravují prostředky Azure. Při vytváření skupiny prostředků se zobrazí výzva k zadání umístění. V tomto umístění se ukládají metadata skupin prostředků, a to i v případě, že se vaše prostředky spouštějí v Azure, pokud při vytváření prostředků nezadáte jinou oblast. Vytvořte skupinu prostředků pomocí rutiny [New-AzResourceGroup][new-azresourcegroup] .

Následující příklad vytvoří skupinu prostředků **myResourceGroup** v umístění **eastus**.

> [!NOTE]
> Tento článek používá syntaxi PowerShellu pro příkazy v tomto kurzu. Pokud používáte Azure Cloud Shell, ujistěte se, že rozevírací seznam v levé horní části okna Cloud Shell je nastaven na **PowerShell**.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Následující příklad výstupu ukazuje, že skupina prostředků byla úspěšně vytvořena:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Pomocí `ssh-keygen` nástroje příkazového řádku vygenerujte pár klíčů ssh. Další podrobnosti najdete v tématu [rychlé kroky: vytvoření a použití páru veřejných privátních klíčů ssh pro virtuální počítače se systémem Linux v Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

Pokud chcete spustit cluster AKS, který podporuje fondy uzlů pro kontejnery Windows serveru, musí cluster používat zásady sítě, které používají modul plug-in [Azure CNI][azure-cni-about] (Advanced) Network. Podrobnější informace, které vám pomůžou naplánovat požadované rozsahy podsítí a požadavky na síť, najdete v tématu [Konfigurace sítě Azure CNI][use-advanced-networking]. Pomocí rutiny [New-AzAks][new-azaks] vytvořte cluster AKS s názvem **myAKSCluster**. Následující příklad vytvoří nezbytné síťové prostředky, pokud neexistují.

> [!NOTE]
> Chcete-li zajistit spolehlivou činnost clusteru, měli byste spustit alespoň 2 (dva) uzly ve výchozím fondu uzlů.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 2 -KubernetesVersion 1.16.7 -NetworkPlugin azure -NodeVmSetType VirtualMachineScaleSets -WindowsProfileAdminUserName akswinuser -WindowsProfileAdminUserPassword $Password
```

> [!Note]
> Pokud nemůžete vytvořit cluster AKS, protože verze není v této oblasti podporovaná, můžete pomocí `Get-AzAksVersion -Location eastus` příkazu najít seznam podporovaných verzí pro tuto oblast.

Po několika minutách se příkaz dokončí a vrátí informace o clusteru. Může se stát, že cluster zřídí déle než několik minut. V těchto případech můžete trvat až 10 minut.

## <a name="add-a-windows-server-node-pool"></a>Přidat fond uzlů Windows serveru

Ve výchozím nastavení se cluster AKS vytvoří s fondem uzlů, který může spouštět kontejnery Linux. Pomocí `New-AzAksNodePool` rutiny přidejte fond uzlů, který může spouštět kontejnery Windows serveru společně s fondem uzlů pro Linux.

```azurepowershell-interactive
New-AzAksNodePool -ResourceGroupName myResourceGroup -ClusterName myAKSCluster -OsType Windows -Name npwin -KubernetesVersion 1.16.7
```

Výše uvedený příkaz vytvoří nový fond uzlů s názvem **npwin** a přidá ho do **myAKSCluster**. Při vytváření fondu uzlů pro spouštění kontejnerů Windows serveru je výchozí hodnota pro **VmSize** **Standard_D2s_v3**. Pokud se rozhodnete nastavit parametr **VmSize** , podívejte se na seznam [omezených velikostí virtuálních počítačů][restricted-vm-sizes]. Minimální doporučená velikost je **Standard_D2s_v3**. Předchozí příkaz používá také výchozí podsíť ve výchozí virtuální síti vytvořené při spuštění `New-AzAks` .

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Ke správě clusteru Kubernetes použijete klienta příkazového řádku Kubernetes [kubectl][kubectl]. Pokud používáte Azure Cloud Shell, `kubectl` je již nainstalováno. Pokud chcete nainstalovat `kubectl` místně, použijte `Install-AzAksKubectl` rutinu:

```azurepowershell-interactive
Install-AzAksKubectl
```

Ke konfiguraci `kubectl` pro připojení ke clusteru Kubernetes použijte rutinu [Import-AzAksCredential][import-azakscredential] . Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```azurepowershell-interactive
kubectl get nodes
```

Následující příklad výstupu ukazuje všechny uzly v clusteru. Ujistěte se, že stav všech uzlů je **připravený**:

```plaintext
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.7
aksnpwin987654                      Ready    agent   108s   v1.16.7
```

## <a name="run-the-application"></a>Spuštění aplikace

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například jaké image kontejnerů se mají spustit. V tomto článku se k vytvoření všech objektů potřebných ke spuštění ukázkové aplikace ASP.NET v kontejneru Windows serveru používá manifest. Tento manifest zahrnuje [nasazení Kubernetes][kubernetes-deployment] pro ukázkovou aplikaci ASP.NET a externí [službu Kubernetes][kubernetes-service] pro přístup k aplikaci z Internetu.

Ukázková aplikace ASP.NET se poskytuje jako součást [ukázek .NET Framework][dotnet-samples] a běží v kontejneru Windows serveru. AKS vyžaduje, aby kontejnery Windows serveru byly založené na imagí **Windows serveru 2019** nebo vyšší. Soubor manifestu Kubernetes musí také definovat [selektor uzlů][node-selector] , aby mohl cluster AKS spustit na uzlu, na kterém je možné spustit kontejnery Windows serveru, na kterém je spuštěná vaše ukázková aplikace ASP.NET.

Vytvořte soubor s názvem `sample.yaml` a zkopírujte ho do následující definice YAML. Pokud Azure Cloud Shell použijete, můžete tento soubor vytvořit pomocí `vi` nebo `nano` jako při práci na virtuálním nebo fyzickém systému:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Nasaďte aplikaci pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název manifestu YAML:

```azurepowershell-interactive
kubectl apply -f sample.yaml
```

Následující příklad výstupu ukazuje, že se úspěšně vytvořilo nasazení a služba:

```plaintext
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testování aplikace

Když je aplikace spuštěná, služba Kubernetes zpřístupňuje front-endové aplikace na internetu.
Dokončení tohoto procesu může trvat několik minut. Služba může občas trvat déle než několik minut. V těchto případech můžete trvat až 10 minut.

Pomocí příkazu [kubectl get service][kubectl-get] s argumentem `--watch` můžete sledovat průběh.

```azurepowershell-interactive
kubectl get service sample --watch
```

Zpočátku je **externí IP adresa** **ukázkové** služby zobrazená jako **čeká na vyřízení**.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Pokud se **IP** adresa změní z **čekání** na skutečnou veřejnou IP adresu, použijte `CTRL-C` k zastavení `kubectl` procesu sledování. Následující příklad výstupu ukazuje platnou veřejnou IP adresu přiřazenou ke službě:

```plaintext
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Pokud chcete vidět ukázkovou aplikaci v akci, otevřete webový prohlížeč na externí IP adresu vaší služby.

![Obrázek přechodu na ukázkovou aplikaci ASP.NET](media/windows-container-powershell/asp-net-sample-app.png)

> [!Note]
> Pokud při pokusu o načtení stránky dojde k vypršení časového limitu připojení, měli byste ověřit, že je ukázková aplikace připravená pomocí následujícího příkazu `kubectl get pods --watch` . V některých případech se kontejner Windows nebude spouštět v době, kdy je k dispozici externí IP adresa.

## <a name="delete-cluster"></a>Odstranění clusteru

Pokud už cluster nepotřebujete, odeberte skupinu prostředků, službu kontejneru a všechny související prostředky pomocí rutiny [Remove-AzResourceGroup][remove-azresourcegroup] .

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete]. Pokud jste použili spravovanou identitu, tato identita je spravovaná platformou a nevyžaduje odebrání.

## <a name="next-steps"></a>Další kroky

V tomto článku jste nasadili cluster Kubernetes a do něj jste nasadili `ASP.NET` ukázkovou aplikaci v kontejneru Windows serveru. [Přístup k webovému řídicímu panelu Kubernetes][kubernetes-dashboard] pro vytvořený cluster.

Další informace o službě AKS a podrobné vysvětlení kompletního příkladu od kódu až po nasazení najdete v kurzu clusteru Kubernetes.

> [!div class="nextstepaction"]
> [Kurz AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[use-advanced-networking]: configure-azure-cni.md
[new-azaks]: /powershell/module/az.aks/new-azaks
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md

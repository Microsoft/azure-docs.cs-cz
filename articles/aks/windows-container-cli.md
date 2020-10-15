---
title: Vytvoření kontejneru Windows serveru v clusteru AKS pomocí Azure CLI
description: Naučte se rychle vytvořit cluster Kubernetes a nasadit aplikaci v kontejneru Windows serveru ve službě Azure Kubernetes Service (AKS) pomocí rozhraní příkazového řádku Azure CLI.
services: container-service
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 2c99244df7811b09abaf10d54d924a727201bbad
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076636"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Vytvoření kontejneru Windows serveru v clusteru služby Azure Kubernetes (AKS) pomocí rozhraní příkazového řádku Azure

Služba Azure Kubernetes Service (AKS) je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. V tomto článku nasadíte cluster AKS pomocí Azure CLI. V kontejneru Windows serveru také nasadíte ukázkovou aplikaci ASP.NET do clusteru.

![Obrázek přechodu na ukázkovou aplikaci ASP.NET](media/windows-container/asp-net-sample-app.png)

V tomto článku se předpokládá základní znalost konceptů Kubernetes. Další informace najdete v tématu [základní koncepty Kubernetes pro Azure Kubernetes Service (AKS)][kubernetes-concepts].

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="limitations"></a>Omezení

Při vytváření a správě clusterů AKS, které podporují více fondů uzlů, platí následující omezení:

* Nemůžete odstranit první fond uzlů.

Pro fondy uzlů Windows serveru platí následující další omezení:

* Cluster AKS může mít maximálně 10 fondů uzlů.
* Cluster AKS může mít maximálně 100 uzlů v každém fondu uzlů.
* Název fondu uzlů Windows serveru má maximálně 6 znaků.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure. Při vytváření skupiny prostředků se zobrazí výzva k zadání umístění. V tomto umístění se ukládají metadata skupin prostředků, a to i v případě, že se vaše prostředky spouštějí v Azure, pokud při vytváření prostředků nezadáte jinou oblast. Vytvořte skupinu prostředků pomocí příkazu [AZ Group Create][az-group-create] .

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

> [!NOTE]
> Tento článek používá syntaxi bash pro příkazy v tomto kurzu.
> Pokud používáte Azure Cloud Shell, ujistěte se, že rozevírací seznam v levém horním rohu okna Cloud Shell je nastaven na **bash**.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Následující příklad výstupu ukazuje, že skupina prostředků byla úspěšně vytvořena:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Pokud chcete spustit cluster AKS, který podporuje fondy uzlů pro kontejnery Windows serveru, musí cluster používat zásady sítě, které používají modul plug-in [Azure CNI][azure-cni-about] (Advanced) Network. Podrobnější informace, které vám pomůžou naplánovat požadované rozsahy podsítí a požadavky na síť, najdete v tématu [Konfigurace sítě Azure CNI][use-advanced-networking]. Pomocí příkazu [AZ AKS Create][az-aks-create] vytvořte cluster AKS s názvem *myAKSCluster*. Tento příkaz vytvoří nezbytné síťové prostředky, pokud neexistují.

* Cluster je nakonfigurovaný se dvěma uzly.
* Parametry *Windows-Admin-Password* a *Windows-admin-username* nastavily přihlašovací údaje správce pro všechny kontejnery Windows serveru vytvořené v clusteru a musí splňovat [požadavky na heslo pro Windows Server][windows-server-password].
* Fond uzlů používá `VirtualMachineScaleSets`

> [!NOTE]
> Aby cluster fungoval spolehlivě, měli byste spustit alespoň 2 (dva) uzly ve výchozím fondu uzlů.

Zadejte vlastní zabezpečený *PASSWORD_WIN* (Nezapomeňte, že příkazy v tomto článku se zadávají do prostředí bash):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --vm-set-type VirtualMachineScaleSets \
    --network-plugin azure
```

> [!NOTE]
> Pokud se zobrazí chyba ověřování hesla, ověřte, že parametr *Windows-Admin-Password* splňuje [požadavky na heslo pro Windows Server][windows-server-password]. Pokud vaše heslo splňuje požadavky, zkuste vytvořit skupinu prostředků v jiné oblasti. Pak zkuste cluster vytvořit s novou skupinou prostředků.

Po několika minutách se příkaz dokončí a vrátí informace o clusteru ve formátu JSON. Může se stát, že cluster zřídí déle než několik minut. V těchto případech můžete trvat až 10 minut.

## <a name="add-a-windows-server-node-pool"></a>Přidat fond uzlů Windows serveru

Ve výchozím nastavení se cluster AKS vytvoří s fondem uzlů, který může spouštět kontejnery Linux. Pomocí `az aks nodepool add` příkazu můžete přidat další fond uzlů, který může spouštět kontejnery Windows serveru společně s fondem uzlů pro Linux.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

Výše uvedený příkaz vytvoří nový fond uzlů s názvem *npwin* a přidá ho do *myAKSCluster*. Při vytváření fondu uzlů pro spouštění kontejnerů Windows serveru je výchozí hodnota pro *Node-VM-size* *Standard_D2s_v3*. Pokud se rozhodnete nastavit parametr *Node-VM-Size* , zkontrolujte prosím seznam [omezených velikostí virtuálních počítačů][restricted-vm-sizes]. Minimální doporučená velikost je *Standard_D2s_v3*. Výše uvedený příkaz používá také výchozí podsíť ve výchozí virtuální síti vytvořené při spuštění `az aks create` .

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Ke správě clusteru Kubernetes použijete klienta příkazového řádku Kubernetes [kubectl][kubectl]. Pokud používáte Azure Cloud Shell, `kubectl` je již nainstalováno. Pokud chcete nainstalovat `kubectl` místně, použijte příkaz [AZ AKS Install-CLI][az-aks-install-cli] :

```azurecli
az aks install-cli
```

Pomocí příkazu [az aks get-credentials][az-aks-get-credentials] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```console
kubectl get nodes
```

Následující příklad výstupu ukazuje všechny uzly v clusteru. Ujistěte se, že stav všech uzlů je *připravený*:

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.9
aksnpwin987654                      Ready    agent   108s   v1.16.9
```

## <a name="run-the-application"></a>Spuštění aplikace

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například jaké image kontejnerů se mají spustit. V tomto článku se k vytvoření všech objektů potřebných ke spuštění ukázkové aplikace ASP.NET v kontejneru Windows serveru používá manifest. Tento manifest zahrnuje [nasazení Kubernetes][kubernetes-deployment] pro ukázkovou aplikaci ASP.NET a externí [službu Kubernetes][kubernetes-service] pro přístup k aplikaci z Internetu.

Ukázková aplikace ASP.NET se poskytuje jako součást [ukázek .NET Framework][dotnet-samples] a běží v kontejneru Windows serveru. AKS vyžaduje, aby kontejnery Windows serveru byly založené na imagí *Windows serveru 2019* nebo vyšší. Soubor manifestu Kubernetes musí také definovat [selektor uzlů][node-selector] , aby mohl cluster AKS spustit na uzlu, na kterém je možné spustit kontejnery Windows serveru, na kterém je spuštěná vaše ukázková aplikace ASP.NET.

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

```console
kubectl apply -f sample.yaml
```

Následující příklad výstupu ukazuje, že se úspěšně vytvořilo nasazení a služba:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testování aplikace

Když je aplikace spuštěná, služba Kubernetes zpřístupňuje front-end aplikace na internetu. Dokončení tohoto procesu může trvat několik minut. Služba může občas trvat déle než několik minut. V těchto případech můžete trvat až 10 minut.

Pomocí příkazu [kubectl get service][kubectl-get] s argumentem `--watch` můžete sledovat průběh.

```console
kubectl get service sample --watch
```

Zpočátku je *externí IP adresa* *ukázkové* služby zobrazená jako *čeká na vyřízení*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Pokud se *IP* adresa změní z *čekání* na skutečnou veřejnou IP adresu, použijte `CTRL-C` k zastavení `kubectl` procesu sledování. Následující příklad výstupu ukazuje platnou veřejnou IP adresu přiřazenou ke službě:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Pokud chcete vidět ukázkovou aplikaci v akci, otevřete webový prohlížeč na externí IP adresu vaší služby.

![Obrázek přechodu na ukázkovou aplikaci ASP.NET](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Pokud při pokusu o načtení stránky dojde k vypršení časového limitu připojení, měli byste ověřit, že ukázková aplikace je připravená pomocí následujícího příkazu [kubectl získat lusky--Watch]. V některých případech se kontejner Windows nebude spouštět v době, kdy je k dispozici externí IP adresa.

## <a name="delete-cluster"></a>Odstranění clusteru

Pokud už cluster nepotřebujete, použijte k odebrání skupiny prostředků, služby kontejneru a všech souvisejících prostředků příkaz [az group delete][az-group-delete].

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete]. Pokud jste použili spravovanou identitu, tato identita je spravovaná platformou a nevyžaduje odebrání.

## <a name="next-steps"></a>Další kroky

V tomto článku jste nasadili cluster Kubernetes a do něj jste nasadili ukázkovou aplikaci ASP.NET v kontejneru Windows serveru. Přihlaste [se k webovému řídicímu panelu Kubernetes][kubernetes-dashboard] pro cluster, který jste právě vytvořili.

Další informace o službě AKS a podrobné vysvětlení kompletního příkladu od kódu až po nasazení najdete v kurzu clusteru Kubernetes.

> [!div class="nextstepaction"]
> [Kurz AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/insights/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
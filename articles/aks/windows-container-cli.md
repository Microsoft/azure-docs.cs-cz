---
title: Preview – vytvoření kontejneru Windows serveru v clusteru služby Azure Kubernetes (AKS)
description: Naučte se rychle vytvořit cluster Kubernetes a nasadit aplikaci v kontejneru Windows serveru ve službě Azure Kubernetes Service (AKS) pomocí rozhraní příkazového řádku Azure CLI.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/17/2019
ms.author: mlearned
ms.openlocfilehash: 6c4d143bdaee0818d32b846a38a63eb48f69f717
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034032"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Preview – vytvoření kontejneru Windows serveru v clusteru služby Azure Kubernetes (AKS) pomocí rozhraní příkazového řádku Azure

Služba Azure Kubernetes Service (AKS) je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. V tomto článku nasadíte cluster AKS pomocí Azure CLI. V kontejneru Windows serveru také nasadíte ukázkovou aplikaci ASP.NET do clusteru.

Tato funkce je aktuálně ve verzi Preview.

![Obrázek přechodu na ukázkovou aplikaci ASP.NET](media/windows-container/asp-net-sample-app.png)

V tomto článku se předpokládá základní znalost konceptů Kubernetes. Další informace najdete v tématu [základní koncepty Kubernetes pro Azure Kubernetes Service (AKS)][kubernetes-concepts].

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.0.61 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="before-you-begin"></a>Před zahájením

Po vytvoření clusteru, který může spouštět kontejnery Windows serveru, musíte přidat další fond uzlů. Přidání dalšího fondu uzlů je zahrnuto v pozdějším kroku, ale nejdřív musíte povolit několik funkcí verze Preview.

> [!IMPORTANT]
> Funkce služby AKS ve verzi Preview jsou samoobslužné přihlašovací. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepšího úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace o tom, jak se zaregistrují, najdete v následujících článcích podpory:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Nainstalovat rozšíření CLI AKS-Preview

Pokud chcete používat kontejnery Windows serveru, potřebujete rozšíření CLI *AKS-Preview* verze 0.4.1 nebo vyšší. Nainstalujte rozšíření Azure CLI *AKS-Preview* pomocí příkazu [AZ Extension Add][az-extension-add] a potom zkontrolujte všechny dostupné aktualizace pomocí příkazu [AZ Extension Update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Registrovat funkci Windows Preview

Pokud chcete vytvořit cluster AKS, který může používat víc fondů uzlů a spustit kontejnery Windows serveru, nejdřív Povolte ve svém předplatném příznaky funkcí *WindowsPreview* . Funkce *WindowsPreview* používá k řízení nasazení a konfigurace uzlů Kubernetes taky clustery s více uzly a sadu škálování virtuálního počítače. Pomocí příkazu [AZ Feature Register][az-feature-register] Zaregistrujte příznak funkce *WindowsPreview* , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Libovolný cluster AKS, který vytvoříte po úspěšném zaregistrování příznaku funkce *WindowsPreview* , použijte tuto verzi Preview prostředí clusteru. Pokud chcete pokračovat v vytváření běžných, plně podporovaných clusterů, nepovolujte funkce ve verzi Preview v produkčních předplatných. Pro testování funkcí ve verzi Preview použijte samostatný test nebo vývojové předplatné Azure.

Dokončení registrace trvá několik minut. Pomocí příkazu [AZ Feature list][az-feature-list] ověřte stav registrace.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Když je `Registered`stav registrace, stisknutím kombinace kláves CTRL + C zastavíte monitorování stavu.  Potom aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Omezení

Při vytváření a správě clusterů AKS, které podporují více fondů uzlů, platí následující omezení:

* Pro clustery vytvořené po úspěšném zaregistrování služby *WindowsPreview*jsou k dispozici více fondů uzlů. Při registraci funkcí *MultiAgentpoolPreview* a *VMSSPreview* pro vaše předplatné jsou k dispozici také více fondů uzlů. Nemůžete přidat ani spravovat fondy uzlů s existujícím clusterem AKS vytvořeným před tím, než se tyto funkce úspěšně zaregistrovaly.
* Nemůžete odstranit první fond uzlů.

I když je tato funkce ve verzi Preview, platí následující další omezení:

* Cluster AKS může mít maximálně osm fondů uzlů.
* Cluster AKS může mít maximálně 400 uzlů v těchto osmi fondech uzlů.
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

Aby bylo možné spustit cluster AKS, který podporuje fondy uzlů pro kontejnery Windows serveru, musí cluster používat zásady sítě, které používají modul plug-in [Azure CNI][azure-cni-about] (Advanced) Network plugin. Podrobnější informace, které vám pomůžou naplánovat požadované rozsahy podsítí a požadavky na síť, najdete v tématu [Konfigurace sítě Azure CNI][use-advanced-networking]. Pomocí příkazu [AZ AKS Create][az-aks-create] vytvořte cluster AKS s názvem *myAKSCluster*. Tento příkaz vytvoří nezbytné síťové prostředky, pokud neexistují.
  * Cluster je nakonfigurovaný s jedním uzlem.
  * Parametry *Windows-Admin-Password* a *Windows-admin-username* nastavily přihlašovací údaje správce pro všechny kontejnery Windows serveru vytvořené v clusteru.

Poskytněte vlastní zabezpečené *PASSWORD_WIN* (Nezapomeňte, že příkazy v tomto článku se zadávají do prostředí bash):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.5 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

> [!Note]
> Pokud se zobrazí chyba ověřování hesla, zkuste vytvořit skupinu prostředků v jiné oblasti.
> Pak zkuste cluster vytvořit s novou skupinou prostředků.

Po několika minutách se příkaz dokončí a vrátí informace o clusteru ve formátu JSON.

## <a name="add-a-windows-server-node-pool"></a>Přidat fond uzlů Windows serveru

Ve výchozím nastavení se cluster AKS vytvoří s fondem uzlů, který může spouštět kontejnery Linux. Pomocí `az aks nodepool add` příkazu můžete přidat další fond uzlů, který může spouštět kontejnery Windows serveru.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.5
```

Výše uvedený příkaz vytvoří nový fond uzlů s názvem *npwin* a přidá ho do *myAKSCluster*. Při vytváření fondu uzlů pro spouštění kontejnerů Windows serveru je výchozí hodnota pro *Node-VM-Size* *Standard_D2s_v3*. Pokud se rozhodnete nastavit parametr *Node-VM-Size* , zkontrolujte prosím seznam [omezených velikostí virtuálních počítačů][restricted-vm-sizes]. Minimální doporučená velikost je *Standard_D2s_v3*. Výše uvedený příkaz používá také výchozí podsíť ve výchozí virtuální síti vytvořené při spuštění `az aks create`.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Ke správě clusteru Kubernetes použijete klienta příkazového řádku Kubernetes [kubectl][kubectl]. Pokud používáte Azure Cloud Shell, `kubectl` je již nainstalováno. Pokud chcete `kubectl` nainstalovat místně, použijte příkaz [AZ AKS Install-CLI][az-aks-install-cli] :

```azurecli
az aks install-cli
```

Pokud chcete `kubectl` nakonfigurovat připojení ke clusteru Kubernetes, použijte příkaz [AZ AKS Get-Credentials][az-aks-get-credentials] . Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```azurecli-interactive
kubectl get nodes
```

Následující příklad výstupu ukazuje jeden uzel vytvořený v předchozích krocích. Ujistěte se, že stav uzlu je *připravený*:

```
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.5
aksnpwin987654                      Ready    agent   108s   v1.14.5
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

```azurecli-interactive
kubectl apply -f sample.yaml
```

Následující příklad výstupu ukazuje, že se úspěšně vytvořilo nasazení a služba:

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testování aplikace

Když je aplikace spuštěná, služba Kubernetes zpřístupňuje front-end aplikace na internetu. Dokončení tohoto procesu může trvat několik minut.

Pomocí příkazu [kubectl get service][kubectl-get] s argumentem `--watch` můžete sledovat průběh.

```azurecli-interactive
kubectl get service sample --watch
```

Zpočátku je *externí IP adresa* *ukázkové* služby zobrazená jako *čeká na vyřízení*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Pokud se *IP* adresa změní z *čekání* na skutečnou veřejnou IP adresu, použijte `CTRL-C` k zastavení `kubectl` procesu sledování. Následující příklad výstupu ukazuje platnou veřejnou IP adresu přiřazenou ke službě:

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Pokud chcete vidět ukázkovou aplikaci v akci, otevřete webový prohlížeč na externí IP adresu vaší služby.

![Obrázek přechodu na ukázkovou aplikaci ASP.NET](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>Odstranit cluster

Pokud už cluster nepotřebujete, odeberte skupinu prostředků, službu kontejneru a všechny související prostředky pomocí příkazu [AZ Group Delete][az-group-delete] .

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu [AKS a informace o instančním objektu a jejich odstranění][sp-delete].

## <a name="next-steps"></a>Další postup

V tomto článku jste nasadili cluster Kubernetes a do něj jste nasadili ukázkovou aplikaci ASP.NET v kontejneru Windows serveru. Přihlaste [se k webovému řídicímu panelu Kubernetes][kubernetes-dashboard] pro cluster, který jste právě vytvořili.

Další informace o službě AKS a podrobné vysvětlení kompletního příkladu od kódu až po nasazení najdete v kurzu clusteru Kubernetes.

> [!div class="nextstepaction"]
> [Kurz k AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
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
[use-advanced-networking]: configure-advanced-networking.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

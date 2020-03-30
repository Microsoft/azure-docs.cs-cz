---
title: Náhled – vytvoření kontejneru Windows Serveru v clusteru služby Azure Kubernetes Service (AKS)
description: Zjistěte, jak rychle vytvořit cluster Kubernetes, nasadit aplikaci v kontejneru Windows Serveru ve službě Azure Kubernetes Service (AKS) pomocí azure cli.
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 259728da5ea7f71110ce183ae25bb47a0f873614
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475506"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Preview – vytvoření kontejneru Windows Serveru v clusteru služby Azure Kubernetes Service (AKS) pomocí příkazového příkazového příkazu Azure

Azure Kubernetes Service (AKS) je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. V tomto článku nasadíte cluster AKS pomocí azure CLI. Do clusteru také nasadíte ukázkovou aplikaci ASP.NET v kontejneru windows serveru.

Tato funkce je aktuálně ve verzi Preview.

![Obrázek procházení ASP.NET ukázkové aplikace](media/windows-container/asp-net-sample-app.png)

Tento článek předpokládá základní znalosti konceptů Kubernetes. Další informace najdete v tématu [Kubernetes základní koncepty pro službu Azure Kubernetes Service (AKS)][kubernetes-concepts].

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat příkaz cli místně, tento článek vyžaduje, abyste spouštěli Azure CLI verze 2.0.61 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="before-you-begin"></a>Než začnete

Po vytvoření clusteru, který může spouštět kontejnery Windows Serveru, je nutné přidat další fond uzlů. Přidání dalšího fondu uzlů je pokryto v pozdějším kroku, ale nejprve musíte povolit několik funkcí náhledu.

> [!IMPORTANT]
> Funkce AKS preview jsou samoobslužné opt-in. Náhledy jsou poskytovány "tak, jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS Previews jsou částečně pokryty zákaznickou podporou na základě maximálního úsilí. Jako takové tyto funkce nejsou určeny pro produkční použití. Další infromace naleznete v následujících článcích podpory:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření cli aks-preview

Chcete-li používat kontejnery Windows Server, potřebujete rozšíření cli *aks-preview* verze 0.4.12 nebo vyšší. Nainstalujte rozšíření *AKS-preview* Azure CLI pomocí příkazu [az extension add][az-extension-add] a pak zkontrolujte všechny dostupné aktualizace pomocí příkazu [aktualizace rozšíření az::][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Registrace funkce náhledu systému Windows

Chcete-li vytvořit cluster AKS, který může používat více fondů uzlů a spouštět kontejnery windows serveru, nejprve povolte příznaky funkce *WindowsPreview* ve vašem předplatném. Funkce *WindowsPreview* také používá clustery fondu více uzlů a škálovací sadu virtuálních počítačů ke správě nasazení a konfigurace uzlů Kubernetes. Zaregistrujte příznak funkce *WindowsPreview* pomocí příkazu [az feature register,][az-feature-register] jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Všechny clustery AKS, které vytvoříte po úspěšné registraci příznaku funkce *WindowsPreview,* používají toto prostředí clusteru preview. Chcete-li pokračovat ve vytváření běžných plně podporovaných clusterů, nepovolujte funkce náhledu v produkčních předplatných. Pro testování funkcí preview použijte samostatné testovací nebo vývojové předplatné Azure.

Trvá několik minut, než se registrace dokončí. Zkontrolujte stav registrace pomocí příkazu [az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Pokud je `Registered`stav registrace , stisknutím kombinace kláves Ctrl-C zastavení sledování stavu.  Potom aktualizujte registraci poskytovatele prostředků *Microsoft.ContainerService* pomocí příkazu [registrovat zprostředkovatele az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Omezení

Následující omezení platí při vytváření a správě clusterů AKS, které podporují více fondů uzlů:

* Nelze odstranit fond prvních uzlů.

Pokud je tato funkce ve verzi Preview, platí následující další omezení:

* Cluster AKS může mít maximálně osm fondů uzlů.
* Cluster AKS může mít maximálně 400 uzlů v těchto osmi fondech uzlů.
* Název fondu uzlů systému Windows Server má limit 6 znaků.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure. Při vytváření skupiny prostředků se zobrazí výzva k zadání umístění. Toto umístění je místo, kde se ukládají metadata skupiny prostředků, je také místo, kde vaše prostředky spustit v Azure, pokud nezadáte jinou oblast během vytváření prostředků. Vytvořte skupinu prostředků pomocí příkazu [vytvořit skupinu az.][az-group-create]

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

> [!NOTE]
> Tento článek používá syntaxi Bash pro příkazy v tomto kurzu.
> Pokud používáte Azure Cloud Shell, ujistěte se, že rozbalovací nabídka v levém horním rohu okna cloudshellu je nastavena na **Bash**.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Následující ukázkový výstup ukazuje skupinu prostředků, která byla úspěšně vytvořena:

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

Aby bylo možné spustit cluster AKS, který podporuje fondy uzlů pro kontejnery Windows Server, musí váš cluster používat zásady sítě, které používají síťový plugin [Azure CNI][azure-cni-about] (Advanced). Podrobnější informace, které vám pomohou naplánovat požadované rozsahy podsítí a aspekty sítě, najdete v [tématu konfigurace sítě Azure CNI][use-advanced-networking]. Pomocí příkazu [az aks create][az-aks-create] vytvořte cluster AKS s názvem *myAKSCluster*. Tento příkaz vytvoří potřebné síťové prostředky, pokud neexistují.
  * Cluster je nakonfigurován se dvěma uzly.
  * Parametry *windows-admin-password* a *windows-admin-username* nastavují pověření správce pro všechny kontejnery Windows Serveru vytvořené v clusteru.

> [!NOTE]
> Chcete-li zajistit, aby váš cluster fungoval spolehlivě, měli byste spustit alespoň 2 (dva) uzly ve výchozím fondu uzlů.

Zadejte vlastní bezpečné *PASSWORD_WIN* (nezapomeňte, že příkazy v tomto článku jsou zadány do prostředí BASH):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --kubernetes-version 1.15.7 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --network-plugin azure
```

> [!Note]
> Pokud se zobrazí chyba ověření hesla, zkuste vytvořit skupinu prostředků v jiné oblasti.
> Potom zkuste vytvořit cluster s novou skupinou prostředků.

> [!Note]
> Pokud nelze vytvořit cluster AKS, protože verze není v této oblasti podporována, můžete použít příkaz [az aks get-versions --location eastus] k nalezení seznamu podporovaných verzí pro tuto oblast.


Po několika minutách příkaz dokončí a vrátí informace o clusteru ve formátu JSON. V některých případě může zřízení clusteru trvat déle než několik minut. V těchto případech vyčkejte až 10 minut. 

## <a name="add-a-windows-server-node-pool"></a>Přidání fondu uzlů systému Windows Server

Ve výchozím nastavení je cluster AKS vytvořen s fondem uzlů, který může spouštět kontejnery Linuxu. Pomocí `az aks nodepool add` příkazu můžete přidat další fond uzlů, který může spouštět kontejnery windows serveru.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.15.7
```

Výše uvedený příkaz vytvoří nový fond uzlů s názvem *npwin* a přidá jej do *myAKSCluster*. Při vytváření fondu uzlů pro spuštění kontejnerů systému Windows Server je výchozí hodnota pro *velikost uzlu a virtuálního počítače* *Standard_D2s_v3*. Pokud se rozhodnete nastavit parametr *velikosti uzlu-vm,* zkontrolujte seznam [omezených velikostí virtuálních her][restricted-vm-sizes]. Minimální doporučená velikost je *Standard_D2s_v3*. Výše uvedený příkaz také používá výchozí podsíť ve `az aks create`výchozí virtuální síti vytvořené při spuštění .

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Chcete-li spravovat cluster Kubernetes, použijte [kubectl][kubectl], klientpříkazového řádku Kubernetes. Pokud používáte Azure `kubectl` Cloud Shell, je už nainstalovaný. Chcete-li nainstalovat `kubectl` místně, použijte příkaz [az aks install-cli:][az-aks-install-cli]

```azurecli
az aks install-cli
```

Pomocí příkazu [az aks get-credentials][az-aks-get-credentials] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového příkazu Kubernetes tak, aby je používalo.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```console
kubectl get nodes
```

Následující příklad výstup ukazuje všechny uzly v clusteru. Ujistěte se, že stav všech uzlů je *připraven*:

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.15.7
aksnpwin987654                      Ready    agent   108s   v1.15.7
```

## <a name="run-the-application"></a>Spuštění aplikace

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například jaké image kontejneru mají být spuštěny. V tomto článku manifest se používá k vytvoření všech objektů potřebných ke spuštění ukázkové aplikace ASP.NET v kontejneru systému Windows Server. Tento manifest zahrnuje [nasazení Kubernetes][kubernetes-deployment] pro ukázkovou aplikaci ASP.NET a externí [službu Kubernetes][kubernetes-service] pro přístup k aplikaci z internetu.

Ukázková aplikace ASP.NET je k dispozici jako součást [ukázek rozhraní .NET Framework][dotnet-samples] a spouští se v kontejneru windows serveru. AKS vyžaduje, aby kontejnery Windows Serveru byly založeny na ifotkách *systému Windows Server 2019* nebo vyšších. Soubor manifestu Kubernetes musí také definovat [volič uzlů,][node-selector] aby váš cluster AKS spouštěl pod ukázkové aplikace ASP.NET na uzlu, který může spouštět kontejnery Windows Server.

Vytvořte soubor `sample.yaml` s názvem a zkopírujte v následující definici YAML. Pokud používáte Azure Cloud Shell, tento `vi` soubor `nano` lze vytvořit pomocí nebo jako by pracovat na virtuální nebo fyzický systém:

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

Nasazení aplikace pomocí příkazu [kubectl apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f sample.yaml
```

Následující ukázkový výstup ukazuje úspěšně vytvořené nasazení a službu:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testování aplikace

Při spuštění aplikace služba Kubernetes zpřístupňuje front-end aplikace k internetu. Dokončení tohoto procesu může trvat několik minut. V některých případě může služba trvat déle než několik minut. V těchto případech vyčkejte až 10 minut.

Pomocí příkazu [kubectl get service][kubectl-get] s argumentem `--watch` můžete sledovat průběh.

```console
kubectl get service sample --watch
```

Zpočátku *external-IP* pro *ukázkovou* službu se zobrazí jako čekající na *vyřízení*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Když se adresa *EXTERNAL-IP* změní z *čekající* na `CTRL-C` skutečnou `kubectl` veřejnou IP adresu, použijte k zastavení procesu sledování. Následující ukázkový výstup ukazuje platnou veřejnou IP adresu přiřazenou službě:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Chcete-li zobrazit ukázkovou aplikaci v akci, otevřete webový prohlížeč s externí IP adresou vaší služby.

![Obrázek procházení ASP.NET ukázkové aplikace](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Pokud obdržíte časový limit připojení při pokusu o načtení stránky, měli byste ověřit ukázkovou aplikaci je připraven s následujícím příkazem [kubectl get pods --watch]. Někdy kontejner systému Windows nebude spuštěn v době, kdy je k dispozici externí IP adresa.

## <a name="delete-cluster"></a>Odstranění clusteru

Pokud už cluster nepotřebujete, použijte k odebrání skupiny prostředků, služby kontejneru a všech souvisejících prostředků příkaz [az group delete][az-group-delete].

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete].

## <a name="next-steps"></a>Další kroky

V tomto článku jste nasadili cluster Kubernetes a nasadili ASP.NET ukázkovou aplikaci v kontejneru Windows Serveru. [Získejte přístup k webovému řídicímu panelu Kubernetes][kubernetes-dashboard] pro cluster, který jste právě vytvořili.

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

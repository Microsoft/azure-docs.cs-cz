---
title: 'Ve verzi Preview: vytvoření kontejneru Windows serveru v clusteru Azure Kubernetes Service (AKS)'
description: Zjistěte, jak rychle vytvořit Kubernetes cluster, nasazení aplikace v kontejneru Windows Server ve službě Azure Kubernetes Service (AKS) pomocí Azure CLI.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 06/17/2019
ms.author: twhitney
ms.openlocfilehash: a9887e923358b5658a365b5cfc88759eca2501e0
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303557"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Ve verzi Preview: vytvoření kontejneru Windows serveru v clusteru Azure Kubernetes Service (AKS) pomocí rozhraní příkazového řádku Azure

Azure Kubernetes Service (AKS) je spravovaná služba, která vám umožní rychle nasadit a spravovat clustery Kubernetes. V tomto článku nasadíte cluster AKS pomocí Azure CLI. Ukázková aplikace ASP.NET v kontejneru Windows serveru se také nasadit do clusteru.

Tato funkce je aktuálně ve verzi Preview.

![Obrázek přechodu na ukázkovou aplikaci ASP.NET](media/windows-container/asp-net-sample-app.png)

Tento článek předpokládá základní znalost konceptů Kubernetes. Další informace najdete v tématu [Kubernetes pro Azure Kubernetes Service (AKS) základní koncepty][kubernetes-concepts].

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, tento článek vyžaduje použití Azure CLI verze 2.0.61 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="before-you-begin"></a>Než začnete

Po vytvoření clusteru, který může spouštět kontejnery Windows serveru, je nutné přidat fond dalšího uzlu. Přidání fondu dalšího uzlu je popsáno v pozdějším kroku, ale je nejprve potřeba povolit několik funkcí ve verzi preview.

> [!IMPORTANT]
> Funkce AKS ve verzi preview jsou samoobslužných služeb, vyjádřit výslovný souhlas. Jsou poskytovány shromažďovat zpětnou vazbu a chyb z naší komunitě. Ve verzi preview nejsou tyto funkce určené k použití v produkčním prostředí. Funkce ve verzi public preview spadají pod "co možná nejlepší" podporu. Pomoc od týmů AKS technická podpora je k dispozici během pracovní doby tichomořské časové pásmo (PST) pouze. Další informace najdete v tématu následující články o podpoře:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření aks ve verzi preview rozhraní příkazového řádku
    
Jsou k dispozici v příkazů rozhraní příkazového řádku můžete vytvářet a spravovat více fondy uzlů *aks ve verzi preview* rozšíření rozhraní příkazového řádku. Nainstalujte *aks ve verzi preview* pomocí rozšíření Azure CLI [přidat rozšíření az][az-extension-add] příkaz, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Pokud jste dříve nainstalovali *aks ve verzi preview* rozšíření, nainstalujte všechny dostupné aktualizace pomocí `az extension update --name aks-preview` příkazu.

### <a name="register-windows-preview-feature"></a>Funkce ve verzi preview registru Windows

K vytvoření clusteru AKS, můžete použít více fondy uzlů a spouštění kontejnerů Windows serveru, nejprve povolit *WindowsPreview* funkcí příznaky v rámci předplatného. *WindowsPreview* funkce také používá clustery s několika uzly fondu a škálovací sady pro správu nasazení a konfigurace uzlů Kubernetes virtuálních počítačů. Zaregistrovat *WindowsPreview* pomocí příznak funkce [az funkce register][az-feature-register] příkaz, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Libovolný cluster AKS, vytvoříte po úspěšném zaregistrování *WindowsPreview* tento cluster prostředí ve verzi preview použít příznak funkce. Pokračujte k vytvoření clusterů pravidelných a plně podporované nepovolí funkce ve verzi preview na předplatná pro produkční prostředí. Používejte samostatný testovací nebo vývojové předplatné Azure pro testování funkcí ve verzi preview.

Trvá několik minut, než registraci dokončit. Podívejte se na stav registrace pomocí [seznam funkcí az][az-feature-list] příkaz:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Pokud je stav registrace `Registered`, stiskněte kombinaci kláves Ctrl-C se zastavit monitorování stavu.  Potom aktualizujte registraci *Microsoft.ContainerService* poskytovatele prostředků pomocí [az provider register][az-provider-register] příkaz:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Omezení

Při vytváření a správě AKS clustery, které podporují více fondy uzlů se vztahují následující omezení:

* Jsou k dispozici v případě clusterů vytvořených po úspěšném zaregistrování více fondy uzlů *WindowsPreview*. Více fondy uzlů jsou také k dispozici, když si zaregistrujete *MultiAgentpoolPreview* a *VMSSPreview* funkce pro vaše předplatné. Nelze přidat nebo spravovat fondy uzlů s existující cluster AKS vytvořili předtím, než tyto funkce byly úspěšně registrovány.
* Nelze odstranit první fond uzlů.

Tato funkce je ve verzi preview, platí následující další omezení:

* AKS cluster může mít maximálně osm fondy uzlů.
* AKS cluster můžou mít maximálně 400 uzlů ve fondech osmi uzlů.
* Název fondu uzel Windows Server má limit 6 znaků.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure. Při vytváření skupiny prostředků se zobrazí výzva k zadání umístění. Toto umístění je uložení metadat skupiny prostředků, je také kde prostředky běží v Azure, pokud nezadáte jiné oblasti při vytváření prostředku. Abyste vytvořili skupinu prostředků pomocí [vytvořit skupiny az][az-group-create] příkazu.

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

> [!NOTE]
> Tento článek používá syntaxi Bash pro příkazy v tomto kurzu.
> Pokud používáte Azure Cloud Shell, ujistěte se, že rozevíracího seznamu v levém horním rohu okna služby Cloud Shell je nastaven na **Bash**.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Následující příklad výstupu ukazuje úspěšně vytvořili skupinu prostředků:

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

Chcete-li spustit cluster AKS, který podporuje fondy uzlů pro kontejnery Windows serveru, musí používat síťové zásady, které používá váš cluster [Azure CNI][azure-cni-about] (advanced) network plugin. For more detailed information to help plan out the required subnet ranges and network considerations, see [configure Azure CNI networking][use-advanced-networking]. Použití [az aks vytvořit][az aks create] příkaz pro vytvoření clusteru AKS s názvem *myAKSCluster*. Tento příkaz vytvoří nezbytné síťovým prostředkům, pokud ještě neexistují.
  * Cluster je nakonfigurovaný s jedním uzlem
  * *Heslo správce systému windows* a *uživatelské jméno windows správce* parametry nastavit přihlašovací údaje správce pro všechny kontejnery Windows serveru na clusteru vytvořený.

Zadejte vlastní zabezpečené *PASSWORD_WIN* (mějte na paměti, že příkazy v tomto článku se zadávají do prostředí BASH):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.0 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

> [!Note]
> Pokud se zobrazí chyba ověření heslo, zkuste vytvořit skupinu prostředků v jiné oblasti.
> Pak zkuste vytvořit cluster s novou skupinu prostředků.

Po několika minutách se příkaz dokončí a vrátí hodnotu ve formátu JSON informace o clusteru.

## <a name="add-a-windows-server-node-pool"></a>Přidat fond uzlů Windows Server

Ve výchozím nastavení se vytvoří AKS cluster pomocí fond uzlů, který může spouštět kontejnery Linuxu. Použití `az aks nodepool add` příkaz pro přidání dalšího uzlu fondu, který může spouštět kontejnery Windows serveru.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.0
```

Výše uvedený příkaz vytvoří nový fond uzel s názvem *npwin* a přidá jej do *myAKSCluster*. Když vytváříte fond uzlů pro spouštění kontejnerů Windows serveru, výchozí hodnota pro *velikost virtuálního počítače uzlu* je *Standard_D2s_v3*. Pokud budete chtít nastavit *velikost virtuálního počítače uzlu* parametr, Zkontrolujte prosím seznam [velikosti virtuálních počítačů s omezením pomocí specifikátoru][restricted-vm-sizes]. Minimální doporučená velikost je *Standard_D2s_v3*. Výše uvedený příkaz také používá výchozí podsíť ve virtuální síti výchozí vytvořit při spuštění `az aks create`.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Ke správě clusteru Kubernetes použijete [kubectl][kubectl], klienta příkazového řádku Kubernetes. Pokud používáte Azure Cloud Shell, `kubectl` je již nainstalována. Chcete-li nainstalovat `kubectl` místně, použijte [az aks install-cli][az-aks-install-cli] příkaz:

```azurecli
az aks install-cli
```

Ke konfiguraci `kubectl` pro připojení k vašemu clusteru Kubernetes, použijte [az aks get-credentials][az-aks-get-credentials] příkazu. Tento příkaz stáhne přihlašovací údaje a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```azurecli-interactive
kubectl get nodes
```

Následující příklad výstupu ukazuje jeden uzel vytvořený v předchozích krocích. Ujistěte se, zda je stav uzlu *připravené*:

```
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.0
aksnpwin987654                      Ready    agent   108s   v1.14.0
```

## <a name="run-the-application"></a>Spuštění aplikace

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například jaké kontejneru obrázků ke spuštění. V tomto článku manifest slouží k vytvoření všech objektů potřebných ke spuštění ukázkové aplikace ASP.NET v kontejneru Windows serveru. Zahrnuje tento manifest [nasazení Kubernetes][kubernetes-deployment] for the ASP.NET sample application and an external [Kubernetes service][kubernetes-service] přístup k aplikaci z Internetu.

Ukázková aplikace ASP.NET je k dispozici jako součást [rozhraní .NET Framework – ukázky][dotnet-samples] a běží v kontejneru Windows serveru. AKS vyžaduje systém Windows Server kontejnery založený na obrázky *systému Windows Server 2019* nebo vyšší. Musíte také definovat soubor manifestu Kubernetes [uzlu selektoru][node-selector] říct clusteru AKS ke spuštění pod ASP.NET ukázkovou aplikaci prvku na uzlu, který může spouštět kontejnery Windows serveru.

Vytvořte soubor s názvem `sample.yaml` a zkopírujte do následující definice YAML. Pokud používáte Azure Cloud Shell, můžete tento soubor vytvořit pomocí `vi` nebo `nano` stejně jako kdybyste pracovali na virtuálním nebo fyzickém systému:

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
            memory: 800m
          requests:
            cpu: .1
            memory: 300m
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

Nasazení aplikace pomocí [použití kubectl][kubectl-apply] příkaz a zadejte název vašeho YAML manifestu:

```azurecli-interactive
kubectl apply -f sample.yaml
```

Následující příklad výstupu ukazuje nasazení a služby se úspěšně vytvořil:

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testování aplikace

Při spuštění aplikace, služba Kubernetes poskytuje front-endu aplikace k Internetu. Dokončení tohoto procesu může trvat několik minut.

Pomocí příkazu [kubectl get service][kubectl-get] s argumentem `--watch` můžete sledovat průběh.

```azurecli-interactive
kubectl get service sample --watch
```

Zpočátku *EXTERNAL-IP* pro *ukázka* služby se zobrazuje jako *čekající*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Když *EXTERNAL-IP* adresa se změní z *čekající* skutečné veřejnou IP adresu, použijte `CTRL-C` Zastavit `kubectl` sledujte proces. Následující příklad výstupu ukazuje platnou veřejnou IP adresu přiřazené příslušné službě:

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Pokud chcete zobrazit ukázkovou aplikaci v akci, otevřete webový prohlížeč na externí IP adresu vaší služby.

![Obrázek přechodu na ukázkovou aplikaci ASP.NET](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>Odstranění clusteru

Pokud už cluster nepotřebujete, použijte [odstranění skupiny az][az-group-delete] příkazu k odebrání skupiny prostředků, služby kontejneru a všech souvisejících prostředků.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Pokyny o tom, jak odebrat instanční objekt služby, najdete v článku [hlavní aspekty a odstranění služby AKS][sp-delete].

## <a name="next-steps"></a>Další postup

V tomto článku nasadili Kubernetes cluster a ukázkové aplikace ASP.NET v kontejneru Windows Server v něm nasazené. [Přístup k řídicímu panelu Kubernetes webové][kubernetes-dashboard] pro cluster, který jste právě vytvořili.

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

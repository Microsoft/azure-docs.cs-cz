---
title: Rychlý Start – vytvoření clusteru služby Azure Kubernetes (AKS)
description: Naučte se rychle vytvořit cluster Kubernetes pomocí šablony Azure Resource Manager a nasadit aplikaci ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: 56bacf1ae68081d5822fdb0e80762926d4eb581c
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173689"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>Rychlý Start: nasazení clusteru služby Azure Kubernetes (AKS) pomocí šablony ARM

Služba Azure Kubernetes Service (AKS) je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. V tomto rychlém startu nasadíte cluster AKS pomocí šablony Azure Resource Manager (šablona ARM). Aplikace s více kontejnery, která zahrnuje webový front-end a instanci Redis, se spouští v clusteru.

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Tento rychlý start předpokládá základní znalosti konceptů Kubernetes. Další informace najdete v tématu [základní koncepty Kubernetes pro Azure Kubernetes Service (AKS)][kubernetes-concepts].

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.61 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

- Pokud chcete vytvořit cluster AKS pomocí šablony Správce prostředků, poskytnete veřejný klíč SSH a Azure Active Directory instančního objektu. Alternativně můžete pro oprávnění použít [spravovanou identitu](use-managed-identity.md) místo instančního objektu. Pokud potřebujete některý z těchto prostředků, přečtěte si následující část. v opačném případě přejděte k části [Kontrola šablony](#review-the-template) .

### <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH

Chcete-li získat přístup k uzlům AKS, připojte se pomocí páru klíčů SSH. Pomocí `ssh-keygen` příkazu vygenerujte soubory veřejného a privátního klíče SSH. Ve výchozím nastavení se tyto soubory vytvoří v adresáři *~/.ssh* . Pokud v daném umístění existuje pár klíčů SSH se stejným názvem, tyto soubory se přepíší.

[https://shell.azure.com](https://shell.azure.com)V prohlížeči otevřete Cloud Shell.

Následující příkaz vytvoří pár klíčů SSH pomocí šifrování RSA a bitovou délku 2048:

```console
ssh-keygen -t rsa -b 2048
```

Další informace o vytváření klíčů SSH najdete v tématu [vytváření a Správa klíčů ssh pro ověřování v Azure][ssh-keys].

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Aby mohl cluster AKS pracovat a komunikovat s jinými prostředky Azure, používá se instanční objekt služby Azure Active Directory. Vytvořte instanční objekt pomocí příkazu [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. Parametr `--skip-assignment` nastavuje omezení, aby už nešla přidělovat žádná další oprávnění. Ve výchozím nastavení je tento instanční objekt platný po dobu jednoho roku. Všimněte si, že místo instančního objektu můžete použít spravovanou identitu. Další informace najdete v tématu [použití spravovaných identit](use-managed-identity.md).

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Výstup se podobá následujícímu příkladu:

```json
{
  "appId": "8b1ede42-d407-46c2-a1bc-6b213b04295f",
  "displayName": "azure-cli-2019-04-19-21-42-11",
  "name": "http://azure-cli-2019-04-19-21-42-11",
  "password": "27e5ac58-81b0-46c1-bd87-85b4ef622682",
  "tenant": "73f978cf-87f2-41bf-92ab-2e7ce012db57"
}
```

Poznamenejte si *appId* a *password*. Tyto hodnoty se použijí v dalších krocích.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-aks/).

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json":::

Další ukázky AKS najdete na webu [šablony pro rychlý Start AKS][aks-quickstart-templates] .

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu.

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty.

    V tomto rychlém startu ponechte výchozí hodnoty pro *velikost disku operačního systému GB*, *počet agentů*, *Velikost virtuálního počítače agenta*, *typ operačního systému* a *verzi Kubernetes*. Zadejte vlastní hodnoty pro následující parametry šablony:

    * **Předplatné**: vyberte předplatné Azure.
    * **Skupina prostředků**: vyberte **vytvořit novou**. Zadejte jedinečný název pro skupinu prostředků, třeba *myResourceGroup*, a pak zvolte **OK**.
    * **Umístění**: vyberte umístění, například **východní USA**.
    * **Název clusteru**: Zadejte jedinečný název pro cluster AKS, jako je například *myAKSCluster*.
    * **Předpona DNS**: Zadejte jedinečnou předponu DNS pro váš cluster, například *myakscluster*.
    * **Uživatelské jméno správce systému Linux**: zadejte uživatelské jméno pro připojení pomocí protokolu SSH, například *azureuser*.
    * **Veřejný klíč SSH RSA**: Zkopírujte a vložte *veřejnou* část páru klíčů ssh (ve výchozím nastavení obsah *~/.ssh/id_rsa. pub*).
    * **ID klienta instančního objektu**: Zkopírujte a vložte *appId* objektu služby z `az ad sp create-for-rbac` příkazu.
    * **Tajný kód klienta instančního objektu**: Zkopírujte a vložte z příkazu *heslo* k instančnímu objektu `az ad sp create-for-rbac` .
    * **Souhlasím s výše uvedenými podmínkami a ujednáními**: zaškrtněte toto políčko, aby bylo možné souhlasit.

    ![Správce prostředků šablonu pro vytvoření clusteru služby Azure Kubernetes na portálu](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Vyberte **Koupit**.

Vytvoření clusteru AKS bude trvat několik minut. Než přejdete k dalšímu kroku, počkejte, než se cluster úspěšně nasadí.

## <a name="validate-the-deployment"></a>Ověření nasazení

### <a name="connect-to-the-cluster"></a>Připojení ke clusteru

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

Následující příklad výstupu ukazuje uzly vytvořené v předchozích krocích. Ujistěte se, že stav všech uzlů je *připravený*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6
aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
```

### <a name="run-the-application"></a>Spuštění aplikace

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například jaké image kontejnerů se mají spustit. V tomto rychlém startu manifest slouží k vytvoření všech objektů potřebných ke spuštění aplikace Azure Vote. Tento manifest obsahuje dvě [Kubernetes nasazení][kubernetes-deployment] – jeden pro ukázkové aplikace v Pythonu pro Azure a druhý pro instanci Redis. Vytvoří se také dvě [služby Kubernetes Services][kubernetes-service] – interní služba pro instanci Redis a externí služba pro přístup k aplikaci hlasování Azure z Internetu.

Vytvořte soubor s názvem `azure-vote.yaml` a zkopírujte ho do následující definice YAML. Pokud Azure Cloud Shell použijete, můžete tento soubor vytvořit pomocí `vi` nebo `nano` jako při práci na virtuálním nebo fyzickém systému:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
        env:
        - name: ALLOW_EMPTY_PASSWORD
          value: "yes"
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Nasaďte aplikaci pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f azure-vote.yaml
```

Následující příklad výstupu ukazuje, že se nasazení a služby úspěšně vytvořily:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

### <a name="test-the-application"></a>Testování aplikace

Když je aplikace spuštěná, služba Kubernetes zpřístupňuje front-end aplikace na internetu. Dokončení tohoto procesu může trvat několik minut.

Pomocí příkazu [kubectl get service][kubectl-get] s argumentem `--watch` můžete sledovat průběh.

```console
kubectl get service azure-vote-front --watch
```

Zpočátku je *externí IP adresa* pro službu *Azure-hlas-front-end* zobrazená jako *nevyřízená*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Pokud se *IP* adresa změní z *čekání* na skutečnou veřejnou IP adresu, použijte `CTRL-C` k zastavení `kubectl` procesu sledování. Následující příklad výstupu ukazuje platnou veřejnou IP adresu přiřazenou ke službě:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Pokud chcete zobrazit hlasovou aplikaci Azure v akci, otevřete webový prohlížeč na externí IP adresu vaší služby.

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už cluster nepotřebujete, použijte k odebrání skupiny prostředků, služby kontejneru a všech souvisejících prostředků příkaz [az group delete][az-group-delete].

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete]. Pokud jste použili spravovanou identitu, tato identita je spravovaná platformou a nevyžaduje odebrání.

## <a name="get-the-code"></a>Získání kódu

V tomto rychlém startu se k vytvoření nasazení Kubernetes použily předem vytvořené image kontejneru. Související kód aplikace, soubor Dockerfile a soubor manifestu Kubernetes jsou k dispozici na GitHubu.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili cluster Kubernetes a do něj jste nasadili vícekontejnerovou aplikaci. [Přístup k webovému řídicímu panelu Kubernetes][kubernetes-dashboard] pro vytvořený cluster.

Další informace o službě AKS a podrobné vysvětlení kompletního příkladu od kódu až po nasazení najdete v kurzu clusteru Kubernetes.

> [!div class="nextstepaction"]
> [Kurz AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac

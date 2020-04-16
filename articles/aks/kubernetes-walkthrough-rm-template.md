---
title: Úvodní příručka – vytvoření clusteru služby Azure Kubernetes Service (AKS)
description: Zjistěte, jak rychle vytvořit cluster Kubernetes pomocí šablony Azure Resource Manager a nasadit aplikaci ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: quickstart
ms.date: 04/19/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: c5ea54a33ee027de0b11c59c53085b9d20ca6a3a
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392832"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-azure-resource-manager-template"></a>Úvodní příručka: Nasazení clusteru služby Azure Kubernetes Service (AKS) pomocí šablony Azure Resource Manager

Azure Kubernetes Service (AKS) je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. V tomto rychlém startu nasadíte cluster AKS pomocí šablony Azure Resource Manager. V clusteru je spuštěna aplikace s více kontejnery, která obsahuje webový front-end a instanci Redis.

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Tento rychlý start předpokládá základní znalosti konceptů Kubernetes. Další informace najdete v tématu [Kubernetes základní koncepty pro službu Azure Kubernetes Service (AKS)][kubernetes-concepts].

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat příkaz cli místně, tento rychlý start vyžaduje, abyste spustili Azure CLI verze 2.0.61 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="prerequisites"></a>Požadavky

Chcete-li vytvořit cluster AKS pomocí šablony Správce prostředků, zadejte veřejný klíč SSH a instanční objekt služby Azure Active Directory.  Případně můžete použít [spravovanou identitu](use-managed-identity.md) namísto instančního objektu pro oprávnění. Pokud potřebujete některý z těchto zdrojů, naleznete v následující části; jinak přejděte k části [Vytvořit cluster AKS.](#create-an-aks-cluster)

### <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH

Chcete-li získat přístup k uzlům AKS, připojte se pomocí dvojice klíčů SSH. Pomocí `ssh-keygen` příkazu vygenerujte soubory s veřejným a soukromým klíčem SSH. Ve výchozím nastavení jsou tyto soubory vytvořeny v adresáři *~/.ssh.* Pokud v daném umístění existuje dvojice klíčů SSH se stejným názvem, jsou tyto soubory přepsány.

Přejděte [https://shell.azure.com](https://shell.azure.com) na otevření cloudového prostředí ve vašem prohlížeči.

Následující příkaz vytvoří dvojici klíčů SSH pomocí šifrování RSA a bitovou délku 2048:

```console
ssh-keygen -t rsa -b 2048
```

Další informace o vytváření klíčů SSH najdete v [tématu Vytvoření a správa klíčů SSH pro ověřování v Azure][ssh-keys].

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Aby mohl cluster AKS pracovat a komunikovat s jinými prostředky Azure, používá se instanční objekt služby Azure Active Directory. Vytvořte instanční objekt pomocí příkazu [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. Parametr `--skip-assignment` nastavuje omezení, aby už nešla přidělovat žádná další oprávnění. Ve výchozím nastavení je tento instanční objekt platný po dobu jednoho roku. Všimněte si, že můžete použít spravovanou identitu namísto instančního objektu. Další informace naleznete v tématu [Použití spravovaných identit](use-managed-identity.md).

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

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://azure.microsoft.com/resources/templates/101-aks/).

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json" range="1-126" highlight="86-118":::

Další ukázky AKS najdete na webu [šablon rychlých startů AKS.][aks-quickstart-templates]

### <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu.

    [![Nasazení do Azure](./media/kubernetes-walkthrough-rm-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty.

    Pro tento rychlý start ponechte výchozí hodnoty pro *velikost disku operačního systému GB*, Počet *agentů*, *Velikost virtuálního počítače agenta*, *Typ operačního systému*a *Verze Kubernetes*. Zadejte vlastní hodnoty pro následující parametry šablony:

    * **Předplatné**: Vyberte předplatné Azure.
    * **Skupina prostředků**: Vyberte **vytvořit nový**. Zadejte jedinečný název skupiny prostředků, například *myResourceGroup*, a pak zvolte **OK**.
    * **Umístění**: Vyberte umístění, například **východní USA**.
    * **Název clusteru**: Zadejte jedinečný název clusteru AKS, například *myAKSCluster*.
    * **Předpona DNS**: Zadejte jedinečnou předponu DNS pro cluster, například *myakscluster*.
    * **Uživatelské jméno správce Linuxu**: Zadejte uživatelské jméno pro připojení pomocí SSH, například *azureuser*.
    * **SSH RSA Public Key**: Zkopírujte a vložte *veřejnou* část dvojice klíčů SSH (ve výchozím nastavení obsah *~/.ssh/id_rsa.pub).*
    * **Id klienta instančního objektu služby**: `az ad sp create-for-rbac` Zkopírujte a vložte *id aplikace* instančního objektu z příkazu.
    * **Tajný klíč klienta hlavního objektu služby** `az ad sp create-for-rbac` : Zkopírujte a vložte *heslo* instančního objektu služby z příkazu.
    * **Souhlasím s výše uvedeným zněním podmínek**: Zaškrtnutím tohoto políčka souhlasíte.

    ![Šablona Správce prostředků pro vytvoření clusteru služby Azure Kubernetes na portálu](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Vyberte **Koupit**.

Vytvoření clusteru AKS trvá několik minut. Než přejdete k dalšímu kroku, počkejte na úspěšné nasazení clusteru.

## <a name="validate-the-deployment"></a>Ověření nasazení

### <a name="connect-to-the-cluster"></a>Připojení ke clusteru

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

Následující příklad výstup uzly vytvořené v předchozích krocích. Ujistěte se, že stav všech uzlů je *připraven*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6
aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
```

### <a name="run-the-application"></a>Spuštění aplikace

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například jaké image kontejneru mají být spuštěny. V tomto rychlém startu manifest slouží k vytvoření všech objektů potřebných ke spuštění aplikace Azure Vote. Tento manifest zahrnuje dvě [nasazení Kubernetes][kubernetes-deployment] – jedno pro ukázkové aplikace Azure Vote Python a druhé pro instanci Redis. Jsou také vytvořeny dvě [služby Kubernetes][kubernetes-service] – interní služba pro instanci Redis a externí služba pro přístup k aplikaci Azure Vote z internetu.

> [!TIP]
> V tomto rychlém startu ručně vytvoříte manifest aplikace a nasadíte ho do clusteru AKS. V reálnějších situacích můžete k rychlé iteraci a ladění kódu přímo v clusteru AKS použít [Azure Dev Spaces][azure-dev-spaces]. Dev Spaces můžete používat na různých platformách operačních systémů a v různých vývojových prostředích a spolupracovat s ostatními členy vašeho týmu.

Vytvořte soubor `azure-vote.yaml` s názvem a zkopírujte v následující definici YAML. Pokud používáte Azure Cloud Shell, tento `vi` soubor `nano` lze vytvořit pomocí nebo jako by pracovat na virtuální nebo fyzický systém:

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
        image: redis
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
        image: microsoft/azure-vote-front:v1
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

Nasazení aplikace pomocí příkazu [kubectl apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f azure-vote.yaml
```

Následující ukázkový výstup ukazuje úspěšně vytvořené nasazení a služby:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

### <a name="test-the-application"></a>Testování aplikace

Při spuštění aplikace služba Kubernetes zpřístupňuje front-end aplikace k internetu. Dokončení tohoto procesu může trvat několik minut.

Pomocí příkazu [kubectl get service][kubectl-get] s argumentem `--watch` můžete sledovat průběh.

```console
kubectl get service azure-vote-front --watch
```

Zpočátku *EXTERNAL-IP* pro *azure-hlasování front* služby se zobrazí jako *čekající*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Když se adresa *EXTERNAL-IP* změní z *čekající* na `CTRL-C` skutečnou `kubectl` veřejnou IP adresu, použijte k zastavení procesu sledování. Následující ukázkový výstup ukazuje platnou veřejnou IP adresu přiřazenou službě:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Chcete-li zobrazit aplikaci Azure Vote v akci, otevřete webový prohlížeč na externí IP adresu vaší služby.

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už cluster nepotřebujete, použijte k odebrání skupiny prostředků, služby kontejneru a všech souvisejících prostředků příkaz [az group delete][az-group-delete].

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete]. Pokud jste použili spravovanou identitu, identita je spravována platformou a nevyžaduje odebrání.

## <a name="get-the-code"></a>Získání kódu

V tomto rychlém startu byly k vytvoření nasazení Kubernetes použity předem vytvořené image kontejneru. Související kód aplikace, soubor Dockerfile a soubor manifestu Kubernetes jsou k dispozici na GitHubu.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili cluster Kubernetes a do něj jste nasadili vícekontejnerovou aplikaci. [Přístup k webovému řídicímu panelu Kubernetes][kubernetes-dashboard] pro cluster, který jste vytvořili.

Další informace o službě AKS a podrobné vysvětlení kompletního příkladu od kódu až po nasazení najdete v kurzu clusteru Kubernetes.

> [!div class="nextstepaction"]
> [Kurz AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
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

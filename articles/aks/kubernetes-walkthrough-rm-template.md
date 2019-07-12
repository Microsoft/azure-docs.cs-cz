---
title: Rychlý start – vytvoření clusteru Azure Kubernetes Service (AKS)
description: Zjistěte, jak rychle vytvořit cluster Kubernetes pomocí šablony Azure Resource Manageru a nasazení aplikace ve službě Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: quickstart
ms.date: 04/19/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: e7cc9b63768385e4665e330b2b02a884b84c2188
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615386"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-azure-resource-manager-template"></a>Rychlý start: Nasaďte cluster Azure Kubernetes Service (AKS) pomocí šablony Azure Resource Manageru

Azure Kubernetes Service (AKS) je spravovaná služba, která vám umožní rychle nasadit a spravovat clustery Kubernetes. V tomto rychlém startu nasadíte cluster AKS pomocí šablony Azure Resource Manageru. V clusteru je spustí vícekontejnerová aplikace, která obsahuje webový front-end a Redis instance.

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

Tento rychlý start předpokládá základní znalosti konceptů Kubernetes. Další informace najdete v tématu [Kubernetes pro Azure Kubernetes Service (AKS) základní koncepty][kubernetes-concepts].

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, tento rychlý start vyžaduje použití Azure CLI verze 2.0.61 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="prerequisites"></a>Požadavky

K vytvoření clusteru AKS pomocí šablony Resource Manageru, zadejte veřejný klíč SSH a instanční objekt Azure Active Directory. Pokud některý z těchto prostředků potřebujete, najdete v následující části; v opačném případě pokračujte [vytvoření clusteru AKS](#create-an-aks-cluster) oddílu.

### <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH

Pro přístup k uzlů AKS, připojíte pomocí páru klíčů SSH. Použití `ssh-keygen` příkazu vygenerujte soubory veřejného a privátního klíče SSH. Ve výchozím nastavení, tyto soubory jsou vytvořeny v *~/.ssh* adresáře. Pokud v daném umístění existuje pár klíčů SSH se stejným názvem, tyto soubory jsou přepsány.

Následující příkaz vytvoří pár klíčů SSH pomocí šifrování RSA a bitovou délku 2048:

```azurecli-interactive
ssh-keygen -t rsa -b 2048
```

Další informace o vytváření klíčů SSH najdete v tématu [vytvořit a spravovat klíče SSH pro ověřování v Azure][ssh-keys].

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Aby mohl cluster AKS pracovat a komunikovat s jinými prostředky Azure, používá se instanční objekt služby Azure Active Directory. Vytvořte instanční objekt pomocí příkazu [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. Parametr `--skip-assignment` nastavuje omezení, aby už nešla přidělovat žádná další oprávnění. Ve výchozím nastavení tento instanční objekt je platný jeden rok.

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

Šablona použitá v tomto rychlém startu je [Nasaďte cluster Azure Kubernetes Service](https://azure.microsoft.com/resources/templates/101-aks/). Další ukázky AKS najdete v tématu [šablony pro rychlý start AKS][aks-quickstart-templates] lokality.

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu.

    [![Nasazení do Azure](./media/kubernetes-walkthrough-rm-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty.  

    Pro účely tohoto rychlého startu ponechte výchozí hodnoty pro *velikost disku operačního systému v GB*, *počet agentů*, *velikost virtuálního počítače agenta*, *typ operačního systému*a *Verze Kubernetes*. Zadejte vlastní hodnoty následujících parametrů šablony:

    * **Předplatné**: Vyberte předplatné služby Azure.
    * **Skupina prostředků**: Vyberte, že chcete **vytvořit novou** IP adresu. Zadejte jedinečný název pro skupinu prostředků, jako například *myResourceGroup*, klikněte na tlačítko **OK**.
    * **Umístění**: Vyberte umístění, jako je například **USA – východ**.
    * **Název clusteru**: Zadejte jedinečný název pro AKS cluster, jako například *myAKSCluster*.
    * **Předpona DNS**: Zadejte jedinečnou předponu DNS pro váš cluster, jako například *myakscluster*.
    * **Uživatelské jméno správce Linuxu**: Zadejte uživatelské jméno pro připojení pomocí protokolu SSH, jako například *azureuser*.
    * **SSH veřejný klíč RSA**: Zkopírujte a vložte *veřejné* součástí páru klíčů SSH (ve výchozím nastavení, obsah *~/.ssh/id_rsa.pub*).
    * **Id klienta instančního objektu**: Zkopírujte a vložte *appId* z instančního objektu služby `az ad sp create-for-rbac` příkazu.
    * **Tajný kód klienta instančního objektu**: Zkopírujte a vložte *heslo* z instančního objektu služby `az ad sp create-for-rbac` příkazu.
    * **Souhlasím s podmínkami a ujednáními stavu výše**: Zaškrtnutím tohoto políčka souhlas.

    ![Šablony Resource Manageru k vytvoření clusteru Azure Kubernetes Service na portálu](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Vyberte **Koupit**.

Trvá několik minut pro vytvoření clusteru AKS. Počkejte klastru, aby byl úspěšně nasazen, než budete pokračovat k dalšímu kroku.

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

Následující příklad výstupu ukazuje uzly vytvořené v předchozích krocích. Ujistěte se, zda je stav pro všechny uzly *připravené*:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6
aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
```

## <a name="run-the-application"></a>Spuštění aplikace

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například jaké kontejneru obrázků ke spuštění. V tomto rychlém startu manifest slouží k vytvoření všech objektů potřebných ke spuštění aplikace Azure Vote. Tento manifest zahrnuje dva [nasazení Kubernetes][kubernetes-deployment] - one for the sample Azure Vote Python applications, and the other for a Redis instance. Two [Kubernetes Services][kubernetes-service] také vytvářejí – interní služba pro instanci Redis a externí služby pro přístup k aplikaci Azure Vote z Internetu.

> [!TIP]
> V tomto rychlém startu ručně vytvoříte manifest aplikace a nasadíte ho do clusteru AKS. Ve více scénářích reálného světa, můžete použít [Azure Dev prostory][azure-dev-spaces] rychle iterovat a ladění kódu přímo v clusteru AKS. Dev Spaces můžete používat na různých platformách operačních systémů a v různých vývojových prostředích a spolupracovat s ostatními členy vašeho týmu.

Vytvořte soubor s názvem `azure-vote.yaml` a zkopírujte do následující definice YAML. Pokud používáte Azure Cloud Shell, můžete tento soubor vytvořit pomocí `vi` nebo `nano` stejně jako kdybyste pracovali na virtuálním nebo fyzickém systému:

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

Nasazení aplikace pomocí [použití kubectl][kubectl-apply] příkaz a zadejte název vašeho YAML manifestu:

```azurecli-interactive
kubectl apply -f azure-vote.yaml
```

Následující příklad výstupu ukazuje nasazení a služby se úspěšně vytvořil:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testování aplikace

Při spuštění aplikace, služba Kubernetes poskytuje front-endu aplikace k Internetu. Dokončení tohoto procesu může trvat několik minut.

Pomocí příkazu [kubectl get service][kubectl-get] s argumentem `--watch` můžete sledovat průběh.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Zpočátku *EXTERNAL-IP* pro *azure-vote-front* služby se zobrazuje jako *čekající*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Když *EXTERNAL-IP* adresa se změní z *čekající* skutečné veřejnou IP adresu, použijte `CTRL-C` Zastavit `kubectl` sledujte proces. Následující příklad výstupu ukazuje platnou veřejnou IP adresu přiřazené příslušné službě:

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Pokud chcete zobrazit aplikaci Azure Vote v akci, otevřete webový prohlížeč na externí IP adresu vaší služby.

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Odstranění clusteru

Pokud už cluster nepotřebujete, použijte [odstranění skupiny az][az-group-delete] příkazu k odebrání skupiny prostředků, služby kontejneru a všech souvisejících prostředků.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Pokyny o tom, jak odebrat instanční objekt služby, najdete v článku [hlavní aspekty a odstranění služby AKS][sp-delete].

## <a name="get-the-code"></a>Získání kódu

V tomto rychlém startu předem vytvořené Image kontejnerů byly použity k vytvoření nasazení Kubernetes. Související kód aplikace, soubor Dockerfile a soubor manifestu Kubernetes jsou k dispozici na GitHubu.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nasadili cluster Kubernetes a do něj jste nasadili vícekontejnerovou aplikaci. [Přístup k řídicímu panelu Kubernetes webové][kubernetes-dashboard] pro cluster, který jste vytvořili.

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

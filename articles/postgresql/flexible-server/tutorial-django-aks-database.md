---
title: 'Kurz: nasazení Django v clusteru AKS s PostgreSQL flexibilním serverem pomocí Azure CLI'
description: Naučte se rychle sestavovat a nasazovat Django v AKS pomocí Azure Database for PostgreSQL-flexibilního serveru.
ms.service: postgresql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 12/10/2020
ms.custom: mvc
ms.openlocfilehash: 71066fc2e2f87405455a059fe23c20277c4b09fb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726375"
---
# <a name="tutorial-deploy-django-app-on-aks-with-azure-database-for-postgresql---flexible-server"></a>Kurz: nasazení aplikace Django v AKS s Azure Database for PostgreSQLm flexibilním serverem

V tomto rychlém startu nasadíte aplikaci Django na cluster Azure Kubernetes Service (AKS) s Azure Database for PostgreSQLm flexibilním serverem (ve verzi Preview) pomocí Azure CLI.

**[AKS](../../aks/intro-kubernetes.md)** je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. **[Azure Database for PostgreSQL-flexibilní Server (Preview)](overview.md)** je plně spravovaná databázová služba navržená tak, aby poskytovala přesnější kontrolu a flexibilitu nad funkcemi správy databáze a nastaveními konfigurace.

> [!NOTE]
> - Azure Database for PostgreSQL flexibilní Server je momentálně ve verzi Public Preview.
> - V tomto rychlém startu se předpokládá základní znalost konceptů Kubernetes, Django a PostgreSQL.

## <a name="pre-requisites"></a>Požadavky
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

- Využijte [Azure Cloud Shell](../../cloud-shell/quickstart.md) s využitím prostředí Bash.

   [![Vložené spuštění](https://shell.azure.com/images/launchcloudshell.png "Spuštění služby Azure Cloud Shell")](https://shell.azure.com)  
- Pokud dáváte přednost, [nainstalujte](/cli/azure/install-azure-cli) rozhraní příkazového řádku Azure CLI pro spuštění příkazů odkazů CLI.
  - Pokud používáte místní instalaci, přihlaste se s Azure CLI pomocí příkazu [az login](/cli/azure/reference-index#az-login).  Pokud chcete dokončit proces ověřování, postupujte podle kroků zobrazených na terminálu.  Další možnosti přihlášení jsou popsané v tématu [Přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli).
  - Po zobrazení výzvy nainstalujte rozšíření Azure CLI při prvním použití.  Další informace o rozšířeních najdete v tématu [Využití rozšíření v Azure CLI](/cli/azure/azure-cli-extensions-overview).
  - Spuštěním příkazu [az version](/cli/azure/reference-index?#az_version) zjistěte verzi a závislé knihovny, které jsou nainstalované. Pokud chcete upgradovat na nejnovější verzi, spusťte [az upgrade](/cli/azure/reference-index?#az_upgrade). Tento článek vyžaduje nejnovější verzi rozhraní příkazového řádku Azure CLI. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

> [!NOTE]
> Pokud se příkazy v tomto rychlém startu spouštějí místně (místo Azure Cloud Shell), ujistěte se, že jste příkazy spustili jako správce.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure. Pojďme vytvořit skupinu prostředků *Django-Project* pomocí příkazu [az Group Create] [az-Group-Create] v umístění *eastus* .

```azurecli-interactive
az group create --name django-project --location eastus
```

> [!NOTE]
> Umístění pro skupinu prostředků, kde se ukládají metadata skupiny prostředků. V případě, že se vaše prostředky spouští v Azure, neurčíte při vytváření prostředku jinou oblast.

Následující příklad výstupu ukazuje, že skupina prostředků byla úspěšně vytvořena:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/django-project",
  "location": "eastus",
  "managedBy": null,
  
  "name": "django-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Vytvoření clusteru AKS

Pomocí příkazu [az aks create](/cli/azure/aks#az-aks-create) vytvořte cluster AKS. Následující příklad vytvoří cluster *myAKSCluster* s jedním uzlem. Dokončení této akce bude trvat několik minut.

```azurecli-interactive
az aks create --resource-group django-project --name djangoappcluster --node-count 1 --generate-ssh-keys
```

Po několika minutách se příkaz dokončí a vrátí informace o clusteru ve formátu JSON.

> [!NOTE]
> Při vytváření clusteru AKS se automaticky vytvoří druhá skupina prostředků pro ukládání prostředků AKS. Podívejte [se, proč jsou dvě skupiny prostředků vytvořené pomocí AKS?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Ke správě clusteru Kubernetes použijete klienta příkazového řádku Kubernetes [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/). Pokud používáte Azure Cloud Shell, `kubectl` je již nainstalováno. Pokud chcete nainstalovat `kubectl` místně, použijte příkaz [AZ AKS Install-CLI](/cli/azure/aks#az-aks-install-cli) :

```azurecli-interactive
az aks install-cli
```

Pomocí příkazu [az aks get-credentials](/cli/azure/aks#az-aks-get-credentials) nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

```azurecli-interactive
az aks get-credentials --resource-group django-project --name djangoappcluster
```

> [!NOTE]
> Výše uvedený příkaz používá výchozí umístění [konfiguračního souboru Kubernetes](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), který je `~/.kube/config` . Můžete zadat jiné umístění konfiguračního souboru Kubernetes pomocí *--File*.

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get), který vrátí seznam uzlů clusteru.

```azurecli-interactive
kubectl get nodes
```

Následující příklad výstupu ukazuje jeden uzel vytvořený v předchozích krocích. Ujistěte se, že stav uzlu je *připravený*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>Vytvoření Azure Database for PostgreSQLho flexibilního serveru
Pomocí příkazu [AZ postgreSQL flexibilního serveru Create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create)vytvořte flexibilní Server. Následující příkaz vytvoří server pomocí výchozího nastavení služby a hodnot z místního kontextu rozhraní příkazového řádku Azure.

```azurecli-interactive
az postgres flexible-server create --public-access <YOUR-IP-ADDRESS>
```

Vytvořený server má následující atributy:
- ```postgres```Při prvním zřízení serveru se vytvoří nová prázdná databáze. V tomto rychlém startu budeme používat tuto databázi.
- Automaticky vygenerovaný název serveru, uživatelské jméno správce, heslo správce, název skupiny prostředků (Pokud už není zadané v místním kontextu) a ve stejném umístění jako vaše skupina prostředků
- Výchozí nastavení služby pro zbývající konfigurace serveru: výpočetní vrstva (Pro obecné účely), velikost služby COMPUTE/SKU (Standard_D2s_v3 která používá 2vCores), doba uchování zálohy (7 dní) a verze PostgreSQL (12)
- Použití argumentu Public-Access umožňuje vytvořit server s veřejným přístupem chráněným pravidly brány firewall. Poskytnutím IP adresy přidejte pravidlo brány firewall, které povolí přístup z klientského počítače.
- Vzhledem k tomu, že příkaz používá místní kontext, vytvoří server ve skupině prostředků ```django-project``` a v oblasti ```eastus``` .


## <a name="build-your-django-docker-image"></a>Sestavení image Docker Django

Vytvořte novou [aplikaci Django](https://docs.djangoproject.com/en/3.1/intro/) nebo použijte existující projekt Django. Ujistěte se, že je váš kód v této struktuře složek.

```
└───my-djangoapp
    └───views.py
    └───models.py
    └───forms.py
    ├───templates
          . . . . . . .
    ├───static
         . . . . . . .
└───my-django-project
    └───settings.py
    └───urls.py
    └───wsgi.py
        . . . . . . .
    └─── Dockerfile
    └─── requirements.txt
    └─── manage.py
    
```
Aktualizujte v nástroji a ujistěte se, že ```ALLOWED_HOSTS``` ```settings.py``` aplikace Django používá externí IP adresu, která je přiřazená aplikaci Kubernetes.

```python
ALLOWED_HOSTS = ['*']
```

Aktualizuje ```DATABASES={ }``` oddíl v ```settings.py```  souboru. Následující fragment kódu čte hostitele databáze, uživatelské jméno a heslo ze souboru manifestu Kubernetes.

```python
DATABASES={
   'default':{
      'ENGINE':'django.db.backends.postgresql_psycopg2',
      'NAME':os.getenv('DATABASE_NAME'),
      'USER':os.getenv('DATABASE_USER'),
      'PASSWORD':os.getenv('DATABASE_PASSWORD'),
      'HOST':os.getenv('DATABASE_HOST'),
      'PORT':'5432',
      'OPTIONS': {'sslmode': 'require'}
   }
}
```

### <a name="generate-a-requirementstxt-file"></a>Vygenerovat soubor requirements.txt
Vytvořte ```requirements.txt``` soubor pro výpis závislostí pro aplikaci Django. Tady je příklad ```requirements.txt``` souboru. Můžete použít [``` pip freeze > requirements.txt```](https://pip.pypa.io/en/stable/reference/pip_freeze/) k vygenerování requirements.txt souboru pro existující aplikaci.

``` text
Django==2.2.17
postgres==3.0.0
psycopg2-binary==2.8.6
psycopg2-pool==1.1
pytz==2020.4
```

### <a name="create-a-dockerfile"></a>Vytvoření souboru Dockerfile
Vytvořte nový soubor s názvem ```Dockerfile``` a zkopírujte fragment kódu níže. Tato souboru Dockerfile při nastavení Python 3,8 a instalaci všech požadavků uvedených v souboru requirements.txt.

```docker
# Use the official Python image from the Docker Hub
FROM python:3.8.2

# Make a new directory to put our code in.
RUN mkdir /code

# Change the working directory.
WORKDIR /code

# Copy to code folder
COPY . /code/

# Install the requirements.
RUN pip install -r requirements.txt

# Run the application:
CMD python manage.py runserver 0.0.0.0:8000
```

### <a name="build-your-image"></a>Sestavení image
Ujistěte se, že jste v adresáři ```my-django-app``` v terminálu, pomocí ```cd``` příkazu. Spusťte následující příkaz, který sestaví obrázek tabule:

``` bash

docker build --tag myblog:latest .

```

Nasaďte svoji image do [Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) nebo do služby [Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md).

> [!IMPORTANT]
>Pokud používáte Azure Container regdistry (ACR), pak spusťte ```az aks update``` příkaz pro připojení účtu ACR s clusterem AKS.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g django-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Vytvořit soubor manifestu Kubernetes

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například jaké image kontejnerů se mají spustit. Pojďme vytvořit soubor manifestu s názvem ```djangoapp.yaml``` a zkopírovat ho do následující definice YAML.

>[!IMPORTANT]
> - Nahraďte ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` skutečným názvem a označením image Docker Django, například ```docker-hub-user/myblog:latest``` .
> - ```env```Níže uvedený oddíl aktualizace vám ```SERVERNAME``` ```YOUR-DATABASE-USERNAME``` ```YOUR-DATABASE-PASSWORD``` Postgres flexibilního serveru.


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: django-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: django-app
  template:
    metadata:
      labels:
        app: django-app
    spec:
      containers:
      - name: django-app
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.postgres.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "postgres"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - django-app
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: python-svc
spec:
  type: LoadBalancer
  ports:
    - port: 8000
  selector:
    app: django-app
```

## <a name="deploy-django-to-aks-cluster"></a>Nasazení Django do clusteru AKS
Nasaďte aplikaci pomocí příkazu [kubectl Apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) a zadejte název manifestu YAML:

```console
kubectl apply -f djangoapp.yaml
```

Následující příklad výstupu ukazuje, že se nasazení a služby úspěšně vytvořily:

```output
deployment "django-app" created
service "python-svc" created
```

Nasazení ```django-app``` vám umožní popsání podrobností o vašem nasazení, jako například jaké obrázky použít pro aplikaci, počet lusků a v konfiguraci pod. Vytvoří se služba ```python-svc``` , která vystaví aplikaci prostřednictvím externí IP adresy.

## <a name="test-the-application"></a>Testování aplikace

Když je aplikace spuštěná, služba Kubernetes zpřístupňuje front-end aplikace na internetu. Dokončení tohoto procesu může trvat několik minut.

Pomocí příkazu [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) s argumentem `--watch` můžete sledovat průběh.

```azurecli-interactive
kubectl get service django-app --watch
```

Zpočátku je *externí IP adresa* pro *Django-App* Service zobrazená jako *čeká na vyřízení*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
django-app   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Pokud se *IP* adresa změní z *čekání* na skutečnou veřejnou IP adresu, použijte `CTRL-C` k zastavení `kubectl` procesu sledování. Následující příklad výstupu ukazuje platnou veřejnou IP adresu přiřazenou ke službě:

```output
django-app  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Teď otevřete webový prohlížeč na externí IP adresu vaší služby. zobrazí se aplikace Django.  

>[!NOTE]
> - V současné době web Django nepoužívá protokol HTTPS. Doporučuje se povolit protokol [TLS s vlastními certifikáty](../../aks/ingress-own-tls.md).
> - Můžete povolit [Směrování protokolu HTTP](../../aks/http-application-routing.md) pro svůj cluster. Když je zapnuté směrování protokolu HTTP, nakonfiguruje v clusteru AKS adaptér příchozího přenosu dat. Při nasazení aplikací řešení také vytvoří veřejně přístupné názvy DNS pro koncové body aplikací.

## <a name="run-database-migrations"></a>Spustit migrace databáze

V případě jakékoli aplikace Django byste museli spustit migraci databáze nebo shromáždit statické soubory. Tyto příkazy prostředí Django můžete spustit pomocí příkazu ```$ kubectl exec <pod-name> -- [COMMAND]``` .  Před spuštěním příkazu potřebujete najít název pod pomocí ```kubectl get pods``` . 

```bash
$ kubectl get pods
```

Zobrazí se výstup podobný tomuto.
```output
NAME                             READY   STATUS          RESTARTS   AGE
django-app-5d9cd6cd8-l6x4b     1/1     Running              0       2m
```

Po nalezení názvu pod můžete pomocí příkazu Spustit migrace databáze Django ```$ kubectl exec <pod-name> -- [COMMAND]``` . Poznámka ```/code/``` je pracovní adresář pro definování projektu ```Dockerfile``` výše.

```bash
$ kubectl exec django-app-5d9cd6cd8-l6x4b -- python /code/manage.py migrate
```

Výstup by vypadal jako 
```output 
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  . . . . . . 
```

Pokud narazíte na problémy, spusťte prosím, ```kubectl logs <pod-name>```  aby se zobrazila výjimka, kterou vaše aplikace vyvolala. Pokud aplikace funguje úspěšně, při spuštění se zobrazí výstup podobný tomuto ```kubectl logs``` .

```output
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
December 08, 2020 - 23:24:14
Django version 2.2.17, using settings 'django_postgres_app.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

## <a name="clean-up-the-resources"></a>Vyčištění prostředků

Abyste se vyhnuli poplatkům za Azure, měli byste vyčistit nepotřebné prostředky.  Pokud už cluster nepotřebujete, použijte k odebrání skupiny prostředků, služby kontejneru a všech souvisejících prostředků příkaz [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name django-project --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění](../../aks/kubernetes-service-principal.md#additional-considerations). Pokud jste použili spravovanou identitu, tato identita je spravovaná platformou a nevyžaduje odebrání.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [, jak získat přístup k webovému řídicímu panelu Kubernetes](../../aks/kubernetes-dashboard.md) pro váš cluster AKS.
- Informace o tom, jak [Povolit průběžné nasazování](../../aks/deployment-center-launcher.md)
- Přečtěte si, jak [škálovat cluster](../../aks/tutorial-kubernetes-scale.md) .
- Informace o tom, jak spravovat [Postgres flexibilní Server](./quickstart-create-server-cli.md)
- Naučte se [Konfigurovat parametry serveru](./howto-configure-server-parameters-using-cli.md) pro databázový server.

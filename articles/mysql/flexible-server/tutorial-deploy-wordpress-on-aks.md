---
title: 'Kurz: nasazení WordPressu v clusteru AKS se serverem MySQL Flexible pomocí Azure CLI'
description: Naučte se rychle sestavovat a nasazovat WordPress v AKS pomocí Azure Database for MySQLho flexibilního serveru.
ms.service: mysql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: mvc
ms.openlocfilehash: b82bb9b7684d1119b6b62216b52210845d8f4c67
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "98199627"
---
# <a name="tutorial-deploy-wordpress-app-on-aks-with-azure-database-for-mysql---flexible-server"></a>Kurz: nasazení aplikace WordPress v AKS s Azure Database for MySQLm flexibilním serverem

V tomto rychlém startu nasadíte aplikaci WordPress do clusteru Azure Kubernetes Service (AKS) s Azure Database for MySQLm flexibilním serverem (ve verzi Preview) pomocí Azure CLI. 
**[AKS](../../aks/intro-kubernetes.md)** je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. **[Azure Database for MySQL-flexibilní Server (Preview)](overview.md)** je plně spravovaná databázová služba navržená tak, aby poskytovala přesnější kontrolu a flexibilitu nad funkcemi správy databáze a nastaveními konfigurace. Aktuálně flexibilní Server je ve verzi Preview.

> [!NOTE]
> - Azure Database for MySQL flexibilní Server je momentálně ve verzi Public Preview.
> - V tomto rychlém startu se předpokládá základní znalost konceptů Kubernetes, WordPress a MySQL.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje nejnovější verzi rozhraní příkazového řádku Azure CLI. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

> [!NOTE]
> Pokud se příkazy v tomto rychlém startu spouštějí místně (místo Azure Cloud Shell), ujistěte se, že jste příkazy spustili jako správce.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure. Pojďme vytvořit skupinu prostředků, *WordPress-Project* pomocí příkazu [az Group Create] [az-Group-Create] v umístění *eastus* .

```azurecli-interactive
az group create --name wordpress-project --location eastus
```

> [!NOTE]
> Umístění pro skupinu prostředků, kde se ukládají metadata skupiny prostředků. V případě, že se vaše prostředky spouští v Azure, neurčíte při vytváření prostředku jinou oblast.

Následující příklad výstupu ukazuje, že skupina prostředků byla úspěšně vytvořena:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/wordpress-project",
  "location": "eastus",
  "managedBy": null,
  "name": "wordpress-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Vytvoření clusteru AKS

Pomocí příkazu [az aks create](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-create) vytvořte cluster AKS. Následující příklad vytvoří cluster *myAKSCluster* s jedním uzlem. Dokončení této akce bude trvat několik minut.

```azurecli-interactive
az aks create --resource-group wordpress-project --name myAKSCluster --node-count 1 --generate-ssh-keys
```

Po několika minutách se příkaz dokončí a vrátí informace o clusteru ve formátu JSON.

> [!NOTE]
> Při vytváření clusteru AKS se automaticky vytvoří druhá skupina prostředků pro ukládání prostředků AKS. Podívejte [se, proč jsou dvě skupiny prostředků vytvořené pomocí AKS?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Ke správě clusteru Kubernetes použijete klienta příkazového řádku Kubernetes [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/). Pokud používáte Azure Cloud Shell, `kubectl` je již nainstalováno. Pokud chcete nainstalovat `kubectl` místně, použijte příkaz [AZ AKS Install-CLI](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-install-cli) :

```azurecli-interactive
az aks install-cli
```

Pomocí příkazu [az aks get-credentials](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-get-credentials) nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

```azurecli-interactive
az aks get-credentials --resource-group wordpress-project --name myAKSCluster
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

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Vytvoření Azure Database for MySQLho flexibilního serveru
Pomocí příkazu [AZ MySQL Flexible-Server Create](/cli/azure/mysql/flexible-server?view=azure-cli-latest&preserve-view=true)vytvořte flexibilní Server. Následující příkaz vytvoří server pomocí výchozího nastavení služby a hodnot z místního kontextu rozhraní příkazového řádku Azure.

```azurecli-interactive
az mysql flexible-server create --public-access <YOUR-IP-ADDRESS>
```

Vytvořený server má následující atributy:
- ```flexibleserverdb```Při prvním zřízení serveru se vytvoří nová prázdná databáze. V tomto rychlém startu budeme používat tuto databázi.
- Automaticky vygenerovaný název serveru, uživatelské jméno správce, heslo správce, název skupiny prostředků (Pokud už není zadané v místním kontextu) a ve stejném umístění jako vaše skupina prostředků
- Výchozí nastavení služby pro zbývající konfigurace serveru: výpočetní vrstva (shluky), výpočetní velikost/SKU (B1MS), doba uchování zálohy (7 dní) a verze MySQL (5,7)
- Použití argumentu Public-Access umožňuje vytvořit server s veřejným přístupem chráněným pravidly brány firewall. Poskytnutím IP adresy přidejte pravidlo brány firewall, které povolí přístup z klientského počítače.
- Vzhledem k tomu, že příkaz používá místní kontext, vytvoří server ve skupině prostředků ```wordpress-project``` a v oblasti ```eastus``` .


### <a name="build-your-wordpress-docker-image"></a>Sestavení image Docker pro WordPress

Stáhněte si [nejnovější verzi WordPress](https://wordpress.org/download/) . Vytvoření nového adresáře ```my-wordpress-app``` pro projekt a použití této jednoduché struktury složek

```
└───my-wordpress-app
    └───public
        ├───wp-admin
        │   ├───css
        . . . . . . .
        ├───wp-content
        │   ├───plugins
        . . . . . . .
        └───wp-includes
        . . . . . . .
        ├───wp-config-sample.php
        ├───index.php
        . . . . . . .
    └─── Dockerfile

```


Přejmenujte ```wp-config-sample.php```  na ```wp-config.php``` a nahraďte řádky 21 a 32 tímto fragmentem kódu. Následující fragment kódu čte hostitele databáze, uživatelské jméno a heslo ze souboru manifestu Kubernetes.

```php
//Using environment variables for DB connection information

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */

$connectstr_dbhost = getenv('DATABASE_HOST');
$connectstr_dbusername = getenv('DATABASE_USERNAME');
$connectstr_dbpassword = getenv('DATABASE_PASSWORD');

/** MySQL database name */
define('DB_NAME', 'flexibleserverdb');

/** MySQL database username */
define('DB_USER', $connectstr_dbusername);

/** MySQL database password */
define('DB_PASSWORD',$connectstr_dbpassword);

/** MySQL hostname */
define('DB_HOST', $connectstr_dbhost);

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/** SSL*/
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
```

### <a name="create-a-dockerfile"></a>Vytvoření souboru Dockerfile
Vytvořte nový souboru Dockerfile a zkopírujte tento fragment kódu. Tato souboru Dockerfile při nastavování webového serveru Apache pomocí PHP a povolení rozšíření MySQL.

```docker
FROM php:7.2-apache
COPY public/ /var/www/html/
RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli
```

### <a name="build-your-docker-image"></a>Sestavení image Docker
Ujistěte se, že jste v adresáři ```my-wordpress-app``` v terminálu, pomocí ```cd``` příkazu. Spusťte následující příkaz, který sestaví bitovou kopii:

``` bash

docker build --tag myblog:latest .

```

Nasaďte svoji image do [Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) nebo do služby [Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md).

> [!IMPORTANT]
>Pokud používáte Azure Container regdistry (ACR), pak spusťte ```az aks update``` příkaz pro připojení účtu ACR s clusterem AKS.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g wordpress-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Vytvořit soubor manifestu Kubernetes

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například jaké image kontejnerů se mají spustit. Pojďme vytvořit soubor manifestu s názvem `mywordpress.yaml` a zkopírovat ho do následující definice YAML.

>[!IMPORTANT]
> - Nahraďte ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` skutečným názvem a značkou image Docker pro WordPress, například ```docker-hub-user/myblog:latest``` .
> - ```env```Níže uvedený oddíl aktualizace ```SERVERNAME``` získáte ```YOUR-DATABASE-USERNAME``` ```YOUR-DATABASE-PASSWORD``` flexibilním serverem MySQL.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-blog
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress-blog
  template:
    metadata:
      labels:
        app: wordpress-blog
    spec:
      containers:
      - name: wordpress-blog
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.mysql.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "flexibleserverdb"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - wordpress-blog
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: php-svc
spec:
  type: LoadBalancer
  ports:
    - port: 80
  selector:
    app: wordpress-blog
```

## <a name="deploy-wordpress-to-aks-cluster"></a>Nasazení WordPressu do clusteru AKS
Nasaďte aplikaci pomocí příkazu [kubectl Apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) a zadejte název manifestu YAML:

```console
kubectl apply -f mywordpress.yaml
```

Následující příklad výstupu ukazuje, že se nasazení a služby úspěšně vytvořily:

```output
deployment "wordpress-blog" created
service "php-svc" created
```

## <a name="test-the-application"></a>Testování aplikace

Když je aplikace spuštěná, služba Kubernetes zpřístupňuje front-end aplikace na internetu. Dokončení tohoto procesu může trvat několik minut.

Pomocí příkazu [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) s argumentem `--watch` můžete sledovat průběh.

```azurecli-interactive
kubectl get service wordpress-blog --watch
```

Zpočátku je *externí IP adresa* pro *WordPress-blog* Service zobrazená jako *čeká na vyřízení*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress-blog   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Pokud se *IP* adresa změní z *čekání* na skutečnou veřejnou IP adresu, použijte `CTRL-C` k zastavení `kubectl` procesu sledování. Následující příklad výstupu ukazuje platnou veřejnou IP adresu přiřazenou ke službě:

```output
wordpress-blog  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

### <a name="browse-wordpress"></a>Procházet WordPress

Otevřete webový prohlížeč na externí IP adresu vaší služby, abyste viděli stránku instalace WordPress.

   :::image type="content" source="./media/tutorial-deploy-wordpress-on-aks/wordpress-aks-installed-success.png" alt-text="Úspěšná instalace WordPressu na AKS a MySQL Flexible Server":::

>[!NOTE]
> - V současné době web WordPress nepoužívá protokol HTTPS. Doporučuje se povolit protokol [TLS s vlastními certifikáty](../../aks/ingress-own-tls.md).
> - Můžete povolit [Směrování protokolu HTTP](../../aks/http-application-routing.md) pro svůj cluster.

## <a name="clean-up-the-resources"></a>Vyčištění prostředků

Abyste se vyhnuli poplatkům za Azure, měli byste vyčistit nepotřebné prostředky.  Pokud už cluster nepotřebujete, použijte k odebrání skupiny prostředků, služby kontejneru a všech souvisejících prostředků příkaz [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete).

```azurecli-interactive
az group delete --name wordpress-project --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění](../../aks/kubernetes-service-principal.md#additional-considerations). Pokud jste použili spravovanou identitu, tato identita je spravovaná platformou a nevyžaduje odebrání.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [, jak získat přístup k webovému řídicímu panelu Kubernetes](../../aks/kubernetes-dashboard.md) pro váš cluster AKS.
- Přečtěte si, jak [škálovat cluster](../../aks/tutorial-kubernetes-scale.md) .
- Naučte se spravovat [flexibilní Server MySQL](./quickstart-create-server-cli.md) .
- Naučte se [Konfigurovat parametry serveru](./how-to-configure-server-parameters-cli.md) pro databázový server.


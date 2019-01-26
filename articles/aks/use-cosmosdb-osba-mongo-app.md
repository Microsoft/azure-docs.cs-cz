---
title: Integrovat stávající aplikaci MongoDB API služby Azure Cosmos DB pro MongoDB a zprostředkovatele Open Service Broker for Azure (OSBA)
description: V tomto článku se dozvíte, jak integrovat existující aplikace Java využívající databázi MongoDB API služby Azure Cosmos DB pro MongoDB pomocí zprostředkovatele Open Service Broker for Azure (OSBA).
services: azure-dev-spaces
author: zr-msft
manager: jeconnoc
ms.service: azure-dev-spaces
ms.topic: article
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, otevřete Service Broker, Open Service Broker for Azure
ms.openlocfilehash: 04b513de1d47749bb87b7aaf79839389ab4d7290
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082500"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Integrovat stávající aplikaci MongoDB API služby Azure Cosmos DB pro MongoDB a zprostředkovatele Open Service Broker for Azure (OSBA)

Azure Cosmos DB je globálně distribuovaná databázová služba pro více modelů. Také poskytuje kompatibilitu s protokolem při přenosu s několika rozhraní API NoSQL včetně pro MongoDB. Rozhraní API Cosmos DB pro MongoDB můžete pomocí služby Cosmos DB s vaší stávající aplikace MongoDB bez nutnosti změny ovladače databáze vaší aplikace nebo implementace. Můžete také vytvářet služby Cosmos DB pomocí zprostředkovatele Open Service Broker for Azure.

V tomto článku využijte existující aplikace v Javě, která používá databázi MongoDB a aktualizujte ji, aby používat databázi Cosmos DB pomocí zprostředkovatele Open Service Broker for Azure.

## <a name="prerequisites"></a>Požadavky

Abyste mohli pokračovat, musíte mít:
    
* Máte [cluster Azure Kubernetes Service](kubernetes-walkthrough.md) vytvořili.
* Mít [Open Service Broker for Azure nainstalovaná a nakonfigurovaná ve vašem clusteru AKS](integrate-azure.md). 
* Máte [Service Catalog CLI](https://svc-cat.io/docs/install/) nainstalovaná a nakonfigurovaná pro spuštění `svcat` příkazy.
* Máte existující [MongoDB](https://www.mongodb.com/) databáze. Můžete mít například MongoDB na vaše [vývojovém počítači](https://docs.mongodb.com/manual/administration/install-community/) nebo [virtuálního počítače Azure](../virtual-machines/linux/install-mongodb.md).
* Způsob připojení a dotazování databáze MongoDB, jako [prostředí mongo shell](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Získání kódu aplikace
    
V tomto článku budete používat [Hudba ukázkovou aplikaci spring z Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) k předvedení aplikaci, která používá databázi MongoDB.
    
Klonování aplikace z Githubu a přejděte do jeho adresář:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Příprava aplikace pro používání databáze MongoDB

Ukázkovou aplikaci spring Hudba poskytuje řadu možností pro zdroje dat. V tomto článku můžete nakonfigurovat k použít stávající databázi MongoDB. 

Přidejte následující YAML na konec *src/main/resources/application.yml*. Toto přidání vytvoří profil s názvem *mongodb* a nakonfiguruje název identifikátoru URI a databáze. Identifikátor URI nahraďte informace o připojení k existující databázi MongoDB. Přidání identifikátoru URI, který obsahuje uživatelské jméno a heslo, přímo do tohoto souboru je pro **vývoji používat pouze** a **by nikdy přidat do správy verzí**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Při spuštění aplikace a že se ho na použití *mongodb* profilu, připojení k databázi MongoDB a ho použít k ukládání dat vaší aplikace.

K sestavení aplikace:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Spusťte aplikaci a že se ho na použití *mongodb* profilu:

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Přejděte na http://localhost:8080 v prohlížeči.

![Aplikace Music Spring s výchozími daty](media/music-app.png)

Všimněte si, že aplikace je vyplněný některé [výchozí data](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). S ní pracovat tak, že odstraníte pár existující alba a vytvořením několika nových.

Můžete ověřit vaše aplikace používá vaše databáze MongoDB k němu připojení a dotazování *musicdb* databáze:

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

V předchozím příkladu [prostředí mongo shell](https://docs.mongodb.com/manual/mongo/) k připojení k databázi MongoDB a bude ji dotazovat. Můžete si taky ověřit, že změny jsou trvalé zastavením vaší aplikace, ho restartovat a přejdete do ji v prohlížeči. Všimněte si, že změny, které jste provedli, jsou stále existuje.


## <a name="create-a-cosmos-db-database"></a>Vytvořit databázi Cosmos DB

Chcete-li vytvořit databázi Cosmos DB v Azure pomocí zprostředkovatele Open Service Broker, použijte `svcat provision` příkaz:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

Ve výstupu předchozího příkazu zřizuje databázi Cosmos DB v Azure ve skupině prostředků *MyResourceGroup* v *eastus* oblasti. Další informace o *resourceGroup*, *umístění*, a je k dispozici v dalších parametrů JSON týkající se Azure [Cosmos DB modulu referenční dokumentaci](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

K ověření vaší databáze byla dokončena, zřizování, použijte `svcat get instance` příkaz:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Vaše databáze je připravený až zobrazí *připravené* pod *stav*.

Po dokončení zřizování databáze potřebujete k vytvoření vazby na jeho metadata [tajného kódu Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/). Jiné aplikace pak můžou tato data poté, co bylo svázáno se tajný klíč. K vytvoření vazby metadata databáze tajného klíče, použijte `svcat bind` příkaz:

```cmd
$ svcat bind musicdb

  Name:        musicdb
  Namespace:   default
  Status:
  Secret:      musicdb
  Instance:    musicdb

Parameters:
  No parameters defined
```


## <a name="use-the-cosmos-db-database-with-your-application"></a>Pomocí databáze Cosmos DB s vaší aplikací

Chcete-li používat databázi Cosmos DB ve vaší aplikaci, je potřeba vědět identifikátor URI pro připojení k němu. Chcete-li získat tyto informace, použijte `kubectl get secret` příkaz:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

V předchozím příkazu je získán *musicdb* tajný klíč a zobrazí pouze identifikátor URI. Tajné kódy jsou uložené ve formátu base64, takže ve výstupu předchozího příkazu také dekóduje.

Použitím tohoto identifikátoru URI databáze Cosmos DB, aktualizujte *src/main/resources/application.yml* jeho použití:

```yaml
...
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
      database: musicdb
```

Aktualizace identifikátoru URI, který obsahuje uživatelské jméno a heslo, přímo do tohoto souboru je pro **vývoji používat pouze** a **by nikdy přidat do správy verzí**.

Znovu sestavte a spusťte aplikaci, abyste mohli začít používat databázi Cosmos DB:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Všimněte si, že vaše aplikace stále používá *mongodb* profilu a identifikátor URI, který začíná *mongodb: / /* pro připojení k databázi Cosmos DB. [Rozhraní API služby Azure Cosmos DB pro MongoDB](../cosmos-db/mongodb-introduction.md) poskytuje tato kompatibilita. To umožňuje vaší aplikaci i nadále fungovat jako by se používat databázi MongoDB, ale ve skutečnosti používá Cosmos DB.

Přejděte na http://localhost:8080 v prohlížeči. Všimněte si, že výchozí data byla obnovena. S ní pracovat tak, že odstraníte pár existující alba a vytvořením několika nových. Můžete ověřit, že změny jsou trvalé zastavením vaší aplikace, ho restartovat a přejdete do ji v prohlížeči. Všimněte si, že změny, které jste provedli, jsou stále existuje. Změny se ukládají do Cosmos DB, který jste vytvořili pomocí zprostředkovatele Open Service Broker for Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Spuštění aplikace ve vašem clusteru AKS

Můžete použít [Azure Dev prostory](../dev-spaces/azure-dev-spaces.md) k nasazení aplikace do clusteru AKS. Azure Dev prostorech můžete generovat artefaktům, jako jsou grafy Dockefiles a Helm, nasazení a spuštění aplikace ve službě AKS.

Pokud chcete povolit prostory vývoj Azure ve vašem clusteru AKS:

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Použití nástrojů Azure Dev prostory připravit aplikace na spouštění ve službě AKS:

```cmd
azds prep --public
```

Tento příkaz vygeneruje několika artefakty, včetně *grafy /* složce, která je helmu, v kořenovém adresáři projektu. Tento příkaz nelze generovat *soubor Dockerfile* pro tento konkrétní projekt, abyste měli k jeho vytvoření.

Vytvořte soubor v kořenové složce projektu s názvem *soubor Dockerfile* tohoto obsahu:

```dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Kromě toho je potřeba aktualizovat *configurations.develop.build* vlastnost *azds.yaml* k *false*:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Také musíte aktualizovat *containerPort* atribut *8080* v *charts/spring-music/templates/deployment.yaml*:

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
```

K nasazení aplikace pro AKS:

```cmd
$ azds up

Using dev space 'default' with target 'MyAKS'
Synchronizing files...1m 18s
Installing Helm chart...5s
Waiting for container image build...23s
Building container image...
Step 1/5 : FROM openjdk:8-jdk-alpine
Step 2/5 : EXPOSE 8080
Step 3/5 : WORKDIR /app
Step 4/5 : COPY build/libs/spring-music-1.0.jar .
Step 5/5 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
Built container image in 21s
Waiting for container...8s
Service 'spring-music' port 'http' is available at http://spring-music.1234567890abcdef1234.eastus.aksapp.io/
Service 'spring-music' port 8080 (TCP) is available at http://localhost:57892
press Ctrl+C to detach
...
```

Přejděte na adresu URL zobrazené v protokolech. V předchozím příkladu můžete využít *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/*. 

Ověřte, že se zobrazí aplikace spolu se vaše změny.

## <a name="next-steps"></a>Další postup

Tento článek popisuje jak aktualizovat stávající aplikace pomocí MongoDB pomocí rozhraní API Cosmos DB pro MongoDB. Tento článek také popisuje jak zřídit službu Cosmos DB pomocí zprostředkovatele Open Service Broker for Azure a nasazení aplikace s Azure Dev prostory AKS.

Další informace o službě Cosmos DB, Open Service Broker for Azure a Azure Dev mezery, naleznete v tématu:
* [Databáze Cosmos](https://docs.microsoft.com/azure/cosmos-db/)
* [Technologie Open Service Broker for Azure](https://osba.sh)
* [Vývoj s využitím vývoje prostorů](../dev-spaces/azure-dev-spaces.md)

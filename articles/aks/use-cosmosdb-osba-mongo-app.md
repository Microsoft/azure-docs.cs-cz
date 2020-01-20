---
title: Integrace existující aplikace v MongoDB s rozhraním API služby Azure Cosmos DB pro MongoDB a otevření Service Broker pro Azure (OSBA)
description: V tomto článku se dozvíte, jak integrovat existující aplikaci Java a MongoDB s rozhraním API Azure Cosmos DB pro MongoDB pomocí Open Service Broker for Azure (OSBA).
author: zr-msft
ms.service: azure-dev-spaces
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, otevřete Service Broker a otevřete Service Broker pro Azure.
ms.openlocfilehash: 3d0ab0b27d77e45d779227d30c5a8e4f824ba62a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277706"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Integrace existující aplikace v MongoDB s rozhraním API služby Azure Cosmos DB pro MongoDB a otevření Service Broker pro Azure (OSBA)

Azure Cosmos DB je globálně distribuovaná databázová služba pro více modelů. Poskytuje taky kompatibilitu s přenosovou protokolem s několika NoSQL rozhraními API, včetně MongoDB. Rozhraní Cosmos DB API pro MongoDB umožňuje používat Cosmos DB s vaší stávající aplikací MongoDB, aniž by bylo nutné měnit ovladače a implementace databáze vaší aplikace. Službu Cosmos DB můžete také zřídit pomocí programu Open Service Broker for Azure.

V tomto článku převezmete existující aplikaci Java, která používá databázi MongoDB, a aktualizujete ji tak, aby používala databázi Cosmos DB s využitím Open Service Broker pro Azure.

## <a name="prerequisites"></a>Požadavky

Než budete moct pokračovat, musíte:
    
* Máte vytvořený [cluster služby Azure Kubernetes](kubernetes-walkthrough.md) .
* Máte [nainstalované a nakonfigurované otevřené Service Broker pro Azure v clusteru AKS](integrate-azure.md). 
* Musí být nainstalovaný a nakonfigurovaný [Service Catalog CLI](https://svc-cat.io/docs/install/) , aby běžely `svcat` příkazy.
* Máte existující databázi [MongoDB](https://www.mongodb.com/) . Například můžete mít MongoDB spuštěný na vašem [vývojovém počítači](https://docs.mongodb.com/manual/administration/install-community/) nebo na [virtuálním počítači Azure](../virtual-machines/linux/install-mongodb.md).
* Máte možnost připojit se k databázi MongoDB, jako je například [prostředí Mongo](https://docs.mongodb.com/manual/mongo/), a dotazovat se na ni.

## <a name="get-application-code"></a>Získání kódu aplikace
    
V tomto článku použijete [ukázkovou aplikaci pro jarní hudbu z Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) k demonstraci aplikace, která používá databázi MongoDB.
    
Naklonujte aplikaci z GitHubu a přejděte do jejího adresáře:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Příprava aplikace pro použití databáze MongoDB

Ukázková aplikace pro jarní hudbu poskytuje mnoho možností pro zdroje dat. V tomto článku ji nakonfigurujete tak, aby používala stávající databázi MongoDB. 

Přidejte YAML za konec *Src/Main/Resources/Application. yml*. Tento doplněk vytvoří profil s názvem *MongoDB* a NAKONFIGURUJE identifikátor URI a název databáze. Nahraďte identifikátor URI informacemi o připojení ke stávající databázi MongoDB. Přidáním identifikátoru URI, který obsahuje uživatelské jméno a heslo, přímo k tomuto souboru slouží **pouze k použití pro vývoj** a **nikdy by neměl být přidán do správy verzí**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Když aplikaci spustíte a sdělíte jí, aby používala profil *MongoDB* , připojí se k databázi MongoDB a použije se k uložení dat aplikace.

Sestavení aplikace:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Spusťte aplikaci a požádejte ji, aby používala profil *MongoDB* :

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Přejděte na `http://localhost:8080` v prohlížeči.

![Aplikace pružinové hudby s výchozími daty](media/music-app.png)

Všimněte si, že aplikace byla naplněna s některými [výchozími daty](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). S ním budete pracovat odstraněním několika stávajících alb a vytvořením několika nových.

Můžete ověřit, že vaše aplikace používá vaši databázi MongoDB a připojuje se k ní a dotazuje se na databázi *musicdb* :

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

Předchozí příklad používá [prostředí Mongo](https://docs.mongodb.com/manual/mongo/) pro připojení k databázi MongoDB a dotazování na ni. Můžete také ověřit, že vaše změny jsou trvalé, zastavením aplikace, restartováním a přechodem zpět do IT v prohlížeči. Všimněte si, že změny, které jste provedli, jsou pořád tam.


## <a name="create-a-cosmos-db-database"></a>Vytvoření databáze Cosmos DB

Pokud chcete vytvořit databázi Cosmos DB v Azure pomocí příkazu Open Service Broker, použijte příkaz `svcat provision`:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

Předchozí příkaz zřídí databázi Cosmos DB v Azure ve skupině prostředků *MyResourceGroup* v oblasti *eastus* . Další informace o *zdroji*, *umístění*a dalších parametrech JSON specifických pro Azure jsou k dispozici v [referenční dokumentaci modulu Cosmos DB](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Chcete-li ověřit, zda byla databáze dokončena, použijte příkaz `svcat get instance`:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Vaše databáze je připravena, když se zobrazí *stav* *připraveno* .

Jakmile se databáze dokončí, budete muset vytvořit vazby svých metadat k [Kubernetes tajnému](https://kubernetes.io/docs/concepts/configuration/secret/)kódu. Ostatní aplikace pak budou mít přístup k těmto datům poté, co jsou svázány s tajným klíčem. Pokud chcete navazovat metadata vaší databáze na tajný klíč, použijte příkaz `svcat bind`:

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


## <a name="use-the-cosmos-db-database-with-your-application"></a>Použití databáze Cosmos DB s vaší aplikací

Pokud chcete použít databázi Cosmos DB s vaší aplikací, musíte znát identifikátor URI, abyste se k němu mohli připojit. Chcete-li získat tyto informace, použijte příkaz `kubectl get secret`:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

Předchozí příkaz načte tajný klíč *musicdb* a zobrazí jenom identifikátor URI. Tajné kódy jsou uložené ve formátu base64, takže ho předchozí příkaz také dekóduje.

Pomocí identifikátoru URI databáze Cosmos DB aktualizujte *Src/Main/Resources/Application. yml* , abyste ji mohli použít:

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

Aktualizace identifikátoru URI, který obsahuje uživatelské jméno a heslo, je přímo k tomuto souboru určena **pouze pro vývojové použití** a **nikdy by neměla být přidána do řízení verze**.

Znovu sestavte a spusťte aplikaci, abyste mohli začít používat databázi Cosmos DB:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Všimněte si, že vaše aplikace pořád používá profil *MongoDB* a identifikátor URI, který začíná *MongoDB://* pro připojení k databázi Cosmos DB. Tato kompatibilita zajišťuje [rozhraní Azure Cosmos DB API pro MongoDB](../cosmos-db/mongodb-introduction.md) . Umožňuje vaší aplikaci pokračovat v práci, jako kdyby používala databázi MongoDB, ale ve skutečnosti používá Cosmos DB.

Přejděte na `http://localhost:8080` v prohlížeči. Všimněte si, že se obnovila výchozí data. S ním budete pracovat odstraněním několika stávajících alb a vytvořením několika nových. Můžete ověřit, že vaše změny jsou trvalé, zastavením aplikace, restartováním a přechodem zpět do IT v prohlížeči. Všimněte si, že změny, které jste provedli, jsou pořád tam. Změny se uloží do Cosmos DB, který jste vytvořili pomocí programu Open Service Broker for Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Spuštění aplikace v clusteru AKS

K nasazení aplikace do clusteru AKS můžete použít [Azure dev Spaces](../dev-spaces/azure-dev-spaces.md) . Azure Dev Spaces vám pomůže generovat artefakty, jako jsou grafy fázemi a Helm, a nasadit a spustit aplikaci v AKS.

Povolení Azure Dev Spaces v clusteru AKS:

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Pomocí nástrojů Azure Dev Spaces Připravte aplikaci tak, aby běžela v AKS:

```cmd
azds prep --public
```

Tento příkaz vygeneruje několik artefaktů, včetně *grafů nebo* složky, které jsou v Helm grafu, v kořenu projektu. Tento příkaz nemůže vygenerovat *souboru Dockerfile* pro tento konkrétní projekt, takže ho budete muset vytvořit.

Vytvořte soubor v kořenu projektu s názvem *souboru Dockerfile* s tímto obsahem:

```Dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Kromě toho musíte aktualizovat vlastnost *configurations. vývoj. Build* v *azds. yaml* na *false*:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Také je potřeba aktualizovat atribut *containerPort* na *8080* v *Charts/Spring-Music/Templates/Deployment. yaml*:

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

Nasazení aplikace do AKS:

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

Přejděte na adresu URL zobrazenou v protokolech. V předchozím příkladu byste použili *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/* . 

Ověřte, že se vám zobrazí aplikace spolu s vašimi změnami.

## <a name="next-steps"></a>Další kroky

Tento článek popisuje, jak aktualizovat existující aplikaci z použití MongoDB na používání rozhraní API pro MongoDB pro Cosmos DB. Tento článek také popisuje, jak zřídit službu Cosmos DB s využitím Open Service Broker for Azure a nasazením této aplikace do AKS s Azure Dev Spaces.

Další informace o Cosmos DB, otevření Service Broker pro Azure a Azure Dev Spaces najdete v těchto tématech:
* [Databáze Cosmos](https://docs.microsoft.com/azure/cosmos-db/)
* [Otevřít Service Broker pro Azure](https://osba.sh)
* [Vývoj s využitím vývojových prostorů](../dev-spaces/azure-dev-spaces.md)

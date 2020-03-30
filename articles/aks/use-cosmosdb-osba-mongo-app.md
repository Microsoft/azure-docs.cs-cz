---
title: Integrace existující aplikace MongoDB s rozhraním API Azure Cosmos DB pro MongoDB a Open Service Broker pro Azure (OSBA)
description: V tomto článku se dozvíte, jak integrovat existující java a MongoDB aplikace s rozhraním API Db Azure Cosmos pro MongoDB pomocí Open Service Broker pro Azure (OSBA).
author: zr-msft
ms.service: azure-dev-spaces
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, Open Service Broker, Open Service Broker pro Azure
ms.openlocfilehash: ddaa3b9aa198bc142e1bcbcab6b7b1e028eff2aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78247923"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Integrace existující aplikace MongoDB s rozhraním API Azure Cosmos DB pro MongoDB a Open Service Broker pro Azure (OSBA)

Azure Cosmos DB je globálně distribuovaná databázová služba pro více modelů. Poskytuje také kompatibilitu drátového protokolu s několika nosql API, včetně mongoDB. Rozhraní COSMOS DB API pro MongoDB umožňuje používat Cosmos DB s vaší existující aplikací MongoDB bez nutnosti měnit ovladače databáze vaší aplikace nebo implementaci. Můžete také zřídit službu Cosmos DB pomocí Open Service Broker pro Azure.

V tomto článku vezmete existující java aplikaci, která používá databázi MongoDB a aktualizujete ji tak, aby používala databázi Cosmos DB pomocí Open Service Broker pro Azure.

## <a name="prerequisites"></a>Požadavky

Než budete moci pokračovat, musíte:
    
* [Nanesete cluster služby Azure Kubernetes.](kubernetes-walkthrough.md)
* Mít [Open Service Broker pro Azure nainstalovaný a nakonfigurovaný ve vašem clusteru AKS](integrate-azure.md). 
* Nainstalujte a nakonfigurujete rozhraní [PŘÍKAZ CLI katalogu služeb](https://svc-cat.io/docs/install/) pro spouštění `svcat` příkazů.
* Mít existující [databázi MongoDB.](https://www.mongodb.com/) Například můžete mít MongoDB spuštěna na [vývojovém počítači](https://docs.mongodb.com/manual/administration/install-community/) nebo ve [virtuálním počítači Azure](../virtual-machines/linux/install-mongodb.md).
* Máte způsob, jak se připojit k databázi MongoDB a dotazování na ní, například k [prostředí mongo](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Získání kódu aplikace
    
V tomto článku použijete [ukázkovou aplikaci jarní hudby z Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) k předvedení aplikace, která používá databázi MongoDB.
    
Klonujte aplikaci z GitHubu a přejděte do jejího adresáře:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Příprava aplikace pro použití databáze MongoDB

Ukázková aplikace jarní hudby poskytuje mnoho možností pro zdroje dat. V tomto článku nakonfigurujete použít existující databázi MongoDB. 

Přidejte následující kód YAML na konec *src/main/resources/application.yml*. Toto přidání vytvoří profil s názvem *mongodb* a nakonfiguruje identifikátor URI a název databáze. Nahraďte identifikátor URI informacemi o připojení k existující databázi MongoDB. Přidání identifikátoru URI, který obsahuje uživatelské jméno a heslo, přímo do tohoto souboru je **pouze** pro vývoj a **nikdy by neměl být přidán do správy verzí**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Když spustíte aplikaci a řeknete jí, aby používala profil *mongodb,* připojí se k databázi MongoDB a použije ji k ukládání dat aplikace.

Vytvoření aplikace:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Spusťte aplikaci a řekněte jí, aby použila profil *mongodb:*

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Přejděte `http://localhost:8080` do prohlížeče.

![Aplikace Spring Music s výchozími daty](media/music-app.png)

Všimněte si, že aplikace byla naplněna některými [výchozími daty](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). Interakci s ním odstraněním několika stávajících alb a vytvořením několika nových alb.

Můžete ověřit, že vaše aplikace používá databázi MongoDB připojením k ní a dotazování *musicdb* databáze:

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

Předchozí příklad používá [prostředí mongo](https://docs.mongodb.com/manual/mongo/) pro připojení k databázi MongoDB a dotaz na něj. Můžete také ověřit, že změny jsou trvalé zastavením aplikace, restartováním a přechodem zpět do aplikace v prohlížeči. Všimněte si, že provedené změny stále existují.


## <a name="create-a-cosmos-db-database"></a>Vytvoření databáze Cosmos DB

Chcete-li vytvořit databázi Cosmos DB v `svcat provision` Azure pomocí služby Open Service Broker, použijte příkaz:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

Předchozí příkaz zřídí databázi Cosmos DB v Azure ve skupině prostředků *MyResourceGroup* v oblasti *eastus.* Další informace o *resourceGroup*, *umístění*a další parametry JSON specifické pro Azure je k dispozici v [referenční dokumentaci modulu Cosmos DB](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Chcete-li ověřit, že databáze `svcat get instance` dokončila zřizování, použijte příkaz:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Databáze je připravena, když je vidět *Připraveno* v části *STAV*.

Po dokončení zřizování databáze je třeba svázat její metadata s [tajným klíčem Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/). Ostatní aplikace pak mohou přistupovat k těmto datům poté, co byly vázány na tajný klíč. Chcete-li svázat metadata databáze s `svcat bind` tajným tajemstvím, použijte příkaz:

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

Chcete-li použít databázi Cosmos DB s vaší aplikací, musíte znát identifikátor URI pro připojení k ní. Chcete-li získat tyto `kubectl get secret` informace, použijte příkaz:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

Předchozí příkaz získá tajný klíč *musicdb* a zobrazí pouze identifikátor URI. Tajné klíče jsou uloženy ve formátu base64, takže předchozí příkaz také dekóduje.

Pomocí identifikátoru URI databáze Cosmos DB aktualizujte *src/main/resources/application.yml* a použijte ji:

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

Aktualizace identifikátoru URI, který obsahuje uživatelské jméno a heslo, přímo do tohoto souboru je **určen pouze** pro vývoj a nikdy by neměl být přidán do **správy verzí**.

Znovu sestavte a spusťte aplikaci a začněte používat databázi Cosmos DB:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Všimněte si, že vaše aplikace stále používá profil *mongodb* a identifikátor URI, který začíná *mongodb://* pro připojení k databázi Cosmos DB. [Rozhraní API Db Azure Cosmos pro MongoDB](../cosmos-db/mongodb-introduction.md) poskytuje tuto kompatibilitu. Umožňuje vaší aplikaci nadále fungovat, jako by se používá databáze MongoDB, ale ve skutečnosti používá Cosmos DB.

Přejděte `http://localhost:8080` do prohlížeče. Všimněte si, že výchozí data byla obnovena. Interakci s ním odstraněním několika stávajících alb a vytvořením několika nových alb. Změny můžete ověřit, že jsou trvalé, zastavením aplikace, restartováním a návratem do aplikace v prohlížeči. Všimněte si, že provedené změny stále existují. Změny se udály do databáze Cosmos, kterou jste vytvořili pomocí služby Open Service Broker pro Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Spuštění aplikace v clusteru AKS

[Azure Dev Spaces](../dev-spaces/azure-dev-spaces.md) můžete použít k nasazení aplikace do clusteru AKS. Azure Dev Spaces vám pomůže generovat artefakty, jako jsou Dockerfiles a Helm grafy a nasadit a spustit aplikaci v AKS.

Povolení Azure Dev Spaces v clusteru AKS:

```azurecli
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Pomocí nástrojů Azure Dev Spaces připravte aplikaci ke spuštění ve službě AKS:

```cmd
azds prep --public
```

Tento příkaz generuje několik artefaktů, včetně *grafy /* složky, což je váš graf Helm, v kořenovém adresáři projektu. Tento příkaz nemůže generovat *Dockerfile* pro tento konkrétní projekt, takže je nutné jej vytvořit.

Vytvořte soubor v kořenovém adresáři projektu s názvem *Dockerfile* s tímto obsahem:

```dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Kromě toho je třeba aktualizovat vlastnost *configurations.develop.build* v *souboru azds.yaml* na *hodnotu false*:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Také je třeba aktualizovat *containerPort* atribut *8080* v *grafech/jaro-music/templates/deployment.yaml*:

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

Přejděte na adresu URL zobrazenou v protokolech. V předchozím příkladu byste *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/* použili . 

Ověřte, zda se aplikace zobrazí spolu se změnami.

## <a name="next-steps"></a>Další kroky

Tento článek popisuje, jak aktualizovat existující aplikace z použití MongoDB pomocí Rozhraní API Cosmos DB pro MongoDB. Tento článek se také týkal toho, jak zřídit službu Cosmos DB pomocí Open Service Broker pro Azure a nasazení této aplikace do AKS s Azure Dev Spaces.

Další informace o Cosmos DB, Open Service Broker for Azure a Azure Dev Spaces najdete v tématu:
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Technologie Open Service Broker for Azure](https://osba.sh)
* [Vývoj s využitím Dev Spaces](../dev-spaces/azure-dev-spaces.md)

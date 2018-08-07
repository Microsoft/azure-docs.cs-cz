---
title: Vytvořit kontejner pro server Apache Tomcat služby Azure Service Fabric v Linuxu | Dokumentace Microsoftu
description: Vytvořte kontejner Linuxu, aby zveřejnit aplikaci spuštěné na serveru Apache Tomcat na platformě Azure Service Fabric. Sestavíte image Dockeru s vaší aplikací a server Apache Tomcat, nahrajete image do registru kontejneru, sestavení a nasazení aplikace typu kontejner pro Service Fabric.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/08/2018
ms.author: v-jamebr
ms.openlocfilehash: 29208bcbdbe6ad01d0e1ac7343bd921f3287260a
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581264"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Vytvoření kontejneru Service Fabric v Linuxu spuštěnou serveru Apache Tomcat
Apache Tomcat je Oblíbené, open source implementace technologií Java Servlet a Java Server. V tomto článku se dozvíte, jak vytvořit kontejner s Apache Tomcat a jednoduchou webovou aplikaci, kontejner nasadí do clusteru Service Fabric s Linuxem a připojit k webové aplikaci.  

Další informace o Apache Tomcat, najdete v článku [Domovská stránka Apache Tomcat](http://tomcat.apache.org/). 

## <a name="prerequisites"></a>Požadavky
* Vývojový počítač s:
  * [Sada Service Fabric SDK a nástroje](service-fabric-get-started-linux.md).
  * [Docker CE pro Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric CLI](service-fabric-cli.md)

* Registr kontejnerů ve službě Azure Container Registry. Můžete vytvořit registr kontejnerů v předplatnému Azure pomocí [na webu Azure portal](../container-registry/container-registry-get-started-portal.md) nebo [rozhraní příkazového řádku Azure](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry). 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Sestavíte Tomcat image a spustit místně
Postupujte podle kroků v této části sestavíte image Dockeru na základě image Apache Tomcat a jednoduchou webovou aplikaci a pak ho spusťte v kontejneru v místním systému. 
 
1. Klonování [Service Fabric: Začínáme s Javou](https://github.com/Azure-Samples/service-fabric-java-getting-started) úložišti ukázek na vašem vývojovém počítači.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Přejděte do adresáře ukázkový server Apache Tomcat (*service-fabric-java-getting-started/container-apache-tomcat-web-server-sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Vytvořte soubor Docker podle official je přínosné pro [Tomcat image](https://hub.docker.com/_/tomcat/) nachází na Docker Hubu a ukázkový server Tomcat. V *service-fabric-java-getting-started/container-apache-tomcat-web-server-sample* adresáři vytvořte soubor s názvem *soubor Dockerfile* (s bez přípony souboru). Do souboru *Dockerfile* přidejte následující a uložte změny:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Zobrazit [odkaz na soubor Dockerfile](https://docs.docker.com/engine/reference/builder/) Další informace.


4. Spustit `docker build` příkazu vytvořte image spouštějící vaši webovou aplikaci:

   ```bash
   docker build . -t tomcattest
   ```

   Tento příkaz sestaví novou image podle pokynů v souboru Dockerfile, pojmenování (-t označování) image `tomcattest`. K sestavení image kontejneru, základní image při prvním stažení dolů z Docker Hubu a aplikace se přidá do ní. 

   Po dokončení příkazu pro sestavení spusťte příkaz `docker images` a zobrazte informace o nové imagi:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Ověřte, že kontejnerizované aplikace běží místně před jejím nahráním do registru kontejneru:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` názvy kontejnerů, tak můžete odkazovat pomocí popisný název, nikoli jeho ID.
   * `-p` Určuje mapování portu kontejneru a hostitelský operační systém. 

   > [!Note]
   > Port otevřete pomocí `-p` parametr by měl být Tomcat aplikace naslouchá na žádosti na port. V uvedeném příkladě se konektor gurovaný *ApacheTomcat/conf/server.xml* souboru tak, aby naslouchala na portu 8080 pro požadavky HTTP. Tento port se mapují na port 8080 na hostiteli. 

   Informace o dalších parametrech najdete v tématu [Docker run dokumentaci](https://docs.docker.com/engine/reference/commandline/run/).

1. Pokud chcete otestovat kontejner, otevřete prohlížeč a zadejte jednu z následujících adres URL. Zobrazí se jeho variantě "Hello World!" úvodní obrazovka pro každou adresu URL.

   - http://localhost:8080/hello 
   - http://localhost:8080/hello/sayhello 
   - http://localhost:8080/hello/sayhi 

   ![Hello world /sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Kontejner zastavit a odstranit z vývojového počítače:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Nahrání Tomcat image do vašeho registru kontejneru
Teď, když jste ověřili, že Tomcat image lze spustit v kontejneru na vašem vývojovém počítači, vložit ho do úložiště v registru kontejneru. Tento článek používá Azure Container Registry k uložení image, ale s určitými úpravami postup můžete použít jakéhokoli registru kontejnerů, které zvolíte. V tomto článku se předpokládá název registru bude *myregistry* a je název registru úplné myregistry.azurecr.io. Změňte tyto odpovídajícím způsobem pro váš scénář. 

1. Spusťte příkaz `docker login` a přihlaste se ke svému registru kontejnerů pomocí [přihlašovacích údajů registru](../container-registry/container-registry-authentication.md).

   Následující příklad předá ID a heslo [instančního objektu](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory. Instanční objekt jste k registru mohli přiřadit například pro účely scénáře automatizace. Nebo se můžete přihlásit pomocí uživatelského jména a hesla registru.

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. Následující příkaz vytvoří značku, neboli alias, image s plně kvalifikovanou cestou k vašemu registru. Tento příklad umístí image do oboru názvů `samples`, aby se zabránilo nepořádku v kořenovém adresáři registru.

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. Nahrajte image do registru kontejneru:

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Vytvoření a nasazení aplikace Service Fabric typu kontejner
Teď, když jste nevložili jste Tomcat image do registru kontejneru, můžete sestavit a nasadit aplikace kontejneru Service Fabric, si vyžádá Tomcat image z registru, který běží jako kontejnerizované služby v clusteru. 

1. Vytvořte nový adresář mimo místní klon (mimo *service-fabric-java-getting-started* adresářový strom). Přepněte do něj a pomocí generátoru Yeoman vytvoříme vygenerované uživatelské rozhraní pro aplikace typu kontejner pro: 

   ```bash
   yo azuresfcontainer 
   ```
   Zadejte následující hodnoty po zobrazení výzvy:

   * Pojmenujte svoji aplikaci: ServiceFabricTomcat
   * Název vytvářené aplikační služby: TomcatService
   * Zadejte název Image: Zadejte adresu URL Image kontejneru do vašeho registru kontejneru. například myregistry.azurecr.io/samples/tomcattest.
   * Příkazy: Ponechte toto prázdné. Vzhledem k tomu, že tato image má definovaný vstupní bod úloh, není potřeba explicitně zadat vstupní příkazy (příkazy se spouští uvnitř kontejneru, což zajistí zachování provozu kontejneru po spuštění).
   * Počet instancí aplikace typu kontejner typu Host: 1

   ![Generátor Service Fabric Yeoman pro kontejnery](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. V manifestu služby (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*), přidejte následující kód XML v kořenu **ServiceManfest** značky pro otevření portu pro vaše aplikace naslouchá na požadavky. **Koncový bod** značky deklaruje protokol a port pro koncový bod. Pro účely tohoto článku kontejnerizovaná služba naslouchá na portu 8080: 

    ```xml
    <Resources>
      <Endpoints>
        <!-- This endpoint is used by the communication listener to obtain the port on which to 
         listen. Please note that if your service is partitioned, this port is shared with 
         replicas of different partitions that are placed in your code. -->
        <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
      </Endpoints>
    </Resources>
    ```

11. V manifestu aplikace (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*) v části **ServiceManifestImport** značky, přidejte následující kód XML. Nahradit **AccountName** a **heslo** v **RepositoryCredentials** značku s názvem vašeho registru kontejneru a heslo, které vyžadují pro přihlášení k němu.

    ```xml
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
      </ContainerHostPolicies>
    </Policies>
    ```

    **ContainerHostPolicies** značka Určuje zásady pro aktivaci hostitelích kontejnerů.
    
    * **PortBinding** značky nakonfiguruje zásady mapování portů kontejneru port hostitele. **ContainerPort** atribut je nastaven na hodnotu 8080, protože kontejner zpřístupňuje port 8080, jak je zadáno v souboru Dockerfile. **EndpointRef** atribut je nastaven na "endpointTest", koncový bod definovaný v manifestu služby v předchozím kroku. Příchozí žádosti o službu na portu 8080, proto se mapují na port 8080 v kontejneru. 
    * **RepositoryCredentials** značka Určuje přihlašovací údaje, které je potřeba ověřit s úložišti (privátní), kde stáhne image z kontejneru. Nepotřebujete tuto zásadu na obrázku se načítají z veřejného úložiště.
    

12. V *ServiceFabricTomcat* složky, připojte se ke clusteru service fabric. 

    * Pokud chcete připojit k místnímu clusteru Service Fabric, spusťte:

       ```bash
       sfctl cluster select --endpoint http://localhost:19080
       ```
    
    * Pro připojení k zabezpečenému clusteru Azure, ujistěte se, že je k dispozici jako soubor .pem certifikátu klienta *ServiceFabricTomcat* adresář a spusťte: 

       ```bash
       sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
       ```
       V předchozím příkazu nahraďte `your-certificate.pem` s názvem vašeho souboru klientského certifikátu. Do vývojového a testovacího prostředí se často používá certifikát clusteru jako na klientský certifikát. Pokud není certifikát podepsaný držitelem, vynechejte `-no-verify` parametru. 
       
       Certifikáty clusteru jsou obvykle stáhli jako soubory PFX. Pokud ještě nemáte certifikát ve formátu PEM, spuštěním následujícího příkazu vytvořte soubor .pem ze souboru .pfx:

       ```bash
       openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
       ```

       Pokud váš soubor PFX není chráněn heslem, použijte `-passin pass:` pro poslední parametr.


13. Spusťte instalační skript, který je součástí šablony, které chcete nasadit aplikaci do clusteru. Tento skript zkopíruje balíček aplikace do úložiště imagí clusteru, zaregistruje typ aplikace a vytvoří instanci aplikace.

       ```bash
       ./install.sh
       ```

    Po spuštění instalačního skriptu, spusťte prohlížeč a přejděte do Service Fabric Exploreru:
    
    * V místním clusteru, použijte http://localhost:19080/Explorer (nahradit *localhost* s privátní IP adresu virtuálního počítače, pokud používáte Vagrant v Mac OS X).
    * V Azure cluster zabezpečený pomocí https://PublicIPorFQDN:19080/Explorer. 
    
    Rozbalte **aplikací** uzel a Všimněte si, že už obsahuje položku pro váš typ aplikace **ServiceFabricTomcatType**a další položku pro první instanci tohoto typu. Může trvat několik minut, než pro aplikaci k nasazení plně, proto buďte prosím trpěliví.

    ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Pro přístup k aplikaci na serveru Tomcat, otevřete okno prohlížeče a zadejte některý z následujících adres URL. Pokud jste nasadili do místního clusteru, použijte *localhost* pro *PublicIPorFQDN*. Zobrazí se jeho variantě "Hello World!" úvodní obrazovka pro každou adresu URL.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Vyčištění
Pomocí odinstalačního skriptu, který je součástí šablony, odstraňte instanci aplikace z clusteru a zrušte registraci typu aplikace.

```bash
./uninstall.sh
```

Po nahrání image do registru kontejneru můžete odstranit místní image z vývojového počítače:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Další postup
* Rychlé kroky na další funkce kontejner Linuxu, přečtěte si [vytvořit svou první aplikaci typu kontejner Service Fabric v Linuxu](service-fabric-get-started-containers-linux.md).
* Podrobné kroky pro kontejnery Linuxu, přečtěte si [vytvořit kurz aplikace kontejneru Linuxu](service-fabric-tutorial-create-container-images.md) kurzu.
* Další informace o spouštění [kontejnerů v Service Fabric](service-fabric-containers-overview.md).



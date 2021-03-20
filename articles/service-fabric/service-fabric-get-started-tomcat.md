---
title: Vytvoření kontejneru pro Apache Tomcat v systému Linux
description: Vytvořte kontejner pro Linux, který vystaví aplikaci běžící na serveru Apache Tomcat na Azure Service Fabric. Sestavte image Docker pomocí aplikace a serveru Apache Tomcat, nahrajte image do registru kontejnerů a sestavte a nasaďte aplikaci kontejneru Service Fabric.
ms.topic: conceptual
ms.date: 6/08/2018
ms.author: pepogors
ms.openlocfilehash: 3de97bc277195dff2daf5868c0eb9aec5d6e27c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96534025"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Vytvoření kontejneru Service Fabric, na kterém běží Apache Tomcat Server v systému Linux
Apache Tomcat je oblíbená a open source implementace technologií serveru Java servlet a Java. V tomto článku se dozvíte, jak vytvořit kontejner s Apache Tomcat a jednoduchou webovou aplikací, nasadit kontejner do Service Fabric clusteru se systémem Linux a připojit se k webové aplikaci.  

Další informace o Apache Tomcat najdete na [domovské stránce Apache Tomcat](https://tomcat.apache.org/). 

## <a name="prerequisites"></a>Předpoklady
* Vývojový počítač s:
  * [Service Fabric SDK a nástroje](service-fabric-get-started-linux.md)
  * [Docker CE pro Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric CLI](service-fabric-cli.md)

* Registr kontejneru v Azure Container Registry. V rámci předplatného Azure můžete vytvořit registr kontejnerů pomocí [Azure Portal](../container-registry/container-registry-get-started-portal.md) nebo [Azure CLI](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry). 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Vytvoření image Tomcat a její místní spuštění
Podle kroků v této části vytvoříte Docker image na základě image Apache Tomcat a jednoduché webové aplikace a pak ji spustíte v kontejneru v místním systému. 
 
1. Naklonujte [Service Fabric Začínáme s](https://github.com/Azure-Samples/service-fabric-java-getting-started) úložištěm ukázek Java ve vývojovém počítači.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Přejděte do adresáře ukázkový adresář serveru Apache Tomcat (*Service-Fabric-Java-Začínáme/kontejner-Apache-Tomcat-Web-Server-Sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Vytvořte soubor Docker založený na oficiální [imagi Tomcat](https://hub.docker.com/_/tomcat/) , která se nachází v Docker Hub a v ukázce serveru Tomcat. V adresáři *Service-Fabric-Java-Začínáme/Container-Apache-Tomcat-Web-Server-Sample* vytvořte soubor s názvem *souboru Dockerfile* (bez přípony souboru). Do souboru *Dockerfile* přidejte následující a uložte změny:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Další informace najdete v [referenčních](https://docs.docker.com/engine/reference/builder/) informacích k souboru Dockerfile.


4. Přihlaste se k Docker a spuštěním `docker build` příkazu vytvořte image spouštějící vaši webovou aplikaci:

   ```bash
   docker login
   docker build . -t tomcattest
   ```

   Tento příkaz sestaví novou image pomocí instrukcí v souboru Dockerfile, pojmenovávání (-t označování) obrázku `tomcattest` . Pokud chcete vytvořit image kontejneru, nejdřív se z dokovacího centra stáhne základní image a do ní se přidá aplikace. 

   Po dokončení příkazu pro sestavení spusťte příkaz `docker images` a zobrazte informace o nové imagi:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Ověřte, zda je vaše kontejnerová aplikace spuštěna místně, a poté ji zatlačte do registru kontejneru:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` pojmenuje kontejner, takže se na něj můžete odkazovat pomocí popisného názvu místo jeho ID.
   * `-p` Určuje mapování portů mezi kontejnerem a hostitelským operačním systémem. 

   > [!Note]
   > Port, který otevřete s `-p` parametrem, by měl být port, na kterém vaše aplikace Tomcat naslouchá požadavkům. V aktuálním příkladu je v souboru *ApacheTomcat/conf/server.xml* nakonfigurovaný konektor, který naslouchá požadavkům HTTP na portu 8080. Tento port je namapován na port 8080 na hostiteli. 

   Další informace o dalších parametrech najdete v [dokumentaci k Docker Run](https://docs.docker.com/engine/reference/commandline/run/).

1. Pokud chcete svůj kontejner otestovat, otevřete prohlížeč a zadejte jednu z následujících adres URL. Zobrazí se varianta "Hello World!". Úvodní obrazovka pro každou adresu URL

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Hello World/sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Zastavte kontejner a odstraňte ho z vývojového počítače:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Vložení image Tomcat do registru kontejneru
Teď, když jste ověřili, že se image Tomcat spouští v kontejneru ve vývojovém počítači, nahrajte ji do úložiště v registru kontejnerů, abyste [snížili dopad](../container-registry/buffer-gate-public-content.md) na vaše pracovní postupy pro vývoj a nasazení imagí. Tento článek používá Azure Container Registry k uložení image, ale s určitou úpravou kroků můžete použít libovolný registr kontejneru, který si zvolíte. V tomto článku se předpokládá, že název registru bude *myregistry* a úplný název registru je myregistry.azurecr.IO. Odpovídajícím způsobem je změňte pro váš scénář. 

1. Spusťte `docker login` , abyste se k registru kontejneru přihlásili pomocí [přihlašovacích údajů registru](../container-registry/container-registry-authentication.md).

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

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Sestavení a nasazení aplikace Service Fabric Container
Teď, když jste vložili image Tomcat do registru kontejnerů, můžete sestavit a nasadit aplikaci typu kontejner pro Service Fabric, která načte image Tomcat z registru a spustí ji jako kontejnerové služby v clusteru. 

1. Vytvořte nový adresář mimo svůj místní klon (mimo strom adresářů *Service-Fabric-Java-Začínáme-Started* ). Přepněte na něj a použijte Yeoman k vytvoření uživatelského rozhraní pro aplikaci typu kontejner: 

   ```bash
   yo azuresfcontainer 
   ```
   Po zobrazení výzvy zadejte následující hodnoty:

   * Pojmenování aplikace: ServiceFabricTomcat
   * Název aplikační služby: TomcatService
   * Zadejte název bitové kopie: zadejte adresu URL image kontejneru ve vašem registru kontejneru. například myregistry.azurecr.io/samples/tomcattest.
   * Příkazy: nechte toto pole prázdné. Vzhledem k tomu, že tato image má definovaný vstupní bod úloh, není potřeba explicitně zadat vstupní příkazy (příkazy se spouští uvnitř kontejneru, což zajistí zachování provozu kontejneru po spuštění).
   * Počet instancí hostované aplikace kontejneru: 1

   ![Generátor Service Fabric Yeoman pro kontejnery](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. V manifestu služby (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*) přidejte do kořenové značky **ServiceManfest** následující kód XML, který otevře port, na kterém aplikace naslouchá požadavky. Značka **koncového bodu** deklaruje protokol a port pro koncový bod. Pro tento článek naslouchá služba kontejnerů na portu 8080: 

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

11. V manifestu aplikace (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*) pod značkou **ServiceManifestImport** přidejte následující kód XML. Nahraďte název **účtu** a **heslo** v tagu **RepositoryCredentials** názvem vašeho registru kontejneru a heslem, které se vyžaduje pro přihlášení.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   Značka **ContainerHostPolicies** určuje zásady pro aktivaci hostitelů kontejnerů.
    
   * Značka **PortBinding** konfiguruje zásadu mapování portů pro port na hostitele. Atribut **ContainerPort** je nastaven na hodnotu 8080, protože kontejner zveřejňuje port 8080, jak je uvedeno v souboru Dockerfile. Atribut **EndpointRef** je nastaven na hodnotu "endpointTest", koncový bod definovaný v manifestu služby v předchozím kroku. Příchozí požadavky na službu na portu 8080 jsou tedy namapovány na port 8080 na kontejneru. 
   * Značka **RepositoryCredentials** určuje přihlašovací údaje, které kontejner potřebuje k ověření pomocí (privátního) úložiště, ze kterého se má image načíst. Tuto zásadu nepotřebujete, pokud se image načte z veřejného úložiště.
    

12. Ve složce *ServiceFabricTomcat* se připojte ke clusteru Service Fabric. 

   * Pokud se chcete připojit k místnímu Service Fabric clusteru, spusťte příkaz:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Pokud se chcete připojit k zabezpečenému clusteru Azure, ujistěte se, že je klientský certifikát přítomen jako soubor. pem v adresáři *ServiceFabricTomcat* a spusťte příkaz: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     V předchozím příkazu nahraďte `your-certificate.pem` názvem souboru klientského certifikátu. Ve vývojových a testovacích prostředích se certifikát clusteru často používá jako klientský certifikát. Pokud certifikát není podepsaný svým držitelem, vynechejte `-no-verify` parametr. 
       
     Certifikáty clusteru jsou obvykle stahovány místně jako soubory. pfx. Pokud ještě nemáte certifikát ve formátu PEM, můžete spuštěním následujícího příkazu vytvořit soubor. pem ze souboru. pfx:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Pokud Váš soubor. pfx není chráněný heslem, použijte `-passin pass:` pro poslední parametr.


13. Spuštěním instalačního skriptu, který je součástí šablony, nasaďte aplikaci do clusteru. Skript zkopíruje balíček aplikace do úložiště imagí clusteru, zaregistruje typ aplikace a vytvoří instanci aplikace.

     ```bash
     ./install.sh
     ```

   Po spuštění instalačního skriptu otevřete prohlížeč a přejděte na Service Fabric Explorer:
    
   * V místním clusteru použijte `http://localhost:19080/Explorer` (nahraďte *localhost* privátní IP adresou virtuálního počítače, pokud používáte Vagrant na Mac OS X).
   * V zabezpečeném clusteru Azure použijte `https://PublicIPorFQDN:19080/Explorer` . 
    
   Rozbalte uzel **aplikace** a Všimněte si, že teď existuje položka pro typ aplikace, **ServiceFabricTomcatType** a další pro první instanci tohoto typu. Může trvat několik minut, než se aplikace plně nasadí, tedy pacient.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Chcete-li získat přístup k aplikaci na serveru Tomcat, otevřete okno prohlížeče a zadejte některou z následujících adres URL. Pokud jste nasadili na místní cluster, použijte *localhost* pro *PublicIPorFQDN*. Zobrazí se varianta "Hello World!". Úvodní obrazovka pro každou adresu URL

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

## <a name="next-steps"></a>Další kroky
* Rychlé kroky dalších funkcí kontejnerů pro Linux najdete [v tématu Vytvoření první Service Fabric aplikace kontejneru v systému Linux](service-fabric-get-started-containers-linux.md).
* Podrobnější kroky k kontejnerům pro Linux najdete v kurzu [Vytvoření aplikace typu kontejner pro Linux](service-fabric-tutorial-create-container-images.md) .
* Další informace o spouštění [kontejnerů v Service Fabric](service-fabric-containers-overview.md).



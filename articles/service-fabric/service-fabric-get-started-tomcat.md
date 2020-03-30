---
title: Vytvoření kontejneru pro Apache Tomcat na Linuxu
description: Vytvořte linuxový kontejner, abyste odhalili aplikaci spuštěnou na serveru Apache Tomcat ve službě Azure Service Fabric. Vytvořte bitovou kopii Dockeru s vaší aplikací a serverem Apache Tomcat, převeďte ji do registru kontejnerů, sestavte a nasaďte aplikaci kontejneru Service Fabric.
ms.topic: conceptual
ms.date: 6/08/2018
ms.author: pepogors
ms.openlocfilehash: 1a699f3b35970270a9800162a6d8717682a168ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614413"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Vytvoření kontejneru Service Fabric se systémem Apache Tomcat server na Linuxu
Apache Tomcat je populární, open-source implementace java servlet a java serverových technologií. Tento článek ukazuje, jak vytvořit kontejner s Apache Tomcat a jednoduchou webovou aplikací, nasadit kontejner do clusteru Service Fabric se systémem Linux a připojit se k webové aplikaci.  

Další informace o Apache Tomcat najdete na [domovské stránce Apache Tomcat](https://tomcat.apache.org/). 

## <a name="prerequisites"></a>Požadavky
* Vývojový počítač s:
  * [Service Fabric SDK a nástroje](service-fabric-get-started-linux.md).
  * [Docker CE pro Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric CLI](service-fabric-cli.md)

* Registr kontejnerů v registru kontejnerů Azure. Registr kontejnerů můžete vytvořit ve svém předplatném Azure pomocí [portálu Azure](../container-registry/container-registry-get-started-portal.md) nebo [azure cli](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry). 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Vytvoření image Tomcat a spuštění místně
Podle kroků v této části vytvořte bitovou kopii Dockeru založenou na bitové kopii Apache Tomcat a jednoduché webové aplikaci a spusťte ji v kontejneru v místním systému. 
 
1. Klonujte [service fabric začínáme s](https://github.com/Azure-Samples/service-fabric-java-getting-started) úložištěm ukázky Javy ve vývojovém počítači.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Změňte adresáře na ukázkový adresář serveru Apache Tomcat *(service-fabric-java-getting-started/container-apache-tomcat-web-server-sample):*

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Vytvořte soubor Dockeru na základě oficiální [image Tomcat](https://hub.docker.com/_/tomcat/) umístěné v Docker Hubu a ukázce serveru Tomcat. V adresáři *service-fabric-java-getting-started/container-apache-tomcat-web-server-sample* vytvořte soubor s názvem *Dockerfile* (bez přípony). Do souboru *Dockerfile* přidejte následující a uložte změny:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Další informace naleznete v [odkazu na soubor Dockerfile.](https://docs.docker.com/engine/reference/builder/)


4. Spuštěním `docker build` příkazu vytvořte bitovou kopii, na které běží webová aplikace:

   ```bash
   docker build . -t tomcattest
   ```

   Tento příkaz vytvoří novou bitovou kopii pomocí pokynů v Dockerfile, pojmenování (-t značkování) image `tomcattest`. Chcete-li vytvořit image kontejneru, základní image se nejprve stáhne z Docker Hub a aplikace se přidá do něj. 

   Po dokončení příkazu pro sestavení spusťte příkaz `docker images` a zobrazte informace o nové imagi:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Před odesláním registru kontejneru ověřte, zda je kontejnerizovaná aplikace spuštěna místně:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name`pojmenuje kontejner, takže na něj můžete odkazovat pomocí popisný název, nikoli jeho ID.
   * `-p`určuje mapování portů mezi kontejnerem a hostitelským os. 

   > [!Note]
   > Port, který otevřete s parametrem, `-p` by měl být port, na který aplikace Tomcat naslouchá požadavkům. V aktuálním příkladu je konektor nakonfigurovaný v souboru *ApacheTomcat/conf/server.xml* pro naslouchání na portu 8080 pro požadavky HTTP. Tento port je mapován na port 8080 na hostiteli. 

   Další informace o dalších parametrech najdete v [dokumentaci ke spuštění Dockeru](https://docs.docker.com/engine/reference/commandline/run/).

1. Chcete-li kontejner otestovat, otevřete prohlížeč a zadejte jednu z následujících adres URL. Uvidíte variantu "Hello World!" úvodní obrazovka pro každou adresu URL.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Dobrý den, svět / sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Zastavte kontejner a odstraňte jej z vývojového počítače:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Zasunutí bitové kopie Tomcat do registru kontejnerů
Teď, když jste ověřili, že image Tomcat běží v kontejneru ve vývojovém počítači, převeďte ji do úložiště v registru kontejneru. Tento článek používá Azure Container Registry k uložení bitové kopie, ale s některými změnami kroků můžete použít libovolný registr kontejneru, který zvolíte. V tomto článku je název registru považován za *myregistry* a úplný název registru je myregistry.azurecr.io. Změňte tyto vhodně pro váš scénář. 

1. Spusťte `docker login` přihlášení k registru kontejneru pomocí [pověření registru](../container-registry/container-registry-authentication.md).

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

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Sestavení a nasazení aplikace kontejneru Service Fabric
Teď, když jste posunuli image Tomcat do registru kontejneru, můžete vytvořit a nasadit aplikaci kontejneru Service Fabric, která vytáhne image Tomcat z vašeho registru a spustí ji jako kontejnerizovanou službu ve vašem clusteru. 

1. Vytvořte nový adresář mimo místní klon (mimo adresářový strom *service-fabric-java-getting-started).* Přepněte na něj a pomocí Yeoman vytvořte lešení pro aplikaci kontejneru: 

   ```bash
   yo azuresfcontainer 
   ```
   Po zobrazení výzvy zadejte následující hodnoty:

   * Pojmenujte svou aplikaci: ServiceFabricTomcat
   * Název aplikační služby: TomcatService
   * Zadejte název bitové kopie: Zadejte adresu URL image kontejneru v registru kontejneru; například myregistry.azurecr.io/samples/tomcattest.
   * Příkazy: Ponechte toto pole prázdné. Vzhledem k tomu, že tato image má definovaný vstupní bod úloh, není potřeba explicitně zadat vstupní příkazy (příkazy se spouští uvnitř kontejneru, což zajistí zachování provozu kontejneru po spuštění).
   * Počet instancí aplikace kontejneru hosta: 1

   ![Generátor Service Fabric Yeoman pro kontejnery](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. Do manifestu služby *(ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*) přidejte do kořenové značky **ServiceManfest** následující XML a otevřete port, na který vaše aplikace naslouchá požadavkům. Značka **Koncového bodu** deklaruje protokol a port pro koncový bod. Pro tento článek kontejnerizované služby naslouchá na portu 8080: 

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

11. V manifestu aplikace *(ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*) pod značkou **ServiceManifestImport** přidejte následující XML. Nahraďte **accountname** a **heslo** ve **značce RepositoryCredentials** názvem registru kontejneru a heslem potřebným k přihlášení.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   Značka **ContainerHostPolicies** určuje zásady pro aktivaci hostitelů kontejnerů.
    
   * Značka **PortBinding** konfiguruje zásadu mapování portů portů na hostitele. **ContainerPort** atribut je nastavena na 8080, protože kontejner zpřístupňuje port 8080, jak je uvedeno v Dockerfile. **EndpointRef** atribut je nastavena na "endpointTest", koncový bod definovaný v manifestu služby v předchozím kroku. Příchozí požadavky na službu na portu 8080 jsou tedy mapovány na port 8080 v kontejneru. 
   * Značka **RepositoryCredentials** určuje pověření, která kontejner potřebuje k ověření pomocí (privátního) úložiště, ze kterého je bitová kopie vytažena. Tuto zásadu nepotřebujete, pokud bude obrázek stažen z veřejného úložiště.
    

12. Ve složce *ServiceFabricTomcat* se připojte ke clusteru prostředků infrastruktury služby. 

   * Chcete-li se připojit k místnímu clusteru Service Fabric, spusťte:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Chcete-li se připojit k zabezpečenému clusteru Azure, ujistěte se, že klientský certifikát je přítomen jako soubor PEM v adresáři *ServiceFabricTomcat* a spusťte: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     V předchozím příkazu `your-certificate.pem` nahraďte názvem souboru klientského certifikátu. Ve vývojových a testovacích prostředích se certifikát clusteru často používá jako klientský certifikát. Pokud váš certifikát není podepsán svým držitelem, parametr vynechejte. `-no-verify` 
       
     Clusterové certifikáty se obvykle stahují místně jako soubory .pfx. Pokud ještě nemáte certifikát ve formátu PEM, můžete spustit následující příkaz a vytvořit soubor PEM ze souboru PFX:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Pokud váš soubor .pfx není `-passin pass:` chráněn heslem, použijte pro poslední parametr.


13. Spusťte instalační skript uvedený v šabloně a nasaďte aplikaci do clusteru. Skript zkopíruje balíček aplikace do úložiště bitových kopií clusteru, zaregistruje typ aplikace a vytvoří instanci aplikace.

     ```bash
     ./install.sh
     ```

   Po spuštění instalačního skriptu otevřete prohlížeč a přejděte do aplikace Service Fabric Explorer:
    
   * V místním clusteru `http://localhost:19080/Explorer` použijte (nahraďte *localhost* privátní IP adresou virtuálního počítače, pokud používáte Vagrant v Systému Mac OS X).
   * V zabezpečeném clusteru `https://PublicIPorFQDN:19080/Explorer`Azure použijte . 
    
   Rozbalte uzel **Aplikace** a všimněte si, že nyní existuje položka pro váš typ **aplikace, ServiceFabricTomcatType**a další pro první instanci tohoto typu. Může trvat několik minut, než se aplikace plně nasadí, takže buďte trpěliví.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Chcete-li získat přístup k aplikaci na serveru Tomcat, otevřete okno prohlížeče a zadejte některou z následujících adres URL. Pokud jste nasadili do místního clusteru, použijte *localhost* pro *PublicIPorFQDN*. Uvidíte variantu "Hello World!" úvodní obrazovka pro každou adresu URL.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Vyčištění
Pomocí odinstalačního skriptu uvedeného v šabloně odstraňte instanci aplikace z clusteru a zrušte registraci typu aplikace.

```bash
./uninstall.sh
```

Po nahrání image do registru kontejneru můžete odstranit místní image z vývojového počítače:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Další kroky
* Rychlé kroky k dalším funkcím kontejneru Linuxu najdete v článek [Vytvoření první aplikace kontejneru Service Fabric v Linuxu](service-fabric-get-started-containers-linux.md).
* Podrobnější kroky týkající se linuxových kontejnerů načtěte [v kurzu k vytvoření aplikace pro kontejnery linuxu.](service-fabric-tutorial-create-container-images.md)
* Další informace o spouštění [kontejnerů v Service Fabric](service-fabric-containers-overview.md).



---
title: Zabalení a nasazení kontejnerů jako aplikace Service Fabric v Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí Yeomanu vygenerovat definici aplikace Azure Service Fabric a jak aplikaci zabalit.
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: Docker, kontejnery, mikroslužby, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 7d622b834cef31552cac60b359cdd8404592eda9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255553"
---
# <a name="tutorial-package-and-deploy-containers-as-a-service-fabric-application-using-yeoman"></a>Kurz: Zabalení a nasazení kontejnerů jako aplikace Service Fabric pomocí Yeomanu

Tento kurz je druhá část série. V tomto kurzu je pomocí nástroje Yeoman na generování šablon vygenerována definice aplikace Service Fabricu. Tuto aplikaci je pak možné použít k nasazení kontejnerů do Service Fabricu. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nainstalovat Yeoman
> * Vytvořit balíček aplikace pomocí Yeomanu
> * Konfigurovat nastavení v balíčku aplikace pro použití s kontejnery
> * Sestavení aplikace
> * Nasadit a spustit aplikaci
> * Vyčistit aplikaci

## <a name="prerequisites"></a>Požadavky

* Použijeme image kontejneru odeslané do služby Azure Container Registry, které jsme vytvořili v [části 1](service-fabric-tutorial-create-container-images.md) tohoto kurzu.
* Musí být [nastavené](service-fabric-tutorial-create-container-images.md) linuxové vývojové prostředí.

## <a name="install-yeoman"></a>Nainstalovat Yeoman

Service Fabric nabízí nástroje pro generování uživatelského rozhraní, které vám pomůžou vytvořit aplikace z terminálu pomocí generátoru šablon Yeoman. Podle následujících kroků zkontrolujte, že máte generátor šablon Yeoman.

1. Nainstalujte si na počítač NodeJS a NPM. Uživatelé Mac OS X musí použít správce balíčků Homebrew.

    ```bash
    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash
    nvm install node 
    ```
2. Nainstalujte na svém počítači generátor šablon Yeoman z NPM.

    ```bash
    npm install -g yo
    ```
3. Nainstalujte generátor kontejnerů Service Fabric Yeoman.

    ```bash 
    npm install -g generator-azuresfcontainer
    ```

## <a name="package-a-docker-image-container-with-yeoman"></a>Zabalení kontejneru image Dockeru pomocí Yeomana

1. K vytvoření kontejnerové aplikace Service Fabric spusťte v adresáři container-tutorial naklonovaného úložiště následující příkaz.

    ```bash
    yo azuresfcontainer
    ```
2. Zadejte text „TestContainer“ a pojmenujte svoji aplikaci.
3. Zadejte text „azurevotefront“ a pojmenujte svoji aplikační službu.
4. Zadejte cestu k imagi kontejneru v ACR pro front-endové úložiště – například \<acrName>.azurecr.io/azure-vote-front:v1. Pole \<acrName> musí mít stejnou hodnotu jako v předchozím kurzu.
5. Část Commands ponechte prázdnou a stisknutím klávesy Enter přejděte dál.
6. Pro počet instancí zadejte hodnotu 1.

Následující kód zobrazuje vstup a výstup spuštění příkazu yo:

```bash
? Name your application TestContainer
? Name of the application service: azurevotefront
? Input the Image Name: <acrName>.azurecr.io/azure-vote-front:v1
? Commands:
? Number of instances of guest container application: 1
   create TestContainer/TestContainer/ApplicationManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/ServiceManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/config/Settings.xml
   create TestContainer/TestContainer/azurevotefrontPkg/code/Dummy.txt
   create TestContainer/install.sh
   create TestContainer/uninstall.sh
```

Pokud chcete přidat další službu kontejneru do aplikace již vytvořené pomocí Yeomanu, proveďte následující kroky:

1. Změňte adresář o jednu úroveň nad adresářem **TestContainer**, například *./TestContainer*.
2. Spusťte `yo azuresfcontainer:AddService`.
3. Službu pojmenujte azurevoteback.
4. Zadejte cestu k imagi kontejneru pro Redis – alpine:redis.
5. Část Commands ponechte prázdnou a stisknutím klávesy Enter přejděte dál.
6. Jako počet instancí zadejte 1.

Zobrazí se všechny položky pro přidání použité služby:

```bash
? Name of the application service: azurevoteback
? Input the Image Name: alpine:redis
? Commands:
? Number of instances of guest container application: 1
   create TestContainer/azurevotebackPkg/ServiceManifest.xml
   create TestContainer/azurevotebackPkg/config/Settings.xml
   create TestContainer/azurevotebackPkg/code/Dummy.txt
```

Ve zbývající části tohoto kurzu budeme pracovat v adresáři **TestContainer**. Například *./TestContainer/TestContainer*. Obsah tohoto adresáře by měl vypadat takto:

```bash
$ ls
ApplicationManifest.xml azurevotefrontPkg azurevotebackPkg
```

## <a name="configure-the-application-manifest-with-credentials-for-azure-container-registry"></a>Konfigurace manifestu aplikace s přihlašovacími údaji pro Azure Container Registry

Aby mohla služba Service Fabric načítat image kontejneru z Azure Container Registry, je potřeba zadat přihlašovací údaje v souboru **ApplicationManifest.xml**.

Přihlaste se k vaší instanci ACR. Dokončete operaci pomocí příkazu **az acr login**. Uveďte jedinečný název zadaný pro registr kontejneru při jeho vytvoření.

```bash
az acr login --name <acrName>
```

Příkaz po dokončení vrátí zprávu **Login Succeeded** (Přihlášení proběhlo úspěšně).

Pak spuštěním následujícího příkazu získejte heslo vašeho registru kontejneru. Pomocí tohoto hesla ověřuje ACR službu Service Fabric při načítání imagí kontejneru.

```bash
az acr credential show -n <acrName> --query passwords[0].value
```

V souboru **ApplicationManifest.xml** přidejte do elementu **ServiceManifestImport** pro front-endovou službu fragment kódu. Do pole **AccountName** vložte hodnotu z pole **acrName** a v poli **Password** použijte heslo vrácené předchozím příkazem. Úplný soubor **ApplicationManifest.xml** najdete na konci tohoto dokumentu.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="<acrName>" Password="<password>" PasswordEncrypted="false"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Konfigurace komunikace a mapování portu kontejneru na port hostitele

### <a name="configure-communication-port"></a>Konfigurace komunikačního portu

Nakonfigurujte koncový bod HTTP, aby klienti mohli komunikovat s vaší službou. Otevřete soubor *./TestContainer/azurevotefrontPkg/ServiceManifest.xml* a v elementu **ServiceManifest** deklarujte prostředek koncového bodu.  Přidejte protokol, port a název. Pro účely tohoto kurzu služba naslouchá na portu 80. Následující fragment kódu je umístěný v prostředku pod tagem *ServiceManifest*.

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```

Podobně upravte také element Service Manifest pro back-endovou službu. Otevřete soubor *./TestContainer/azurevotebackPkg/ServiceManifest.xml* a v elementu **ServiceManifest** deklarujte prostředek koncového bodu. V tomto kurzu zůstává zachovaná výchozí hodnota pro redis 6379. Následující fragment kódu je umístěný v prostředku pod tagem *ServiceManifest*.

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
  </Endpoints>
</Resources>
```

Pokud zadáte **UriScheme**, koncový bod kontejneru se automaticky zaregistruje ve službě pojmenování Service Fabric, aby byl zjistitelný. Úplný ukázkový soubor ServiceManifest.xml pro back-endovou službu najdete jako příklad na konci tohoto článku.

### <a name="map-container-ports-to-a-service"></a>Mapování portů kontejneru na službu

Aby bylo možné zveřejnit kontejnery v clusteru, je také potřeba vytvořit v souboru ApplicationManifest.xml vazbu na port. Zásada **PortBinding** odkazuje na **koncové body**, které jsme definovali v souborech **ServiceManifest.xml**. Příchozí žádosti na tyto koncové body se namapují na porty kontejneru, které se tady otevřou a vytvoří se na ně vazba. Do souboru **ApplicationManifest.xml** přidejte následující kód k vytvoření vazby mezi porty 80 a 6379 a koncovými body. Úplný soubor **ApplicationManifest.xml** je k dispozici na konci tohoto dokumentu.

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
</ContainerHostPolicies>
```

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
</ContainerHostPolicies>
```

### <a name="add-a-dns-name-to-the-backend-service"></a>Přidání názvu DNS do back-endové služby

Aby mohla služba Service Fabric tento název DNS přiřadit back-endové službě, je potřeba zadat ho v souboru **ApplicationManifest.xml**. Podle následující ukázky přidejte do elementu **Service** atribut **ServiceDnsName**:

```xml
<Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
  <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
    <SingletonPartition/>
  </StatelessService>
</Service>
```

Front-endová služba získává název DNS instance Redis přečtením proměnné prostředí. Tato proměnná prostředí už je definovaná v souboru Dockerfile, který byl použit k vygenerování image Dockeru, není tedy potřeba provádět žádnou akci.

```Dockerfile
ENV REDIS redisbackend.testapp
```

Následující fragment kódu ukazuje, jak front-endový kód Pythonu přebírá proměnnou prostředí popsanou v souboru Dockerfile. Není potřeba provádět žádnou akci.

```python
# Get DNS Name
redis_server = os.environ['REDIS']

# Connect to the Redis store
r = redis.StrictRedis(host=redis_server, port=6379, db=0)
```

V tomto bodě tohoto kurzu je šablona pro aplikaci balíčku služby k dispozici pro nasazení do clusteru. V následujícím kurzu tuto aplikaci nasadíme a spustíme v clusteru Service Fabric.

## <a name="create-a-service-fabric-cluster"></a>Vytvoření clusteru Service Fabric

Pokud chcete nasadit aplikaci do clusteru v Azure, vytvořte si vlastní cluster.

Party Clustery jsou bezplatné, časově omezené clustery Service Fabric hostované v Azure. Jsou provozované týmem Service Fabric a kdokoli na nich může nasazovat aplikace a seznamovat se s platformou. Pokud chcete získat přístup k Party Clusteru, [postupujte podle těchto pokynů](https://aka.ms/tryservicefabric).

K provádění operací správy na zabezpečeném Party Clusteru můžete použít Service Fabric Explorer, rozhraní příkazového řádku nebo PowerShell. Pokud chcete použít Service Fabric Explorer, budete muset z webu Party Clusteru stáhnout soubor PFX a importovat certifikát do svého úložiště certifikátů (Windows nebo Mac) nebo do samotného prohlížeče (Ubuntu). K certifikátům podepsaným svým držitelem z Party Clusteru není žádné heslo.

Pokud chcete provádět operace správy pomocí PowerShellu nebo rozhraní příkazového řádku, budete potřebovat soubor PFX (PowerShell) nebo PEM (rozhraní příkazového řádku). Pokud chcete převést soubor PFX na soubor PEM, spusťte následující příkaz:

```bash
openssl pkcs12 -in party-cluster-1277863181-client-cert.pfx -out party-cluster-1277863181-client-cert.pem -nodes -passin pass:
```

Informace o vytvoření vlastního clusteru najdete v tématu věnovaném [vytvoření clusteru Service Fabric v Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

## <a name="build-and-deploy-the-application-to-the-cluster"></a>Vytvoření a nasazení aplikace do clusteru

Aplikaci můžete nasadit do clusteru Azure pomocí rozhraní příkazového řádku Service Fabric. Pokud na počítači nemáte nainstalované rozhraní příkazového řádku Service Fabric, nainstalujte si ho podle [těchto pokynů](service-fabric-get-started-linux.md#set-up-the-service-fabric-cli).

Připojte se ke clusteru Service Fabric v Azure. Ukázkový koncový bod nahraďte svým vlastním. Koncový bod musí být úplná adresa URL podobná této.

```bash
sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
```

Pomocí instalačního skriptu, který je k dispozici v adresáři **TestContainer**, zkopírujte balíček aplikace do úložiště imagí clusteru, zaregistrujte typ aplikace a vytvořte její instanci.

```bash
./install.sh
```

Otevřete prohlížeč a přejděte do Service Fabric Exploreru na adrese http://lin4hjim3l4.westus.cloudapp.azure.com:19080/Explorer. Rozbalte uzel Aplikace a všimněte si, že obsahuje položku pro váš typ aplikace a další položku pro instanci.

![Service Fabric Explorer][sfx]

Abyste se mohli ke spuštěné aplikaci připojit, otevřete webový prohlížeč a přejděte na adresu URL clusteru – například http://lin0823ryf2he.cloudapp.azure.com:80. Ve webovém uživatelském rozhraní byste měli vidět aplikaci Voting.

![aplikacevoting][votingapp]

## <a name="clean-up"></a>Vyčištění

Pomocí odinstalačního skriptu, který je součástí šablony, odstraňte instanci aplikace z clusteru a zrušte registraci typu aplikace. Vyčištění instance tímto příkazem nějakou dobu trvá a příkaz install.sh nelze spustit ihned po tomto skriptu.

```bash
./uninstall.sh
```

## <a name="examples-of-completed-manifests"></a>Příklady dokončených manifestů

### <a name="applicationmanifestxml"></a>ApplicationManifest.xml

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ApplicationManifest ApplicationTypeName="TestContainerType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotefrontPkg" ServiceManifestVersion="1.0.0"/>
    <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myaccountname" Password="<password>" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
    </ContainerHostPolicies>
        </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotebackPkg" ServiceManifestVersion="1.0.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
          <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
        </ContainerHostPolicies>
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="azurevotefront">
      <StatelessService ServiceTypeName="azurevotefrontType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
    <Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
      <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

### <a name="front-end-servicemanifestxml"></a>Front-end ServiceManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotefrontPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotefrontType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>

   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>acrName.azurecr.io/azure-vote-front:v1</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables>
      </EnvironmentVariables>
   </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>

 </ServiceManifest>
```

### <a name="redis-servicemanifestxml"></a>Redis ServiceManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotebackPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotebackType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>

   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>alpine:redis</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables>
      </EnvironmentVariables>
   </CodePackage>
     <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu bylo zabaleno několik kontejnerů do aplikace Service Fabric pomocí Yeomanu. Tato aplikace pak byla nasazena a spuštěna v clusteru Service Fabric. Dokončili jste následující kroky:

> [!div class="checklist"]
> * Nainstalovat Yeoman
> * Vytvořit balíček aplikace pomocí Yeomanu
> * Konfigurovat nastavení v balíčku aplikace pro použití s kontejnery
> * Sestavení aplikace
> * Nasadit a spustit aplikaci
> * Vyčistit aplikaci

Přejděte na další kurz na téma převzetí služeb při selhání a škálování aplikace v Service Fabricu.

> [!div class="nextstepaction"]
> [Informace o převzetí služeb při selhání a škálování aplikací](service-fabric-tutorial-containers-failover.md)

[votingapp]: ./media/service-fabric-tutorial-deploy-run-containers/votingapp.png
[sfx]: ./media/service-fabric-tutorial-deploy-run-containers/containerspackagetutorialsfx.png
---
title: Vytvoření aplikace Azure Service Fabric Container v systému Linux
description: Vytvoříte svou první aplikaci typu kontejner pro Linux na platformě Azure Service Fabric. Sestavíte image Dockeru s vaší aplikací, nahrajete image do registru kontejneru a sestavíte a nasadíte aplikaci Service Fabric typu kontejner.
ms.topic: conceptual
ms.date: 1/4/2019
ms.custom: devx-track-python
ms.openlocfilehash: 0481cc2d36f7882bbd8eea9b984c3dc388de5dee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96534076"
---
# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Vytvoření první aplikace Service Fabric typu kontejner v Linuxu
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Spuštění existující aplikace v kontejneru Linux v clusteru Service Fabric nevyžaduje žádné změny aplikace. Tento článek vás provede vytvořením image Dockeru obsahující webovou aplikaci Python [Flask](http://flask.pocoo.org/) a jejím nasazením do clusteru Service Fabric. Kontejnerizovanou aplikaci budete také sdílet prostřednictvím služby [Azure Container Registry](../container-registry/index.yml). Tento článek předpokládá základní znalost Dockeru. Informace o Dockeru najdete v článku [Docker Overview](https://docs.docker.com/engine/understanding-docker/) (Přehled Dockeru).

> [!NOTE]
> Tento článek se týká prostředí pro vývoj pro Linux.  Modul runtime clusteru Service Fabric a modul runtime Docker musí být spuštěný ve stejném operačním systému.  V clusteru Windows nemůžete spouštět kontejnery Linux.

## <a name="prerequisites"></a>Předpoklady
* Vývojový počítač s:
  * [Service Fabric SDK a nástroje](service-fabric-get-started-linux.md)
  * [Docker CE pro Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric CLI](service-fabric-cli.md)

* Cluster se systémem Linux se třemi nebo více uzly.

* Registr ve službě Azure Container Registry – [Vytvořte registr kontejneru](../container-registry/container-registry-get-started-portal.md) ve svém předplatném Azure. 

## <a name="define-the-docker-container"></a>Definice kontejneru Dockeru
Sestavte image založenou na [imagi Pythonu](https://hub.docker.com/_/python/), která se nachází na Docker Hubu. 

Zadejte kontejner Dockeru v souboru Dockerfile. Soubor Dockerfile obsahuje pokyny k nastavení prostředí uvnitř kontejneru, načtení aplikace, kterou chcete spustit, a mapování portů. Soubor Dockerfile je vstupem příkazu `docker build`, který vytvoří image. 

Vytvořte prázdný adresář a soubor *Dockerfile* (bez přípony souboru). Do souboru *Dockerfile* přidejte následující a uložte změny:

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Další informace najdete v [referenčních informacích k souboru Dockerfile](https://docs.docker.com/engine/reference/builder/).

## <a name="create-a-basic-web-application"></a>Vytvoření základní webové aplikace
Vytvořte webovou aplikaci Flask, která naslouchá na portu 80 a vrací „Hello World!“. Ve stejném adresáři vytvořte soubor *requirements.txt*. Přidejte do něj následující a uložte změny:
```
Flask
```

Vytvořte také soubor *app.py* a přidejte do něj následující fragment kódu:

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello():

    return 'Hello World!'


if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="login-to-docker-and-build-the-image"></a>Přihlaste se k Docker a sestavte image.

V dalším kroku vytvoříme image, která spustí vaši webovou aplikaci. Při nastavování veřejných imagí z Docker (jako `python:2.7-slim` v našem souboru Dockerfile) je osvědčeným postupem ověřit si účet Docker Hub namísto vytvoření anonymní žádosti o získání dat.

> [!NOTE]
> Při provádění častých anonymních žádostí o přijetí změn se může zobrazit chyba Docker, která je podobná `ERROR: toomanyrequests: Too Many Requests.` nebo `You have reached your pull rate limit.` ověřena z dokovacího centra, aby tyto chyby nedocházelo. Další informace najdete v tématu [Správa veřejného obsahu pomocí Azure Container Registry](../container-registry/buffer-gate-public-content.md) .

Otevřete okno PowerShellu a přejděte do adresáře, který obsahuje soubor Dockerfile. Potom spusťte následující příkazy:

```
docker login
docker build -t helloworldapp .
```

Tento příkaz sestaví novou image podle pokynů ve vašem souboru Dockerfile a pojmenuje ji (označení -t) `helloworldapp`. Pro sestavení image kontejneru se nejprve z Docker Hubu stáhne základní image, do které se přidá aplikace. 

Po dokončení příkazu pro sestavení spusťte příkaz `docker images` a zobrazte informace o nové imagi:

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>Aplikaci spustíte místně.
Než kontejnerizovanou aplikaci nahrajete do registru kontejneru, ověřte, že se spustí místně. 

Spusťte aplikaci s mapováním portu 4000 vašeho počítače na zpřístupněný port 80 kontejneru:

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

Parametr *name* udává název spuštěného kontejneru (namísto ID kontejneru).

Připojte se ke spuštěnému kontejneru. Otevřete webový prohlížeč, který odkazuje na IP adresu vrácenou na portu 4000, například http: \/ /localhost: 4000. V prohlížeči by se měl zobrazit nadpis „Hello World!“.

![Hello World!][hello-world]

Pokud chcete kontejner zastavit, spusťte:

```bash
docker stop my-web-site
```

Odstraňte kontejner z vývojového počítače:

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>Nahrání image do registru kontejneru
Po ověření, že se aplikace spustí v Dockeru, nahrajte image do vašeho registru ve službě Azure Container Registry.

Spusťte `docker login` , abyste se k registru kontejneru přihlásili pomocí [přihlašovacích údajů registru](../container-registry/container-registry-authentication.md).

Následující příklad předá ID a heslo [instančního objektu](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory. Instanční objekt jste k registru mohli přiřadit například pro účely scénáře automatizace. Nebo se můžete přihlásit pomocí uživatelského jména a hesla registru.

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Následující příkaz vytvoří značku, neboli alias, image s plně kvalifikovanou cestou k vašemu registru. Tento příklad umístí image do oboru názvů `samples`, aby se zabránilo nepořádku v kořenovém adresáři registru.

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Nahrajte image do registru kontejneru:

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>Zabalení image Dockeru pomocí Yeomana
Sada Service Fabric SDK pro Linux zahrnuje generátor [Yeoman](https://yeoman.io/), který usnadňuje vytvoření aplikace a přidání image kontejneru. Použijme Yeomana k vytvoření aplikace *SimpleContainerApp* s jediným kontejnerem Dockeru.

Pokud chcete vytvořit aplikaci Service Fabric typu kontejner, otevřete okno terminálu a spusťte příkaz `yo azuresfcontainer`. 

Pojmenujte aplikaci (například `mycontainer`) a aplikační službu (například `myservice`).

Jako název image zadejte adresu URL image kontejneru v registru kontejneru (například myregistry.azurecr.io/samples/helloworldapp). 

Vzhledem k tomu, že tato image má definovaný vstupní bod úloh, není potřeba explicitně zadat vstupní příkazy (příkazy se spouští uvnitř kontejneru, což zajistí zachování provozu kontejneru po spuštění). 

Jako počet instancí zadejte 1.

Zadejte mapování portů v příslušném formátu. V tomto článku je třeba zadat ```80:4000``` mapování portů. Díky tomu jste nakonfigurovali, aby všechny příchozí požadavky přicházející na port 4000 na hostitelském počítači byly přesměrované na port 80 na kontejneru.

![Generátor Service Fabric Yeoman pro kontejnery][sf-yeoman]

## <a name="configure-container-repository-authentication"></a>Konfigurace ověřování úložiště kontejnerů

V tématu [ověřování úložiště kontejnerů](configure-container-repository-credentials.md)se dozvíte, jak nakonfigurovat různé typy ověřování pro stahování imagí kontejneru.

## <a name="configure-isolation-mode"></a>Konfigurace režimu izolace
V případě běhové verze 6,3 je pro kontejnery Linux podporovaná izolace virtuálních počítačů, což pro kontejnery podporuje dva režimy izolace: proces a Hyper-V. V režimu izolace technologie Hyper-V jsou jádra izolovaná mezi jednotlivými kontejnery a hostitelem kontejneru. Izolace Hyper-V se implementuje pomocí prázdných [kontejnerů](https://software.intel.com/en-us/articles/intel-clear-containers-2-using-clear-containers-with-docker). Režim izolace je určen pro clustery systému Linux v `ServicePackageContainerPolicy` elementu v souboru manifestu aplikace. Je možné zadat tyto režimy izolace: `process`, `hyperv` a `default`. Výchozím nastavením je režim izolace procesu. Následující fragment kódu ukazuje, jakým způsobem je režim izolace určený v souboru manifestu aplikace.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0"/>
      <Policies>
        <ServicePackageContainerPolicy Hostname="votefront" Isolation="hyperv">
          <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ServicePackageContainerPolicy>
    </Policies>
  </ServiceManifestImport>
```


## <a name="configure-resource-governance"></a>Konfigurace zásad správného řízení prostředků
[Zásady správného řízení prostředků](service-fabric-resource-governance.md) omezují prostředky, které kontejner může použít na hostiteli. Element `ResourceGovernancePolicy`, který je zadaný v manifestu aplikace, slouží k deklaraci omezení prostředků pro balíček kódu služby. Omezení prostředků je možné nastavit pro tyto prostředky: Memory, MemorySwap, CpuShares (relativní váha CPU), MemoryReservationInMB, BlkioWeight (relativní váha BlockIO). V tomto příkladu balíček služby Guest1Pkg získá jedno jádro na uzlech clusteru, kde je umístěný. Omezení paměti jsou absolutní, takže balíček kódu je omezený na 1024 MB paměti (a má tuto paměť softwarově vyhrazenou). Balíčky kódu (kontejnery a procesy) nejsou schopné přidělit víc paměti, než je toto omezení, a případný pokus o takové přidělení má za následek výjimku z důvodu nedostatku paměti. Aby vynucení omezení prostředků fungovala, musí být omezení paměti zadaná pro všechny balíčky kódu v rámci balíčku služby.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePKg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```




## <a name="configure-docker-healthcheck"></a>Konfigurace dockeru HEALTHCHECK 

Počínaje v6.1 Service Fabric automaticky integruje události [dockeru HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) do sestavy stavu systému. To znamená, že pokud váš kontejner má **HEALTHCHECK** povolený, Service Fabric oznámí stav vždy, když se změní stav kontejneru (nahlášený Dockerem). Pokud *health_status* je *healthy*, v [Service Fabric Exploreru](service-fabric-visualizing-your-cluster.md) se zobrazí sestava stavu **OK**. Pokud *health_status* je *unhealthy*, zobrazí se **UPOZORNĚNÍ**. 

Počínaje nejnovější verzí aktualizace v 6.4 máte možnost určit, že se mají tato hodnocení Docker HEALTHCHECK hlásit jako chyba. Pokud je tato možnost povolená, zobrazí se zpráva o stavu **OK** , když *health_status* je *v pořádku* a zobrazí se **Chyba** , když *health_status* není v *pořádku*.

Instrukce **HEALTHCHECK** ukazující na skutečnou kontrolu prováděnou pro monitorování stavu kontejneru musí být přítomna v souboru Dockerfile použitém při generování image kontejneru.

![Snímek obrazovky zobrazuje podrobnosti o nasazeném balíčku služby NodeServicePackage.][1]

![HealthCheckUnhealthyApp][2]

![HealthCheckUnhealthyDsp][3]

Chování **HEALTHCHECK** pro jednotlivé kontejnery můžete nakonfigurovat zadáním možností **HealthConfig** jako součásti **ContainerHostPolicies** v manifestu aplikace.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true"
              RestartContainerOnUnhealthyDockerHealthStatus="false" 
              TreatContainerUnhealthyStatusAsError="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
Ve výchozím nastavení je *IncludeDockerHealthStatusInSystemHealthReport* nastaveno na **hodnotu true**, hodnota *RestartContainerOnUnhealthyDockerHealthStatus* je nastavena na **hodnotu false** a vlastnost *TreatContainerUnhealthyStatusAsError* je nastavena na **hodnotu false**. 

Pokud je pro *RestartContainerOnUnhealthyDockerHealthStatus* nastavená hodnota **true**, kontejner, který je opakovaně nahlášený ve špatném stavu, se restartuje (potenciálně na jiných uzlech).

Pokud je *TreatContainerUnhealthyStatusAsError* nastavené na **true**, zobrazí se **chybové** zprávy o stavu, když *health_status* kontejneru není v *pořádku*.

Pokud chcete zakázat integraci **HEALTHCHECK** pro celý cluster Service Fabric, musíte nastavit [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) na **false**.

## <a name="deploy-the-application"></a>Nasazení aplikace
Jakmile je aplikace sestavená, můžete ji nasadit do místního clusteru pomocí rozhraní příkazového řádku Service Fabric.

Připojte se k místnímu clusteru služby Service Fabric.

```bash
sfctl cluster select --endpoint http://localhost:19080
```

Pomocí instalačního skriptu, který je součástí šablon v části https://github.com/Azure-Samples/service-fabric-containers/ , zkopírujte balíček aplikace do úložiště imagí clusteru, zaregistrujte typ aplikace a vytvořte instanci aplikace.


```bash
./install.sh
```

Otevřete prohlížeč a přejděte na adresu Service Fabric Explorer na adrese http: \/ /localhost: 19080/Explorer (Pokud používáte Vagrant v Mac OS X, nahraďte localhost privátní IP adresou virtuálního počítače. Rozbalte uzel Aplikace a všimněte si, že už obsahuje položku pro váš typ aplikace a další položku pro první instanci tohoto typu.

Připojte se ke spuštěnému kontejneru. Otevřete webový prohlížeč, který odkazuje na IP adresu vrácenou na portu 4000, například http: \/ /localhost: 4000. V prohlížeči by se měl zobrazit nadpis „Hello World!“.

![Hello World!][hello-world]


## <a name="clean-up"></a>Vyčištění
Pomocí odinstalačního skriptu, který je součástí šablony, odstraňte instanci aplikace z místního vývojového clusteru a zrušte registraci typu aplikace.

```bash
./uninstall.sh
```

Po nahrání image do registru kontejneru můžete odstranit místní image z vývojového počítače:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Kompletní příklad manifestů služby a aplikace Service Fabric
Tady jsou kompletní manifesty aplikace a služby použité v tomto článku.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1. On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>Přidání více služeb do stávající aplikace

Pokud chcete přidat další službu kontejneru do aplikace již vytvořené pomocí Yeomana, proveďte následující kroky:

1. Změňte adresář na kořenovou složku stávající aplikace. Například `cd ~/YeomanSamples/MyApplication`, pokud `MyApplication` je aplikace vytvořená pomocí Yeomanu.
2. Spuštěním příkazu `yo azuresfcontainer:AddService`

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>Konfigurace časového intervalu před vynuceným ukončením kontejneru

Můžete nakonfigurovat časový interval, který určuje, jak dlouho modul runtime počká před odebráním kontejneru po zahájení odstraňování služby (nebo jejího přesunu do jiného uzlu). Konfigurací časového intervalu se do kontejneru odešle příkaz `docker stop <time in seconds>`.  Další podrobnosti najdete v dokumentaci k příkazu [docker stop](https://docs.docker.com/engine/reference/commandline/stop/). Časový interval pro čekání se zadává v části `Hosting`. Následující fragment manifestu clusteru ukazuje nastavení intervalu čekání:


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
}
```

Výchozí časový interval je nastavený na 10 sekund. Vzhledem k tomu, že je tato konfigurace dynamická, časový limit aktualizuje v clusteru upgrade pouze konfigurace. 

## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Konfigurace modulu runtime pro odebrání nepoužívaných imagí kontejneru

Cluster Service Fabric můžete nakonfigurovat tak, aby z uzlu odebral nepoužívané image kontejneru. Tato konfigurace umožňuje znovu získat místo na disku v případě, že je na uzlu příliš mnoho imagí kontejneru. Pokud chcete tuto funkci povolit, aktualizujte část `Hosting` v manifestu clusteru, jak je znázorněno v následujícím fragmentu kódu: 


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "mcr.microsoft.com/windows/servercore|mcr.microsoft.com/windows/nanoserver|mcr.microsoft.com/dotnet/framework/aspnet|..."
          }
          ...
          }
        ]
} 
```

Image, které se nesmí odstranit, můžete zadat v rámci parametru `ContainerImagesToSkip`. 

## <a name="configure-container-image-download-time"></a>Konfigurace doby stahování image kontejneru

Modul runtime Service Fabric pro stažení a extrakci imagí kontejneru přidělí 20 minut. Pro většinu imagí kontejnerů to stačí. U větších imagí nebo při pomalém síťovém připojení může být potřeba prodloužit čas, po který se čeká, než dojde ke zrušení stahování a extrakce imagí. Tento časový limit se nastavuje pomocí atributu **ContainerImageDownloadTimeout** v části **Hosting** manifestu clusteru, jak ukazuje následující fragment kódu:

```json
{
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
}
```


## <a name="set-container-retention-policy"></a>Nastavení zásad uchovávání informací kontejneru

Jako pomoc s diagnostikou selhání spuštění kontejneru Service Fabric (verze 6.1 nebo vyšší) podporuje zachování kontejnerů, které se ukončily nebo které se nepovedlo spustit. Tuto zásadu je možné nastavit v souboru **ApplicationManifest.xml**, jak ukazuje následující fragment kódu:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

Nastavení **ContainersRetentionCount** určuje počet kontejnerů, které se při svém selhání zachovají. Pokud je zadaná hodnota záporná, zachovají se všechny kontejnery, které selhaly. Když atribut **ContainersRetentionCount** není zadaný, nezachovají se žádné kontejnery. Atribut **ContainersRetentionCount** také podporuje parametry aplikace, takže uživatelé mohou zadat různé hodnoty pro testovací a produkční clustery. Při použití této funkce použijte omezení umístění, aby služba kontejneru cílila na konkrétní uzel. Zabrání se tak přesunu služby kontejneru na jiné uzly. Všechny kontejnery zachované pomocí této funkce je nutné ručně odebrat.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>Spuštění démona Dockeru s vlastními argumenty

V modulu runtime Service Fabric verze 6.2 a novější můžete spustit démona Dockeru s vlastními argumenty. Pokud zadáte vlastní argumenty, Service Fabric do modulu Dockeru nepředá žádné další argumenty s výjimkou argumentu `--pidfile`. Proto by se `--pidfile` nemělo předávat jako argument. Kromě toho by tento argument měl umožnit, aby démon Dockeru i nadále naslouchal kanálu s výchozím názvem ve Windows (nebo unixovému soketu domény v Linuxu), aby se zajistila komunikace Service Fabric s démonem. Vlastní argumenty se zadávají v manifestu clusteru v části **Hosting** v rámci části **ContainerServiceArguments**. Příklad je znázorněný v následujícím fragmentu kódu: 
 

```json
{ 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
} 

```

## <a name="next-steps"></a>Další kroky
* Další informace o spouštění [kontejnerů v Service Fabric](service-fabric-containers-overview.md).
* Přečtěte si kurz [Nasazení aplikace .NET v kontejneru](service-fabric-host-app-in-a-container.md).
* Informace o [životním cyklu aplikace](service-fabric-application-lifecycle.md) Service Fabric.
* Prohlédněte si [ukázky kódu kontejneru Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) na GitHubu.

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png

[1]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[2]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png

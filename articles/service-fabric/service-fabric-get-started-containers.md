---
title: Vytvoření aplikace Azure Service Fabric Container
description: Vytvoříte svou první aplikaci typu kontejner pro Windows na platformě Azure Service Fabric. Sestavte image Docker pomocí aplikace Pythonu, nahrajte image do registru kontejneru a potom Sestavte a nasaďte kontejner do Azure Service Fabric.
ms.topic: conceptual
ms.date: 01/25/2019
ms.custom: devx-track-python
ms.openlocfilehash: 197423670ffe05f15fdc5bfd351efdfba33b53cd
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533770"
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Vytvoření první aplikace Service Fabric typu kontejner v systému Windows

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Spuštění existující aplikace v kontejneru Windows v clusteru Service Fabric nevyžaduje žádné změny aplikace. Tento článek vás provede vytvořením image Docker [obsahující webovou aplikaci](http://flask.pocoo.org/) v Pythonu a nasazením do clusteru Azure Service Fabric. Kontejnerizovanou aplikaci budete také sdílet prostřednictvím služby [Azure Container Registry](../container-registry/index.yml). Tento článek předpokládá základní znalost Dockeru. Informace o Dockeru najdete v článku [Docker Overview](https://docs.docker.com/engine/understanding-docker/) (Přehled Dockeru).

> [!NOTE]
> Tento článek se týká prostředí pro vývoj ve Windows.  Modul runtime clusteru Service Fabric a modul runtime Docker musí být spuštěný ve stejném operačním systému.  Kontejnery Windows nelze spouštět v clusteru se systémem Linux.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

* Vývojový počítač s:
  * Visual Studio 2015 nebo Visual Studio 2019.
  * [Service Fabric SDK a nástroje](service-fabric-get-started.md)
  *  Docker pro Windows. [Získejte Docker CE pro Windows (stabilní verze)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Po nainstalování a spuštění Dockeru klikněte pravým tlačítkem myši na ikonu na hlavním panelu a vyberte **Switch to Windows containers** (Přepnout na kontejnery Windows). Tento krok se vyžaduje pro spuštění imagí Dockeru založených na Windows.

* Cluster se systémem Windows se třemi nebo více uzly, které jsou spuštěny v systému Windows Server s kontejnery. 

  V tomto článku se verze (sestavení) Windows serveru s kontejnery běžícími na uzlech clusteru musí shodovat s tím, že se nachází na vašem vývojovém počítači. Důvodem je to, že na svém vývojovém počítači sestavíte image Docker a že existují omezení kompatibility mezi verzemi operačního systému kontejneru a hostitelského operačního systému, na kterém je nasazený. Další informace najdete v tématu [věnovaném systému Windows Server Container OS a kompatibility s hostitelským operačním systémem](#windows-server-container-os-and-host-os-compatibility). 
  
    Pokud chcete zjistit verzi Windows serveru s kontejnery, které potřebujete pro svůj cluster, spusťte `ver` příkaz z příkazového řádku Windows na vašem vývojovém počítači. Před [vytvořením clusteru](service-fabric-cluster-creation-via-portal.md)se podívejte na [systém Windows Server Container OS a kompatibilitu s operačním systémem](#windows-server-container-os-and-host-os-compatibility) .

* Registr ve službě Azure Container Registry – [Vytvořte registr kontejneru](../container-registry/container-registry-get-started-portal.md) ve svém předplatném Azure.

> [!NOTE]
> Nasazení kontejnerů do clusteru Service Fabric se systémem Windows 10 je podporováno.  V [tomto článku](service-fabric-how-to-debug-windows-containers.md) najdete informace o tom, jak nakonfigurovat Windows 10 pro spouštění kontejnerů Windows.

> [!NOTE]
> Service Fabric verze 6,2 a novější podporují nasazení kontejnerů do clusterů se systémem Windows Server verze 1709.

## <a name="define-the-docker-container"></a>Definice kontejneru Dockeru

Sestavte image založenou na [imagi Pythonu](https://hub.docker.com/_/python/), která se nachází na Docker Hubu.

Zadejte kontejner Dockeru v souboru Dockerfile. Soubor Dockerfile obsahuje pokyny k nastavení prostředí uvnitř kontejneru, načtení aplikace, kterou chcete spustit, a mapování portů. Soubor Dockerfile je vstupem příkazu `docker build`, který vytvoří image.

Vytvořte prázdný adresář a soubor *Dockerfile* (bez přípony souboru). Do souboru *Dockerfile* přidejte následující a uložte změny:

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Další informace najdete v [referenčních informacích k souboru Dockerfile](https://docs.docker.com/engine/reference/builder/).

## <a name="create-a-basic-web-application"></a>Vytvoření základní webové aplikace
Vytvořte webovou aplikaci Flask, která naslouchá na portu 80 a vrací `Hello World!`. Ve stejném adresáři vytvořte soubor *requirements.txt*. Přidejte do něj následující a uložte změny:
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

<a id="Build-Containers"></a>

## <a name="login-to-docker-and-build-the-image"></a>Přihlaste se k Docker a sestavte image.

V dalším kroku vytvoříme image, která spustí vaši webovou aplikaci. Při nastavování veřejných imagí z Docker (jako `python:2.7-windowsservercore` v našem souboru Dockerfile) je osvědčeným postupem ověřit si účet Docker Hub namísto vytvoření anonymní žádosti o získání dat.

> [!NOTE]
> Při provádění častých anonymních žádostí o přijetí změn se může zobrazit chyba Docker, která je podobná `ERROR: toomanyrequests: Too Many Requests.` nebo `You have reached your pull rate limit.` ověřena z dokovacího centra, aby tyto chyby nedocházelo. Další informace najdete v tématu [Správa veřejného obsahu pomocí Azure Container Registry](../container-registry/buffer-gate-public-content.md) .

Otevřete okno PowerShellu a přejděte do adresáře, který obsahuje soubor Dockerfile. Potom spusťte následující příkazy:

```
docker login
docker build -t helloworldapp .
```

Tento příkaz sestaví novou image podle pokynů ve vašem souboru Dockerfile a pojmenuje ji (označení -t) `helloworldapp`. Pro sestavení image kontejneru se nejprve z Docker Hubu stáhne základní image, do které se přidá aplikace. 

Po dokončení příkazu pro sestavení spusťte příkaz `docker images` a zobrazte informace o nové imagi:

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>Aplikaci spustíte místně.
Než image nahrajete do registru kontejneru, ověřte ji místně. 

Spusťte aplikaci:

```
docker run -d --name my-web-site helloworldapp
```

Parametr *name* udává název spuštěného kontejneru (namísto ID kontejneru).

Po spuštění kontejneru vyhledejte jeho IP adresu, abyste se ke spuštěnému kontejneru mohli připojit z prohlížeče:
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Pokud tento příkaz nevrátí žádnou hodnotu, spusťte následující příkaz a zkontrolujte prvek **NetworkSettings** -> **Networks** pro IP adresu:
```
docker inspect my-web-site
```

Připojte se ke spuštěnému kontejneru. Otevřete webový prohlížeč, který odkazuje na vrácenou IP adresu, například http: \/ /172.31.194.61. V prohlížeči by se měl zobrazit nadpis „Hello World!“.

Pokud chcete kontejner zastavit, spusťte:

```
docker stop my-web-site
```

Odstraňte kontejner z vývojového počítače:

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>Nahrání image do registru kontejneru

Po ověření, že se kontejner spustí na vývojovém počítači, nahrajte image do vašeho registru ve službě Azure Container Service.

Spusťte ``docker login`` , abyste se k registru kontejneru přihlásili pomocí [přihlašovacích údajů registru](../container-registry/container-registry-authentication.md).

Následující příklad předá ID a heslo [instančního objektu](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory. Instanční objekt jste k registru mohli přiřadit například pro účely scénáře automatizace. Nebo se můžete přihlásit pomocí uživatelského jména a hesla registru.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Následující příkaz vytvoří značku, neboli alias, image s plně kvalifikovanou cestou k vašemu registru. Tento příklad umístí image do oboru názvů ```samples```, aby se zabránilo nepořádku v kořenovém adresáři registru.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Nahrajte image do registru kontejneru:

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>Vytvoření kontejnerizované služby v sadě Visual Studio
Sada Service Fabric SDK a nástroje poskytují šablonu služby, která vám pomůže s vytvořením kontejnerizované aplikace.

1. Spusťte Visual Studio. Vyberte **soubor**  >  **Nový**  >  **projekt**.
2. Vyberte **Aplikace Service Fabric**, pojmenujte ji MyFirstContainer a klikněte na **OK**.
3. Ze seznamu **šablon služeb** vyberte **Kontejner**.
4. Do pole **Název image** zadejte „myregistry.azurecr.io/samples/helloworldapp“, tedy image, kterou jste nahráli do úložiště kontejnerů.
5. Zadejte název služby a klikněte na **OK**.

## <a name="configure-communication"></a>Konfigurace komunikace
Kontejnerizovaná služba potřebuje koncový bod pro komunikaci. Do souboru ServiceManifest.xml přidejte element `Endpoint` s protokolem, portem a typem. V tomto příkladu se používá pevný port 8081. Pokud port není zadaný, zvolí se v rozsahu portů aplikace náhodně. 

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```
> [!NOTE]
> Další koncové body pro službu lze přidat deklarováním dalších prvků koncového bodu s použitelnými hodnotami vlastností. Každý port může deklarovat jenom jednu hodnotu protokolu.

Když Service Fabric definuje koncový bod, publikuje ho ve službě pojmenování. Ostatní služby spuštěné v clusteru mohou tento kontejner vyhledat. Ke komunikaci mezi kontejnery můžete také využít [reverzní proxy server](service-fabric-reverseproxy.md). Komunikace se provede tak, že se reverznímu proxy serveru poskytne port pro naslouchání HTTP a název služeb, se kterými chcete komunikovat, jako proměnné prostředí.

Služba naslouchá na určitém portu (v tomto příkladu je to 8081). Když se aplikace nasadí do clusteru v Azure, běží cluster i aplikace na pozadí služby Azure Load Balancer. Port aplikace ve službě Azure Load Balancer musí být otevřený, aby příchozí přenosy měly ke službě přístup.  Tento port můžete otevřít ve službě Azure Load Balancer pomocí [skriptu PowerShellu](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) nebo na webu [Azure Portal](https://portal.azure.com).

## <a name="configure-and-set-environment-variables"></a>Konfigurace a nastavení proměnných prostředí
Proměnné prostředí je možné zadat pro každý balíček kódu v manifestu služby. Tato funkce je dostupná pro všechny služby, bez ohledu na to, jestli jsou nasazené jako kontejnery, procesy nebo spustitelné soubory typu Host. Hodnoty proměnných prostředí můžete přepsat v manifestu aplikace, nebo je můžete zadat v průběhu nasazení jako parametry aplikace.

Následující fragment kódu XML manifestu služby ukazuje příklad toho, jak zadat proměnné prostředí pro balíček kódu:
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

Tyto proměnné prostředí je možné přepsat v manifestu aplikace:

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <EnvironmentOverrides CodePackageRef="FrontendService.Code">
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>Konfigurace mapování portu kontejneru na port hostitele a zjišťování mezi kontejnery
Nakonfigurujte port hostitele používaný ke komunikaci s kontejnerem. Vazba portu mapuje port, na kterém služba naslouchá uvnitř kontejneru, na port na hostiteli. Přidejte element `PortBinding` do `ContainerHostPolicies` v souboru ApplicationManifest.xml. Pro účely tohoto článku je `ContainerPort` nastaven na 80 (kontejner zpřístupňuje port 80, jak je uvedené v souboru Dockerfile) a `EndpointRef` je „Guest1TypeEndpoint“ (koncový bod dříve definovaný v manifestu služby). Příchozí požadavky na službu na portu 8081 se mapují na port 80 v kontejneru.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```
> [!NOTE]
> Další PortBindings pro službu lze přidat deklarováním dalších prvků PortBinding s použitelnými hodnotami vlastností.

## <a name="configure-container-repository-authentication"></a>Konfigurace ověřování úložiště kontejnerů

V tématu [ověřování úložiště kontejnerů](configure-container-repository-credentials.md)se dozvíte, jak nakonfigurovat různé typy ověřování pro stahování imagí kontejneru.

## <a name="configure-isolation-mode"></a>Konfigurace režimu izolace
Systém Windows podporuje pro kontejnery dva režimy izolace: procesy a Hyper-V. V režimu izolace procesů všechny kontejnery spuštěné na stejném hostitelském počítači sdílejí jádro s hostitelem. V režimu izolace Hyper-V se jádra pro jednotlivé kontejnery Hyper-V a hostitele kontejneru izolují. Režim izolace určuje element `ContainerHostPolicies` v souboru manifestu aplikace. Je možné zadat tyto režimy izolace: `process`, `hyperv` a `default`. Výchozím nastavením je režim izolace procesů na hostitelích se systémem Windows Server. V hostitelích s Windows 10 se podporuje jenom režim izolace Hyper-V, takže se kontejner spouští v režimu izolace technologie Hyper-V bez ohledu na nastavení režimu izolace. Následující fragment kódu ukazuje, jakým způsobem je režim izolace určený v souboru manifestu aplikace.

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > Režim izolace hyperv je k dispozici ve skladových položkách Azure Ev3 a Dv3 s podporou vnořené virtualizace. 
   >
   >

## <a name="configure-resource-governance"></a>Konfigurace zásad správného řízení prostředků
[Zásady správného řízení prostředků](service-fabric-resource-governance.md) omezují prostředky, které kontejner může použít na hostiteli. Element `ResourceGovernancePolicy`, který je zadaný v manifestu aplikace, slouží k deklaraci omezení prostředků pro balíček kódu služby. Omezení prostředků je možné nastavit pro tyto prostředky: Memory, MemorySwap, CpuShares (relativní váha CPU), MemoryReservationInMB, BlkioWeight (relativní váha BlockIO). V tomto příkladu balíček služby Guest1Pkg získá jedno jádro na uzlech clusteru, kde je umístěný. Omezení paměti jsou absolutní, takže balíček kódu je omezený na 1024 MB paměti (a má tuto paměť softwarově vyhrazenou). Balíčky kódu (kontejnery a procesy) nejsou schopné přidělit víc paměti, než je toto omezení, a případný pokus o takové přidělení má za následek výjimku z důvodu nedostatku paměti. Aby vynucení omezení prostředků fungovala, musí být omezení paměti zadaná pro všechny balíčky kódu v rámci balíčku služby.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
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

![Snímek obrazovky zobrazuje podrobnosti o nasazeném balíčku služby NodeServicePackage.][3]

![HealthCheckUnhealthyApp][4]

![HealthCheckUnhealthyDsp][5]

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

## <a name="deploy-the-container-application"></a>Nasazení aplikace typu kontejner
Uložte všechny provedené změny a sestavte aplikaci. Pokud chcete aplikaci publikovat, klikněte pravým tlačítkem na **MyFirstContainer** v Průzkumníku řešení a vyberte **Publikovat**.

Do pole **Koncový bod připojení** zadejte koncový bod správy pro příslušný cluster. Například, `containercluster.westus2.cloudapp.azure.com:19000`. Koncový bod připojení klienta najdete na kartě Přehled pro váš cluster na webu [Azure Portal](https://portal.azure.com).

Klikněte na **Publikovat**.

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) je webový nástroj pro kontrolu a správu aplikací a uzlů v clusteru Service Fabric. Otevřete prohlížeč, přejděte na adresu `http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/` a postupujte podle pokynů k nasazení aplikace. Aplikace se nasadí, ale bude v chybovém stavu, dokud se image nestáhne na uzlech clusteru (což v závislosti na velikosti image může nějakou dobu trvat): ![Chyba][1]

Aplikace je připravena, když je ve stavu ```Ready```: ![Připraveno][2]

Otevřete prohlížeč a přejděte na adresu `http://containercluster.westus2.cloudapp.azure.com:8081`. V prohlížeči by se měl zobrazit nadpis „Hello World!“.

## <a name="clean-up"></a>Vyčištění

Za spuštěný cluster se vám stále účtují poplatky, proto zvažte [odstranění clusteru](./service-fabric-tutorial-delete-cluster.md).

Po nahrání image do registru kontejneru můžete odstranit místní image z vývojového počítače:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="windows-server-container-os-and-host-os-compatibility"></a>Systém Windows Server Container OS a kompatibilita s hostitelským operačním systémem

Kontejnery Windows serveru nejsou kompatibilní napříč všemi verzemi hostitelského operačního systému. Příklad:
 
- Kontejnery Windows serveru sestavené pomocí Windows serveru verze 1709 nefungují na hostiteli se systémem Windows Server verze 2016. 
- Kontejnery Windows serveru vytvořené pomocí systému Windows Server 2016 fungují v režimu izolace technologie Hyper-V pouze v hostiteli se systémem Windows Server verze 1709. 
- Pomocí kontejnerů Windows serveru vytvořených pomocí systému Windows Server 2016 může být nutné zajistit, aby revize operačního systému v kontejneru a hostitelském operačním systému byla stejná při spuštění v režimu izolace procesu na hostiteli se systémem Windows Server 2016.
 
Další informace najdete v tématu [Kompatibilita verzí kontejnerů Windows](/virtualization/windowscontainers/deploy-containers/version-compatibility).

Při sestavování a nasazování kontejnerů do clusteru Service Fabric zvažte kompatibilitu s hostitelským operačním systémem a vaším kontejnerovým operačním systémem. Příklad:

- Ujistěte se, že jste nasadili kontejnery s operačním systémem kompatibilním s operačním systémem na uzlech clusteru.
- Zajistěte, aby byl režim izolace zadaný pro vaši aplikaci kontejneru konzistentní s podporou pro kontejnerový operační systém na uzlu, na kterém je nasazený.
- Vezměte v úvahu, jak může mít upgrade operačního systému na uzly nebo kontejnery clusteru vliv na jejich kompatibilitu. 

Doporučujeme, abyste se ujistili, že se kontejnery na Service Fabricovém clusteru správně nasazují v následujících postupech:

- Pomocí explicitního označování imagí s imagemi Docker určete verzi operačního systému Windows Server, ze které je kontejner sestavený. 
- Používejte [označení operačního systému](#specify-os-build-specific-container-images) v souboru manifestu aplikace, abyste měli jistotu, že je vaše aplikace kompatibilní napříč různými verzemi a upgrady Windows serveru.

> [!NOTE]
> Pomocí Service Fabric verze 6,2 a novější můžete nasazovat kontejnery založené na systému Windows Server 2016 místně na hostiteli s Windows 10. V systému Windows 10 jsou kontejnery spouštěny v režimu izolace technologie Hyper-V bez ohledu na režim izolace nastavený v manifestu aplikace. Další informace najdete v tématu [Konfigurace režimu izolace](#configure-isolation-mode).   
>
 
## <a name="specify-os-build-specific-container-images"></a>Zadání imagí kontejneru pro konkrétní sestavení operačního systému 

Kontejnery Windows serveru nemusí být kompatibilní napříč různými verzemi operačního systému. Například kontejnery Windows serveru sestavené pomocí Windows serveru 2016 nefungují na Windows serveru verze 1709 v režimu izolace procesů. Proto pokud jsou uzly clusteru aktualizovány na nejnovější verzi, služba kontejneru vytvořená pomocí předchozích verzí operačního systému může selhat. Chcete-li obejít tento postup s verzí 6,1 modulu runtime a novějším, Service Fabric podporuje určení více imagí operačního systému na kontejner a jejich označení s verzemi sestavení operačního systému v manifestu aplikace. Verzi buildu operačního systému můžete získat spuštěním `winver` příkazu na příkazovém řádku systému Windows. Nejprve aktualizujte manifesty aplikací a zadejte přepsání image pro jednotlivé verze operačního systému a teprve potom aktualizujte operační systém na uzlech. Následující fragment kódu ukazuje, jak v manifestu aplikace **ApplicationManifest.xml** zadat několik imagí kontejneru:


```xml
      <ContainerHostPolicies> 
         <ImageOverrides> 
           <Image Name="myregistry.azurecr.io/samples/helloworldappDefault" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1701" Os="14393" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1709" Os="16299" /> 
         </ImageOverrides> 
      </ContainerHostPolicies> 
```
Verze sestavení pro Windows Server 2016 je 14393 a verze sestavení pro Windows Server verze 1709 je 16299. Manifest služby i nadále určuje jenom jednu image pro každou službu kontejneru, jak ukazuje následující kód:

```xml
<ContainerHost>
    <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName> 
</ContainerHost>
```

   > [!NOTE]
   > Funkce označování verze sestavení operačního systému jsou dostupné jenom pro Service Fabric ve Windows.
   >

Pokud základním operačním systémem virtuálního počítače je sestavení 16299 (verze 1709), Service Fabric vybere image kontejneru, která odpovídá této verzi Windows Serveru. Pokud manifest aplikace obsahuje kromě označených imagí kontejneru také neoznačenou image, Service Fabric bude předpokládat, že tato neoznačená image funguje napříč verzemi. Image kontejnerů označujte explicitně, abyste se vyhnuli problémům během upgradování.

Neoznačená image kontejneru bude fungovat jako přepsání image kontejneru uvedené v souboru ServiceManifest. Takže image myregistry.azurecr.io/samples/helloworldappDefault přepíše název image myregistry.azurecr.io/samples/helloworldapp v souboru ServiceManifest.

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Kompletní příklad manifestů služby a aplikace Service Fabric
Tady jsou kompletní manifesty aplikace a služby použité v tomto článku.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this
         application type is created. You can also create one or more instances of service type using the
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="configure-time-interval-before-container-is-force-terminated"></a>Konfigurace časového intervalu před vynuceným ukončením kontejneru

Můžete nakonfigurovat časový interval, který určuje, jak dlouho modul runtime počká před odebráním kontejneru po zahájení odstraňování služby (nebo jejího přesunu do jiného uzlu). Konfigurací časového intervalu se do kontejneru odešle příkaz `docker stop <time in seconds>`.  Další podrobnosti najdete v dokumentaci k příkazu [docker stop](https://docs.docker.com/engine/reference/commandline/stop/). Časový interval pro čekání se zadává v části `Hosting`. `Hosting`Oddíl lze přidat při vytváření clusteru nebo později v upgradu konfigurace. Následující fragment manifestu clusteru ukazuje nastavení intervalu čekání:

```json
"fabricSettings": [
    ...,
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
]
```
Výchozí časový interval je nastavený na 10 sekund. Vzhledem k tomu, že je tato konfigurace dynamická, časový limit aktualizuje v clusteru upgrade pouze konfigurace. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Konfigurace modulu runtime pro odebrání nepoužívaných imagí kontejneru

Cluster Service Fabric můžete nakonfigurovat tak, aby z uzlu odebral nepoužívané image kontejneru. Tato konfigurace umožňuje znovu získat místo na disku v případě, že je na uzlu příliš mnoho imagí kontejneru. Chcete-li povolit tuto funkci, aktualizujte část [hostování](service-fabric-cluster-fabric-settings.md#hosting) v manifestu clusteru, jak je znázorněno v následujícím fragmentu kódu: 


```json
"fabricSettings": [
    ...,
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
]
```

Image, které se nesmí odstranit, můžete zadat v rámci parametru `ContainerImagesToSkip`.  


## <a name="configure-container-image-download-time"></a>Konfigurace doby stahování image kontejneru

Modul runtime Service Fabric pro stažení a extrakci imagí kontejneru přidělí 20 minut. Pro většinu imagí kontejnerů to stačí. U větších imagí nebo při pomalém síťovém připojení může být potřeba prodloužit čas, po který se čeká, než dojde ke zrušení stahování a extrakce imagí. Tento časový limit se nastavuje pomocí atributu **ContainerImageDownloadTimeout** v části **Hosting** manifestu clusteru, jak ukazuje následující fragment kódu:

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
    }
]
```


## <a name="set-container-retention-policy"></a>Nastavení zásad uchovávání informací kontejneru

Jako pomoc s diagnostikou selhání spuštění kontejneru Service Fabric (verze 6.1 nebo vyšší) podporuje zachování kontejnerů, které se ukončily nebo které se nepovedlo spustit. Tuto zásadu je možné nastavit v souboru **ApplicationManifest.xml**, jak ukazuje následující fragment kódu:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

Nastavení **ContainersRetentionCount** určuje počet kontejnerů, které se při svém selhání zachovají. Pokud je zadaná hodnota záporná, zachovají se všechny kontejnery, které selhaly. Když atribut **ContainersRetentionCount** není zadaný, nezachovají se žádné kontejnery. Atribut **ContainersRetentionCount** také podporuje parametry aplikace, takže uživatelé mohou zadat různé hodnoty pro testovací a produkční clustery. Při použití této funkce použijte omezení umístění, aby služba kontejneru cílila na konkrétní uzel. Zabrání se tak přesunu služby kontejneru na jiné uzly. Všechny kontejnery zachované pomocí této funkce je nutné ručně odebrat.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>Spuštění démona Dockeru s vlastními argumenty

V modulu runtime Service Fabric verze 6.2 a novější můžete spustit démona Dockeru s vlastními argumenty. Pokud zadáte vlastní argumenty, Service Fabric do modulu Dockeru nepředá žádné další argumenty s výjimkou argumentu `--pidfile`. Proto by se `--pidfile` nemělo předávat jako argument. Kromě toho by tento argument měl umožnit, aby démon Dockeru i nadále naslouchal kanálu s výchozím názvem ve Windows (nebo unixovému soketu domény v Linuxu), aby se zajistila komunikace Service Fabric s démonem. Vlastní argumenty se předávají v manifestu clusteru v části **Hosting** v rámci části **ContainerServiceArguments**, jak ukazuje následující fragment kódu: 
 

```json
"fabricSettings": [
    ...,
    { 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
    } 
]
```

## <a name="next-steps"></a>Další kroky
* Další informace o spouštění [kontejnerů v Service Fabric](service-fabric-containers-overview.md).
* Přečtěte si kurz [Nasazení aplikace .NET v kontejneru](service-fabric-host-app-in-a-container.md).
* Informace o [životním cyklu aplikace](service-fabric-application-lifecycle.md) Service Fabric.
* Prohlédněte si [ukázky kódu kontejneru Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) na GitHubu.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[4]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[5]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png

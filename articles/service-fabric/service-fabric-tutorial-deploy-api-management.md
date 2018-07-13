---
title: Integrace služby API Management se Service Fabric v Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak rychle začít se službou Azure API Management a Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 1b0588e25c0d156080a2e879185b76714d8691b2
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113370"
---
# <a name="tutorial-integrate-api-management-with-service-fabric-in-azure"></a>Kurz: Integrace služby API Management se Service Fabric v Azure

Tento kurz je čtvrtou částí série.  Nasazení služby Azure API Management s využitím Service Fabric je pokročilý scénář.  Služba API Management je užitečná v případě, že potřebujete publikovat rozhraní API s bohatou sadou pravidel směrování pro vaše back-end služby Service Fabric. Cloudové aplikace obvykle potřebují front-end bránu, která poskytuje jediný bod příjmu příchozího přenosu od uživatelů, zařízení nebo dalších aplikací. V Service Fabric může být brána bezstavová služba navržená pro příjem provozu, například aplikace ASP.NET Core, služba Event Hubs, služba IoT Hub nebo služba Azure API Management.

V tomto kurzu se dozvíte, jak s využitím Service Fabric nastavit službu [Azure API Management](../api-management/api-management-key-concepts.md) pro směrování provozu do back-end služby v Service Fabric.  Po dokončení budete mít nasazenou službu API Management ve virtuální síti a nakonfigurovanou operaci rozhraní API pro odesílání provozu do back-end bezstavové služby. Další informace o scénářích služby Azure API Management s využitím Service Fabric najdete v článku [Přehled](service-fabric-api-management-overview.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nasadit službu API Management
> * Nakonfigurovat službu API Management
> * Vytvořit operaci rozhraní API
> * Nakonfigurovat zásadu back-endu
> * Přidat rozhraní API do produktu

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného [clusteru s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) nebo [clusteru s Linuxem](service-fabric-tutorial-create-vnet-and-linux-cluster.md) v Azure pomocí šablony
> * [Horizontální snížení nebo navýšení kapacity clusteru](service-fabric-tutorial-scale-cluster.md)
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * Nasazení služby API Management s využitím Service Fabric

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte [modul Azure PowerShellu verze 4.1 nebo vyšší](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) nebo [Azure CLI 2.0](/cli/azure/install-azure-cli).
* Vytvořte zabezpečený [cluster s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) nebo [cluster s Linuxem](service-fabric-tutorial-create-vnet-and-linux-cluster.md) v Azure.
* Pokud nasadíte cluster s Windows, nastavte vývojové prostředí ve Windows. Nainstalujte sadu [Visual Studio 2017](http://www.visualstudio.com) a sady funkcí **Vývoj pro Azure**, **Vývoj pro ASP.NET a web** a **Vývoj multiplatformních aplikací pomocí rozhraní .NET Core**.  Potom nastavte [vývojové prostředí .NET](service-fabric-get-started.md).
* Pokud nasadíte cluster s Linuxem, nastavte vývojové prostředí Java v [Linuxu](service-fabric-get-started-linux.md) nebo [MacOS](service-fabric-get-started-mac.md).  Nainstalujte [Service Fabric CLI](service-fabric-cli.md).

## <a name="network-topology"></a>Síťová topologie

Když teď máte v Azure zabezpečený [cluster s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) nebo [cluster s Linuxem](service-fabric-tutorial-create-vnet-and-linux-cluster.md), nasaďte službu API Management do virtuální sítě v podsíti a skupině zabezpečení sítě určených pro službu API Management. Pro účely tohoto kurzu je šablona Resource Manageru pro službu API Management předkonfigurovaná tak, aby používala názvy virtuální sítě, podsítě a skupiny zabezpečení sítě, které jste nastavili v předchozím [kurzu clusteru s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) nebo [kurzu clusteru s Linuxem](service-fabric-tutorial-create-vnet-and-linux-cluster.md). V tomto kurzu se do Azure nasadí následující topologie, ve které jsou služba API Management a Service Fabric v podsítích stejné virtuální sítě:

 ![Popisek obrázku][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Přihlášení k Azure a výběr předplatného

Před spouštěním příkazů Azure se přihlaste ke svému účtu Azure a vyberte své předplatné.

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Nasazení back-end služby Service Fabric

Než ve službě API Management nakonfigurujete směrování provozu do back-end služby Service Fabric, potřebujete nejprve spuštěnou službu, která bude požadavky přijímat.  Pokud jste předtím vytvořili [cluster s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md), nasaďte službu Service Fabric v .NET.  Pokud jste předtím vytvořili [cluster s Linuxem](service-fabric-tutorial-create-vnet-and-linux-cluster.md), nasaďte službu Service Fabric v Javě.

### <a name="deploy-a-net-service-fabric-service"></a>Nasazení služby Service Fabric v .NET

Pro účely tohoto kurzu vytvořte základní spolehlivou bezstavovou službu ASP.NET Core s použitím výchozí šablony projektu webového rozhraní API. Tím se pro vaši službu vytvoří koncový bod HTTP, který zveřejníte prostřednictvím služby Azure API Management.

Spusťte sadu Visual Studio jako správce a vytvořte službu ASP.NET Core:

 1. V sadě Visual Studio vyberte Soubor -> Nový projekt.
 2. Vyberte šablonu aplikace Service Fabric v části Cloud a pojmenujte aplikaci **ApiApplication**.
 3. Vyberte šablonu bezstavové služby ASP.NET Core a pojmenujte projekt **WebApiService**.
 4. Vyberte šablonu projektu webového rozhraní API v ASP.NET Core 2.0.
 5. Po vytvoření projektu otevřete soubor `PackageRoot\ServiceManifest.xml` a z konfigurace prostředku koncového bodu odeberte atribut `Port`:

    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    Odebrání portu umožní platformě Service Fabric určit port dynamicky z rozsahu portů aplikace otevřených prostřednictvím skupiny zabezpečení sítě v šabloně Resource Manageru pro cluster. Tím se umožní příjem provozu ze služby API Management.

 6. Stisknutím klávesy F5 v sadě Visual Studio ověřte, že je webové rozhraní API místně dostupné.

    Otevřete Service Fabric Explorer, projděte hierarchií ke konkrétní instanci služby ASP.NET Core a zobrazte základní adresu, na které služba naslouchá. Přidejte k základní adrese `/api/values` a otevřete ji v prohlížeči. Tím se v šabloně webového rozhraní API vyvolá metoda Get v kontroleru ValuesController. Metoda vrátí výchozí odpověď poskytovanou šablonou, což je pole JSON obsahující dva řetězce:

    ```json
    ["value1", "value2"]`
    ```

    Toto je koncový bod, který zveřejníte prostřednictvím služby API Management v Azure.

 7. Nakonec aplikaci nasaďte do svého clusteru v Azure. V sadě Visual Studio klikněte pravým tlačítkem na projekt aplikace a vyberte **Publikovat**. Zadejte koncový bod svého clusteru (například `mycluster.southcentralus.cloudapp.azure.com:19000`), aby se aplikace nasadila do vašeho clusteru Service Fabric v Azure.

Ve vašem clusteru Service Fabric v Azure by teď měla být spuštěná bezstavová služba ASP.NET Core s názvem `fabric:/ApiApplication/WebApiService`.

### <a name="create-a-java-service-fabric-service"></a>Vytvoření služby Service Fabric v Javě

Pro účely tohoto kurzu nasaďte základní webový server, který vrací zprávy zpět uživateli. Ukázková aplikace serveru odezvy obsahuje koncový bod HTTP pro vaši službu, který zveřejníte prostřednictvím služby Azure API Management.

1. Naklonujte úvodní ukázky v Javě.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   cd service-fabric-java-getting-started/reliable-services-actor-sample
   ```

2. Upravte soubor *Services/EchoServer/EchoServer1.0/EchoServerApplication/EchoServerPkg/ServiceManifest.xml*. Aktualizujte koncový bod tak, aby služba naslouchala na portu 8081.

   ```xml
   <Endpoint Name="WebEndpoint" Protocol="http" Port="8081" />
   ```

3. Uložte soubor *ServiceManifest.xml* a pak sestavte aplikaci EchoServer1.0.

   ```bash
   cd Services/EchoServer/EchoServer1.0/
   gradle
   ```

4. Nasaďte aplikaci do clusteru.

   ```bash
   cd Scripts
   sfctl cluster select --endpoint https://mycluster.southcentralus.cloudapp.azure.com:19080 --pem <full_path_to_pem_on_dev_machine> --no-verify
   ./install.sh
   ```

   Ve vašem clusteru Service Fabric v Azure by teď měla být spuštěná bezstavová služba v Javě s názvem `fabric:/EchoServerApplication/EchoServerService`.

5. Otevřete prohlížeč a zadejte http://mycluster.southcentralus.cloudapp.azure.com:8081/getMessage. Měla by se zobrazit zpráva „[version 1.0]Hello World!!!“. .

## <a name="download-and-understand-the-resource-manager-templates"></a>Stažení a pochopení šablon Resource Manageru

Stáhněte a uložte následující šablony Resource Manageru a soubor parametrů:

* [network-apim.json][network-arm]
* [network-apim.parameters.json][network-parameters-arm]
* [apim.json][apim-arm]
* [apim.parameters.json][apim-parameters-arm]

Šablona *network-apim.json* do virtuální sítě, ve které je nasazený cluster Service Fabric, nasadí novou podsíť a skupinu zabezpečení sítě.

Následující části popisují prostředky definované šablonou *apim.json*. Další informace najdete na odkazech na referenční dokumentaci k šabloně v každé části. Konfigurovatelné parametry definované v souboru parametrů *apim.parameters.json* se nastaví v pozdější části tohoto článku.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) popisuje instanci služby API Management: název, skladovou položku nebo úroveň, umístění skupiny prostředků, informace o vydavateli a virtuální síť.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates

[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) konfiguruje zabezpečení služby API Management. Aby byla služba API Management zjistitelná, musí se ve vašem clusteru Service Fabric ověřit pomocí klientského certifikátu s přístupem k vašemu clusteru. V tomto kurzu se používá stejný certifikát, který jste zadali dříve při vytváření [clusteru s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) nebo [clusteru s Linuxem](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor) a který je možné ve výchozím nastavení použít pro přístup k vašemu clusteru.

V tomto kurzu se používá stejný certifikát k ověřování klientů i k zajištění zabezpečení mezi uzly clusteru. Pro přístup k vašemu clusteru Service Fabric můžete použít i samostatný klientský certifikát, pokud máte nějaký nakonfigurovaný. Zadejte **název**, **heslo** a **data** (řetězec s kódováním Base 64) souboru privátního klíče (.pfx) certifikátu clusteru, který jste zadali při vytváření clusteru Service Fabric.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends

[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) popisuje back-end službu, do které se směruje provoz.

V případě back-endů Service Fabric je místo konkrétní služby Service Fabric back-endem cluster Service Fabric. Díky tomu může jediná zásada směrovat do více než jedné služby v clusteru. Zde uvedené pole **url** je plně kvalifikovaný název služby ve vašem clusteru, do které se ve výchozím nastavení směrují všechny požadavky, pokud v zásadě back-endu není zadaný žádný název služby. Pokud nemáte v úmyslu mít náhradní službu, můžete použít fiktivní název služby, například fabric:/fiktivni/sluzba. **resourceId** určuje koncový bod správy clusteru.  **clientCertificateThumbprint** a **serverCertificateThumbprints** identifikují certifikáty sloužící k ověření v clusteru.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products

[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) vytvoří produkt. Ve službě Azure API Management obsahuje produkt jedno nebo více rozhraní API a také kvótu využití a podmínky použití. Jakmile je projekt publikovaný, vývojáři se můžou přihlásit k jeho odběru a začít používat jeho rozhraní API.

Zadejte popisný zobrazovaný název (**displayName**) a popis (**description**) produktu. Pro účely tohoto kurzu se vyžaduje předplatné, ale schválení předplatného správcem už ne.  **Stav** tohoto produktu je „publikováno“ a produkt je viditelný pro předplatitele.

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis

[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) vytvoří rozhraní API. Rozhraní API ve službě API Management představuje sadu operací, které můžou vyvolat klientské aplikace. Po přidání operací se rozhraní API přidá do produktu a může se publikovat. Jakmile je rozhraní API publikované, vývojáři se můžou přihlásit k jeho odběru a začít ho používat.

* **displayName** (zobrazovaný název) může být jakýkoli název vašeho rozhraní API. Pro účely tohoto kurzu použijte Service Fabric App.
* **name** (název) představuje jedinečný a popisný název rozhraní API, například service-fabric-app. Tento název se zobrazí na portálech pro vývojáře a vydavatele.
* **serviceUrl** (adresa URL služby) odkazuje na službu HTTP implementující toto rozhraní API. Služba API Management na tuto adresu směruje požadavky. Pro back-endy Service Fabric se tato hodnota adresy URL nepoužívá. Sem můžete zadat jakoukoli hodnotu. Pro účely tohoto kurzu zadejte například http://servicefabric.
* **path** (cesta) se připojí k základní adrese URL služby API Management. Základní adresa URL je společná pro všechna rozhraní API hostovaná jednou instancí služby API Management. Služba API Management rozlišuje rozhraní API podle jejich přípony, proto musí být přípona jedinečná pro každé rozhraní API daného vydavatele.
* **protocols** (protokoly) určuje, které protokoly je možné použít k přístupu k rozhraní API. Pro účely tohoto kurzu uveďte **http** a **https**.
* **path** (cesta) je přípona rozhraní API. Pro účely tohoto kurzu použijte myapp.

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations

[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) Než bude možné použít rozhraní API ve službě API Management, musí se do tohoto rozhraní API přidat operace.  Externí klienti pomocí operací komunikují s bezstavovou službou ASP.NET Core spuštěnou v clusteru Service Fabric.

Pokud chcete přidat front-end operaci rozhraní API, vyplňte následující hodnoty:

* **displayName** (zobrazovaný název) a **description** (popis) popisujíc operaci. Pro účely tohoto kurzu použijte Values.
* **method** (metoda) určuje příkaz HTTP.  Pro účely tohoto kurzu zadejte **GET**.
* **urlTemplate** (šablona adresy URL) se připojí k základní adrese URL rozhraní API a identifikuje jednu operaci HTTP.  Pro účely tohoto kurzu použijte `/api/values`, pokud jste přidali back-end službu v .NET, nebo `getMessage`, pokud jste přidali back-end službu v Javě.  Zde zadaná cesta URL je ve výchozím nastavení cestou URL, která se odesílá do back-end služby Service Fabric. Pokud tady použijete stejnou cestu URL, jakou používá vaše služba, například /api/values, bude operace fungovat bez dalších úprav. Můžete tady zadat také jinou cestu URL, než kterou používá vaše back-end služba Service Fabric. V takovém případě je potřeba později také určit přepsání cesty v zásadách operace.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies

[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) vytvoří zásadu back-endu, která vše spojí dohromady. Tady konfigurujete back-end službu Service Fabric, do které se požadavky směrují. Tuto zásadu můžete použít pro jakoukoli operaci rozhraní API.  Další informace najdete v tématu [Přehled zásad](/azure/api-management/api-management-howto-policies).

[Konfigurace back-endu pro Service Fabric](/azure/api-management/api-management-transformation-policies#SetBackendService) poskytuje následující ovládací prvky směrování požadavků:

* Výběr instance služby zadáním názvu instance služby Service Fabric, a to buď pevně zakódovaného (například `"fabric:/myapp/myservice"`), nebo vygenerovaného z požadavku HTTP (například `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
* Překlad oddílů vygenerováním klíče oddílu pomocí jakéhokoli schématu vytváření oddílů Service Fabric.
* Výběr replik pro stavové služby.
* Podmínky opakování překladu, které umožňují zadat podmínky pro opakování překladu umístění služby a odeslání požadavku.

**policyContent** (obsah zásady) je obsah XML zásady uvozený ve formátu JSON.  Pro účely tohoto kurzu vytvořte back-end zásady pro směrování požadavků přímo do dříve nasazené bezstavové služby v .NET nebo Javě. Mezi příchozí zásady přidejte zásadu `set-backend-service`.  Nahraďte hodnotu *sf-service-instance-name* za `fabric:/ApiApplication/WebApiService`, pokud jste předtím nasadili back-end službu v .NET, nebo za `fabric:/EchoServerApplication/EchoServerService`, pokud jste nasadili službu v Javě.  *backend-id* odkazuje na prostředek back-endu, v tomto případě na prostředek `Microsoft.ApiManagement/service/backends` definovaný v šabloně *apim.json*. *backend-id* může odkazovat i na jiný prostředek back-endu vytvořený pomocí rozhraní API služby API Management. Pro účely tohoto kurzu nastavte *backend-id* na hodnotu parametru *service_fabric_backend_name*.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Úplnou sadu atributů zásad back-endu Service Fabric najdete v [dokumentaci k back-endu služby API Management](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService).

## <a name="set-parameters-and-deploy-api-management"></a>Nastavení parametrů a nasazení služby API Management

Vyplňte následující prázdné parametry v souboru *apim.parameters.json* pro vaše nasazení.

|Parametr|Hodnota|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Developer|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6|
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;řetězec s kódováním Base 64&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|https://mysfcluster.southcentralus.cloudapp.azure.com:19080|
|inbound_policy|&lt;Řetězec XML&gt;|

*certificatePassword* a *serviceFabricCertificateThumbprint* musí odpovídat certifikátu clusteru použitému k nastavení clusteru.

*serviceFabricCertificate* je certifikát v podobě řetězce s kódováním Base 64, který můžete vygenerovat pomocí následujícího skriptu:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

V části *inbound_policy* nahraďte hodnotu *sf-service-instance-name* za `fabric:/ApiApplication/WebApiService`, pokud jste předtím nasadili back-end službu v .NET, nebo za `fabric:/EchoServerApplication/EchoServerService`, pokud jste nasadili službu v Javě. *backend-id* odkazuje na prostředek back-endu, v tomto případě na prostředek `Microsoft.ApiManagement/service/backends` definovaný v šabloně *apim.json*. *backend-id* může odkazovat i na jiný prostředek back-endu vytvořený pomocí rozhraní API služby API Management. Pro účely tohoto kurzu nastavte *backend-id* na hodnotu parametru *service_fabric_backend_name*.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Pomocí následujícího skriptu nasaďte šablonu Resource Manageru a soubory parametrů pro službu API Management:

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
$templatepath="C:\clustertemplates"

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json
```

## <a name="test-it"></a>Testování

Přímo na webu [Azure Portal](https://portal.azure.com) teď můžete do své back-end služby v Service Fabric zkusit odeslat požadavek přes službu API Management.

 1. Ve službě API Management vyberte **Rozhraní API**.
 2. V rozhraní API **Service Fabric App**, které jste vytvořili v předchozích krocích, vyberte kartu **Test** a pak operaci **Values**.
 3. Kliknutím na tlačítko **Odeslat** odešlete do back-end služby testovací požadavek.  Měla by se zobrazit podobná odpověď HTTP jako tato:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Ocp-Apim-Trace-Location: https://apimgmtstodhwklpry2xgkdj.blob.core.windows.net/apiinspectorcontainer/PWSQOq_FCDjGcaI1rdMn8w2-2?sv=2015-07-08&sr=b&sig=MhQhzk%2FEKzE5odlLXRjyVsgzltWGF8OkNzAKaf0B1P0%3D&se=2018-01-28T01%3A04%3A44Z&sp=r&traceId=9f8f1892121e445ea1ae4d2bc8449ce4

    Date: Sat, 27 Jan 2018 01:04:44 GMT


    ["value1", "value2"]
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Cluster se kromě vlastního prostředku clusteru skládá z dalších prostředků Azure. Nejjednodušší způsob, jak odstranit cluster a všechny prostředky, které využívá, je odstranit příslušnou skupinu prostředků.

Přihlaste se k Azure a vyberte ID předplatného, pro které chcete cluster odebrat.  Své ID předplatného můžete zjistit po přihlášení k webu [Azure Portal](http://portal.azure.com). Pomocí rutiny [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) odstraňte skupinu prostředků a všechny prostředky clusteru.

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasadit službu API Management
> * Nakonfigurovat službu API Management
> * Vytvořit operaci rozhraní API
> * Nakonfigurovat zásadu back-endu
> * Přidat rozhraní API do produktu

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png

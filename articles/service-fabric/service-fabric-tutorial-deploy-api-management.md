---
title: Integrace API Management s Service Fabric v Azure
description: Naučte se rychle začít s Azure API Management a směrovat provoz do back-endové služby v Service Fabric.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 681a1c5241743a0164d83d73753efa0b6c446109
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735586"
---
# <a name="integrate-api-management-with-service-fabric-in-azure"></a>Integrace API Management s Service Fabric v Azure

Nasazení služby Azure API Management s využitím Service Fabric je pokročilý scénář.  Služba API Management je užitečná v případě, že potřebujete publikovat rozhraní API s bohatou sadou pravidel směrování pro vaše back-end služby Service Fabric. Cloudové aplikace obvykle potřebují front-end bránu, která poskytuje jediný bod příjmu příchozího přenosu od uživatelů, zařízení nebo dalších aplikací. V Service Fabric může být brána bezstavová služba navržená pro příjem provozu, například aplikace ASP.NET Core, služba Event Hubs, služba IoT Hub nebo služba Azure API Management.

V tomto článku se dozvíte, jak nastavit [API Management Azure](../api-management/api-management-key-concepts.md) pomocí Service Fabric pro směrování provozu do back-endové služby v Service Fabric.  Po dokončení budete mít nasazenou službu API Management ve virtuální síti a nakonfigurovanou operaci rozhraní API pro odesílání provozu do back-end bezstavové služby. Další informace o scénářích služby Azure API Management s využitím Service Fabric najdete v článku [Přehled](service-fabric-api-management-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability"></a>Dostupnost

> [!IMPORTANT]
> Tato funkce je dostupná na úrovních úrovně **Premium** a **Developer** API Management v důsledku podpory požadované virtuální sítě.

## <a name="prerequisites"></a>Požadavky

Než začnete:

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* Nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) nebo rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli).
* Vytvořte zabezpečený [cluster se systémem Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ve skupině zabezpečení sítě.
* Pokud nasadíte cluster s Windows, nastavte vývojové prostředí ve Windows. Nainstalujte [Visual Studio 2019](https://www.visualstudio.com) a vývojové úlohy pro vývoj pro **Azure**, **ASP.NET a** vývoj pro web a **.NET Core pro různé platformy** .  Potom nastavte [vývojové prostředí .NET](service-fabric-get-started.md).

## <a name="network-topology"></a>Síťová topologie

Teď, když máte zabezpečený [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure, nasadíte API Management do virtuální sítě (VNET) v PODSÍTI a NSG určené pro API Management. Pro účely tohoto článku je šablona API Management Správce prostředků předem nakonfigurovaná tak, aby používala názvy virtuální sítě, podsítě a NSG, které jste nastavili v [clusteru Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) . Tento článek nasadí následující topologii do Azure, ve které API Management a Service Fabric jsou v podsítích stejné Virtual Network:

 ![Popisek obrázku][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Přihlášení k Azure a výběr předplatného

Před spouštěním příkazů Azure se přihlaste ke svému účtu Azure a vyberte své předplatné.

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Nasazení back-end služby Service Fabric

Než ve službě API Management nakonfigurujete směrování provozu do back-end služby Service Fabric, potřebujete nejprve spuštěnou službu, která bude požadavky přijímat.  

Pomocí výchozí šablony projektu webového rozhraní API vytvořte základní bezstavovou ASP.NET Core spolehlivou službu. Tím se pro vaši službu vytvoří koncový bod HTTP, který zveřejníte prostřednictvím služby Azure API Management.

Spusťte sadu Visual Studio jako správce a vytvořte službu ASP.NET Core:

 1. V sadě Visual Studio vyberte Soubor -> Nový projekt.
 2. Vyberte šablonu aplikace Service Fabric v části Cloud a pojmenujte aplikaci **ApiApplication**.
 3. Vyberte šablonu bezstavové služby ASP.NET Core a pojmenujte projekt **WebApiService**.
 4. Vyberte šablonu projektu Web API ASP.NET Core 2,1.
 5. Po vytvoření projektu otevřete soubor `PackageRoot\ServiceManifest.xml` a z konfigurace prostředku koncového bodu odeberte atribut `Port`:

    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    Odebrání portu umožňuje Service Fabric dynamicky určovat port z rozsahu portů aplikace, který se otevřel přes skupinu zabezpečení sítě v šabloně clusteru Správce prostředků a umožňuje, aby do něj tok z API Management.

 6. Stisknutím klávesy F5 v sadě Visual Studio ověřte, že je webové rozhraní API místně dostupné.

    Otevřete Service Fabric Explorer, projděte hierarchií ke konkrétní instanci služby ASP.NET Core a zobrazte základní adresu, na které služba naslouchá. Přidejte k základní adrese `/api/values` a otevřete ji v prohlížeči. Tím se v šabloně webového rozhraní API vyvolá metoda Get v kontroleru ValuesController. Metoda vrátí výchozí odpověď poskytovanou šablonou, což je pole JSON obsahující dva řetězce:

    ```json
    ["value1", "value2"]`
    ```

    Toto je koncový bod, který zveřejníte prostřednictvím služby API Management v Azure.

 7. Nakonec aplikaci nasaďte do svého clusteru v Azure. V sadě Visual Studio klikněte pravým tlačítkem na projekt aplikace a vyberte **Publikovat**. Zadejte koncový bod svého clusteru (například `mycluster.southcentralus.cloudapp.azure.com:19000`), aby se aplikace nasadila do vašeho clusteru Service Fabric v Azure.

Ve vašem clusteru Service Fabric v Azure by teď měla být spuštěná bezstavová služba ASP.NET Core s názvem `fabric:/ApiApplication/WebApiService`.

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

[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) konfiguruje zabezpečení služby API Management. Aby byla služba API Management zjistitelná, musí se ve vašem clusteru Service Fabric ověřit pomocí klientského certifikátu s přístupem k vašemu clusteru. Tento článek používá stejný certifikát, který jste zadali dříve při vytváření [clusteru Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor), který se ve výchozím nastavení dá použít pro přístup ke clusteru.

Tento článek používá stejný certifikát pro ověřování klientů a zabezpečení mezi uzly clusteru. Pro přístup k vašemu clusteru Service Fabric můžete použít i samostatný klientský certifikát, pokud máte nějaký nakonfigurovaný. Zadejte **název**, **heslo** a **data** (řetězec s kódováním Base 64) souboru privátního klíče (.pfx) certifikátu clusteru, který jste zadali při vytváření clusteru Service Fabric.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends

[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) popisuje back-end službu, do které se směruje provoz.

V případě back-endů Service Fabric je místo konkrétní služby Service Fabric back-endem cluster Service Fabric. Díky tomu může jediná zásada směrovat do více než jedné služby v clusteru. Zde uvedené pole **url** je plně kvalifikovaný název služby ve vašem clusteru, do které se ve výchozím nastavení směrují všechny požadavky, pokud v zásadě back-endu není zadaný žádný název služby. Pokud nemáte v úmyslu mít náhradní službu, můžete použít fiktivní název služby, například fabric:/fiktivni/sluzba. **resourceId** určuje koncový bod správy clusteru.  **clientCertificateThumbprint** a **serverCertificateThumbprints** identifikují certifikáty sloužící k ověření v clusteru.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products

[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) vytvoří produkt. Ve službě Azure API Management obsahuje produkt jedno nebo více rozhraní API a také kvótu využití a podmínky použití. Jakmile je projekt publikovaný, vývojáři se můžou přihlásit k jeho odběru a začít používat jeho rozhraní API.

Zadejte popisný zobrazovaný název (**displayName**) a popis (**description**) produktu. V tomto článku se vyžaduje předplatné, ale schválení předplatného pro správce není.  **Stav** tohoto produktu je „publikováno“ a produkt je viditelný pro předplatitele.

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis

[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) vytvoří rozhraní API. Rozhraní API ve službě API Management představuje sadu operací, které můžou vyvolat klientské aplikace. Po přidání operací se rozhraní API přidá do produktu a může se publikovat. Jakmile je rozhraní API publikované, vývojáři se můžou přihlásit k jeho odběru a začít ho používat.

* **displayName** (zobrazovaný název) může být jakýkoli název vašeho rozhraní API. V tomto článku použijte Service Fabric App.
* **name** (název) představuje jedinečný a popisný název rozhraní API, například service-fabric-app. Tento název se zobrazí na portálech pro vývojáře a vydavatele.
* **serviceUrl** (adresa URL služby) odkazuje na službu HTTP implementující toto rozhraní API. Služba API Management na tuto adresu směruje požadavky. Pro back-endy Service Fabric se tato hodnota adresy URL nepoužívá. Sem můžete zadat jakoukoli hodnotu. V tomto článku například "http: \/ /servicefabric".
* **path** (cesta) se připojí k základní adrese URL služby API Management. Základní adresa URL je společná pro všechna rozhraní API hostovaná jednou instancí služby API Management. Služba API Management rozlišuje rozhraní API podle jejich přípony, proto musí být přípona jedinečná pro každé rozhraní API daného vydavatele.
* **protocols** (protokoly) určuje, které protokoly je možné použít k přístupu k rozhraní API. V tomto článku vypíšete **http** a **https**.
* **path** (cesta) je přípona rozhraní API. Pro tento článek použijte "myapp".

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations

[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) Než bude možné použít rozhraní API ve službě API Management, musí se do tohoto rozhraní API přidat operace.  Externí klienti pomocí operací komunikují s bezstavovou službou ASP.NET Core spuštěnou v clusteru Service Fabric.

Pokud chcete přidat front-end operaci rozhraní API, vyplňte následující hodnoty:

* **displayName** (zobrazovaný název) a **description** (popis) popisujíc operaci. V tomto článku použijte "Values".
* **method** (metoda) určuje příkaz HTTP.  V tomto článku zadejte **Get**.
* **urlTemplate** (šablona adresy URL) se připojí k základní adrese URL rozhraní API a identifikuje jednu operaci HTTP.  V tomto článku použijte, `/api/values` Pokud jste přidali službu back-end rozhraní .NET nebo `getMessage` jste přidali back-end službu Java.  Zde zadaná cesta URL je ve výchozím nastavení cestou URL, která se odesílá do back-end služby Service Fabric. Pokud tady použijete stejnou cestu URL, jakou používá vaše služba, například /api/values, bude operace fungovat bez dalších úprav. Můžete tady zadat také jinou cestu URL, než kterou používá vaše back-end služba Service Fabric. V takovém případě je potřeba později také určit přepsání cesty v zásadách operace.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies

[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) vytvoří zásadu back-endu, která vše spojí dohromady. Tady konfigurujete back-end službu Service Fabric, do které se požadavky směrují. Tuto zásadu můžete použít pro jakoukoli operaci rozhraní API.  Další informace najdete v tématu [Přehled zásad](../api-management/api-management-howto-policies.md).

[Konfigurace back-endu pro Service Fabric](../api-management/api-management-transformation-policies.md#SetBackendService) poskytuje následující ovládací prvky směrování požadavků:

* Výběr instance služby zadáním názvu instance služby Service Fabric, a to buď pevně zakódovaného (například `"fabric:/myapp/myservice"`), nebo vygenerovaného z požadavku HTTP (například `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
* Překlad oddílů vygenerováním klíče oddílu pomocí jakéhokoli schématu vytváření oddílů Service Fabric.
* Výběr replik pro stavové služby.
* Podmínky opakování překladu, které umožňují zadat podmínky pro opakování překladu umístění služby a odeslání požadavku.

**policyContent** (obsah zásady) je obsah XML zásady uvozený ve formátu JSON.  V tomto článku Vytvořte zásady back-end pro směrování požadavků přímo do bezstavové služby .NET nebo Java nasazené dříve. Mezi příchozí zásady přidejte zásadu `set-backend-service`.  Nahraďte hodnotu *sf-service-instance-name* za `fabric:/ApiApplication/WebApiService`, pokud jste předtím nasadili back-end službu v .NET, nebo za `fabric:/EchoServerApplication/EchoServerService`, pokud jste nasadili službu v Javě.  *backend-id* odkazuje na prostředek back-endu, v tomto případě na prostředek `Microsoft.ApiManagement/service/backends` definovaný v šabloně *apim.json*. *backend-id* může odkazovat i na jiný prostředek back-endu vytvořený pomocí rozhraní API služby API Management. Pro účely tohoto článku nastavte *back-end-ID* na hodnotu parametru *service_fabric_backend_name* .

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Úplnou sadu atributů zásad back-endu Service Fabric najdete v [dokumentaci k back-endu služby API Management](../api-management/api-management-transformation-policies.md#SetBackendService).

## <a name="set-parameters-and-deploy-api-management"></a>Nastavení parametrů a nasazení služby API Management

Vyplňte následující prázdné parametry v souboru *apim.parameters.json* pro vaše nasazení.

|Parametr|Hodnota|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Vývojář|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6|
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;řetězec s kódováním Base 64&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|`https://mysfcluster.southcentralus.cloudapp.azure.com:19080`|
|inbound_policy|&lt;Řetězec XML&gt;|

*certificatePassword* a *serviceFabricCertificateThumbprint* musí odpovídat certifikátu clusteru použitému k nastavení clusteru.

*serviceFabricCertificate* je certifikát v podobě řetězce s kódováním Base 64, který můžete vygenerovat pomocí následujícího skriptu:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

V části *inbound_policy* nahraďte hodnotu *sf-service-instance-name* za `fabric:/ApiApplication/WebApiService`, pokud jste předtím nasadili back-end službu v .NET, nebo za `fabric:/EchoServerApplication/EchoServerService`, pokud jste nasadili službu v Javě. *backend-id* odkazuje na prostředek back-endu, v tomto případě na prostředek `Microsoft.ApiManagement/service/backends` definovaný v šabloně *apim.json*. *backend-id* může odkazovat i na jiný prostředek back-endu vytvořený pomocí rozhraní API služby API Management. Pro účely tohoto článku nastavte *back-end-ID* na hodnotu parametru *service_fabric_backend_name* .

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
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

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az deployment group create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az deployment group create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json
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

Přihlaste se k Azure a vyberte ID předplatného, se kterým chcete cluster odebrat.  Své ID předplatného můžete zjistit po přihlášení k webu [Azure Portal](https://portal.azure.com). Odstraňte skupinu prostředků a všechny prostředky clusteru pomocí [rutiny Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o použití [API Management](../api-management/import-and-publish.md).

[Azure Portal](../api-management/how-to-configure-service-fabric-backend.md) můžete použít také k vytvoření a správě Service Fabric back-endy pro API Management.

[azure-powershell]: /powershell/azure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
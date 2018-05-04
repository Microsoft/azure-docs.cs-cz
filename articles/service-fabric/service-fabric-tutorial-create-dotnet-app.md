---
title: Vytvoření aplikace .NET pro Service Fabric | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit aplikaci s front-endem v ASP.NET Core a stavovým back-endem spolehlivé služby a jak tuto aplikaci nasadit do clusteru.
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
ms.date: 01/29/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 7840dc86ec7753980bb2c35f932f132c50d65f9e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Kurz: Vytvoření a nasazení aplikace s front-end službou webového rozhraní API pro ASP.NET Core a stavovou back-end službou
Tento kurz je první částí série.  Dozvíte se, jak vytvořit aplikaci Azure Service Fabric s front-endem webového rozhraní API pro ASP.NET Core a stavovou back-end službou pro ukládání dat. Až budete hotovi, budete mít hlasovací aplikaci s webovým front-endem v ASP.NET Core, která ukládá výsledky hlasování do stavové back-end služby v clusteru. Pokud nechcete hlasovací aplikaci vytvářet ručně, můžete si [stáhnout zdrojový kód](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) dokončené aplikace a přeskočit k části [Prohlídka ukázkové hlasovací aplikace](#walkthrough_anchor).  Pokud chcete, můžete se podívat na [video s průvodcem](https://channel9.msdn.com/Events/Connect/2017/E100) tímto kurzem.

![Diagram aplikace](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

V první části této série se naučíte:

> [!div class="checklist"]
> * Vytvořit službu webového rozhraní API pro ASP.NET Core jako spolehlivou stavovou službu
> * Vytvořit službu webové aplikace ASP.NET Core jako bezstavovou webovou službu
> * Použít reverzní proxy ke komunikaci se stavovou službou

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Sestavit aplikaci .NET pro Service Fabric
> * [Nasadit aplikaci do vzdáleného clusteru](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Přidat koncový bod HTTPS do front-end služby ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Nakonfigurovat CI/CD pomocí Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Nastavit monitorování a diagnostiku aplikace](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Požadavky
Než začnete s tímto kurzem:
- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Nainstalujte sadu Visual Studio 2017](https://www.visualstudio.com/) verze 15.5 nebo novější se sadami funkcí **Vývoj pro Azure** a **Vývoj pro ASP.NET a web**.
- [Nainstalujte sadu Service Fabric SDK](service-fabric-get-started.md).

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Vytvoření služby webového rozhraní API pro ASP.NET jako spolehlivé služby
Nejprve vytvořte webový front-end hlasovací aplikace pomocí ASP.NET Core. ASP.NET Core je jednoduché, multiplatformní rozhraní pro vývoj pro web, pomocí kterého můžete vytvářet moderní webová uživatelská rozhraní a webová rozhraní API. Pokud chcete naplno porozumět integraci ASP.NET Core se Service Fabric, důrazně doporučujeme prostudovat si článek [ASP.NET Core v Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md). Prozatím můžete rychle začít tímto kurzem. Další informace o ASP.NET Core najdete v [dokumentaci k ASP.NET Core](https://docs.microsoft.com/aspnet/core/).

1. Spusťte sadu Visual Studio jako **správce**.

2. Vytvořte nový projekt přes **Soubor**->**Nový**->**Projekt**.

3. V dialogovém okně **Nový projekt** zvolte **Cloud > Aplikace Service Fabric**.

4. Pojmenujte aplikaci **Voting** a stiskněte **OK**.

   ![Dialogové okno Nový projekt ve Visual Studiu](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. Na stránce **Nová služba Service Fabric** zvolte **Bezstavová služba ASP.NET Core** a pojmenujte službu **VotingWeb**.
   
   ![Volba webové služby ASP.NET v dialogovém okně Nová služba](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. Na další stránce se zobrazí sada šablon projektů ASP.NET Core. Pro účely tohoto kurzu zvolte **Webová aplikace (model-zobrazení-kontroler)**. 
   
   ![Volba typu projektu ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Sada Visual Studio vytvoří aplikaci a projekt služby a zobrazí je v Průzkumníku řešení.

   ![Průzkumník řešení po vytvoření aplikace se službou webového rozhraní API pro ASP.NET Core]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-angularjs-to-the-votingweb-service"></a>Přidání AngularJS do služby VotingWeb
Přidejte do své služby [AngularJS](http://angularjs.org/) pomocí [podpory Bower](/aspnet/core/client-side/bower). Nejprve do projektu přidejte konfigurační soubor Bower.  V Průzkumníku řešení klikněte pravým tlačítkem na **VotingWeb** a vyberte **Přidat -> Nová položka**. Vyberte **Web** a pak **Konfigurační soubor Bower**.  Vytvoří se soubor *bower.json*.

Otevřete soubor *bower.json*, přidejte položky pro angular a angular-bootstrap a pak uložte provedené změny.

```json
{
  "name": "asp.net",
  "private": true,
  "dependencies": {
    "bootstrap": "3.3.7",
    "jquery": "3.2.1",
    "jquery-validation": "1.16.0",
    "jquery-validation-unobtrusive": "3.2.6",
    "angular": "v1.6.8",
    "angular-bootstrap": "v1.1.0"
  }
}
```
Po uložení souboru *bower.json* se Angular nainstaluje do složky *wwwroot/lib* vašeho projektu. Kromě toho je uvedený také ve složce *Dependencies/Bower*.

### <a name="update-the-sitejs-file"></a>Aktualizace souboru site.js
Otevřete soubor *wwwroot/js/site.js*.  Nahraďte jeho obsah kódem JavaScriptu, který používají zobrazení Home:

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Aktualizace souboru Index.cshtml
Otevřete soubor *Views/Home/Index.cshtml*, což je zobrazení specifické pro kontroler Home.  Nahraďte jeho obsah následujícím kódem a pak uložte provedené změny.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item"/>
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr/>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-layoutcshtml-file"></a>Aktualizace souboru _Layout.cshtml
Otevřete soubor *Views/Shared/_Layout.cshtml*, což je výchozí rozložení aplikace ASP.NET.  Nahraďte jeho obsah následujícím kódem a pak uložte provedené změny.

```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="http://angularjs.org">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet"/>
    <link href="~/css/site.css" rel="stylesheet"/>

</head>
<body>
<div class="container body-content">
    @RenderBody()
</div>

<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
<script src="~/lib/angular/angular.js"></script>
<script src="~/lib/angular-bootstrap/ui-bootstrap-tpls.js"></script>
<script src="~/js/site.js"></script>

@RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>Aktualizace souboru VotingWeb.cs
Otevřete soubor *VotingWeb.cs*, který v rámci bezstavové služby vytváří webového hostitele ASP.NET Core s využitím webového serveru WebListener.  

Na začátek souboru přidejte direktivu `using System.Net.Http;`.  

Funkci `CreateServiceInstanceListeners()` nahraďte následujícím kódem a pak uložte provedené změny.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(
            serviceContext =>
                new KestrelCommunicationListener(
                    serviceContext,
                    "ServiceEndpoint",
                    (url, listener) =>
                    {
                        ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

                        return new WebHostBuilder()
                            .UseKestrel()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<HttpClient>(new HttpClient())
                                    .AddSingleton<FabricClient>(new FabricClient())
                                    .AddSingleton<StatelessServiceContext>(serviceContext))
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
                    }))
    };
}
```

Přidejte také metodu `GetVotingDataServiceName`, která na dotaz vrací název služby:

```csharp
internal static Uri GetVotingDataServiceName(ServiceContext context)
{
    return new Uri($"{context.CodePackageActivationContext.ApplicationName}/VotingData");
}
```

### <a name="add-the-votescontrollercs-file"></a>Přidání souboru VotesController.cs
Přidejte kontroler definující akce hlasování. Klikněte pravým tlačítkem na složku **Controllers** (Kontrolery) a pak vyberte **Přidat -> Nová položka -> Třída**.  Pojmenujte soubor VotesController.cs a klikněte na **Přidat**.  

Nahraďte obsah souboru následujícím kódem a pak uložte provedené změny.  V pozdější části [Aktualizace souboru VotesController.cs](#updatevotecontroller_anchor) se tento soubor upraví tak, aby načítal a zapisoval data hlasování z back-end služby.  Prozatím kontroler vrací do zobrazení statická řetězcová data.

```csharp
namespace VotingWeb.Controllers
{
    using System;
    using System.Collections.Generic;
    using System.Fabric;
    using System.Fabric.Query;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Newtonsoft.Json;

    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```

### <a name="configure-the-listening-port"></a>Konfigurace portu pro naslouchání
Po vytvoření front-end služby VotingWeb sada Visual Studio náhodně vybere port, na kterém bude služba naslouchat.  Služba VotingWeb se chová jako front-end této aplikace a přijímá externí provoz, takže tuto službu svážeme s pevným a dobře známým portem.  [Manifest služby](service-fabric-application-and-service-manifests.md) deklaruje koncové body služby. V Průzkumníku řešení otevřete soubor *VotingWeb/PackageRoot/ServiceManifest.xml*.  V části **Resources** (Prostředky) vyhledejte prostředek **Endpoint** (Koncový bod) a změňte hodnotu **Port** na 80 nebo jiný port. Pokud chcete aplikaci nasadit a spustit místně, port pro naslouchání aplikace musí být otevřený a dostupný na vašem počítači.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="80" />
    </Endpoints>
  </Resources>
```

Aktualizujte také hodnotu vlastnosti Adresa URL aplikace v projektu Voting, aby se webový prohlížeč při ladění pomocí F5 otevřel na správném portu.  V Průzkumníku řešení vyberte projekt **Voting** a aktualizujte vlastnost **Adresa URL aplikace**.

![Adresa URL aplikace](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-url.png)

### <a name="deploy-and-run-the-application-locally"></a>Místní nasazení a spuštění aplikace
Teď můžete aplikaci spustit. Stisknutím klávesy `F5` v sadě Visual Studio aplikaci nasaďte pro účely ladění. `F5` selže, pokud jste předtím sadu Visual Studio neotevřeli jako **správce**.

> [!NOTE]
> Při prvním místním spuštění a nasazení aplikace sada Visual Studio vytvoří místní cluster pro účely ladění.  Vytvoření clusteru může nějakou dobu trvat. Stav vytváření clusteru se zobrazí v okně výstupu sady Visual Studio.

V tuto chvíli by vaše webová aplikace měla vypadat přibližně takto:

![Front-end ASP.NET Core](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Pokud chcete zastavit ladění aplikace, vraťte se do sady Visual Studio a stiskněte **Shift+F5**.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Přidání stavové back-end služby do aplikace
Když je teď v aplikaci spuštěná služba webového rozhraní API pro ASP.NET, přidejte spolehlivou stavovou službu, která v aplikaci bude ukládat nějaká data.

Service Fabric umožňuje konzistentně a spolehlivě ukládat data přímo v rámci služby s použitím spolehlivých kolekcí. Spolehlivé kolekce jsou sady vysoce dostupných a spolehlivých tříd kolekcí, které budou znát všichni, kteří už někdy používali kolekce jazyka C#.

V tomto kurzu vytvoříte službu, která do spolehlivé kolekce ukládá hodnotu čítače.

1. V Průzkumníku řešení klikněte pravým tlačítkem na **Služby** v rámci projektu aplikace a zvolte **Přidat -> Nová služba Service Fabric**.
    
2. V dialogovém okně **Nová služba Service Fabric** zvolte **Stavová služba ASP.NET Core**, pojmenujte službu **VotingData** a stiskněte **OK**.

    ![Dialogové okno Nová služba ve Visual Studiu](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    Po vytvoření projektu služby budete mít ve své aplikaci dvě služby. V průběhu vytváření aplikace můžete stejným způsobem přidávat další služby. Každou z nich je možné nezávisle označovat verzí a upgradovat.

3. Na další stránce se zobrazí sada šablon projektů ASP.NET Core. Pro účely tohoto kurzu zvolte **Webové rozhraní API**.

    ![Volba typu projektu ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog2.png)

    Sada Visual Studio vytvoří projekt služby a zobrazí ho v Průzkumníku řešení.

    ![Průzkumník řešení](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>Přidání souboru VoteDataController.cs

V projektu **VotingData** klikněte pravým tlačítkem na složku **Controllers** (Kontrolery) a pak vyberte **Přidat -> Nová položka -> Třída**. Pojmenujte soubor VoteDataController.cs a klikněte na **Přidat**. Nahraďte obsah souboru následujícím kódem a pak uložte provedené změny.

```csharp
namespace VotingData.Controllers
{
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.ServiceFabric.Data;
    using Microsoft.ServiceFabric.Data.Collections;

    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            CancellationToken ct = new CancellationToken();

            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                Microsoft.ServiceFabric.Data.IAsyncEnumerable<KeyValuePair<string, int>> list = await votesDictionary.CreateEnumerableAsync(tx);

                Microsoft.ServiceFabric.Data.IAsyncEnumerator<KeyValuePair<string, int>> enumerator = list.GetAsyncEnumerator();

                List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return this.Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```


## <a name="connect-the-services"></a>Propojení služeb
V tomto kroku propojíte tyto dvě služby a nastavíte front-end webovou aplikaci tak, aby získávala a nastavovala informace o hlasování z back-end služby.

Service Fabric nabízí naprostou flexibilitu způsobu, jakým komunikujete se spolehlivými službami. V rámci jedné aplikace můžete mít služby přístupné přes protokol TCP. Další služby můžou být přístupné přes rozhraní HTTP REST API a ještě další služby můžou být přístupné přes webové sokety. Další informace o dostupných možnostech a souvisejících kompromisech najdete v tématu [Komunikace se službami](service-fabric-connect-and-communicate-with-services.md).

V tomto kurzu použijete [Webové rozhraní API pro ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md).

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>Aktualizace souboru VotesController.cs
V projektu **VotingWeb** otevřete soubor *Controllers/VotesController.cs*.  Nahraďte obsah definice třídy `VotesController` následujícím kódem a pak uložte provedené změny.

```csharp
public class VotesController : Controller
{
    private readonly HttpClient httpClient;
    private readonly FabricClient fabricClient;
    private readonly StatelessServiceContext serviceContext;

    public VotesController(HttpClient httpClient, StatelessServiceContext context, FabricClient fabricClient)
    {
        this.fabricClient = fabricClient;
        this.httpClient = httpClient;
        this.serviceContext = context;
    }

    // GET: api/Votes
    [HttpGet("")]
    public async Task<IActionResult> Get()
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);

        ServicePartitionList partitions = await this.fabricClient.QueryManager.GetPartitionListAsync(serviceName);

        List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

        foreach (Partition partition in partitions)
        {
            string proxyUrl =
                $"{proxyAddress}/api/VoteData?PartitionKey={((Int64RangePartitionInformation) partition.PartitionInformation).LowKey}&PartitionKind=Int64Range";

            using (HttpResponseMessage response = await this.httpClient.GetAsync(proxyUrl))
            {
                if (response.StatusCode != System.Net.HttpStatusCode.OK)
                {
                    continue;
                }

                result.AddRange(JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync()));
            }
        }

        return this.Json(result);
    }

    // PUT: api/Votes/name
    [HttpPut("{name}")]
    public async Task<IActionResult> Put(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        StringContent putContent = new StringContent($"{{ 'name' : '{name}' }}", Encoding.UTF8, "application/json");
        putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

        using (HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent))
        {
            return new ContentResult()
            {
                StatusCode = (int) response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }
    }

    // DELETE: api/Votes/name
    [HttpDelete("{name}")]
    public async Task<IActionResult> Delete(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        using (HttpResponseMessage response = await this.httpClient.DeleteAsync(proxyUrl))
        {
            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int) response.StatusCode);
            }
        }

        return new OkResult();
    }


    /// <summary>
    /// Constructs a reverse proxy URL for a given service.
    /// Example: http://localhost:19081/VotingApplication/VotingData/
    /// </summary>
    /// <param name="serviceName"></param>
    /// <returns></returns>
    private Uri GetProxyAddress(Uri serviceName)
    {
        return new Uri($"http://localhost:19081{serviceName.AbsolutePath}");
    }

    /// <summary>
    /// Creates a partition key from the given name.
    /// Uses the zero-based numeric position in the alphabet of the first letter of the name (0-25).
    /// </summary>
    /// <param name="name"></param>
    /// <returns></returns>
    private long GetPartitionKey(string name)
    {
        return Char.ToUpper(name.First()) - 'A';
    }
}
```
<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>Prohlídka ukázkové hlasovací aplikace
Hlasovací aplikace se skládá ze dvou služeb:
- Webová front-end služba (VotingWeb) – Webová front-end služba v ASP.NET, která obsluhuje webovou stránku a zveřejňuje webová rozhraní API pro komunikaci s back-end službou.
- Back-end služba (VotingData) – Webová služba v ASP.NET Core, která zveřejňuje rozhraní API pro ukládání výsledků hlasování do spolehlivého slovníku trvale uloženého na disku.

![Diagram aplikace](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Při hlasování v aplikaci dojde k následujícím událostem:
1. JavaScript odešle požadavek s hlasem do webového rozhraní API ve webové front-end službě jako požadavek HTTP PUT.

2. Webová front-end služba pomocí proxy vyhledá požadavek HTTP PUT a přesměruje ho do back-end služby.

3. Back-end služba přijme příchozí požadavek a uloží aktualizované výsledky do spolehlivého slovníku, který se replikuje do několika uzlů v rámci clusteru a trvale se uloží na disku. Veškerá data aplikace se ukládají v clusteru, takže není potřeba žádná databáze.

## <a name="debug-in-visual-studio"></a>Ladění v sadě Visual Studio
Při ladění aplikace v sadě Visual Studio používáte místní vývojový cluster Service Fabric. Možnosti ladění si můžete upravit tak, aby vyhovovaly vašemu scénáři. V této aplikaci budete data ukládat v back-end službě s použitím spolehlivého slovníku. Sada Visual Studio ve výchozím nastavení odebere aplikaci při zastavení ladicího programu. Odebrání aplikace způsobí i odebrání dat v back-end službě. Pokud chcete zachovat data mezi ladicími relacemi, můžete změnit **Režim ladění aplikace** ve vlastnosti projektu **Voting** v sadě Visual Studio.

Pokud se chcete podívat, co se děje v kódu, proveďte následující kroky:
1. Otevřete soubor **VotesController.cs** a nastavte zarážku v metodě **Put** webového rozhraní API (řádek 63) – Soubor můžete vyhledat v Průzkumníku řešení v sadě Visual Studio.

2. Otevřete soubor **VoteDataController.cs** a nastavte zarážku v metodě **Put** tohoto webového rozhraní API (řádek 53).

3. Vraťte se do prohlížeče a klikněte na některou možnost hlasování nebo přidejte novou. Dostanete se k první zarážce v kontroleru rozhraní API webového front-endu.
    
    1. Tady JavaScript v prohlížeči odesílá požadavek do kontroleru webového rozhraní API ve front-end službě.
    
    ![Front-end služba pro přidání hlasu](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

    2. Nejprve se vytvoří adresa URL k ReverseProxy pro back-end službu **(1)**.
    3. Pak se do ReverseProxy odešle požadavek HTTP PUT **(2)**.
    4. Nakonec se do klienta vrátí odpověď z back-end služby **(3)**.

4. Pokračujte stisknutím **F5**.
    1. Nyní jste se dostali k zarážce v back-end službě.
    
    ![Back-end služba pro přidání hlasu](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

    2. Na prvním řádku v metodě **(1)** se pomocí `StateManager` načte nebo přidá spolehlivý slovník `counts`.
    3. Všechny interakce s hodnotami ve spolehlivém slovníku vyžadují transakci. Tuto transakci vytvoří tento příkaz using **(2)**.
    4. V transakci se pro možnost hlasování aktualizuje hodnota příslušného klíče a potvrdí se operace **(3)**. Jakmile se vrátí metoda potvrzení, data ve slovníku se aktualizují a replikují do dalších uzlů v clusteru. Data jsou teď bezpečně uložená v clusteru a v případě selhání back-end služby ji můžou převzít ostatní uzly, aby data byla i nadále dostupná.
5. Pokračujte stisknutím **F5**.

Pokud chcete zastavit ladicí relaci, stiskněte **Shift + F5**.


## <a name="next-steps"></a>Další kroky
V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit službu webového rozhraní API pro ASP.NET Core jako spolehlivou stavovou službu
> * Vytvořit službu webové aplikace ASP.NET Core jako bezstavovou webovou službu
> * Použít reverzní proxy ke komunikaci se stavovou službou

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Nasazení aplikace do Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)

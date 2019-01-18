---
title: Monitorování a diagnostika služeb ASP.NET Core na platformě Service Fabric v Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak nastavit monitorování a diagnostiku pro aplikaci ASP.NET Core v Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/17/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 27a114378cf72e766e894dc0dd6886197f56a841
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54390255"
---
# <a name="tutorial-monitor-and-diagnose-an-aspnet-core-application-on-service-fabric-using-application-insights"></a>Kurz: Monitorování a Diagnostika aplikace ASP.NET Core v Service Fabric pomocí Application Insights

Tento kurz je pátou částí série. Prochází kroky k nastavení monitorování a diagnostiky pro aplikaci ASP.NET Core spuštěnou v clusteru Service Fabric pomocí Application Insights. Budeme shromažďovat telemetrii z aplikace vyvinuté v první části kurzu [Sestavení aplikace .NET pro Service Fabric](service-fabric-tutorial-create-dotnet-app.md).

Ve čtvrté části této série kurzů se naučíte:
> [!div class="checklist"]
> * Nakonfigurovat Application Insights pro vaši aplikaci
> * Shromažďovat telemetrii odpovědí za účelem trasování komunikace založené na HTTP mezi službami
> * Používat funkci Mapa aplikace v Application Insights
> * Přidat vlastní události pomocí rozhraní API pro Application Insights

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Sestavit aplikaci .NET pro Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Nasadit aplikaci do vzdáleného clusteru](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Přidat koncový bod HTTPS do front-end služby ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Nakonfigurovat CI/CD s využitím služby Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * Nastavit monitorování a diagnostiku aplikace

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Nainstalujte sadu Visual Studio 2017](https://www.visualstudio.com/) se sadami funkcí **Vývoj pro Azure** a **Vývoj pro ASP.NET a web**.
* [Nainstalujte sadu Service Fabric SDK](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Stažení ukázkové aplikace Voting

Pokud jste nesestavili ukázkovou aplikaci Voting v [první části této série kurzů](service-fabric-tutorial-create-dotnet-app.md), můžete si ji stáhnout. Spuštěním následujícího příkazu v příkazovém okně nebo terminálu naklonujte úložiště ukázkové aplikace do místního počítače.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Nastavení prostředku Application Insights

Application Insights je platforma pro správu výkonu aplikací Azure a doporučená platforma Service Fabric pro monitorování a diagnostiku aplikací. Pokud chcete vytvořit prostředek Application Insights, přejděte na web [Azure Portal](https://portal.azure.com). Kliknutím na **Vytvořit prostředek** v levé navigační nabídce otevřete Azure Marketplace. Klikněte na **Application Insights**.

![Vytvoření nového prostředku Application Insights](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

Teď budete muset vyplnit požadované informace o atributech vytvářeného prostředku. Zadejte vhodný *Název*, *Skupinu prostředků* a *Předplatné*. Nastavte *Umístění*, do kterého v budoucnu nasadíte cluster Service Fabric. V tomto kurzu nasadíme aplikaci do místního clusteru, takže pole *Umístění* není relevantní. Jako *Typ aplikace* ponechte možnost Webová aplikace ASP.NET.

![Atributy prostředku Application Insights](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

Po vyplnění požadovaných informací kliknutím na **Vytvořit** zřiďte prostředek – mělo by to trvat přibližně minutu.
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Přidání Application Insights do služeb aplikace

Spusťte sadu Visual Studio 2017 se zvýšenými oprávněními. Provedete to tak, že kliknete pravým tlačítkem na ikonu sady Visual Studio v nabídce Start a zvolíte **Spustit jako správce**. Klikněte na **Soubor** > **Otevřít** > **Projekt nebo řešení** a přejděte k hlasovací aplikaci (vytvořené v první části kurzu nebo naklonované z Gitu). Otevřete řešení *Voting.sln*, a pokud se zobrazí výzva k obnovení balíčků NuGet aplikace, klikněte na **Ano**.

Postupujte podle těchto kroků a nakonfigurujte Application Insights pro službu VotingWeb i VotingData:

1. Klikněte pravým tlačítkem na název služby a vyberte **Přidat -> Telemetrie Application Insights...** .    
2. Klikněte na tlačítko **Začínáme**.
3. Přihlaste se ke svému účtu (se kterým jste také nastavili své předplatné Azure) a vyberte předplatné, ve kterém jste vytvořili prostředek Application Insights. Vyhledejte prostředek v rozevírací nabídce Prostředek v části *Stávající prostředek Application Insights*. Kliknutím na **Zaregistrovat** přidejte Application Insights do své služby.

    ![Registrace Application Insights](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. Jakmile otevřené dialogové okno dokončí akci, klikněte na **Dokončit**.

Nezapomeňte výše uvedené kroky provést pro **obě** služby v aplikaci. Tím se dokončí konfigurace Application Insights pro aplikaci. Pro obě služby se používá stejný prostředek Application Insights za účelem zobrazení příchozích i odchozích požadavků a komunikace mezi službami.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>Přidání balíčku NuGet Microsoft.ApplicationInsights.ServiceFabric.Native do služeb

Application Insights má dva balíčky NuGet specifické pro Service Fabric, které je možné použít v závislosti na scénáři. Jeden se používá s nativními službami Service Fabric a druhý s kontejnery a spustitelnými soubory hosta. V tomto případě použijeme balíček NuGet Microsoft.ApplicationInsights.ServiceFabric.Native, abychom mohli využít znalost kontextu služby, kterou poskytuje. Další informace o sadě SDK pro Application Insights a balíčcích NuGet specifických pro Service Fabric najdete v tématu [Microsoft Application Insights pro Service Fabric](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md).

Tady je postup nastavení balíčku NuGet:

1. Klikněte pravým tlačítkem na **Řešení Voting** v horní části Průzkumníku řešení a pak klikněte na **Spravovat balíčky NuGet pro řešení**.
2. Klikněte na **Procházet** v horní části navigační nabídky v okně NuGet – Řešení a zaškrtněte políčko **Zahrnout předběžné verze** vedle panelu vyhledávání.
3. Vyhledejte `Microsoft.ApplicationInsights.ServiceFabric.Native` a klikněte na odpovídající balíček NuGet.

    >[!NOTE]
    >Podobným způsobem možná bude potřeba nainstalovat balíček Microsoft.ServiceFabric.Diagnostics.Internal, pokud už nebyl nainstalovaný před instalací balíčku Application Insights.

4. Na pravé straně klikněte na dvě zaškrtávací políčka vedle dvou služeb v aplikaci – **VotingWeb** a **VotingData** – a klikněte na **Nainstalovat**.
    ![Balíček NuGet sady Application Insights SDK](./media/service-fabric-tutorial-monitoring-aspnet/ai-sdk-nuget-new.png)
5. V dialogovém okně *Zkontrolovat změny*, které se otevře, klikněte na **OK** a přijměte *Souhlas s podmínkami licence*. Tím se dokončí přidání balíčku NuGet do služeb.
6. Teď je potřeba v obou službách nastavit inicializátor telemetrie. Provedete to tak, že otevřete soubory *VotingWeb.cs* a *VotingData.cs*. U obou z nich proveďte následující kroky:
    1. Na začátek obou souborů *\<název_služby>.cs* přidejte tyto dva příkazy *using*:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```

    2. Ve vnořeném příkazu *return* metody *CreateServiceInstanceListeners()* nebo *CreateServiceReplicaListeners()* v části *ConfigureServices* > *služby* přidejte mezi dvě deklarované služby Singleton následující kód: `.AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))`
    Tím se do vaší telemetrie přidá *kontext služby*, který umožňuje lepší porozumění zdroji telemetrie v Application Insights. Váš vnořený příkaz *return* v souboru *VotingWeb.cs* by měl vypadat takto:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<HttpClient>(new HttpClient())
                .AddSingleton<FabricClient>(new FabricClient())
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    Podobně byste v souboru *VotingData.cs* měli mít následující:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<IReliableStateManager>(this.StateManager)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

Pečlivě zkontrolujte, že se v obou souborech volá metoda `UseApplicationInsights()`, jak je znázorněno výše.

>[!NOTE]
>Tato ukázková aplikace pro zajištění komunikace služeb používá protokol HTTP. Pokud vyvíjíte aplikaci s využitím vzdálené komunikace služeb V2, budete muset na stejné místo jako výše přidat také následující řádky kódu.

```csharp
ConfigureServices(services => services
    ...
    .AddSingleton<ITelemetryModule>(new ServiceRemotingDependencyTrackingTelemetryModule())
    .AddSingleton<ITelemetryModule>(new ServiceRemotingRequestTrackingTelemetryModule())
)
```

V tuto chvíli jste připraveni nasadit aplikaci. Klikněte na **Spustit** v horní části (nebo stiskněte **F5**) a sada Visual Studio sestaví a zabalí aplikaci, nastaví místní cluster a nasadí do něj aplikaci.

Po nasazení aplikace přejděte na adresu [localhost:8080](localhost:8080), kde by se měla zobrazit jednostránková aplikace Voting Sample. Hlasujte pro několik různých položek, abyste vytvořili nějaká ukázková data a telemetrii – my jsme zvolili zákusky.

![Ukázkové hlasy v Application Insights](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

Až budete hotovi s přidáváním hlasů, můžete některé možnosti hlasování *Odebrat*.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Zobrazení telemetrie a mapy aplikace v Application Insights

Přejděte ke svému prostředku Application Insights na webu Azure Portal.

Kliknutím na **Přehled** se vraťte na úvodní stránku vašeho prostředku. Pak kliknutím na **Vyhledávání** v horní části zobrazte příchozí trasování. Zobrazení trasování v Application Insights trvá několik minut. V případě, že se žádné nezobrazí, chvíli počkejte a stiskněte tlačítko **Aktualizovat** v horní části.
![Zobrazení trasování Application Insights](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

Když se posunete dolů v okně *Vyhledávání*, zobrazí se veškerá příchozí telemetrie, která je součástí Application Insights. Pro každou akci, kterou trvala v hlasovací aplikaci, musí být požadavek PUT odchozí ze *VotingWeb* (PUT hlasy nebo Put [name]), z příchozí žádosti PUT *VotingData* (PUT VoteData nebo Put název []), za nímž následuje pár požadavků GET pro aktualizace dat, které se zobrazí. Vzhledem k tomu, že se jedná o požadavky HTTP, zobrazí se také trasování závislosti protokolu HTTP na místním hostiteli. Tady je příklad se zobrazí jednoho hlasu se přidá: ![Trasování ukázkového požadavku AI](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

Kliknutím na některé z trasování můžete zobrazit další podrobnosti. Zobrazí se užitečné informace o požadavku, které poskytuje Application Insights, včetně *doby odezvy* a *adresy URL požadavku*. Navíc vzhledem k tomu, že jste přidali balíček NuGet specifický pro Service Fabric, se v části *Vlastní data* uvedené níže zobrazí také data o vaší aplikaci v kontextu clusteru Service Fabric. To zahrnuje kontext služby, takže se zobrazí *ID oddílu* a *ID repliky* zdroje požadavku, pomocí kterých můžete lépe vyhledat problémy při diagnostice chyb v aplikaci.

![Podrobnosti o trasování Application Insights](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

Kromě toho můžete kliknutím na *Mapa aplikace* v levé nabídce na stránce Přehled nebo kliknutím na ikonu **Mapa aplikace** přejít do mapy aplikace, ve které se zobrazí dvě propojené služby.

![Podrobnosti o trasování Application Insights](./media/service-fabric-tutorial-monitoring-aspnet/app-map-new.png)

Mapa aplikace vám může pomoct lépe porozumět topologii vaší aplikace, zejména když začnete přidávat více různých služeb, které mezi sebou spoluprací. Poskytuje také základní data o úspěšnosti požadavků a může vám pomoct diagnostikovat požadavky, které selhaly, abyste zjistili, kde pravděpodobně dochází k problémům. Další informace o používání mapy aplikace najdete v tématu [Mapa aplikace v Application Insights](../azure-monitor/app/app-map.md).

## <a name="add-custom-instrumentation-to-your-application"></a>Přidání vlastní instrumentace do aplikace

Přestože Application Insights poskytuje od začátku spoustu telemetrie, možná budete chtít přidat další vlastní instrumentaci. Ta může vycházet z vašich obchodních potřeb nebo být určená k vylepšení diagnostiky v případě, že ve vaší aplikaci dochází k problémům. Application Insights má rozhraní API pro ingestování vlastních událostí a metrik, o kterém si můžete přečíst více [tady](../azure-monitor/app/api-custom-events-metrics.md).

Přidejme do souboru *VoteDataController.cs* (v části *VotingData* > *Kontrolery*) několik vlastních událostí, abychom mohli sledovat přidávání a odstraňování hlasů ze základního *votesDictionary*.

1. Za ostatní příkazy using přidejte `using Microsoft.ApplicationInsights;`.
2. Na začátku třídy za vytvořením objektu *IReliableStateManager* deklarujte nový objekt *TelemetryClient*: `private TelemetryClient telemetry = new TelemetryClient();`.
3. Do funkce *Put()* přidejte událost, která potvrzuje přidání hlasu. Po dokončení transakce přímo před příkaz return *OkResult* přidejte `telemetry.TrackEvent($"Added a vote for {name}");`.
4. Ve funkci *Delete()* je podmínka if/else na základě toho, jestli *votesDictionary* obsahuje hlasy pro danou možnost hlasování.
    1. Do příkazu *if* za metodu *await tx.CommitAsync()* přidejte událost, která potvrzuje odstranění hlasu: `telemetry.TrackEvent($"Deleted votes for {name}");`
    2. Do příkazu *else* před příkaz return přidejte událost, která ukazuje, že nedošlo k odstranění: `telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

Vaše funkce *Put()* a *Delete()* by po přidání událostí měly vypadat podobně jako v následujícím příkladu:

```csharp
// PUT api/VoteData/name
[HttpPut("{name}")]
public async Task<IActionResult> Put(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
        await tx.CommitAsync();
    }

    telemetry.TrackEvent($"Added a vote for {name}");
    return new OkResult();
}

// DELETE api/VoteData/name
[HttpDelete("{name}")]
public async Task<IActionResult> Delete(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        if (await votesDictionary.ContainsKeyAsync(tx, name))
        {
            await votesDictionary.TryRemoveAsync(tx, name);
            await tx.CommitAsync();
            telemetry.TrackEvent($"Deleted votes for {name}");
            return new OkResult();
        }
        else
        {
            telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");
            return new NotFoundResult();
        }
    }
}
```

Až budete hotovi s prováděním změn, **spusťte** aplikaci, aby se sestavila a nasadila její nejnovější verze. Po nasazení aplikace přejděte na adresu [localhost:8080](localhost:8080) a přidejte a odstraňte nějaké možnosti hlasování. Pak se vraťte do svého prostředku Application Insights a zobrazte trasování posledního spuštění (stejně jako předtím může zobrazení trasování v Application Insights trvat 1 až 2 minuty). Pro všechny přidané a odstraněné hlasy by se měla kromě telemetrie odpovědi zobrazit také vlastní událost\*.

![vlastní události](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:
> [!div class="checklist"]
> * Nakonfigurovat Application Insights pro vaši aplikaci
> * Shromažďovat telemetrii odpovědí za účelem trasování komunikace založené na HTTP mezi službami
> * Používat funkci Mapa aplikace v Application Insights
> * Přidat vlastní události pomocí rozhraní API pro Application Insights

Když jste teď dokončili nastavování monitorování a diagnostiky vaší aplikace ASP.NET, vyzkoušejte následující:

* [Další zkoumání monitorování a diagnostiky v Service Fabric](service-fabric-diagnostics-overview.md)
* [Analýza událostí Service Fabric pomocí Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* Další informace o Application Insights najdete v [dokumentaci k Application Insights](https://docs.microsoft.com/azure/application-insights/).

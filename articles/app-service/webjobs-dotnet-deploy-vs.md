---
title: Vývoj a nasazování webových úloh pomocí VS
description: Zjistěte, jak vyvíjet Azure WebJobs ve Visual Studiu a nasazovat je do služby Azure App Service, včetně vytvoření naplánované úlohy.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: feacd463a10bae66dc8fa88a99b9ea60f399e9ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684171"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Vývoj a nasazení webových úloh pomocí sady Visual Studio – Azure App Service

Tento článek vysvětluje, jak pomocí Sady Visual Studio nasadit projekt konzolové aplikace do webové aplikace ve [službě App Service](overview.md) jako Azure [WebJob](https://go.microsoft.com/fwlink/?LinkId=390226). Informace o nasazení webových úloh pomocí [portálu Azure](https://portal.azure.com)najdete v tématu [Spouštění úloh na pozadí pomocí webových úloh](webjobs-create.md).

Do jedné webové aplikace můžete publikovat více webových úloh. Ujistěte se, že každá webová úloha ve webové aplikaci má jedinečný název.

Verze 3.x [sady Azure WebJobs SDK](webjobs-sdk-how-to.md) umožňuje vyvíjet webová úlohy, které běží jako aplikace .NET Core nebo .NET Framework, zatímco verze 2.x podporuje jenom rozhraní .NET Framework. Způsob nasazení projektu WebJobs se liší pro projekty .NET Core oproti projektům rozhraní .NET Framework.

## <a name="webjobs-as-net-core-console-apps"></a>Webové úlohy jako konzolové aplikace .NET Core

Při použití verze 3.x webjobs můžete vytvořit a publikovat WebJobs jako konzolové aplikace .NET Core. Podrobné pokyny k vytvoření a publikování aplikace konzoly .NET Core do Azure jako webjob najdete v tématu [Začínáme s sadou Azure WebJobs SDK pro zpracování na pozadí řízené událostmi](webjobs-sdk-get-started.md).

> [!NOTE]
> Úlohy .NET Core WebJobs nelze propojit s webovými projekty. Pokud potřebujete nasadit webovou úlohu pomocí webové aplikace, měli byste [webovou úlohu vytvořit jako konzolovou aplikaci rozhraní .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Nasazení do Azure App Service

Publikování .NET Core WebJob do služby App Service z Visual Studia používá stejné nástroje jako publikování aplikace ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Typy webových úloh

Ve výchozím nastavení se webová úloha publikovaná z projektu konzoly .NET Core spustí pouze v případě, že je spuštěna nebo na vyžádání. Můžete také aktualizovat projekt [spustit podle plánu](#scheduled-execution) nebo spustit nepřetržitě.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Plánované spuštění

Při publikování aplikace konzoly .NET Core do Azure se do projektu přidá nový soubor *settings.job.* Tento soubor slouží k nastavení plánu provádění webové úlohy. Další informace naleznete [v tématu Scheduling a triggered WebJob](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Průběžné provádění

Pomocí sady Visual Studio můžete změnit webovou úlohu tak, aby běžela nepřetržitě, když je v Azure povoleno vždy zapnuto.

1. Pokud jste tak ještě neučinili, [publikujte projekt do Azure](#deploy-to-azure-app-service).

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

1. Na kartě **Publikovat** zvolte **Nastavení**. 

1. V dialogovém okně **Nastavení profilu** zvolte **Souvislý** pro **typ webové úlohy**a zvolte **Uložit**.

    ![Dialogové okno Publikovat nastavení pro webovou úlohu](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Vyberte **Publikovat,** chcete-li znovu publikovat webovou úlohu s aktualizovaným nastavením.

## <a name="webjobs-as-net-framework-console-apps"></a>Webové úlohy jako konzolové aplikace rozhraní .NET Framework  

Když Visual Studio nasadí projekt aplikace .NET Framework Console s podporou WebJobs, zkopíruje soubory runtime do příslušné složky ve webové aplikaci (*App_Data/úlohy/souvislé* pro nepřetržité webové úlohy a *App_Data/úlohy/spuštěné* pro naplánované nebo na vyžádání).

V projektu s podporou webjobs jsou přidány následující položky:

* Balíček [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet.
* [Webjob-publish-settings.json](#publishsettings) soubor, který obsahuje nastavení nasazení a plánovače. 

![Diagram znázorňující, co je přidáno do konzolové aplikace, aby bylo možné nasazení jako webovou úlohu](./media/webjobs-dotnet-deploy-vs/convert.png)

Tyto položky můžete přidat do existujícího projektu konzolové aplikace nebo pomocí šablony vytvořit nový projekt konzolové aplikace s podporou webjobs. 

Projekt můžete nasadit jako webovou úlohu samostatně nebo jej propojit s webovým projektem tak, aby se automaticky nasazoval při každém nasazení webového projektu. Chcete-li propojit projekty, Visual Studio obsahuje název projektu s podporou WebJobs v souboru [webjobs-list.json](#webjobslist) ve webovém projektu.

![Diagram znázorňující projekt WebJob odkazující na webový projekt](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Požadavky

Pokud používáte Visual Studio 2015, nainstalujte [Azure SDK pro .NET (Visual Studio 2015).](https://azure.microsoft.com/downloads/)

Pokud používáte Visual Studio 2017, nainstalujte [úlohu vývoje Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a>Povolení nasazení úloh WebJobs pro existující projekt konzolové aplikace

Máte dvě možnosti:

* [Povolte automatické nasazení s webovým projektem](#convertlink).

  Nakonfigurujte existující projekt konzolové aplikace tak, aby se při nasazení webového projektu automaticky nasazoval jako webovou úlohu. Tuto možnost použijte, pokud chcete spustit webovou úlohu ve stejné webové aplikaci, ve které spustíte související webovou aplikaci.

* [Povolte nasazení bez webového projektu](#convertnolink).

  Nakonfigurujte existující projekt konzolové aplikace, který se nasadí jako webovou úlohu sám, bez odkazu na webový projekt. Tuto možnost použijte, pokud chcete spustit webovou úlohu ve webové aplikaci samostatně, přičemž ve webové aplikaci není spuštěna žádná webová aplikace. Můžete to provést, abyste mohli škálovat prostředky WebJob nezávisle na prostředcích webových aplikací.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a>Povolení automatického nasazení webových úloh pomocí webového projektu

1. Klikněte pravým tlačítkem myši na webový projekt v **Průzkumníku řešení**a potom klikněte na **Přidat** > **existující projekt jako Azure WebJob**.
   
    ![Existující projekt jako Azure WebJob](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Zobrazí se dialogové okno [Přidat Azure WebJob.](#configure)
2. V rozevíracím seznamu **Název projektu** vyberte projekt konzolové aplikace, který chcete přidat jako webovou úlohu.
   
    ![Výběr projektu v dialogovém okně Přidat Azure WebJob](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Dokončete dialogové okno [Přidat Azure WebJob](#configure) a klikněte na **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a>Povolení nasazení webových úloh bez webového projektu
1. Klepněte pravým tlačítkem myši na projekt konzolové aplikace v **Průzkumníku řešení**a potom klikněte na **příkaz Publikovat jako Azure WebJob...**. 
   
    ![Publikování jako Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Zobrazí se dialogové okno [Přidat Azure WebJob](#configure) s vybraným projektem v poli **Název projektu.**
2. Dokončete dialogové okno [Přidat Azure WebJob](#configure) a klikněte na **OK**.
   
   Zobrazí se Průvodce **publikováním webu.**  Pokud nechcete publikovat okamžitě, zavřete průvodce. Zadané nastavení se uloží, když chcete projekt [nasadit](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Vytvoření nového projektu s podporou webjobs
Chcete-li vytvořit nový projekt s podporou webjobs, můžete použít šablonu projektu konzolové aplikace a povolit nasazení webjobs, jak je vysvětleno v [předchozí části](#convert). Jako alternativu můžete použít šablonu nového projektu WebJobs:

* [Použití šablony nového projektu WebJobs pro nezávislou webovou úlohu](#createnolink)
  
    Vytvořte projekt a nakonfigurujte jej tak, aby se nasazoval sám jako webovou úlohu bez odkazu na webový projekt. Tuto možnost použijte, pokud chcete spustit webovou úlohu ve webové aplikaci samostatně, přičemž ve webové aplikaci není spuštěna žádná webová aplikace. Můžete to provést, abyste mohli škálovat prostředky WebJob nezávisle na prostředcích webových aplikací.
* [Použití šablony nového projektu WebJobs pro webovou úlohu propojenou s webovým projektem](#createlink)
  
    Vytvořte projekt, který je nakonfigurován pro automatické nasazení jako webová úloha při nasazení webového projektu ve stejném řešení. Tuto možnost použijte, pokud chcete spustit webovou úlohu ve stejné webové aplikaci, ve které spustíte související webovou aplikaci.

> [!NOTE]
> Šablona nového projektu WebJobs automaticky nainstaluje balíčky NuGet a obsahuje kód v *Program.cs* pro [webjobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Pokud nechcete používat webjobs sdk, odeberte `host.RunAndBlock` nebo změňte příkaz v *Program.cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a>Použití šablony nového projektu WebJobs pro nezávislou webovou úlohu
1. Klikněte na **Soubor** > **nového projektu**a potom v dialogovém okně Nový **projekt** klikněte na **Cloud** > Azure**WebJob (.NET Framework).**
   
    ![Dialogové okno Nový projekt se šablonou WebJob](./media/webjobs-dotnet-deploy-vs/np.png)
2. Postupujte podle pokynů uvedených výše, [aby se projekt konzolové aplikace nezávislý projekt WebJobs](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a>Použití šablony nového projektu WebJobs pro webovou úlohu propojenou s webovým projektem
1. Klikněte pravým tlačítkem myši na webový projekt v **Průzkumníku řešení**a potom klikněte na **Přidat** > **nový projekt Azure WebJob Project**.
   
    ![Nová položka nabídky projektu Azure WebJob Project](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Zobrazí se dialogové okno [Přidat Azure WebJob.](#configure)
2. Dokončete dialogové okno [Přidat Azure WebJob](#configure) a klikněte na **OK**.

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>Dialogové okno Přidat Azure WebJob
Dialogové **okno Přidat Azure WebJob** umožňuje zadat název webové úlohy a nastavit režim spuštění pro webovou úlohu. 

![Dialogové okno Přidat Azure WebJob](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Pole v tomto dialogovém okně odpovídají polím v dialogovém okně **Přidat webovou úlohu** na portálu Azure. Další informace naleznete v [tématu Spuštění úloh na pozadí pomocí webových úloh](webjobs-create.md).

> [!NOTE]
> * Informace o nasazení příkazového řádku naleznete [v tématu Povolení příkazového řádku nebo průběžného doručování webových úloh Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Pokud nasadíte webovou úlohu a pak se rozhodnete změnit typ webjoba a znovu nasadit, budete muset odstranit soubor *webjobs-publish-settings.json.* Díky tomu visual studio znovu zobrazí možnosti publikování, takže můžete změnit typ webjob.
> * Pokud nasadíte webovou úlohu a později změníte režim spuštění z nepřetržitého na nesouvislý nebo naopak, Visual Studio vytvoří novou webovou úlohu v Azure při opětovném nasazení. Pokud změníte jiné nastavení plánování, ale ponecháte režim spuštění stejný nebo přepnete mezi Naplánovanou a Na vyžádání, Visual Studio aktualizuje stávající úlohu, nikoli vytvoří novou.
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.json
Při konfiguraci nasazení konzolové aplikace pro webjobs nainstaluje visual studio balíček [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet a uloží informace o plánování do souboru *webjob-publish-settings.json* do složky *Vlastnosti* projektu projektu WebJobs. Zde je příklad tohoto souboru:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Tento soubor můžete upravit přímo a Visual Studio poskytuje technologie IntelliSense. Schéma souboru je uloženo [https://schemastore.org](https://schemastore.org/schemas/json/webjob-publish-settings.json) na a lze jej zobrazit zde.  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.json
Když propojíte projekt s podporou webjobs s webovým projektem, visual studio uloží název projektu WebJobs do souboru *webjobs-list.json* ve složce *Vlastnosti* webového projektu. Seznam může obsahovat více projektů WebJobs, jak je znázorněno v následujícím příkladu:

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

Tento soubor můžete upravit přímo a Visual Studio poskytuje technologie IntelliSense. Schéma souboru je uloženo [https://schemastore.org](https://schemastore.org/schemas/json/webjobs-list.json) na a lze jej zobrazit zde.

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Nasazení projektu WebJobs
Projekt WebJobs, který jste propojili s webovým projektem, se automaticky nasadí s webovým projektem. Informace o nasazení webového projektu najdete v **tématu Návody nasazení** > **aplikace** v levém navigačním panelu.

Chcete-li nasadit projekt WebJobs sám, klepněte pravým tlačítkem myši na projekt v **Průzkumníkovi řešení** a klikněte na **publikovat jako Azure WebJob...**. 

![Publikování jako Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)

U nezávislé webové úlohy se zobrazí stejný průvodce **publikováním webu,** který se používá pro webové projekty, ale má méně nastavení, které je k dispozici pro změnu.

## <a name="scheduling-a-triggered-webjob"></a>Naplánování aktivované webové úlohy

WebJobs používá soubor *settings.job* k určení, kdy je webová úloha spuštěna. Tento soubor slouží k nastavení plánu provádění webové úlohy. Následující příklad se spouští každou hodinu od 9:00 do 17:00:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Tento soubor musí být umístěn v kořenovém adresáři složky WebJobs `wwwroot\app_data\jobs\triggered\{job name}` spolu `wwwroot\app_data\jobs\continuous\{job name}`se skriptem webjob, například nebo . Při nasazování webové úlohy z `settings.job` aplikace Visual Studio označte vlastnosti souboru jako **Kopírovat, pokud je novější**. 

Když [vytvoříte webovou úlohu z portálu Azure](webjobs-create.md), vytvoří se za vás soubor settings.job.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>Cron výrazy

WebJobs používá stejné cron výrazy pro plánování jako aktivační událost časovače v Azure Functions. Další informace o podpoře cronu najdete v [článku s odkazem na aktivační událost časovače](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingjob-reference"></a>setting.job reference

WebJobs podporuje následující nastavení:

| **Nastavení** | **Typ**  | **Popis** |
| ----------- | --------- | --------------- |
| `is_in_place` | Všechny | Umožňuje spuštění úlohy na místě, aniž by byla nejprve zkopírována do dočasné složky. Další informace naleznete v [tématu Pracovní adresář WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Průběžný | WebJobs spusťte pouze na jedné instanci při škálování. Další informace naleznete [v tématu Nastavení průběžné úlohy jako singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Spuštěna | Spusťte webovou úlohu podle plánu cron. Další informace naleznete v [článku s odkazem na aktivační událost časovače](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Všechny | Umožňuje řízení chování vypnutí. Další informace naleznete v [tématu Graceful shutdown](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o sdk webjobs](webjobs-sdk-how-to.md)

---
title: Vývoj a nasazení WebJobs pomocí sady Visual Studio – Azure
description: Naučte se vyvíjet a nasazovat Azure WebJobs pro Azure App Service pomocí sady Visual Studio.
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 2695ee2751a2834466c42d224101af246b829aca
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717624"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Vývoj a nasazení WebJobs pomocí sady Visual Studio – Azure App Service

Tento článek vysvětluje, jak pomocí sady Visual Studio nasadit projekt konzolové aplikace do webové aplikace v [App Service](overview.md) jako webovou [úlohu Azure](https://go.microsoft.com/fwlink/?LinkId=390226). Informace o tom, jak nasadit webové úlohy pomocí [Azure Portal](https://portal.azure.com), najdete v tématu [spouštění úloh na pozadí pomocí WebJobs](webjobs-create.md).

Do jedné webové aplikace můžete publikovat několik webových úloh. Ujistěte se, že každá webová úloha ve webové aplikaci má jedinečný název.

Verze 3. x sady [Azure WEBJOBS SDK](webjobs-sdk-how-to.md) umožňuje vyvíjet webové úlohy, které běží jako aplikace .NET Core nebo aplikace .NET Framework, zatímco verze 2. x podporuje pouze .NET Framework. Způsob, jakým nasazujete projekt WebJobs, se liší v projektech .NET Core a .NET Framework.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs jako konzolové aplikace .NET Core

Při použití verze 3. x WebJobs můžete vytvářet a publikovat webové úlohy jako konzolové aplikace .NET Core. Podrobné pokyny k vytvoření a publikování konzolové aplikace .NET Core v Azure jako webové úlohy najdete v tématu [Začínáme s Azure WEBJOBS SDK pro zpracování na pozadí](webjobs-sdk-get-started.md)založeném na událostech.

> [!NOTE]
> Webové úlohy .NET Core nelze propojit s webovými projekty. Pokud potřebujete webovou úlohu nasadit pomocí webové aplikace, měli byste [vytvořit webovou úlohu jako konzolovou aplikaci .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Nasazení do Azure App Service

Publikování webové úlohy .NET Core do App Service ze sady Visual Studio používá stejné nástroje jako publikování ASP.NET Core aplikace.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Typy webové úlohy

Ve výchozím nastavení se webová úloha publikovaná z konzoly projektu .NET Core spouští jenom v případě, že se aktivuje nebo na vyžádání. Projekt můžete také aktualizovat tak, aby [běžel podle plánu](#scheduled-execution) nebo běžel průběžně.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Plánované spuštění

Když publikujete konzolovou aplikaci .NET Core do Azure, přidá se do projektu nový soubor *nastavení. job* . Tento soubor použijte k nastavení plánu spuštění pro webovou úlohu. Další informace najdete v tématu [plánování aktivované webové úlohy](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Průběžné provádění

Pomocí sady Visual Studio můžete změnit, aby se webová úloha spouštěla nepřetržitě, když je v Azure povolená možnost Always On.

1. Pokud jste to ještě neudělali, [publikujte projekt do Azure](#deploy-to-azure-app-service).

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

1. Na kartě **publikovat** vyberte **Nastavení**. 

1. V dialogovém okně **nastavení profilu** vyberte možnost **souvislá** pro **typ úlohy WebJob**a klikněte na **Uložit**.

    ![Dialog publikování nastavení pro webovou úlohu](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Vyberte **publikovat** a publikujte webovou úlohu znovu s aktualizovaným nastavením.

## <a name="webjobs-as-net-framework-console-apps"></a>Webové úlohy jako .NET Framework konzolových aplikacích  

Když Visual Studio nasadí projekt konzolové aplikace .NET Framework s podporou WebJobs, kopíruje soubory za běhu do příslušné složky ve webové aplikaci (*App_Data/úlohy/nepřetržitě* pro průběžné WebJobs a *App_Data/úlohy/aktivované* pro plánované nebo na vyžádání WebJobs.

K projektu s podporou WebJobs jsou přidány následující položky:

* Balíček NuGet [Microsoft. Web. WebJobs. Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* Soubor [WebJob – Publish-Settings. JSON](#publishsettings) , který obsahuje nastavení nasazení a plánovače. 

![Diagram znázorňující, co je přidáno do konzolové aplikace, aby bylo možné nasazení aktivovat jako webovou úlohu](./media/webjobs-dotnet-deploy-vs/convert.png)

Tyto položky můžete přidat do existujícího projektu konzolové aplikace nebo použít šablonu k vytvoření nového projektu konzolové aplikace s podporou WebJobs. 

Projekt můžete nasadit jako webovou úlohu samostatně nebo ho připojit k webovému projektu tak, aby se automaticky nasadil při každém nasazení webového projektu. Aby bylo možné propojit projekty, Visual Studio obsahuje název projektu s podporou WebJobs v souboru [WebJobs-list. JSON](#webjobslist) ve webovém projektu.

![Diagram znázorňující, že se projekt úlohy WebJob odkazuje na webový projekt](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Požadavky

Pokud používáte sadu Visual Studio 2015, nainstalujte [sadu Azure SDK pro .NET (Visual studio 2015)](https://azure.microsoft.com/downloads/).

Pokud používáte Visual Studio 2019, nainstalujte [úlohu vývoj pro Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a id="convert"></a>Povolit nasazení WebJobs pro existující projekt konzolové aplikace

Máte dvě možnosti:

* [Povolte automatické nasazení pomocí webového projektu](#convertlink).

  Nakonfigurujte existující projekt konzolové aplikace tak, aby se automaticky nasadil jako webová úloha při nasazení webového projektu. Tuto možnost použijte, pokud chcete spustit webovou úlohu ve stejné webové aplikaci, ve které spouštíte související webovou aplikaci.

* [Povolit nasazení bez webového projektu](#convertnolink).

  Nakonfigurujte existující projekt konzolové aplikace tak, aby se nasadil jako webová úloha samostatně, bez propojení s webovým projektem. Tuto možnost použijte, pokud chcete webovou aplikaci spustit samostatně, a to bez spuštěné webové aplikace ve webové aplikaci. To můžete chtít udělat, aby bylo možné škálovat prostředky webové úlohy nezávisle na svých prostředcích webových aplikací.

#### <a id="convertlink"></a>Povolení automatického nasazení webových úloh pomocí webového projektu

1. Klikněte pravým tlačítkem na webový projekt v **Průzkumník řešení**a pak klikněte na **Přidat** > **existující projekt jako webovou úlohu Azure**.
   
    ![Existující projekt jako webová úloha Azure](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Zobrazí se dialogové okno [Přidat webovou úlohu Azure](#configure) .
2. V rozevíracím seznamu **název projektu** vyberte projekt konzolové aplikace, který chcete přidat jako webovou úlohu.
   
    ![Výběr projektu v dialogovém okně Přidat webovou úlohu Azure](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Dokončete dialog [Přidat webovou úlohu Azure](#configure) a pak klikněte na tlačítko **OK**. 

#### <a id="convertnolink"></a>Povolit nasazení webové úlohy bez webového projektu
1. Klikněte pravým tlačítkem na projekt konzolové aplikace v **Průzkumník řešení**a pak klikněte na **Publikovat jako webovou úlohu Azure...** . 
   
    ![Publikovat jako webovou úlohu Azure](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Zobrazí se dialogové okno [Přidat webovou úlohu Azure](#configure) s projektem vybraným v poli **název projektu** .
2. Dokončete dialog [Přidat webovou úlohu Azure](#configure) a pak klikněte na tlačítko **OK**.
   
   Zobrazí se průvodce **publikováním webu** .  Pokud nechcete publikovat hned, zavřete průvodce. Nastavení, která jste zadali, budou uložena v případě, že chcete [nasadit projekt](#deploy).

### <a id="create"></a>Vytvořit nový projekt s podporou WebJobs
Chcete-li vytvořit nový projekt s podporou WebJobs, můžete použít šablonu projektu Konzolová aplikace a povolit nasazení webových úloh, jak je vysvětleno v [předchozí části](#convert). Alternativně můžete použít šablonu New-Project šablony WebJobs:

* [Použití šablony WebJobs New-Project pro nezávislou úlohu WebJob](#createnolink)
  
    Vytvořte projekt a nakonfigurujte ho tak, aby se nasadil sám jako webová úloha bez odkazu na webový projekt. Tuto možnost použijte, pokud chcete webovou aplikaci spustit samostatně, a to bez spuštěné webové aplikace ve webové aplikaci. To můžete chtít udělat, aby bylo možné škálovat prostředky webové úlohy nezávisle na svých prostředcích webových aplikací.
* [Použití šablony WebJobs New-Project pro webovou úlohu propojenou s webovým projektem](#createlink)
  
    Vytvořte projekt, který je nakonfigurován pro automatické nasazení jako webovou úlohu, když je nasazen webový projekt ve stejném řešení. Tuto možnost použijte, pokud chcete spustit webovou úlohu ve stejné webové aplikaci, ve které spouštíte související webovou aplikaci.

> [!NOTE]
> Šablona New – projekt WebJobs automaticky nainstaluje balíčky NuGet a obsahuje kód v *program.cs* pro [sadu WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Pokud nechcete používat sadu WebJobs SDK, odeberte nebo změňte `host.RunAndBlock` příkaz v *program.cs*.
> 
> 

#### <a id="createnolink"></a>Použití šablony WebJobs New-Project pro nezávislou úlohu WebJob
1. Klikněte **na soubor** > **Nový projekt**a potom v dialogovém **okně Nový projekt** klikněte na **cloudová** > **Webová úloha Azure (.NET Framework)** .
   
    ![Dialog Nový projekt znázorňující šablonu WebJob](./media/webjobs-dotnet-deploy-vs/np.png)
2. Postupujte podle pokynů uvedených výše a [zpřístupněte konzolovou aplikaci projekt](#convertnolink)nezávisle na projektu WebJobs.

#### <a id="createlink"></a>Použití šablony WebJobs New-Project pro webovou úlohu propojenou s webovým projektem
1. V **Průzkumník řešení**klikněte pravým tlačítkem na webový projekt a pak klikněte na **Přidat** > **Nový projekt úlohy WebJob Azure**.
   
    ![Nový záznam nabídky projektu úlohy WebJob Azure](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Zobrazí se dialogové okno [Přidat webovou úlohu Azure](#configure) .
2. Dokončete dialog [Přidat webovou úlohu Azure](#configure) a pak klikněte na tlačítko **OK**.

### <a id="configure"></a>Dialogové okno Přidat webovou úlohu Azure
Dialog **Přidat Azure WebJob** umožňuje zadat název webové úlohy a nastavení režimu spuštění pro vaši webovou úlohu. 

![Přidat dialog Azure WebJob](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Pole v tomto dialogovém okně odpovídají polím v dialogovém okně **Přidat webovou úlohu** v Azure Portal. Další informace najdete v tématu [spouštění úloh na pozadí pomocí WebJobs](webjobs-create.md).

> [!NOTE]
> * Informace o nasazení příkazového řádku najdete v tématu [Povolení příkazového řádku nebo průběžného doručování Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Pokud nasadíte webovou úlohu a potom se rozhodnete, že chcete změnit typ webové úlohy a znovu nasadit, bude nutné odstranit soubor *WebJobs-Publish-Settings. JSON* . Díky tomu bude aplikace Visual Studio znovu zobrazovat možnosti publikování, takže můžete změnit typ webové úlohy.
> * Pokud nasadíte webovou úlohu a později změníte režim spuštění z nepřetržitého na nesouvislý nebo naopak, Visual Studio vytvoří novou webovou úlohu v Azure při opětovném nasazení. Pokud změníte jiné nastavení plánování, ale ponecháte režim spuštění stejný nebo přepínač mezi plánovanými a na vyžádání, Visual Studio aktualizuje existující úlohu místo vytvoření nového.
> 
> 

### <a id="publishsettings"></a>webjob-publish-settings.json
Když konfigurujete konzolovou aplikaci pro nasazení služby WebJobs, Visual Studio nainstaluje balíček NuGet [Microsoft. Web. WebJobs. Publisher](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) a uloží informace o plánování do souboru *WebJob – Publish-Settings. JSON* v projektu *. Složka vlastností* projektu WebJobs Tady je příklad tohoto souboru:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Tento soubor můžete upravit přímo a aplikace Visual Studio poskytuje technologii IntelliSense. Schéma souboru je uloženo v umístění [https://schemastore.org](https://schemastore.org/schemas/json/webjob-publish-settings.json) a lze ho zobrazit.  

### <a id="webjobslist"></a>webjobs-list.json
Když propojíte projekt s aktivními úlohami na webový projekt, Visual Studio uloží název projektu WebJobs do souboru *WebJobs-list. JSON* ve složce *vlastností* webového projektu. Seznam může obsahovat více projektů WebJobs, jak je znázorněno v následujícím příkladu:

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

Tento soubor můžete upravit přímo a aplikace Visual Studio poskytuje technologii IntelliSense. Schéma souboru je uloženo v umístění [https://schemastore.org](https://schemastore.org/schemas/json/webjobs-list.json) a lze ho zobrazit.

### <a id="deploy"></a>Nasazení projektu WebJobs
Projekt WebJobs, který jste propojili s webovým projektem, se nasadí automaticky s webovým projektem. Informace o nasazení webového projektu naleznete v tématu **Průvodci** > **nasazením aplikace** v levém navigačním panelu.

Pokud chcete nasadit projekt WebJobs sám o sobě, klikněte pravým tlačítkem myši na projekt v **Průzkumník řešení** a klikněte na **Publikovat jako webovou úlohu Azure...** . 

![Publikovat jako webovou úlohu Azure](./media/webjobs-dotnet-deploy-vs/paw.png)

U nezávislé úlohy WebJob se zobrazí stejný průvodce **publikováním webu** , který se používá pro webové projekty, ale s menším množstvím nastavení, které lze změnit.

## <a name="scheduling-a-triggered-webjob"></a>Plánování aktivované webové úlohy

WebJobs používá soubor *Settings. job* k určení, kdy se webová úloha spustí. Tento soubor použijte k nastavení plánu spuštění pro webovou úlohu. Následující příklad se spouští každou hodinu od 9 do 5 hodin:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Tento soubor se musí nacházet v kořenovém adresáři složky WebJobs, a to na straně skriptu vaší webové úlohy, `wwwroot\app_data\jobs\triggered\{job name}` jako `wwwroot\app_data\jobs\continuous\{job name}`je například nebo. Když nasadíte webovou úlohu ze sady Visual Studio, `settings.job` označte vlastnosti souboru jako **kopie, pokud je novější**. 

Když [vytvoříte webovou úlohu z Azure Portal](webjobs-create.md), vytvoří se soubor Settings. job.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>Výrazy CRON

WebJobs používá stejné výrazy CRON pro plánování jako Trigger časovače v Azure Functions. Další informace o podpoře CRON najdete v [článku referenční](../azure-functions/functions-bindings-timer.md#cron-expressions)informace o aktivačních událostech časovače.

### <a name="settingjob-reference"></a>nastavení odkazu na úlohu

WebJobs podporuje následující nastavení:

| **Nastavení** | **Typ**  | **Popis** |
| ----------- | --------- | --------------- |
| `is_in_place` | Vše | Umožňuje, aby se úloha běžela na místě, aniž by se nejdřív zkopírovala do dočasné složky. Další informace najdete v tématu [pracovní adresář WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Nepřetržitě | Při horizontálním navýšení kapacity spouštějte jenom WebJobs na jedné instanci. Další informace najdete v tématu [Nastavení průběžné úlohy jako singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Aktivované | Spusťte úlohu WebJob v plánu založeném na CRON. Další informace najdete v [článku referenční](../azure-functions/functions-bindings-timer.md#cron-expressions)informace o aktivačních událostech časovače. |
| `stopping_wait_time`| Vše | Umožňuje kontrolu nad chováním při vypnutí. Další informace najdete v tématu [řádné vypnutí](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o sadě WebJobs SDK](webjobs-sdk-how-to.md)

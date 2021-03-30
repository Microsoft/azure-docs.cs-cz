---
title: Vývoj a nasazení WebJobs pomocí sady Visual Studio
description: Naučte se vyvíjet Azure WebJobs v aplikaci Visual Studio a nasazovat je do Azure App Service, včetně vytvoření naplánované úlohy.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: devx-track-csharp, vs-azure
ms.date: 07/30/2020
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: de10903be86b52b3415b57a53be81e7fd1661f63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89226025"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio"></a>Vývoj a nasazení WebJobs pomocí sady Visual Studio

Tento článek vysvětluje, jak pomocí sady Visual Studio nasadit projekt konzolové aplikace do webové aplikace v [Azure App Service](overview.md) jako webovou [úlohu Azure](https://go.microsoft.com/fwlink/?LinkId=390226). Informace o tom, jak nasadit webové úlohy pomocí [Azure Portal](https://portal.azure.com), najdete v tématu [spouštění úloh na pozadí pomocí webjobs v Azure App Service](webjobs-create.md).

Můžete se rozhodnout pro vývoj webové úlohy, která běží jako [aplikace .NET Core](#webjobs-as-net-core-console-apps) nebo [aplikace .NET Framework](#webjobs-as-net-framework-console-apps). Verze 3. x sady [Azure WEBJOBS SDK](webjobs-sdk-how-to.md) umožňuje vyvíjet webové úlohy, které běží jako aplikace .NET Core nebo aplikace .NET Framework, zatímco verze 2. x podporuje pouze .NET Framework. Způsob nasazení projektu WebJobs je jiný než u projektů .NET Core než u .NET Frameworkch projektů.

Můžete publikovat více webových úloh do jedné webové aplikace za předpokladu, že každá webová úloha ve webové aplikaci má jedinečný název.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs jako konzolové aplikace .NET Core

S verzí 3. x sady Azure WebJobs SDK můžete vytvářet a publikovat webové úlohy jako konzolové aplikace .NET Core. Podrobné pokyny k vytvoření a publikování konzolové aplikace .NET Core v Azure jako webové úlohy najdete v tématu [Začínáme s Azure WEBJOBS SDK pro zpracování na pozadí založeném na událostech](webjobs-sdk-get-started.md).

> [!NOTE]
> Webové úlohy .NET Core nelze propojit s webovými projekty. Pokud potřebujete webovou úlohu nasadit pomocí webové aplikace, [Vytvořte své WebJobs jako konzolovou aplikaci .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Nasazení do Azure App Service

Publikování webové úlohy .NET Core do Azure App Service ze sady Visual Studio používá stejné nástroje jako publikování ASP.NET Core aplikace.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

## <a name="webjobs-as-net-framework-console-apps"></a>Webové úlohy jako .NET Framework konzolových aplikacích  

Pokud použijete Visual Studio k nasazení projektu konzolové aplikace .NET Framework s podporou WebJobs, zkopíruje soubory runtime do příslušné složky ve webové aplikaci (*App_Data/Jobs/Continuous* pro nepřetržité WebJobs a *App_Data/Jobs/Triggered* pro plánované webové úlohy nebo na vyžádání).

Visual Studio přidá následující položky do projektu s podporou WebJobs:

* Balíček NuGet [Microsoft. Web. WebJobs. Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* [webjob-publish-settings.jsv](#publishsettings) souboru, který obsahuje nastavení nasazení a plánovače. 

![Diagram znázorňující, co se přidalo do konzolové aplikace, aby bylo možné nasazení aktivovat jako webovou úlohu](./media/webjobs-dotnet-deploy-vs/convert.png)

Tyto položky můžete přidat do existujícího projektu aplikace konzoly nebo použít šablonu k vytvoření nového projektu konzolové aplikace s podporou WebJobs. 

Nasaďte projekt jako webovou úlohu samostatně nebo ji propojte s webovým projektem tak, aby se automaticky nasadil při každém nasazení webového projektu. Aby bylo možné propojit projekty, Visual Studio obsahuje název projektu s podporou WebJobs v [webjobs-list.js](#webjobslist) souboru ve webovém projektu.

![Diagram znázorňující, že se projekt úlohy WebJob odkazuje na webový projekt](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Požadavky

Nainstalujte Visual Studio 2017 nebo Visual Studio 2019 s [úlohou vývoj pro Azure](/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a name="enable-webjobs-deployment-for-an-existing-console-app-project"></a><a id="convert"></a> Povolit nasazení WebJobs pro existující projekt konzolové aplikace

Máte dvě možnosti:

* [Povolte automatické nasazení pomocí webového projektu](#convertlink).

  Nakonfigurujte existující projekt konzolové aplikace tak, aby se automaticky nasadil jako webová úloha při nasazení webového projektu. Tuto možnost použijte, pokud chcete spustit webovou úlohu ve stejné webové aplikaci, ve které spouštíte související webovou aplikaci.

* [Povolit nasazení bez webového projektu](#convertnolink).

  Nakonfigurujte existující projekt konzolové aplikace tak, aby se nasadil jako webová úloha samostatně, bez odkazu na webový projekt. Tuto možnost použijte, pokud chcete webovou aplikaci spustit samostatně, a to bez spuštěné webové aplikace ve webové aplikaci. Možná budete chtít škálovat prostředky webové úlohy nezávisle na svých prostředcích webových aplikací.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> Povolení automatického nasazení webových úloh pomocí webového projektu

1. Klikněte pravým tlačítkem na webový projekt v **Průzkumník řešení** a pak vyberte **Přidat**  >  **existující projekt jako webovou úlohu Azure**.
   
    ![Existující projekt jako webová úloha Azure](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Zobrazí se dialogové okno [Přidat webovou úlohu Azure](#configure) .
2. V rozevíracím seznamu **název projektu** vyberte projekt konzolové aplikace, který chcete přidat jako webovou úlohu.
   
    ![Výběr projektu v dialogovém okně Přidat webovou úlohu Azure](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Dokončete dialog [Přidat webovou úlohu Azure](#configure) a pak vyberte **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> Povolit nasazení webové úlohy bez webového projektu
1. Klikněte pravým tlačítkem na projekt konzolové aplikace v **Průzkumník řešení** a pak vyberte **Publikovat jako webovou úlohu Azure**. 
   
    ![Publikovat jako webovou úlohu Azure](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Zobrazí se dialogové okno [Přidat webovou úlohu Azure](#configure) s projektem vybraným v poli **název projektu** .
2. Dokončete dialog [Přidat webovou úlohu Azure](#configure) a pak vyberte **OK**.
   
   Zobrazí se průvodce **publikováním webu** . Pokud nechcete publikovat hned, zavřete průvodce. Nastavení, která jste zadali, budou uložena v případě, že chcete [nasadit projekt](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Vytvořit nový projekt s podporou WebJobs
Chcete-li vytvořit nový projekt s podporou WebJobs, použijte šablonu projektu Konzolová aplikace a povolte nasazení WebJobs, jak je vysvětleno v [předchozí části](#convert). Alternativně můžete použít šablonu New-Project šablony WebJobs:

* [Použití šablony WebJobs New-Project pro nezávislou úlohu WebJob](#createnolink)
  
    Vytvořte projekt a nakonfigurujte ho tak, aby se nasadil sám jako webová úloha bez odkazu na webový projekt. Tuto možnost použijte, pokud chcete webovou aplikaci spustit samostatně, a to bez spuštěné webové aplikace ve webové aplikaci. Možná budete chtít škálovat prostředky webové úlohy nezávisle na svých prostředcích webových aplikací.
* [Použití šablony WebJobs New-Project pro webovou úlohu propojenou s webovým projektem](#createlink)
  
    Vytvořte projekt, který je nakonfigurován pro automatické nasazení jako webovou úlohu při nasazení webového projektu ve stejném řešení. Tuto možnost použijte, pokud chcete spustit webovou úlohu ve stejné webové aplikaci, ve které spouštíte související webovou aplikaci.

> [!NOTE]
> Šablona nové projektu WebJobs automaticky nainstaluje balíčky NuGet a obsahuje kód v *programu program. cs* pro [sadu WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Pokud nechcete používat sadu WebJobs SDK, odeberte nebo změňte `host.RunAndBlock` příkaz v *programu program. cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> Použití šablony WebJobs New-Project pro nezávislou úlohu WebJob
1. Vyberte **soubor**  >  **Nový**  >  **projekt**. V dialogovém okně **Crete a nový projekt** vyhledejte a vyberte **Azure WebJob (.NET Framework)** pro C#.
   
2. Postupujte podle předchozích pokynů a [zpřístupněte projekt konzolové aplikace nezávisle na projektu WebJobs](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> Použití šablony WebJobs New-Project pro webovou úlohu propojenou s webovým projektem
1. Klikněte pravým tlačítkem na webový projekt v **Průzkumník řešení** a pak vyberte **Přidat**  >  **Nový projekt webové úlohy Azure**.
   
    ![Nový záznam nabídky projektu úlohy WebJob Azure](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Zobrazí se dialogové okno [Přidat webovou úlohu Azure](#configure) .
2. Dokončete dialog [Přidat webovou úlohu Azure](#configure) a pak vyberte **OK**.


### <a name="webjob-publish-settingsjson-file"></a><a id="publishsettings"></a>webjob-publish-settings.jsv souboru
Když nakonfigurujete konzolovou aplikaci pro nasazení služby WebJobs, Visual Studio nainstaluje balíček NuGet [Microsoft. Web. WebJobs. Publisher](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) a uloží informace o plánování do *webjob-publish-settings.js* v souboru ve složce Project *Properties (vlastnosti* projektu) projektu WebJobs. Tady je příklad tohoto souboru:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
  "webJobName": "WebJob1",
  "startTime": "null",
  "endTime": "null",
  "jobRecurrenceFrequency": "null",
  "interval": null,
  "runMode": "Continuous"
}
```

Tento soubor můžete upravit přímo a aplikace Visual Studio poskytuje technologii IntelliSense. Schéma souboru je uloženo v umístění [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) a lze ho zobrazit.  

### <a name="webjobs-listjson-file"></a><a id="webjobslist"></a>webjobs-list.jsv souboru
Když propojíte projekt s aktivními úlohami na webový projekt, Visual Studio uloží název projektu WebJobs do *webjobs-list.js* v souboru ve složce *vlastností* webového projektu. Seznam může obsahovat více projektů WebJobs, jak je znázorněno v následujícím příkladu:

```json
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
```

Tento soubor můžete upravit přímo v aplikaci Visual Studio pomocí technologie IntelliSense. Schéma souboru je uloženo v umístění [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) .

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Nasazení projektu WebJobs
Projekt WebJobs, který jste propojili s webovým projektem, se nasadí automaticky s webovým projektem. Informace o nasazení webového projektu naleznete v tématu **návody k**  >  **nasazení aplikace** v levém navigačním panelu.

Pokud chcete nasadit projekt WebJobs sám o sobě, klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **Publikovat jako Azure WebJob**. 

![Publikovat jako webovou úlohu Azure](./media/webjobs-dotnet-deploy-vs/paw.png)

U nezávislé úlohy WebJob se zobrazí stejný průvodce **publikováním webu** , který se používá pro webové projekty, ale s menším množstvím nastavení, které lze změnit.

### <a name="add-azure-webjob-dialog-box"></a><a id="configure"></a>Dialogové okno Přidat webovou úlohu Azure
Dialogové okno **Přidat webovou úlohu Azure** umožňuje zadat název webové úlohy a nastavení režimu spuštění vaší webové úlohy. 

![Dialogové okno Přidat webovou úlohu Azure](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Některá pole v tomto dialogovém okně odpovídají polím v dialogovém okně **Přidat webovou úlohu** Azure Portal. Další informace najdete v tématu [spouštění úloh na pozadí pomocí WebJobs v Azure App Service](webjobs-create.md).

Informace o nasazení úlohy WebJob:

* Informace o nasazení příkazového řádku najdete v tématu [Povolení příkazového řádku nebo průběžného doručování Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).

* Pokud nasadíte webovou úlohu a potom se rozhodnete, že chcete změnit typ webové úlohy a znovu nasadit, odstraňte *webjobs-publish-settings.jsv* souboru. Tím dojde k tomu, že Visual Studio znovu zobrazí možnosti publikování, takže můžete změnit typ úlohy WebJob.

* Pokud nasadíte webovou úlohu a později změníte režim spuštění z nepřetržitého na nesouvislý nebo naopak, Visual Studio vytvoří novou webovou úlohu v Azure při opětovném nasazení. Pokud změníte jiné nastavení plánování, ale ponecháte režim spuštění stejný nebo přepínač mezi plánovanými a na vyžádání, Visual Studio aktualizuje existující úlohu místo vytvoření nové.

## <a name="webjob-types"></a>Typy webové úlohy

Typ webové úlohy může být buď *aktivován* , nebo *průběžně*:

- Aktivované (výchozí): aktivovaná webová úloha se spustí v závislosti na události vazby, podle [plánu](#scheduling-a-triggered-webjob)nebo na ruční aktivaci (na vyžádání). Spouští se na jedné instanci, na které běží webová aplikace.

- Průběžné: [nepřetržitá](#continuous-execution) webová úloha se spustí hned po vytvoření webové úlohy. Ve výchozím nastavení běží na všech instancích se škálováním webové aplikace, ale dá se nakonfigurovat tak, aby se spouštěla jako jediná instance prostřednictvím *nastavení. job*.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="scheduling-a-triggered-webjob"></a>Naplánování aktivované webové úlohy

Když publikujete konzolovou aplikaci do Azure, sada Visual Studio nastaví typ úlohy WebJob, která se **aktivuje** ve výchozím nastavení, a přidá do projektu nový soubor *Settings. job* . U aktivovaných typů webové úlohy můžete použít tento soubor k nastavení plánu spuštění pro webovou úlohu.

Pomocí souboru *Settings. job* nastavte plán spuštění vaší webové úlohy. Následující příklad se spouští každou hodinu od 9 do 5 hodin:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Tento soubor se nachází v kořenu složky WebJobs pomocí skriptu vaší webové úlohy, jako je například `wwwroot\app_data\jobs\triggered\{job name}` nebo `wwwroot\app_data\jobs\continuous\{job name}` . Když nasadíte webovou úlohu ze sady Visual Studio, označte *nastavení.* vlastnosti souboru úlohy v aplikaci Visual Studio jako **kopie, pokud je novější**.

Pokud [vytvoříte webovou úlohu z Azure Portal](webjobs-create.md), vytvoří se soubor *Settings. job* .

#### <a name="cron-expressions"></a>Výrazy CRON

WebJobs používá stejné výrazy CRON pro plánování jako Trigger časovače v Azure Functions. Další informace o podpoře CRON najdete v tématu [Trigger časovače pro Azure Functions](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

#### <a name="settingsjob-reference"></a>nastavení. odkaz na úlohu

WebJobs podporuje následující nastavení:

| **Nastavení** | **Typ**  | **Popis** |
| ----------- | --------- | --------------- |
| `is_in_place` | Vše | Umožňuje, aby se webová úloha spouštěla na místě, aniž by se nejdřív zkopírovala do dočasné složky. Další informace najdete v tématu [pracovní adresář webové úlohy](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Průběžný | Při horizontálním navýšení kapacity spouštějte pouze webovou úlohu v jedné instanci. Další informace najdete v tématu [Nastavení průběžné úlohy jako singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Aktivuje | Spusťte úlohu WebJob v plánu založeném na CRON. Další informace najdete v tématu [NCRONTAB výrazy](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Vše | Umožňuje kontrolu nad chováním při vypnutí. Další informace najdete v tématu [řádné vypnutí](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

### <a name="continuous-execution"></a>Průběžné provádění

Pokud povolíte možnost **vždy zapnuto** v Azure, můžete pomocí sady Visual Studio změnit úlohu, aby běžela nepřetržitě:

1. Pokud jste to ještě neudělali, [publikujte projekt do Azure](#deploy-to-azure-app-service).

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

1. Na kartě **publikovat** vyberte možnost **Upravit**. 

1. V dialogovém okně **nastavení profilu** zvolte možnost **souvislá** pro **typ webové úlohy** a pak zvolte **Uložit**.

    ![Dialogové okno nastavení publikování pro webovou úlohu](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Vyberte **publikovat** na kartě **publikovat** a publikujte webovou úlohu znovu s aktualizovaným nastavením.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o sadě WebJobs SDK](webjobs-sdk-how-to.md)
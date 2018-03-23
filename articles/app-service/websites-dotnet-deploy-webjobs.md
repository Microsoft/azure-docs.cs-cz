---
title: Vývoj a nasazení webové úlohy pomocí sady Visual Studio – Azure
description: Zjistěte, jak k vývoji a nasazení Azure WebJobs Azure App Service pomocí sady Visual Studio.
services: app-service
documentationcenter: ''
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: babe190c0865f5be4aeecb40ca48b52673c6920e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Vývoj a nasazení webové úlohy pomocí sady Visual Studio – Azure App Service

## <a name="overview"></a>Přehled

Toto téma vysvětluje, jak používat Visual Studio k nasazení projektu konzolové aplikace do webové aplikace ve [služby App Service](app-service-web-overview.md) jako [webové úlohy Azure](http://go.microsoft.com/fwlink/?LinkId=390226). Informace o tom, jak nasadit webové úlohy pomocí [portál Azure](https://portal.azure.com), najdete v části [úlohy na pozadí spustit s WebJobs](web-sites-create-web-jobs.md).

Když Visual Studio nasadí projekt aplikace s povolenými webové konzoly, provádí dvě úlohy:

* Kopíruje soubory modulu runtime do příslušné složky ve webové aplikaci (*App_Data/úlohy/průběžné* pro nepřetržité webové úlohy *App_Data, úlohy nebo aktivaci* pro webové úlohy naplánované i na vyžádání).
* Nastaví [Azure Scheduler](https://docs.microsoft.com/azure/scheduler/) úlohy pro webové úlohy, které mají naplánované spuštění v určitou dobu. (To není nutné pro nepřetržité webové úlohy).

Projekt webové úlohy povolené má přidána následující položky:

* [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) balíček NuGet.
* A [webové úlohy publikovat settings.json](#publishsettings) soubor, který obsahuje nastavení nasazení a plánovače. 

![Diagram znázorňující, co se přidá do konzoly aplikaci, která umožní nasazení jako webová](./media/websites-dotnet-deploy-webjobs/convert.png)

Můžete přidat tyto položky do existujícího projektu konzolové aplikace nebo použijte šablonu pro vytvoření nového projektu aplikace s povolenými webové úlohy konzoly. 

Můžete nasadit na projekt jako webová samostatně, nebo odkaz na projekt webové, tak, aby automaticky nasadí vždy, když je nasazení webového projektu. Odkaz projekty, Visual Studio obsahuje název projektu webové úlohy povolené v [webjobs list.json](#webjobslist) souboru webového projektu.

![Diagram zobrazující projektu úlohy WebJob připojení k webovému projektu](./media/websites-dotnet-deploy-webjobs/link.png)

## <a name="prerequisites"></a>Požadavky

Pokud používáte Visual Studio 2015, nainstalujte [Azure SDK for .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Pokud používáte Visual Studio 2017, nainstalujte [pracovního vytížení Azure development](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads).

## <a id="convert"></a> Povolit nasazení webové úlohy pro existující projekt konzolové aplikace

Máte dvě možnosti:

* [Povolit automatické nasazení webového projektu](#convertlink).

  Existující projekt konzolové aplikace nakonfigurujte tak, aby se automaticky nasadí jako webová při nasazení webového projektu. Tuto možnost použijte, pokud chcete spustit vaše webová úloha ve stejné webové aplikaci, ve kterém můžete spouštět související webové aplikace.

* [Povolit nasazení bez webového projektu](#convertnolink).

  Existující projekt konzolové aplikace nasadit jako webovou úlohu samostatně, se žádný odkaz na projekt webové konfigurace. Tuto možnost použijte, pokud chcete spustit webovou úlohu ve webové aplikaci samostatně, s žádná webová aplikace spuštěna ve webové aplikaci. Můžete k tomu, aby bylo možné škálovat vaše prostředky webové úlohy nezávisle na prostředkům webové aplikace.

### <a id="convertlink"></a> Povolit automatické nasazení webové úlohy s webovým projektem

1. Klikněte pravým tlačítkem na projekt webové v **Průzkumníku řešení**a potom klikněte na **přidat** > **stávající projekt jako webová úloha Azure**.
   
    ![Stávající projekt jako webová úloha Azure](./media/websites-dotnet-deploy-webjobs/eawj.png)
   
    [Přidat webové úlohy Azure](#configure) zobrazí se dialogové okno.
2. V **název projektu** rozevíracím seznamu vyberte projektu konzolové aplikace přidejte jako webovou úlohu.
   
    ![Výběr projektu v dialogovém okně Přidat webové úlohy Azure](./media/websites-dotnet-deploy-webjobs/aaw1.png)
3. Dokončení [přidat webové úlohy Azure](#configure) dialogové okno a potom klikněte na **OK**. 

### <a id="convertnolink"></a> Povolit nasazení webové úlohy bez webového projektu
1. Klikněte pravým tlačítkem na projekt konzolové aplikace v **Průzkumníku řešení**a pak klikněte na tlačítko **publikovat jako webová úloha Azure...** . 
   
    ![Publikovat jako webová úloha Azure](./media/websites-dotnet-deploy-webjobs/paw.png)
   
    [Přidat webové úlohy Azure](#configure) dialogové okno se zobrazí s projekt vybraný v **název projektu** pole.
2. Dokončení [přidat webové úlohy Azure](#configure) dialogové okno a pak klikněte na tlačítko **OK**.
   
   **Publikovat Web** zobrazí se průvodce.  Pokud nechcete publikovat okamžitě, zavřete průvodce. Nastavení, které jste zadali se ukládají pro, pokud chcete [nasazení projektu](#deploy).

## <a id="create"></a>Vytvořte nový projekt webové úlohy povolena
Pokud chcete vytvořit nový projekt webové úlohy povolené, můžete pomocí šablony projektu konzolové aplikace a povolit nasazení webové úlohy, jak je popsáno v [v předchozí části](#convert). Případně můžete šablonu nový projekt webové úlohy:

* [Použít šablonu nový projekt webové úlohy pro nezávislé webové úlohy](#createnolink)
  
    Vytvoření projektu a nakonfigurujte ho nasadit jako webová úloha se žádný odkaz na projekt webové samostatně. Tuto možnost použijte, pokud chcete spustit webovou úlohu ve webové aplikaci samostatně, s žádná webová aplikace spuštěna ve webové aplikaci. Můžete k tomu, aby bylo možné škálovat vaše prostředky webové úlohy nezávisle na prostředkům webové aplikace.
* [Použít šablonu nový projekt webové úlohy pro webovou úlohu propojené s webového projektu](#createlink)
  
    Vytvoření projektu nakonfigurovaný tak, aby automaticky jako webová nasazení při nasazení webového projektu ve stejném řešení. Tuto možnost použijte, pokud chcete spustit vaše webová úloha ve stejné webové aplikaci, ve kterém můžete spouštět související webové aplikace.

> [!NOTE]
> Automaticky nainstaluje balíčky NuGet a obsahuje kód v šabloně nový projekt webové úlohy *Program.cs* pro [WebJobs SDK](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Pokud nechcete, aby se používat sadu WebJobs SDK, odebrat ani změnit `host.RunAndBlock` příkaz v *Program.cs*.
> 
> 

### <a id="createnolink"></a> Použít šablonu nový projekt webové úlohy pro nezávislé webové úlohy
1. Klikněte na tlačítko **soubor** > **nový projekt**a potom v **nový projekt** dialogovém okně **cloudu** > **webové úlohy Azure (rozhraní .NET Framework)**.
   
    ![Dialogové okno Nový projekt zobrazující šablony webové úlohy](./media/websites-dotnet-deploy-webjobs/np.png)
2. Postupujte podle pokynů, zobrazí dříve do [zpřístupnění aplikace konzoly projektu nezávislé projekt webové úlohy](#convertnolink).

### <a id="createlink"></a> Použít šablonu nový projekt webové úlohy pro webovou úlohu propojené s webového projektu
1. Klikněte pravým tlačítkem na projekt webové v **Průzkumníku řešení**a potom klikněte na **přidat** > **nový projekt webové úlohy Azure**.
   
    ![Nová položka nabídky projektu webová úloha Azure](./media/websites-dotnet-deploy-webjobs/nawj.png)
   
    [Přidat webové úlohy Azure](#configure) zobrazí se dialogové okno.
2. Dokončení [přidat webové úlohy Azure](#configure) dialogové okno a pak klikněte na tlačítko **OK**.

## <a id="configure"></a>Dialogové okno Přidat webové úlohy Azure
**Přidat webové úlohy Azure** dialogovém okně můžete zadat název webové úlohy a spusťte nastavení režimu pro vaše webová úloha. 

![Přidat dialogové okno webová úloha Azure](./media/websites-dotnet-deploy-webjobs/aaw2.png)

Pole v tomto dialogovém okně odpovídají pole na **přidat webové úlohy** dialogové okno portálu Azure. Další informace najdete v tématu [úlohy na pozadí spustit s WebJobs](web-sites-create-web-jobs.md).

> [!NOTE]
> * Informace o nasazení příkazového řádku najdete v tématu [povolení příkazového řádku nebo průběžné doručování Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Pokud nasadíte webovou úlohu a potom se rozhodnete chcete změnit typ webové úlohy a znovu ho zaveďte, budete muset odstranit *webjobs publikování settings.json* souboru. Abyste mohli změnit typ webová úloha bude nezobrazovat možnosti publikování, sady Visual Studio.
> * Pokud nasadíte webovou úlohu a později změníte režimu spuštění z průběžné nesouvislé nebo naopak, Visual Studio vytvoří nové webové úlohy v Azure při opětovném nasazování. Pokud změníte další plánování nastavení, ale ponechte režim spuštění stejné nebo přepínat mezi naplánovaná a na vyžádání, Visual Studio aktualizuje existující úlohy a nikoli vytvořit novou.
> 
> 

## <a id="publishsettings"></a>webjob-publish-settings.json
Při konfiguraci konzolovou aplikaci pro webové úlohy nasazení nainstaluje Visual Studio [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet balíčku a ukládá informace v o plánování *webové úlohy publikovat settings.json* v projektu *vlastnosti* složku projekt webové úlohy. Tady je příklad tohoto souboru:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Tento soubor můžete upravit přímo a Visual Studio poskytuje technologii IntelliSense. Schéma souboru je uložené v [ http://schemastore.org ](http://schemastore.org/schemas/json/webjob-publish-settings.json) a lze je zobrazit.  

## <a id="webjobslist"></a>webjobs-list.json
Když propojíte projekt webové úlohy povolené webového projektu, Visual Studio uloží název projektu webové úlohy v *webjobs list.json* souboru webového projektu *vlastnosti* složky. Seznam může obsahovat více projektů webové úlohy, jak je znázorněno v následujícím příkladu:

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

Tento soubor můžete upravit přímo a Visual Studio poskytuje technologii IntelliSense. Schéma souboru je uložené v [ http://schemastore.org ](http://schemastore.org/schemas/json/webjobs-list.json) a lze je zobrazit.

## <a id="deploy"></a>Nasazení projektu webové úlohy
Projekt webové úlohy, který propojení projektu webové nasadí automaticky s webového projektu. Informace o nasazení webového projektu najdete v tématu **postupy provede** > **nasadit aplikaci** v levém navigačním panelu.

Chcete-li nasadit projekt webové úlohy sám o sobě, klikněte pravým tlačítkem na projekt v **Průzkumníku řešení** a klikněte na tlačítko **publikovat jako webová úloha Azure...** . 

![Publikovat jako webová úloha Azure](./media/websites-dotnet-deploy-webjobs/paw.png)

Pro nezávislé webové úlohy, stejné **Publikovat Web** průvodce, který se používá pro webové projekty, ale s méně nastavení, které jsou k dispozici pro změnit.

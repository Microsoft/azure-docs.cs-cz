---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ef0aa8ba1983ca30fd44c27fe570b6b5f51733a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744868"
---
## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Vytvoření plánované webové úlohy


1. Byl získán do [Azure Portal](https://portal.azure.com).
1. Přejít na **App Service** svého <abbr title="Prostředkem aplikace může být webová aplikace, aplikace API nebo mobilní aplikace.">Prostředek aplikace</abbr>.
1. Vyberte **WebJobs**.

   ![Vybrat webové úlohy](../media/web-sites-create-web-jobs/select-webjobs.png)

1. Na stránce webové **úlohy** vyberte **Přidat**.

    ![Stránka WebJob](../media/web-sites-create-web-jobs/wjblade.png)

1. Použijte možnost **Přidat nastavení úlohy WebJob** , jak je uvedeno v tabulce.

    ![Přidat stránku WebJob](../media/web-sites-create-web-jobs/addwjscheduled.png)
    
    | Nastavení      | Ukázková hodnota   |
    | ------------ | ----------------- | 
    | <abbr title="Název, který je jedinečný v rámci aplikace App Service. Musí začínat písmenem nebo číslicí a nesmí obsahovat speciální znaky jiné než `-` a `_` .">Name</a> | myScheduledWebJob |  |
    | <abbr title="Soubor *. zip* , který obsahuje spustitelný soubor nebo soubor skriptu a všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu.">Nahrání souboru</abbr> | ConsoleApp.zip |
    | <abbr title="Mezi typy patří průběžné, aktivované.">Typ</abbr> | Aktivuje |
    | <abbr title="Aby plánování fungovalo spolehlivě, povolte funkci Always On. Always On je k dispozici jenom pro cenové úrovně Basic, Standard a Premium.">Aktivační události</a> | Plánované |
    | Výraz CRON</a> | 0 0/20 * * * * | 
    
    <br>
    
    <details>
     <summary>Další informace o výrazech CRON</summary>
     <a name="#ncrontab-expressions"></a>
    
     Na portálu můžete zadat [výraz NCRONTAB](../../azure-functions/functions-bindings-timer.md#ncrontab-expressions) nebo zahrnout `settings.job` soubor do kořenové složky souboru WebJob *. zip* , jak je uvedeno v následujícím příkladu:
     
     ```json
     {
         "schedule": "0 */15 * * * *"
     }
     ```
     
     Další informace najdete v tématu [plánování aktivované webové úlohy](../webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).
     
     [!INCLUDE [webjobs-cron-timezone-note](../../../includes/webjobs-cron-timezone-note.md)]
     </details>
     <br>

1. Klikněte na **OK**.

    Nová webová úloha se zobrazí na stránce **WebJobs** .
    
    ![Seznam WebJobs](../media/web-sites-create-web-jobs/listallwebjobs.png)

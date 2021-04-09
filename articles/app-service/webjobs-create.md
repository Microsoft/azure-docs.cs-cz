---
title: Spouštění úloh na pozadí pomocí WebJobs
description: Naučte se používat webové úlohy ke spouštění úloh na pozadí v Azure App Service. Vyberte si z nejrůznějších formátů skriptů a spouštějte je s CRON výrazy.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./webjobs-create-ieux
ms.openlocfilehash: 8afc8ca9b0dedb10ecdb30e8abb22a5d0986de5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101723873"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Spouštění úloh na pozadí pomocí WebJobs v Azure App Service

Tento článek ukazuje, jak nasadit webové úlohy pomocí [Azure Portal](https://portal.azure.com) k nahrání spustitelného souboru nebo skriptu. Informace o tom, jak vyvíjet a nasazovat WebJobs pomocí sady Visual Studio, najdete v tématu [Nasazení WebJobs pomocí sady Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Přehled
WebJobs je funkce [Azure App Service](index.yml) , která umožňuje spustit program nebo skript ve stejné instanci jako webová aplikace, aplikace API nebo mobilní aplikace. Pro použití WebJobs se neúčtují žádné další náklady.

> [!IMPORTANT]
> Webové úlohy se zatím nepodporují pro App Service v systému Linux.

Sadu Azure WebJobs SDK lze použít s WebJobs k zjednodušení mnoha programovacích úloh. Další informace najdete v tématu [co je sada WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Azure Functions poskytuje další způsob, jak spouštět programy a skripty. Porovnání mezi službami WebJobs a Functions najdete v tématu [Výběr mezi tokem, Logic Apps, funkcemi a WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Typy webové úlohy

Následující tabulka popisuje rozdíly mezi *průběžnými* a *aktivovanými* WebJobs.


|Průběžný  |Aktivuje  |
|---------|---------|
| Spustí se hned po vytvoření webové úlohy. Aby bylo možné úlohu ukončit, program nebo skript obvykle provede svou práci v nekonečné smyčce. Pokud úloha skončí, můžete ji restartovat. | Spustí se jenom v případě, že se aktivuje ručně nebo podle plánu. |
| Spustí se ve všech instancích, na kterých běží webová aplikace. Můžete volitelně omezit webovou úlohu na jednu instanci. |Spustí se v jediné instanci, kterou Azure vybere pro vyrovnávání zatížení.|
| Podporuje vzdálené ladění. | Nepodporuje vzdálené ladění.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Podporované typy souborů pro skripty nebo programy

Podporované jsou následující typy souborů:

* . cmd,. bat,. exe (použití Windows CMD)
* . ps1 (použití PowerShellu)
* . sh (použití bash)
* . php (použití PHP)
* . py (použití Pythonu)
* . js (použití Node.js)
* . jar (použití jazyka Java)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Vytvoření průběžné úlohy WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

> [!IMPORTANT]
> Pokud máte s vaší aplikací nakonfigurovaný zdrojový ovládací prvek, webové úlohy by se měly nasadit jako součást integrace správy zdrojového kódu. Po konfiguraci správy zdrojového kódu s vaší aplikací nejde webovou úlohu přidat z webu Azure Portal.

1. V [Azure Portal](https://portal.azure.com)navštivte stránku **App Service** vaší App Service webové aplikace, aplikace API nebo mobilní aplikace.

2. Vyberte **WebJobs**.

   ![Vybrat webové úlohy](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stránce webové **úlohy** vyberte **Přidat**.

    ![Stránka WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Použijte možnost **Přidat nastavení úlohy WebJob** , jak je uvedeno v tabulce.

   ![Snímek obrazovky, který zobrazuje nastavení přidání úlohy WebJob, které je třeba nakonfigurovat.](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Nastavení      | Ukázková hodnota   | Popis  |
   | ------------ | ----------------- | ------------ |
   | **Název** | myContinuousWebJob | Název, který je jedinečný v rámci aplikace App Service. Musí začínat písmenem nebo číslicí a nesmí obsahovat speciální znaky jiné než "-" a "_". |
   | **Nahrání souboru** | ConsoleApp.zip | Soubor *. zip* , který obsahuje spustitelný soubor nebo soubor skriptu a všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu. Podporované spustitelné soubory nebo typy souborů skriptu jsou uvedené v části [podporované typy souborů](#acceptablefiles) . |
   | **Typ** | Průběžný | [Typy webové úlohy](#webjob-types) jsou popsány výše v tomto článku. |
   | **Škálování** | Více instancí | K dispozici pouze pro nepřetržité webové úlohy. Určuje, zda se program nebo skript spouští na všech instancích nebo pouze v jedné instanci. Možnost spuštění na více instancích se nevztahuje na [cenové úrovně](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)Free nebo Shared. | 

4. Klikněte na **OK**.

   Nová webová úloha se zobrazí na stránce **WebJobs** .

   ![Seznam WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Chcete-li ukončit nebo restartovat průběžnou úlohu WebJob, klikněte pravým tlačítkem myši na webovou úlohu v seznamu a klikněte na tlačítko **zastavit** nebo **Spustit**.

    ![Zastavení průběžné úlohy WebJob](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Vytvoření ručně aktivované webové úlohy

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. V [Azure Portal](https://portal.azure.com)navštivte stránku **App Service** vaší App Service webové aplikace, aplikace API nebo mobilní aplikace.

2. Vyberte **WebJobs**.

   ![Vybrat webové úlohy](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stránce webové **úlohy** vyberte **Přidat**.

    ![Stránka WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Použijte možnost **Přidat nastavení úlohy WebJob** , jak je uvedeno v tabulce.

   ![Snímek obrazovky zobrazující nastavení, které je potřeba nastavit pro vytvoření ručně aktivované webové úlohy](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Nastavení      | Ukázková hodnota   | Popis  |
   | ------------ | ----------------- | ------------ |
   | **Název** | myTriggeredWebJob | Název, který je jedinečný v rámci aplikace App Service. Musí začínat písmenem nebo číslicí a nesmí obsahovat speciální znaky jiné než "-" a "_".|
   | **Nahrání souboru** | ConsoleApp.zip | Soubor *. zip* , který obsahuje spustitelný soubor nebo soubor skriptu a všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu. Podporované spustitelné soubory nebo typy souborů skriptu jsou uvedené v části [podporované typy souborů](#acceptablefiles) . |
   | **Typ** | Aktivuje | [Typy webové úlohy](#webjob-types) jsou popsány výše v tomto článku. |
   | **Triggery** | Ruční | |

4. Klikněte na **OK**.

   Nová webová úloha se zobrazí na stránce **WebJobs** .

   ![Seznam WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Webovou úlohu spustíte tak, že kliknete pravým tlačítkem na jeho název v seznamu a kliknete na **Spustit**.
   
    ![Spustit úlohu WebJob](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Vytvoření plánované webové úlohy

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. V [Azure Portal](https://portal.azure.com)navštivte stránku **App Service** vaší App Service webové aplikace, aplikace API nebo mobilní aplikace.

2. Vyberte **WebJobs**.

   ![Vybrat webové úlohy](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stránce webové **úlohy** vyberte **Přidat**.

   ![Stránka WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Použijte možnost **Přidat nastavení úlohy WebJob** , jak je uvedeno v tabulce.

   ![Přidat stránku WebJob](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Nastavení      | Ukázková hodnota   | Popis  |
   | ------------ | ----------------- | ------------ |
   | **Název** | myScheduledWebJob | Název, který je jedinečný v rámci aplikace App Service. Musí začínat písmenem nebo číslicí a nesmí obsahovat speciální znaky jiné než "-" a "_". |
   | **Nahrání souboru** | ConsoleApp.zip | Soubor *. zip* , který obsahuje spustitelný soubor nebo soubor skriptu a všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu. Podporované spustitelné soubory nebo typy souborů skriptu jsou uvedené v části [podporované typy souborů](#acceptablefiles) . |
   | **Typ** | Aktivuje | [Typy webové úlohy](#webjob-types) jsou popsány výše v tomto článku. |
   | **Triggery** | Plánované | Aby plánování fungovalo spolehlivě, povolte funkci Always On. Always On je k dispozici jenom pro cenové úrovně Basic, Standard a Premium.|
   | **Výraz CRON** | 0 0/20 * * * * | [Výrazy cron](#ncrontab-expressions) jsou popsány v následující části. |

4. Klikněte na **OK**.

   Nová webová úloha se zobrazí na stránce **WebJobs** .

   ![Seznam WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>Výrazy NCRONTAB

Na portálu můžete zadat [výraz NCRONTAB](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) nebo zahrnout `settings.job` soubor do kořenové složky souboru WebJob *. zip* , jak je uvedeno v následujícím příkladu:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Další informace najdete v tématu [plánování aktivované webové úlohy](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a> Zobrazit historii úlohy

1. Vyberte webovou úlohu, pro kterou chcete zobrazit historii, a pak vyberte tlačítko **protokoly** .
   
   ![Tlačítko protokoly](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. Na stránce s **podrobnostmi webové** služby vyberte čas pro zobrazení podrobností o jednom spuštění.
   
   ![Podrobnosti úlohy WebJob](./media/web-sites-create-web-jobs/webjobdetails.png)

3. Na stránce s **podrobnostmi o spuštění webové úlohy** vyberte možnost **přepnout výstup** . zobrazí se text obsahu protokolu.
   
    ![Podrobnosti o spuštění webové úlohy](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Pokud chcete zobrazit výstupní text v samostatném okně prohlížeče, vyberte **Stáhnout**. Pokud chcete stáhnout samotný text, klikněte pravým tlačítkem na **Stáhnout** a pomocí možností prohlížeče uložte obsah souboru.
   
5. V horní části stránky vyberte odkaz s popisem webové **úlohy** , který umožňuje přejít na seznam WebJobs.

    ![Webová úloha navigace](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Seznam WebJobs na řídicím panelu Historie](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a> Další kroky

Sadu Azure WebJobs SDK lze použít s WebJobs k zjednodušení mnoha programovacích úloh. Další informace najdete v tématu [co je sada WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

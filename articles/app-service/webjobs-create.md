---
title: Spouštění úloh na pozadí pomocí WebJobs – Azure App Service
description: Naučte se používat webové úlohy ke spouštění úloh na pozadí v Azure App Service Web Apps, API Apps nebo Mobile Apps.
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;david.ebbo;suwatch;pbatum;naren.soni
ms.custom: seodec18
ms.openlocfilehash: 55d040e96b92f73a632fd415220f9cf135335736
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851010"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Spouštění úloh na pozadí pomocí WebJobs v Azure App Service

## <a name="overview"></a>Přehled
WebJobs je funkce [Azure App Service](https://docs.microsoft.com/azure/app-service/) , která umožňuje spustit program nebo skript ve stejném kontextu jako webová aplikace, aplikace API nebo mobilní aplikace. Pro použití WebJobs se neúčtují žádné další náklady.

> [!IMPORTANT]
> Webové úlohy se zatím nepodporují pro App Service v systému Linux.

Tento článek ukazuje, jak nasadit webové úlohy pomocí [Azure Portal](https://portal.azure.com) k nahrání spustitelného souboru nebo skriptu. Informace o tom, jak vyvíjet a nasazovat WebJobs pomocí sady Visual Studio, najdete v tématu [Nasazení WebJobs pomocí sady Visual Studio](webjobs-dotnet-deploy-vs.md).

Sadu Azure WebJobs SDK lze použít s WebJobs k zjednodušení mnoha programovacích úloh. Další informace najdete v tématu [co je sada WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Azure Functions poskytuje další způsob, jak spouštět programy a skripty. Porovnání mezi službami WebJobs a Functions najdete v tématu [Výběr mezi tokem, Logic Apps, funkcemi a WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Typy webové úlohy

Následující tabulka popisuje rozdíly mezi průběžnými a *aktivovanými* WebJobs.


|Nepřetržitě  |Aktivované  |
|---------|---------|
| Spustí se hned po vytvoření webové úlohy. Aby bylo možné úlohu ukončit, program nebo skript obvykle provede svou práci v nekonečné smyčce. Pokud úloha skončí, můžete ji restartovat. | Spustí se jenom v případě, že se aktivuje ručně nebo podle plánu. |
| Spustí se ve všech instancích, na kterých běží webová aplikace. Můžete volitelně omezit webovou úlohu na jednu instanci. |Spustí se v jediné instanci, kterou Azure vybere pro vyrovnávání zatížení.|
| Podporuje vzdálené ladění. | Nepodporuje vzdálené ladění.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="acceptablefiles"></a>Podporované typy souborů pro skripty nebo programy

Podporovány jsou následující typy souborů:

* . cmd,. bat,. exe (použití Windows CMD)
* . ps1 (použití PowerShellu)
* . sh (použití bash)
* . php (použití PHP)
* . py (použití Pythonu)
* . js (použití Node. js)
* . jar (použití jazyka Java)

## <a name="CreateContinuous"></a>Vytvoření průběžné úlohy WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. V [Azure Portal](https://portal.azure.com)navštivte stránku **App Service** vaší App Service webové aplikace, aplikace API nebo mobilní aplikace.

2. Vyberte **WebJobs**.

   ![Vybrat webové úlohy](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stránce webové **úlohy** vyberte **Přidat**.

    ![Stránka WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Použijte možnost **Přidat nastavení úlohy WebJob** , jak je uvedeno v tabulce.

   ![Přidat stránku WebJob](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Nastavení      | Ukázková hodnota   | Popis  |
   | ------------ | ----------------- | ------------ |
   | **Název** | myContinuousWebJob | Název, který je jedinečný v rámci aplikace App Service. Musí začínat písmenem nebo číslicí a nesmí obsahovat speciální znaky jiné než "-" a "_". |
   | **Nahrání souboru** | ConsoleApp. zip | Soubor *. zip* , který obsahuje spustitelný soubor nebo soubor skriptu a všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu. Podporované spustitelné soubory nebo typy souborů skriptu jsou uvedené v části [podporované typy souborů](#acceptablefiles) . |
   | **Textový** | Nepřetržitě | [Typy webové úlohy](#webjob-types) jsou popsány výše v tomto článku. |
   | **Škálování** | Více instancí | K dispozici pouze pro nepřetržité webové úlohy. Určuje, zda se program nebo skript spouští na všech instancích nebo pouze v jedné instanci. Možnost spuštění na více instancích se nevztahuje na [cenové úrovně](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)Free nebo Shared. | 

4. Klikněte na **OK**.

   Nová webová úloha se zobrazí na stránce WebJobs.

   ![Seznam WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Chcete-li ukončit nebo restartovat průběžnou úlohu WebJob, klikněte pravým tlačítkem myši na webovou úlohu v seznamu a klikněte na tlačítko **zastavit** nebo **Spustit**.

    ![Zastavení průběžné úlohy WebJob](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a>Vytvoření ručně aktivované webové úlohy

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. V [Azure Portal](https://portal.azure.com)navštivte stránku **App Service** vaší App Service webové aplikace, aplikace API nebo mobilní aplikace.

2. Vyberte **WebJobs**.

   ![Vybrat webové úlohy](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stránce webové **úlohy** vyberte **Přidat**.

    ![Stránka WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Použijte možnost **Přidat nastavení úlohy WebJob** , jak je uvedeno v tabulce.

   ![Přidat stránku WebJob](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Nastavení      | Ukázková hodnota   | Popis  |
   | ------------ | ----------------- | ------------ |
   | **Název** | myTriggeredWebJob | Název, který je jedinečný v rámci aplikace App Service. Musí začínat písmenem nebo číslicí a nesmí obsahovat speciální znaky jiné než "-" a "_".|
   | **Nahrání souboru** | ConsoleApp. zip | Soubor *. zip* , který obsahuje spustitelný soubor nebo soubor skriptu a všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu. Podporované spustitelné soubory nebo typy souborů skriptu jsou uvedené v části [podporované typy souborů](#acceptablefiles) . |
   | **Textový** | Aktivované | [Typy webové úlohy](#webjob-types) jsou popsány výše v tomto článku. |
   | **Zpráv** | Ručně | |

4. Klikněte na **OK**.

   Nová webová úloha se zobrazí na stránce WebJobs.

   ![Seznam WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Webovou úlohu spustíte tak, že kliknete pravým tlačítkem na jeho název v seznamu a kliknete na **Spustit**.
   
    ![Zprovoznění webové úlohy](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a>Vytvoření plánované webové úlohy

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. V [Azure Portal](https://portal.azure.com)navštivte stránku **App Service** vaší App Service webové aplikace, aplikace API nebo mobilní aplikace.

2. Vyberte **WebJobs**.

   ![Vybrat webové úlohy](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stránce webové **úlohy** vyberte **Přidat**.

   ![Stránka WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Použijte možnost **Přidat nastavení úlohy WebJob** , jak je uvedeno v tabulce.

   ![Přidat stránku WebJob](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Nastavení      | Ukázková hodnota   | Popis  |
   | ------------ | ----------------- | ------------ |
   | **Název** | myScheduledWebJob | Název, který je jedinečný v rámci aplikace App Service. Musí začínat písmenem nebo číslicí a nesmí obsahovat speciální znaky jiné než "-" a "_". |
   | **Nahrání souboru** | ConsoleApp. zip | Soubor *. zip* , který obsahuje spustitelný soubor nebo soubor skriptu a všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu. Podporované spustitelné soubory nebo typy souborů skriptu jsou uvedené v části [podporované typy souborů](#acceptablefiles) . |
   | **Textový** | Aktivované | [Typy webové úlohy](#webjob-types) jsou popsány výše v tomto článku. |
   | **Zpráv** | Naplánované | Aby plánování fungovalo spolehlivě, povolte funkci Always On. Always On je k dispozici jenom pro cenové úrovně Basic, Standard a Premium.|
   | **Výraz CRON** | 0 0/20 * * * * | [Výrazy cron](#cron-expressions) jsou popsány v následující části. |

4. Klikněte na **OK**.

   Nová webová úloha se zobrazí na stránce WebJobs.

   ![Seznam WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>Výrazy NCRONTAB

Na portálu můžete zadat [výraz NCRONRAB](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) nebo zahrnout `settings.job` soubor do kořenové složky souboru WebJob *. zip* , jak je uvedeno v následujícím příkladu:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Další informace najdete v tématu [plánování aktivované webové úlohy](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

## <a name="ViewJobHistory"></a>Zobrazit historii úlohy

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
   
## <a name="NextSteps"></a> Další kroky

Sadu Azure WebJobs SDK lze použít s WebJobs k zjednodušení mnoha programovacích úloh. Další informace najdete v tématu [co je sada WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

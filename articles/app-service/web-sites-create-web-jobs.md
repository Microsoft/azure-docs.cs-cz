---
title: Spouštění úloh na pozadí pomocí WebJobs v Azure App Service
description: Zjistěte, jak pomocí WebJobs můžete spouštět úlohy na pozadí ve službě Azure App Service web apps, API apps nebo mobilní aplikace.
services: app-service
documentationcenter: ''
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: c3a41733dd193d10349a0126bfa9c25ce4ba56e7
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577673"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Spouštění úloh na pozadí pomocí WebJobs v Azure App Service

## <a name="overview"></a>Přehled
WebJobs je funkce [služby Azure App Service](https://docs.microsoft.com/azure/app-service/) , která umožňuje spouštět program nebo skript ve stejném kontextu jako webové aplikace, aplikace API nebo mobilní aplikace. Se neúčtují žádné další poplatky používání WebJobs.

Tento článek ukazuje, jak nasadit WebJobs pomocí [webu Azure portal](https://portal.azure.com) nahrát spustitelný soubor nebo skript. Informace o tom, jak vyvinout a nasadit WebJobs pomocí sady Visual Studio najdete v tématu [nasadit WebJobs pomocí sady Visual Studio](websites-dotnet-deploy-webjobs.md).

Azure WebJobs SDK je možné pomocí WebJobs ke zjednodušení mnoha programovacích úloh. Další informace najdete v tématu [co je sada WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Služba Azure Functions poskytuje jiný způsob spouštění programů a skriptů. Porovnání mezi WebJobs a funkcích najdete v tématu [zvolit mezi službami Flow, Logic Apps, Functions a WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Typy webová úloha.

Následující tabulka popisuje rozdíly mezi *průběžné* a *aktivuje* WebJobs.


|Nepřetržitě  |Aktivované  |
|---------|---------|
| Spustí se okamžitě, když se vytvoří webová úloha. Zabránit koncové úlohy, programu nebo skriptu obvykle provede svou práci v nekonečné smyčce. Je-li ukončit úlohu, můžete ji restartovat. | Spustí pouze v případě, že se aktivuje ručně, nebo podle plánu. |
| Spustí se všechny instance webové aplikace, na kterých běží. Webové úlohy můžete volitelně omezit na jednu instanci. |Spuštění na jednu instanci, Azure vybere pro vyrovnávání zatížení.|
| Podporuje vzdálené ladění. | Nepodporuje vzdálené ladění.|

> [!NOTE]
> Webové aplikace můžete po 20 minutách nečinnosti časový limit. Jenom požadavky k webu scm (nasazení) nebo na stránky webové aplikace v portálu pro resetování časovač. Požadavky na webu skutečné Neobnovovat časovač. Pokud vaše aplikace běží průběžné nebo plánované webové úlohy, povolte **Always On** zajistit spolehlivě spouštět webové úlohy. Tato funkce je dostupná jenom na Basic, Standard a Premium [cenové úrovně](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="acceptablefiles"></a>Podporované typy souborů pro skripty nebo programy

Jsou podporovány následující typy souborů:

* .cmd, .bat, .exe (pomocí příkazového řádku Windows)
* .ps1 (s použitím prostředí PowerShell)
* .SH (pomocí skriptu Bash)
* PHP (pomocí PHP)
* .PY (pomocí Pythonu)
* .js (pomocí Node.js)
* .JAR (využívající jazyk Java)

## <a name="CreateContinuous"></a> Vytvoření průběžné webové úlohy

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. V [webu Azure portal](https://portal.azure.com), přejděte **služby App Service** stránku webová aplikace App Service, aplikace API nebo mobilní aplikace.

2. Vyberte **WebJobs**.

   ![Vyberte WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. V **WebJobs** stránce **přidat**.

    ![Stránka webové úlohy](./media/web-sites-create-web-jobs/wjblade.png)

3. Použití **přidat WebJob** nastavení uvedená v tabulce.

   ![Přidejte stránku webové úlohy](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Nastavení      | Ukázková hodnota   | Popis  |
   | ------------ | ----------------- | ------------ |
   | **Název** | myContinuousWebJob | Název, který je jedinečný v rámci aplikace služby App Service. Musí začínat písmenem nebo číslicí a nesmí obsahovat speciální znaky jiné než "-" a "_". |
   | **Nahrání souboru** | ConsoleApp.zip | A *ZIP* soubor, který obsahuje váš soubor spustitelný soubor nebo skript, stejně jako všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu. Podporované typy souborů spustitelný soubor nebo skript jsou uvedeny v [podporované typy souborů](#acceptablefiles) oddílu. |
   | **Typ** | Nepřetržitě | [WebJob typy](#webjob-types) jsou popsány dříve v tomto článku. |
   | **Škálování** | Více instancí | K dispozici pouze pro průběžné WebJobs. Určuje, jestli se program nebo skript spouští na všech instancích nebo jenom jednu instanci. Možnost spouštět na více instancí se nevztahuje na Free nebo Shared [cenové úrovně](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). | 

4. Klikněte na **OK**.

   Nová webová úloha se zobrazí na **WebJobs** stránky.

   ![Seznam WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Zastavit nebo restartovat nepřetržitá webová úloha, klikněte pravým tlačítkem na webové úlohy v seznamu a klikněte na tlačítko **Zastavit** nebo **Start**.

    ![Zastavit průběžné Webjobs](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a> Vytvoření ručně aktivované webové úlohy

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. V [webu Azure portal](https://portal.azure.com), přejděte **služby App Service** stránku webová aplikace App Service, aplikace API nebo mobilní aplikace.

2. Vyberte **WebJobs**.

   ![Vyberte WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. V **WebJobs** stránce **přidat**.

    ![Stránka webové úlohy](./media/web-sites-create-web-jobs/wjblade.png)

3. Použití **přidat WebJob** nastavení uvedená v tabulce.

   ![Přidejte stránku webové úlohy](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Nastavení      | Ukázková hodnota   | Popis  |
   | ------------ | ----------------- | ------------ |
   | **Název** | myTriggeredWebJob | Název, který je jedinečný v rámci aplikace služby App Service. Musí začínat písmenem nebo číslicí a nesmí obsahovat speciální znaky jiné než "-" a "_".|
   | **Nahrání souboru** | ConsoleApp.zip | A *ZIP* soubor, který obsahuje váš soubor spustitelný soubor nebo skript, stejně jako všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu. Podporované typy souborů spustitelný soubor nebo skript jsou uvedeny v [podporované typy souborů](#acceptablefiles) oddílu. |
   | **Typ** | Aktivované | [WebJob typy](#webjob-types) jsou popsány dříve v tomto článku. |
   | **Triggery** | Ručně | |

4. Klikněte na **OK**.

   Nová webová úloha se zobrazí na **WebJobs** stránky.

   ![Seznam WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Ke spuštění webové úlohy, klikněte pravým tlačítkem na jeho název v seznamu a klikněte na tlačítko **spustit**.
   
    ![Zprovoznění webové úlohy](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a> Vytvoření plánované webové úlohy

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. V [webu Azure portal](https://portal.azure.com), přejděte **služby App Service** stránku webová aplikace App Service, aplikace API nebo mobilní aplikace.

2. Vyberte **WebJobs**.

   ![Vyberte WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. V **WebJobs** stránce **přidat**.

   ![Stránka webové úlohy](./media/web-sites-create-web-jobs/wjblade.png)

3. Použití **přidat WebJob** nastavení uvedená v tabulce.

   ![Přidejte stránku webové úlohy](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Nastavení      | Ukázková hodnota   | Popis  |
   | ------------ | ----------------- | ------------ |
   | **Název** | myScheduledWebJob | Název, který je jedinečný v rámci aplikace služby App Service. Musí začínat písmenem nebo číslicí a nesmí obsahovat speciální znaky jiné než "-" a "_". |
   | **Nahrání souboru** | ConsoleApp.zip | A *ZIP* soubor, který obsahuje váš soubor spustitelný soubor nebo skript, stejně jako všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu. Podporované typy souborů spustitelný soubor nebo skript jsou uvedeny v [podporované typy souborů](#acceptablefiles) oddílu. |
   | **Typ** | Aktivované | [WebJob typy](#webjob-types) jsou popsány dříve v tomto článku. |
   | **Triggery** | Naplánované | Pro plánování spolehlivé fungování povolte funkci Always On. Always On je k dispozici pouze na Basic, Standard a cenových úrovní Premium.|
   | **Výraz CRON** | 0 0/20 * * * * | [Výrazů CRON](#cron-expressions) jsou popsány v následující části. |

4. Klikněte na **OK**.

   Nová webová úloha se zobrazí na **WebJobs** stránky.

   ![Seznam WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="cron-expressions"></a>Výrazů CRON

Můžete zadat [výraz CRON](../azure-functions/functions-bindings-timer.md#cron-expressions) na portálu nebo zahrnout `settings.job` souboru v kořenovém adresáři webové úlohy *ZIP* soubor jako v následujícím příkladu:

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

> [!NOTE]
> Když nasadíte webovou úlohu ze sady Visual Studio, označit vaše `settings.job` vlastnosti jako souboru **kopírovat, pokud je novější**.

## <a name="ViewJobHistory"></a> Zobrazení historie úloh

1. Vyberte webovou úlohu, kterou chcete zobrazit historii pro a pak vyberte **protokoly** tlačítko.
   
   ![Tlačítko protokoly](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. V **podrobnosti úlohy WebJob** vyberte čas, kdy má pro jedno spuštění zobrazte podrobnosti.
   
   ![Podrobnosti o webové úlohy](./media/web-sites-create-web-jobs/webjobdetails.png)

3. V **detaily spuštění úlohy WebJob** stránce **přepnout výstup** zobrazíte textový obsah protokolu.
   
    ![Podrobnosti o spuštění webové úlohy](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Pokud chcete zobrazit výstup textu v samostatném okně prohlížeče, vyberte **Stáhnout**. Stáhnout samotného textu, klikněte pravým tlačítkem na **Stáhnout** a uložit obsah souboru pomocí možnosti v prohlížeči.
   
5. Vyberte **WebJobs** propojení s popisem cesty v horní části stránky a přejděte na seznam WebJobs.

    ![Webovou úlohu s popisem cesty](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Seznam WebJobs v řídicím panelu historii](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> Další kroky

Azure WebJobs SDK je možné pomocí WebJobs ke zjednodušení mnoha programovacích úloh. Další informace najdete v tématu [co je sada WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

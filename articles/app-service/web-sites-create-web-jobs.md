---
title: "Spuštění úlohy na pozadí s webové úlohy v Azure App Service"
description: "Naučte se používat webové úlohy ke spuštění úlohy na pozadí v Azure App Service webové aplikace, aplikace API nebo mobilní aplikace."
services: app-service
documentationcenter: 
author: tdykstra
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
ms.openlocfilehash: 3652e0171e0c6a53c16fb5b5fde20df9ed2ab7c7
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Spuštění úlohy na pozadí s webové úlohy v Azure App Service

## <a name="overview"></a>Přehled
Webové úlohy je funkce [Azure App Service](https://docs.microsoft.com/azure/app-service/) , můžete spuštěním programu nebo skriptu v rámci stejné jako webové aplikace, aplikace API nebo mobilní aplikace. Není k dispozici bez dalších nákladů používat webové úlohy.

Tento článek ukazuje, jak nasadit webové úlohy pomocí [portál Azure](https://portal.azure.com) nahrát spustitelný soubor nebo skript. Informace o tom, jak vyvíjet a nasazení webové úlohy pomocí sady Visual Studio najdete v tématu [nasazení webové úlohy pomocí sady Visual Studio](websites-dotnet-deploy-webjobs.md).

Sada Azure WebJobs SDK umožňuje s webové úlohy zjednodušují mnoho úloh programování. Další informace najdete v tématu [co je sada WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Azure Functions nabízí další způsob spouštění programů a skriptů. Porovnání mezi webové úlohy a funkcí najdete v tématu [volba mezi toku, Logic Apps, funkce a WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Typy webové úlohy

Následující tabulka popisuje rozdíly mezi *průběžné* a *aktivaci* webové úlohy.


|Nepřetržitě  |Aktivované  |
|---------|---------|
| Spustí se okamžitě, když je vytvořena vytvářené webové úlohy. Pokud chcete zachovat ukončování úlohy, program nebo skript obvykle nemá svou práci v nekonečné smyčce. Pokud úloha skončit, můžete ho restartovat. | Spustí jenom v případě, že aktivovat ručně nebo podle plánu. |
| Běží na všechny instance, které webová aplikace spuštěna. Volitelně můžete omezit vytvářené webové úlohy do jediné instance. |Spustí na jednu instanci, která vybere položku Azure pro vyrovnávání zatížení.|
| Podporuje vzdálené ladění. | Nepodporuje vzdálené ladění.|

> [!NOTE]
> Webové aplikace můžete vypršení časového limitu po 20 minutách nečinnosti. Pouze požadavky na web scm (nasazení) nebo na stránky do webové aplikace na portálu pro resetování časovač. Požadavky na web skutečné Neresetovat časovač. Pokud vaše aplikace běží průběžné nebo plánované webové úlohy, povolte **Always On** zajistit spolehlivě spuštění webové úlohy. Tato funkce je k dispozici pouze v Basic, Standard a Premium [cenové úrovně](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="acceptablefiles"></a>Podporované typy souborů skriptů nebo programů

Jsou podporovány následující typy souborů:

* .bat, .cmd, .exe (pomocí Windows cmd)
* .ps1 (pomocí prostředí PowerShell)
* .SH (pomocí Bash)
* .php (pomocí PHP)
* .PY (pomocí Python)
* .js (pomocí Node.js)
* .JAR (využívající jazyk Java)

## <a name="CreateContinuous"></a> Vytvoření nepřetržitá webová úloha

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. V [portál Azure](https://portal.azure.com), přejděte na **služby App Service** stránku vaší webové aplikace App Service, aplikace API nebo mobilní aplikace.

2. Vyberte **WebJobs**.

   ![Vyberte webové úlohy](./media/web-sites-create-web-jobs/select-webjobs.png)

2. V **WebJobs** vyberte **přidat**.

    ![Webová stránka](./media/web-sites-create-web-jobs/wjblade.png)

3. Použití **přidat webové úlohy** nastavení uvedeného v tabulce.

   ![Přidat stránku webové úlohy](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Nastavení      | Ukázková hodnota   | Popis  |
   | ------------ | ----------------- | ------------ |
   | **Název** | myContinuousWebJob | Název, který je jedinečná v rámci aplikace služby App Service. Musí začínat písmenem nebo číslem a nesmí obsahovat speciální znaky jiné než "-" a "_". |
   | **Nahrávání souborů** | ConsoleApp.zip | A *.zip* soubor, který obsahuje váš soubor spustitelný soubor nebo skript, jakož i všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu. Podporované typy souborů spustitelný soubor nebo skript jsou uvedeny v [podporované typy souborů](#acceptablefiles) části. |
   | **Typ** | Nepřetržitě | [Webové úlohy typy](#webjob-types) jsou popsané výše v tomto článku. |
   | **Škálování** | Více instancí | K dispozici pouze pro nepřetržité webové úlohy. Určuje, zda program nebo skript se spustí na všech instancích nebo právě jedné instance. Možnost spustit na více instancí se nevztahuje na volné nebo sdílené [cenové úrovně](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). | 

4. Klikněte na **OK**.

   Nové webové úlohy se zobrazí na **WebJobs** stránky.

   ![Seznam webové úlohy](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Pro zastavení nebo restartování nepřetržitá webová úloha, klikněte pravým tlačítkem na úlohu v seznamu a klikněte na tlačítko **Zastavit** nebo **spustit**.

    ![Zastavit nepřetržitá webová úloha](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a> Vytvořit ručně aktivované webové úlohy

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. V [portál Azure](https://portal.azure.com), přejděte na **služby App Service** stránku vaší webové aplikace App Service, aplikace API nebo mobilní aplikace.

2. Vyberte **WebJobs**.

   ![Vyberte webové úlohy](./media/web-sites-create-web-jobs/select-webjobs.png)

2. V **WebJobs** vyberte **přidat**.

    ![Webová stránka](./media/web-sites-create-web-jobs/wjblade.png)

3. Použití **přidat webové úlohy** nastavení uvedeného v tabulce.

   ![Přidat stránku webové úlohy](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Nastavení      | Ukázková hodnota   | Popis  |
   | ------------ | ----------------- | ------------ |
   | **Název** | myTriggeredWebJob | Název, který je jedinečná v rámci aplikace služby App Service. Musí začínat písmenem nebo číslem a nesmí obsahovat speciální znaky jiné než "-" a "_".|
   | **Nahrávání souborů** | ConsoleApp.zip | A *.zip* soubor, který obsahuje váš soubor spustitelný soubor nebo skript, jakož i všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu. Podporované typy souborů spustitelný soubor nebo skript jsou uvedeny v [podporované typy souborů](#acceptablefiles) části. |
   | **Typ** | Aktivované | [Webové úlohy typy](#webjob-types) jsou popsané výše v tomto článku. |
   | **Triggery** | Ručně | |

4. Klikněte na **OK**.

   Nové webové úlohy se zobrazí na **WebJobs** stránky.

   ![Seznam webové úlohy](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Pokud chcete spustit vytvářené webové úlohy, klikněte pravým tlačítkem na jeho název v seznamu a klikněte na tlačítko **spustit**.
   
    ![Zprovoznění webové úlohy](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a> Vytvoření plánované webové úlohy

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. V [portál Azure](https://portal.azure.com), přejděte na **služby App Service** stránku vaší webové aplikace App Service, aplikace API nebo mobilní aplikace.

2. Vyberte **WebJobs**.

   ![Vyberte webové úlohy](./media/web-sites-create-web-jobs/select-webjobs.png)

2. V **WebJobs** vyberte **přidat**.

   ![Webová stránka](./media/web-sites-create-web-jobs/wjblade.png)

3. Použití **přidat webové úlohy** nastavení uvedeného v tabulce.

   ![Přidat stránku webové úlohy](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Nastavení      | Ukázková hodnota   | Popis  |
   | ------------ | ----------------- | ------------ |
   | **Název** | myScheduledWebJob | Název, který je jedinečná v rámci aplikace služby App Service. Musí začínat písmenem nebo číslem a nesmí obsahovat speciální znaky jiné než "-" a "_". |
   | **Nahrávání souborů** | ConsoleApp.zip | A *.zip* soubor, který obsahuje váš soubor spustitelný soubor nebo skript, jakož i všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu. Podporované typy souborů spustitelný soubor nebo skript jsou uvedeny v [podporované typy souborů](#acceptablefiles) části. |
   | **Typ** | Aktivované | [Webové úlohy typy](#webjob-types) jsou popsané výše v tomto článku. |
   | **Triggery** | Naplánované | Pro plánování fungovat spolehlivě, povolte funkci Always On. Always On je k dispozici pouze v Basic, Standard a Premium cenové úrovně.|
   | **Výraz CRON** | 0 0/20 * * * * | [Výrazy CRON](#cron-expressions) jsou popsané v následující části. |

4. Klikněte na **OK**.

   Nové webové úlohy se zobrazí na **WebJobs** stránky.

   ![Seznam webové úlohy](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="cron-expressions"></a>Výrazy procesu CRON

A [výraz CRON](https://en.wikipedia.org/wiki/Cron) se skládá z šesti pole: `{second} {minute} {hour} {day} {month} {day of the week}`.  Zde je několik příkladů:

* Každých 15 minut: `0 */15 * * * *`
* Každou hodinu (který je vždy, když počet minut je 0): `0 0 * * * *` 
* Každou hodinu od 9: 00 do 17: 00: `0 0 9-17 * * *` 
* V 9:30:00 každý den: `0 30 9 * * *`
* V 9:30:00 každý den v týdnu: `0 30 9 * * 1-5`

Můžete zadat výraz CRON na portálu nebo zahrnout `settings.job` soubor v kořenovém adresáři vaše webová úloha *.zip* souboru, jako v následujícím příkladu:

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

> [!NOTE]
> Když nasadíte webovou úlohu ze sady Visual Studio, označte vaše `settings.job` vlastnosti jako souboru **kopírovat, pokud je novější**.

## <a name="ViewJobHistory"></a> Zobrazení historie úlohy

1. Vyberte webové úlohy, které chcete vidět historie a pak vyberte **protokoly** tlačítko.
   
   ![Tlačítko protokoly](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. V **podrobnosti webové úlohy** vyberte dobu a získáte v podrobnostech o jeden spustit.
   
   ![Podrobnosti o webové úlohy](./media/web-sites-create-web-jobs/webjobdetails.png)

3. V **podrobnosti o spuštění úlohy WebJob** vyberte **přepnutí výstup** zobrazíte text obsah protokolu.
   
    ![Webovou úlohu podrobnosti o spuštění](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Pokud chcete zobrazit výstup textu v samostatném okně prohlížeče, vyberte **Stáhnout**. Pokud chcete stáhnout vlastní text, klikněte pravým tlačítkem na **Stáhnout** a k uložení obsahu souboru použijte možnosti v prohlížeči.
   
5. Vyberte **WebJobs** odkaz s popisem cesty v horní části stránky a přejít na seznam webové úlohy.

    ![Webovou úlohu s popisem cesty](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Seznam webové úlohy na řídicím panelu historii](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> Další kroky

Sada Azure WebJobs SDK umožňuje s webové úlohy zjednodušují mnoho úloh programování. Další informace najdete v tématu [co je sada WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

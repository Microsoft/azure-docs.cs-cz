---
title: Spouštění úloh na pozadí pomocí webových úloh
description: Zjistěte, jak pomocí webových úloh spouštět úlohy na pozadí ve službě Azure App Service. Vyberte si z různých formátů skriptů a spusťte je pomocí cron výrazů.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;david.ebbo;suwatch;pbatum;naren.soni
ms.custom: seodec18
ms.openlocfilehash: 4c568c95a5dbc1799a765c95a2b224de53dfbe9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279140"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Spouštění úloh na pozadí pomocí webových úloh ve službě Azure App Service

Tento článek ukazuje, jak nasadit WebJobs pomocí [portálu Azure](https://portal.azure.com) k nahrání spustitelného souboru nebo skriptu. Informace o vývoji a nasazování webových úloh pomocí sady Visual Studio naleznete v [tématu Deploy WebJobs using Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Přehled
WebJobs je funkce [služby Azure App Service,](index.yml) která umožňuje spouštět program nebo skript ve stejném kontextu jako webová aplikace, aplikace rozhraní API nebo mobilní aplikace. Použití webových úloh neplatí žádné další náklady.

> [!IMPORTANT]
> WebJobs ještě není podporovánpro app service na Linuxu.

Sada Azure WebJobs SDK lze použít s webjobs zjednodušit mnoho programovacích úloh. Další informace naleznete [v tématu What is the WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Azure Functions poskytuje další způsob, jak spouštět programy a skripty. Porovnání mezi webjobs a funkce, najdete v [tématu výběr mezi toku, logic apps, funkce a webjobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Typy webových úloh

Následující tabulka popisuje rozdíly mezi *souvislými* a *aktivovanými* webovými úlohami.


|Průběžný  |Spuštěna  |
|---------|---------|
| Spustí se okamžitě po vytvoření webjob. Chcete-li zabránit ukončení úlohy, program nebo skript obvykle provádí svou práci uvnitř nekonečné smyčky. Pokud úloha skončí, můžete ji restartovat. | Spustí se pouze při ručním nebo plánovaném spuštění. |
| Spustí se na všech instancích, na kterých je webová aplikace spuštěna. Volitelně můžete omezit webovou úlohu na jednu instanci. |Běží na jedné instanci, kterou Azure vybere pro vyrovnávání zatížení.|
| Podporuje vzdálené ladění. | Nepodporuje vzdálené ladění.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Podporované typy souborů pro skripty nebo programy

Podporované jsou následující typy souborů:

* .cmd, .bat, .exe (pomocí windows cmd)
* .ps1 (pomocí PowerShellu)
* .sh (pomocí Bash)
* .php (pomocí PHP)
* .py (pomocí Pythonu)
* .js (pomocí souboru Node.js)
* .jar (pomocí Javy)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a>Vytvoření souvislé webové úlohy

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Na [webu Azure portal](https://portal.azure.com)přejděte na stránku **Služby aplikací** webové aplikace App Service, aplikace API nebo mobilní aplikace.

2. Vyberte **webovou úlohu**.

   ![Vybrat webovou úlohu](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stránce **WebJobs** vyberte **Přidat**.

    ![Webová úloha](./media/web-sites-create-web-jobs/wjblade.png)

3. Použijte nastavení **Přidat webovou úlohu,** jak je uvedeno v tabulce.

   ![Stránka Přidat webovou úlohu](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Nastavení      | Ukázková hodnota   | Popis  |
   | ------------ | ----------------- | ------------ |
   | **Název** | myContinuousWebJob | Název, který je jedinečný v rámci aplikace App Service. Musí začínat písmenem nebo číslem a nesmí obsahovat jiné speciální znaky než "-" a "_". |
   | **Nahrání souboru** | ConsoleApp.zip | Soubor *ZIP,* který obsahuje spustitelný soubor nebo soubor skriptu, stejně jako všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu. Podporované typy spustitelných souborů nebo souborů skriptu jsou uvedeny v části [Podporované typy souborů.](#acceptablefiles) |
   | **Typ** | Průběžný | Typy [WebJob](#webjob-types) jsou popsány dříve v tomto článku. |
   | **Měřítko** | Víceindicí instancí | K dispozici pouze pro průběžné webové úlohy. Určuje, zda je program nebo skript spuštěn ve všech instancích nebo pouze v jedné instanci. Možnost spouštět ve více instancích se nevztahuje na cenové [úrovně](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)Free nebo Shared . | 

4. Klikněte na tlačítko **OK**.

   Nová webová úloha se zobrazí na stránce **WebJobs.**

   ![Seznam webových úloh](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Chcete-li zastavit nebo restartovat souvislou webovou úlohu, klepněte v seznamu pravým tlačítkem myši na webovou úlohu a klepněte na příkaz **Zastavit** nebo **spustit**.

    ![Zastavení souvislé webové úlohy](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a>Vytvoření ručně aktivované webové úlohy

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Na [webu Azure portal](https://portal.azure.com)přejděte na stránku **Služby aplikací** webové aplikace App Service, aplikace API nebo mobilní aplikace.

2. Vyberte **webovou úlohu**.

   ![Vybrat webovou úlohu](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stránce **WebJobs** vyberte **Přidat**.

    ![Webová úloha](./media/web-sites-create-web-jobs/wjblade.png)

3. Použijte nastavení **Přidat webovou úlohu,** jak je uvedeno v tabulce.

   ![Stránka Přidat webovou úlohu](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Nastavení      | Ukázková hodnota   | Popis  |
   | ------------ | ----------------- | ------------ |
   | **Název** | myTriggeredWebJob | Název, který je jedinečný v rámci aplikace App Service. Musí začínat písmenem nebo číslem a nesmí obsahovat jiné speciální znaky než "-" a "_".|
   | **Nahrání souboru** | ConsoleApp.zip | Soubor *ZIP,* který obsahuje spustitelný soubor nebo soubor skriptu, stejně jako všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu. Podporované typy spustitelných souborů nebo souborů skriptu jsou uvedeny v části [Podporované typy souborů.](#acceptablefiles) |
   | **Typ** | Spuštěna | Typy [WebJob](#webjob-types) jsou popsány dříve v tomto článku. |
   | **Aktivační události** | Ruční | |

4. Klikněte na tlačítko **OK**.

   Nová webová úloha se zobrazí na stránce **WebJobs.**

   ![Seznam webových úloh](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Chcete-li webovou úlohu spustit, klepněte pravým tlačítkem myši na její název v seznamu a klepněte na příkaz **Spustit**.
   
    ![Spustit webovou úlohu](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a>Vytvoření naplánované webové úlohy

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Na [webu Azure portal](https://portal.azure.com)přejděte na stránku **Služby aplikací** webové aplikace App Service, aplikace API nebo mobilní aplikace.

2. Vyberte **webovou úlohu**.

   ![Vybrat webovou úlohu](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stránce **WebJobs** vyberte **Přidat**.

   ![Webová úloha](./media/web-sites-create-web-jobs/wjblade.png)

3. Použijte nastavení **Přidat webovou úlohu,** jak je uvedeno v tabulce.

   ![Stránka Přidat webovou úlohu](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Nastavení      | Ukázková hodnota   | Popis  |
   | ------------ | ----------------- | ------------ |
   | **Název** | myScheduledWebJob | Název, který je jedinečný v rámci aplikace App Service. Musí začínat písmenem nebo číslem a nesmí obsahovat jiné speciální znaky než "-" a "_". |
   | **Nahrání souboru** | ConsoleApp.zip | Soubor *ZIP,* který obsahuje spustitelný soubor nebo soubor skriptu, stejně jako všechny podpůrné soubory potřebné ke spuštění programu nebo skriptu. Podporované typy spustitelných souborů nebo souborů skriptu jsou uvedeny v části [Podporované typy souborů.](#acceptablefiles) |
   | **Typ** | Spuštěna | Typy [WebJob](#webjob-types) jsou popsány dříve v tomto článku. |
   | **Aktivační události** | Naplánované | Aby plánování fungovalo spolehlivě, povolte funkci Vždy zapnuto. Vždy zapnuto je k dispozici pouze v cenových úrovních Basic, Standard a Premium.|
   | **Výraz CRON** | 0 0/20 * * * * | [Výrazy CRON](#ncrontab-expressions) jsou popsány v následující části. |

4. Klikněte na tlačítko **OK**.

   Nová webová úloha se zobrazí na stránce **WebJobs.**

   ![Seznam webových úloh](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>Výrazy NCRONTAB

Na portál můžete zadat [výraz NCRONTAB](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) `settings.job` nebo zahrnout soubor do kořenového adresáře souboru *ZIP* webjob, jako v následujícím příkladu:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Další informace najdete [v tématu Plánování spuštěné webové úlohy](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a>Zobrazení historie úloh

1. Vyberte webovou úlohu, pro kterou chcete zobrazit historii, a pak vyberte tlačítko **Protokoly.**
   
   ![Tlačítko Protokoly](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. Na stránce **Podrobnosti o webové úloze** vyberte čas pro zobrazení podrobností pro jedno spuštění.
   
   ![Podrobnosti o webové úloze](./media/web-sites-create-web-jobs/webjobdetails.png)

3. Na stránce **Podrobnosti spuštění webu** vyberte **Přepnout výstup,** abyste viděli text obsahu protokolu.
   
    ![Podrobnosti o spuštění webové úlohy](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Chcete-li zobrazit výstupní text v samostatném okně prohlížeče, vyberte **možnost Stáhnout**. Chcete-li stáhnout samotný text, klepněte pravým tlačítkem myši na **tlačítko Stáhnout** a pomocí možností prohlížeče uložit obsah souboru.
   
5. Vyberte odkaz S popisem cesty **WebJobs** v horní části stránky a přejděte na seznam webových úloh.

    ![Popis cesty webovou úlohou](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Seznam webových úloh v řídicím panelu historie](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a>Další kroky

Sada Azure WebJobs SDK lze použít s webjobs zjednodušit mnoho programovacích úloh. Další informace naleznete [v tématu What is the WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

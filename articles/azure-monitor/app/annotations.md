---
title: Poznámky pro službu Application Insights k verzi | Dokumentace Microsoftu
description: Přidat nasazení nebo vytvořit značky do grafy Průzkumníka metrik ve službě Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mbullwin
ms.openlocfilehash: 6567d7f2ebaab5bd7b5bc8fb7b5a62970f169161
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476173"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Poznámky na grafy metrik ve službě Application Insights

Poznámky na [Průzkumníka metrik](../../azure-monitor/app/metrics-explorer.md) grafy zobrazují, kam jste nasadili nového sestavení nebo jiné významné události. Využívají ji snadno zjistit, zda změny měla vliv na výkon vaší aplikace. Můžete být automaticky vytvoří podle [Azure DevOps služby sestavovací systém](https://docs.microsoft.com/azure/devops/pipelines/tasks/). Můžete také vytvořit poznámky označit, že všechny události, kterou chcete, můžete vytvořit z prostředí PowerShell.

> [!NOTE]
> Tento článek odráží zastaralá **prostředí klasické metriky**. Poznámky jsou aktuálně dostupné v klasickém prostředí a  **[sešity](../../azure-monitor/app/usage-workbooks.md)** . Další informace o aktuálním prostředí metriky, můžete konzultovat [v tomto článku](../../azure-monitor/platform/metrics-charts.md).

![Příklad poznámky](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>Poznámky k sestavení služeb Azure DevOps verzi

Poznámky k verzi jsou funkce cloudovou službu Azure kanály DevOps služeb Azure.

### <a name="install-the-annotations-extension-one-time"></a>Instalace rozšíření poznámky (jednou)
Aby bylo možné vytvořit anotace k vydání verze, bude nutné nainstalovat některou z mnoha služeb Azure DevOps rozšíření jsou dostupná v aplikaci Visual Studio Marketplace.

1. Přihlaste se k vaší [Azure DevOps služby](https://azure.microsoft.com/services/devops/) projektu.
2. Visual Studio Marketplace [získat rozšíření poznámek](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)a přidejte ji do vaší organizace služby Azure DevOps.

![Vyberte organizaci Azure DevOps a pak nainstalujte.](./media/annotations/1-install.png)

Stačí provést jednou pro organizaci služeb Azure DevOps. Poznámky k verzi lze nyní nastavit pro libovolný projekt ve vaší organizaci.

### <a name="configure-release-annotations"></a>Konfigurace poznámek

Je nutné získat samostatný klíč rozhraní API pro každé šablony vydané verze služby Azure DevOps.

1. Přihlaste se k [portálu Microsoft Azure](https://portal.azure.com) a otevřete prostředek Application Insights, který monitoruje vaše aplikace. (Nebo [založte si ho teď](../../azure-monitor/app/app-insights-overview.md), pokud jste tak ještě neučinili.)
2. Otevřít **přístup přes rozhraní API** karty a kopii **ID aplikace Insights**.
   
    ![Na stránce portal.azure.com otevřete prostředek Application Insights a vyberte nastavení. Otevřete přístup přes rozhraní API. Zkopírujte ID aplikace](./media/annotations/2-app-id.png)

4. V samostatném okně prohlížeče otevřete (nebo vytvořte) šablony vydané verze, která spravuje vaše nasazení služby Azure DevOps.
   
    Přidat úlohy a vyberte úlohu poznámky Application Insights verze v nabídce.

   ![Klikněte na symbol plus přidejte úkol a vyberte poznámky Application Insights verze. Vložte ID aplikace Insights.](./media/annotations/3-add-task.png)

    Vložit **ID aplikace** , který jste zkopírovali na kartě přístup přes rozhraní API.
   
    ![Vložte ID aplikace Insights](./media/annotations/4-paste-app-id.png)

5. Zpět v okně Azure vytvořte nový klíč rozhraní API a pořiďte si ho.
   
    ![Na kartě přístup přes rozhraní API v okně Azure klikněte na vytvořit klíč rozhraní API.](./media/annotations/5-create-api-key.png)

    ![Na kartě klíče vytvořit rozhraní API zadejte komentář, zkontrolujte poznámky zápisu a klikněte na tlačítko vygenerovat klíč. Zkopírujte nový klíč.](./media/annotations/6-create-api-key.png)

6. Otevřete kartu Konfigurace šablony vydané verze.
   
    Vytvořit definici proměnné pro `ApiKey`.
   
    Vložte svůj klíč rozhraní API pro definici proměnné ApiKey.
   
    ![V okně Azure DevOps služby vyberte na kartě proměnné a klikněte na tlačítko Přidat. Nastavte název ApiKey a do hodnoty, vložte klíč, který jste vygenerovali a klikněte na ikonu zámku.](./media/annotations/7-paste-api-key.png)
1. Nakonec **Uložit** kanál pro vydávání verzí.


## <a name="view-annotations"></a>Zobrazit poznámky
Nyní pokaždé, když použijete šablonu vydané verze pro nasazení nové verze, Poznámka se odešlou do služby Application Insights. Poznámky se zobrazí v grafech v Průzkumníku metrik.

Klikněte na všechny značky poznámek (světle šedá šipka) zobrazíte podrobnosti o verzi, včetně žadatel, zdrojová větev ovládacího prvku, vydaných verzí kanálu, prostředí a další.

![Klikněte na možnost všechny verze poznámky značky.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Vytvoření vlastní poznámky z prostředí PowerShell
Můžete také vytvořit poznámky z nějaký proces, který rádi používáte (bez použití služby Azure DevOps). 


1. Vytvořit místní kopii [Powershellový skript z Githubu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Získání ID aplikace a vytvořte klíč rozhraní API na kartě přístup přes rozhraní API.

3. Volání skriptu tímto způsobem:

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

Je snadné a upravte skript, například k vytváření poznámek za poslední.

## <a name="next-steps"></a>Další postup

* [Vytváření pracovních položek](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatizace pomocí Powershellu](../../azure-monitor/app/powershell.md)

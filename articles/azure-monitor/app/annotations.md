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
ms.date: 07/01/2019
ms.author: mbullwin
ms.openlocfilehash: e3ec202ba6126b150fb78c76591682f163018661
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604549"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Poznámky na grafy metrik ve službě Application Insights

Poznámky na [Průzkumníka metrik](../../azure-monitor/app/metrics-explorer.md) grafy zobrazují, kam jste nasadili nového sestavení nebo jiné významné události. Zkontrolujte poznámky snadno zjistit, zda změny měla vliv na výkon vaší aplikace. Můžete být automaticky vytvoří podle [kanály Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/) sestavovací systém. Můžete také vytvořit poznámky označit, že všechny události, kterou chcete, můžete vytvořit z prostředí PowerShell.

> [!NOTE]
> Tento článek odráží zastaralá **prostředí klasické metriky**. Poznámky jsou aktuálně dostupné v klasickém prostředí a  **[sešity](../../azure-monitor/app/usage-workbooks.md)** . Další informace o aktuálním prostředí metrik najdete v tématu [pokročilé funkce Průzkumníka metrik Azure](../../azure-monitor/platform/metrics-charts.md).

![Příklad poznámky](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Poznámky k sestavení kanály Azure verzi

Poznámky k verzi jsou funkce založené na cloudu Azure kanálů služby Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Instalace rozšíření poznámky (jednou)
Aby bylo možné vytvořit anotace k vydání verze, bude nutné nainstalovat některou z mnoha dostupných rozšíření Azure DevOps ve Visual Studio Marketplace.

1. Přihlaste se k vaší [Azure DevOps](https://azure.microsoft.com/services/devops/) projektu.
   
1. Na webu Visual Studio Marketplace [poznámek rozšíření](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) stránky, vyberte svoji organizaci Azure DevOps a pak vyberte **nainstalovat** přidání rozšíření pro vaši organizaci Azure DevOps.
   
   ![Vyberte organizaci Azure DevOps a pak vyberte instalace.](./media/annotations/1-install.png)
   
Stačí nainstalovat rozšíření jednou pro vaši organizaci Azure DevOps. Teď můžete nakonfigurovat poznámek pro libovolný projekt ve vaší organizaci.

### <a name="configure-release-annotations"></a>Konfigurace poznámek

Vytvořte samostatný klíč rozhraní API pro každou z vaší šablony vydané verze kanály Azure.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a otevřete prostředek Application Insights, který monitoruje vaše aplikace. Nebo pokud ho nemáte, [vytvořit nový prostředek Application Insights](../../azure-monitor/app/app-insights-overview.md).
   
1. Otevřít **přístup přes rozhraní API** karty a kopii **ID aplikace Insights**.
   
   ![V části přístup přes rozhraní API zkopírujte ID aplikace.](./media/annotations/2-app-id.png)

1. V samostatném okně prohlížeče otevřete nebo vytvořte šablony vydané verze, která spravuje vaše kanály Azure nasazení.
   
1. Vyberte **přidat úkol**a pak vyberte **poznámky Application Insights verze** úloh v nabídce.
   
   ![Vyberte Přidat úkol a vyberte Application Insights verze poznámky.](./media/annotations/3-add-task.png)
   
1. V části **ID aplikace**, vložte ID aplikace Insights jste zkopírovali ze **přístup přes rozhraní API** kartu.
   
   ![Vložte ID aplikace Insights](./media/annotations/4-paste-app-id.png)
   
1. Zpět v Application Insights **přístup přes rozhraní API** okně **vytvořit klíč rozhraní API**. 
   
   ![Na kartě přístup přes rozhraní API vyberte vytvořit klíč rozhraní API.](./media/annotations/5-create-api-key.png)
   
1. V **vytvořit klíč rozhraní API** okno, zadejte popis, vyberte **psaní poznámek**a pak vyberte **vygenerovat klíč**. Zkopírujte nový klíč.
   
   ![V okně klíče vytvořit rozhraní API zadejte popis, vyberte zápis poznámky a vyberte vygenerovat klíč.](./media/annotations/6-create-api-key.png)
   
1. V okně verze šablony na **proměnné** kartu, vyberte možnost **přidat** vytvořit definici proměnné pro nový klíč rozhraní API.

1. V části **název**, zadejte `ApiKey`a v části **hodnotu**, vložte klíč rozhraní API, který jste zkopírovali ze **přístup přes rozhraní API** kartu.
   
   ![Na kartě proměnné Azure DevOps vyberte možnost přidat, název proměnné ApiKey a vložte klíč rozhraní API podle hodnoty.](./media/annotations/7-paste-api-key.png)
   
1. Vyberte **Uložit** v okně hlavní verze šablony tím uložíte šablonu.

## <a name="view-annotations"></a>Zobrazit poznámky
Teď se pokaždé, když použijete šablonu vydané verze pro nasazení nové verze, anotaci odesílaných do Application Insights. Poznámky se zobrazí v grafech v **Průzkumníka metrik**.

Vyberte všechny značky poznámek (světle šedá šipka) zobrazíte podrobnosti o verzi, včetně žadatele, zdrojová větev ovládacího prvku, kanál pro vydávání verzí a prostředí.

![Vyberte značku poznámek vydání.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Vytvoření vlastní poznámky z prostředí PowerShell
Můžete použít [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) Powershellový skript z Githubu k vytváření poznámek ze nějaký proces, který rádi používáte, bez použití Azure DevOps. 

1. Vytvořit místní kopii [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Postupujte podle kroků v předchozím postupu k získání ID aplikace Insights a vytvořte klíč rozhraní API z Application Insights **přístup přes rozhraní API** kartu.
   
1. Volání Powershellový skript s následující kód a nahraďte zástupné symboly uváděn úhel s vašimi hodnotami. `-releaseProperties` Jsou volitelné. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Můžete upravit skript, například k vytváření poznámek za poslední.

## <a name="next-steps"></a>Další postup

* [Vytváření pracovních položek](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatizace pomocí Powershellu](../../azure-monitor/app/powershell.md)

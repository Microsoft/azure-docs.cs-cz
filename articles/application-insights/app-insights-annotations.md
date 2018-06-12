---
title: Verze poznámky pro službu Application Insights | Microsoft Docs
description: Přidání nasazení nebo vytvořit značek do grafů Průzkumníku metrik ve službě Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/16/2016
ms.author: mbullwin
ms.openlocfilehash: fb4bcd57062017c0d7ee802ba1f46660476f6af2
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293431"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Poznámky u metriky grafů ve službě Application Insights
Poznámky na [Průzkumníku metrik](app-insights-metrics-explorer.md) grafy zobrazují, kde jste nasadili nového sestavení nebo jinou významnou událost. Ujistěte se snadno zjistit, zda změny měla vliv na výkon vaší aplikace. Můžete být automaticky vytvoří pomocí [Visual Studio Team Services sestavení systému](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs). Můžete také vytvořit poznámky pro všechny události, které si přejete, a příznak [jejich vytváření z prostředí PowerShell](#create-annotations-from-powershell).

![Příklad poznámky s viditelné korelace s doba odezvy serveru](./media/app-insights-annotations/00.png)



## <a name="release-annotations-with-vsts-build"></a>Poznámky k verzi s služby VSTS sestavení

Poznámky k verzi jsou funkce cloudového sestavování a verzí služby Visual Studio Team Services. 

### <a name="install-the-annotations-extension-one-time"></a>Nainstalujte rozšíření poznámky (jednou)
Abyste mohli vytvořit poznámky k verzi, musíte nainstalovat některou z mnoha rozšíření Team služeb, která je k dispozici ve Visual Studio Marketplace.

1. Přihlaste se k vaší [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online) projektu.
2. V sadě Visual Studio Marketplace [získat rozšíření verze poznámek](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)a přidejte ji do vašeho účtu Team Services.

![Top na pravé straně Team Services webové stránky, otevřete Marketplace. Vyberte Visual Team Services a potom v části sestavení a verze, zvolte najdete v části více.](./media/app-insights-annotations/10.png)

Stačí k tomu jednou pro váš účet Visual Studio Team Services. Poznámky k verzi se teď dá nakonfigurovat pro každý projekt ve vašem účtu. 

### <a name="configure-release-annotations"></a>Konfigurace verze poznámek

Je nutné získat samostatné klíč rozhraní API pro každou verzi šablony služby VSTS.

1. Přihlaste se k [portálu Microsoft Azure](https://portal.azure.com) a otevřete prostředek Application Insights, který monitoruje vaše aplikace. (Nebo [vytvořit nový](app-insights-overview.md), pokud jste tak ještě neučinili.)
2. Otevřete **přístup pomocí rozhraní API**, **Application Insights Id**.
   
    ![Na stránce portal.azure.com otevřete prostředek Application Insights a zvolte nastavení. Otevřete přístup pomocí rozhraní API. Zkopírujte ID aplikace](./media/app-insights-annotations/20.png)

4. V samostatném okně prohlížeče otevřete (nebo vytvořte) verzi šablony, která spravuje vaše nasazení z Visual Studio Team Services. 
   
    Přidat úlohu a vyberte úlohu Application Insights verze poznámky z nabídky.
   
    Vložení **Id aplikace** který jste zkopírovali v okně přístup pomocí rozhraní API.
   
    ![Ve Visual Studio Team Services otevřete verzi, vyberte verzi definice a zvolte Upravit. Klikněte na tlačítko Přidat úloha a vyberte Application Insights verze poznámky. Vložte ID Application Insights.](./media/app-insights-annotations/30.png)
4. Nastavte **APIKey** pole k proměnné `$(ApiKey)`.

5. Zpět v okně Azure vytvořte nový klíč rozhraní API a provést jeho kopii.
   
    ![V okně přístup pomocí rozhraní API v okně Azure klikněte na tlačítko vytvořit klíč rozhraní API. Zadejte komentář, zkontrolujte poznámky zápisu a klikněte na tlačítko vygenerovat klíč. Zkopírujte nový klíč.](./media/app-insights-annotations/40.png)

6. Otevřete kartu Konfigurace šablony verze.
   
    Vytvoření proměnné definice pro `ApiKey`.
   
    Vložte klíč rozhraní API k definici ApiKey proměnné.
   
    ![V okně Team Services vyberte na kartě Konfigurace a klikněte na tlačítko Přidat proměnnou. Nastavte název pro ApiKey a do hodnoty, vložte klíč, který jste právě vygenerovali a klikněte na ikonu zámku.](./media/app-insights-annotations/50.png)
7. Nakonec **Uložit** definici verze.


## <a name="view-annotations"></a>Zobrazení poznámky
Nyní vždy, když použijete šablonu verze nasadit novou verzi, poznámky odešle do služby Application Insights. Poznámky se zobrazí v grafech v Průzkumníku metrik.

Klikněte na všechny značky poznámky otevřít podrobnosti o verze, včetně žadatele, zdroj ovládacího prvku větve, verze definice, prostředí a další.

![Klikněte na možnost všechny značky poznámky verze.](./media/app-insights-annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Vytvořit vlastní poznámky z prostředí PowerShell
Můžete také vytvořit poznámky z jakýkoli proces, který chcete (bez použití VS Team System). 


1. Vytvořit místní kopii [skript prostředí Powershell z Githubu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Získání ID aplikace a vytvořte klíč rozhraní API v okně přístup pomocí rozhraní API.

3. Volání skriptu takto:

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

Je snadné a upravte skript, například vytvořit poznámky za poslední.

## <a name="next-steps"></a>Další postup

* [Vytváření pracovních položek](app-insights-diagnostic-search.md#create-work-item)
* [Automatizace v prostředí PowerShell](app-insights-powershell.md)

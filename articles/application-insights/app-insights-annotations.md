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
ms.devlang: na
ms.topic: conceptual
ms.date: 11/16/2016
ms.author: mbullwin
ms.openlocfilehash: 660080a629e00884dd61a49bc0950ebe25b6a0c5
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054223"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Poznámky na grafy metrik ve službě Application Insights
Poznámky na [Průzkumníka metrik](app-insights-metrics-explorer.md) grafy zobrazují, kam jste nasadili nového sestavení nebo jiné významné události. Využívají ji snadno zjistit, zda změny měla vliv na výkon vaší aplikace. Můžete být automaticky vytvoří podle [sestavovací systém Visual Studio Team Services](https://docs.microsoft.com/vsts/pipelines/tasks/). Můžete také vytvořit poznámky k nastavení příznaku jakékoli události, například podle [jejich vytváření z Powershellu](#create-annotations-from-powershell).

![Příklad poznámky s viditelné korelace s doba odezvy serveru](./media/app-insights-annotations/00.png)



## <a name="release-annotations-with-vsts-build"></a>Poznámky k sestavení VSTS verzi

Poznámek jsou funkce založené na cloudu sestavení a vydání verze služby Visual Studio Team Services. 

### <a name="install-the-annotations-extension-one-time"></a>Instalace rozšíření poznámky (jednou)
Aby bylo možné vytvořit anotace k vydání verze, bude nutné nainstalovat některou z mnoha rozšíření Team Service jsou dostupná v aplikaci Visual Studio Marketplace.

1. Přihlaste se k vaší [Visual Studio Team Services](https://visualstudio.microsoft.com/vso/) projektu.
2. Visual Studio Marketplace [získat rozšíření poznámek](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)a přidejte ji do vašeho účtu Team Services.

![AT horní pravé části stránky webové služby Team Services, otevřete Marketplace. Vyberte Visual Team Services a pak v rámci sestavení a vydaných verzí, zvolte Další.](./media/app-insights-annotations/10.png)

Stačí provést jednou pro váš účet Visual Studio Team Services. Poznámky k vydání verze můžete teď nakonfigurované pro libovolný projekt ve vašem účtu. 

### <a name="configure-release-annotations"></a>Konfigurace poznámek

Je nutné získat samostatný klíč rozhraní API pro každou šablonu vydané verze VSTS.

1. Přihlaste se k [Microsoft Azure Portal](https://portal.azure.com) a otevřete prostředek Application Insights, který monitoruje vaše aplikace. (Nebo [založte si ho teď](app-insights-overview.md), pokud jste tak ještě neučinili.)
2. Otevřít **přístup přes rozhraní API**, **Id aplikace Insights**.
   
    ![Na stránce portal.azure.com otevřete prostředek Application Insights a vyberte nastavení. Otevřete přístup přes rozhraní API. Zkopírujte ID aplikace](./media/app-insights-annotations/20.png)

4. V samostatném okně prohlížeče otevřete (nebo vytvořte) šablony vydané verze, která spravuje vaše nasazení z Visual Studio Team Services. 
   
    Přidat úlohy a vyberte úlohu poznámky Application Insights verze v nabídce.
   
    Vložit **Id aplikace** , který jste zkopírovali z okna přístup přes rozhraní API.
   
    ![Ve službě Visual Studio Team Services otevřete verzi, vyberte definici verze a klikněte na položku upravit. Klikněte na tlačítko Přidat úkol a vyberte Application Insights verze poznámky. Vložte ID aplikace Insights.](./media/app-insights-annotations/30.png)
4. Nastavte **APIKey** pole proměnné `$(ApiKey)`.

5. Zpět v okně Azure vytvořte nový klíč rozhraní API a pořiďte si ho.
   
    ![V okně přístup přes rozhraní API v okně Azure klikněte na vytvořit klíč rozhraní API. Zadejte komentář, zkontrolujte poznámky zápisu a klikněte na tlačítko vygenerovat klíč. Zkopírujte nový klíč.](./media/app-insights-annotations/40.png)

6. Otevřete kartu Konfigurace šablony vydané verze.
   
    Vytvořit definici proměnné pro `ApiKey`.
   
    Vložte svůj klíč rozhraní API pro definici proměnné ApiKey.
   
    ![V okně služby Team Services vyberte na kartě Konfigurace a klikněte na tlačítko Přidat proměnnou. Nastavte název ApiKey a do hodnoty, vložte klíč, který jste právě vygenerovali a klikněte na ikonu zámku.](./media/app-insights-annotations/50.png)
7. Nakonec **Uložit** definice vydané verze.


## <a name="view-annotations"></a>Zobrazit poznámky
Nyní pokaždé, když použijete šablonu vydané verze pro nasazení nové verze, Poznámka se odešlou do služby Application Insights. Poznámky se zobrazí v grafech v Průzkumníku metrik.

Klikněte na všechny značky poznámek otevřete podrobnosti o verzi, včetně žadatel, zdrojová větev ovládacího prvku, verze, definice, prostředí a další.

![Klikněte na možnost všechny verze poznámky značky.](./media/app-insights-annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Vytvoření vlastní poznámky z prostředí PowerShell
Můžete také vytvořit poznámky z nějaký proces, který rádi používáte (bez použití VS Team System). 


1. Vytvořit místní kopii [Powershellový skript z Githubu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Získání ID aplikace a vytvořte klíč rozhraní API v okně přístup přes rozhraní API.

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

* [Vytváření pracovních položek](app-insights-diagnostic-search.md#create-work-item)
* [Automatizace pomocí Powershellu](app-insights-powershell.md)

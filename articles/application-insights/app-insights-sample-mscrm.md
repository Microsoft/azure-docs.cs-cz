---
title: Microsoft Dynamics CRM a Azure Application Insights | Dokumentace Microsoftu
description: Získání telemetrie z Microsoft Dynamics CRM Online pomocí Application Insights. Průvodce instalací, získávání dat, vizualizace a export.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/16/2018
ms.reviewer: mazhar
ms.author: mbullwin
ms.openlocfilehash: b4bc632b4951781b85847699d38fd10df14c1a87
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720741"
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Názorný postup: Zapnutí Telemetrie pro Microsoft Dynamics CRM Online pomocí Application Insights
V tomto článku se dozvíte, jak získat telemetrická data z [Microsoft Dynamics CRM Online](https://www.dynamics.com/) pomocí [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Provedeme procesem kompletní proces přidávání skript Application Insights do vaší aplikace, zachycení dat a vizualizace dat.

> [!NOTE]
> [Procházet ukázkové řešení](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Přidejte Application Insights do nové nebo stávající instance CRM Online
Pro monitorování vaší aplikace, přidejte sadu Application Insights SDK do vaší aplikace. Sada SDK odesílá telemetrická data [portál Application Insights](https://portal.azure.com), kde můžete použít naši výkonné analýzy a diagnostické nástroje, nebo exportovat data do úložiště.

### <a name="create-an-application-insights-resource-in-azure"></a>Vytvořte prostředek Application Insights v Azure
1. Získat [účet v Microsoft Azure](http://azure.com/pricing). 
2. Přihlaste se [webu Azure portal](https://portal.azure.com) a přidat nový prostředek Application Insights. Toto je, kde zpracovat a zobrazit vaše data.

    ![Klikněte na +, služby pro vývojáře, Application Insights.](./media/app-insights-sample-mscrm/01.png)

    Vyberte jako typ aplikace ASP.NET.
3. Postupujte podle pokynů a [získat skript jazyka JavaScript SDK pro vaši aplikaci](app-insights-javascript.md#set-up-application-insights-for-your-web-page), zkopírujte fragment kódu jazyka JavaScript a ujistěte se, že Instrumentační klíč nahraďte správné hodnoty pro váš prostředek Application Insights.

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Vytvoření webovém prostředku jazyka JavaScript v aplikaci Microsoft Dynamics CRM
1. Otevřete instanci CRM Online a přihlášení s oprávněními správce.
2. Otevřete Microsoft Dynamics CRM nastavení přizpůsobení, přizpůsobení systému

    ![Nastavení aplikace Microsoft Dynamics CRM](./media/app-insights-sample-mscrm/00001.png)

    ![Nastavení > Přizpůsobení](./media/app-insights-sample-mscrm/00002.png)

1. Vytvoření prostředku jazyka JavaScript.

    ![Dialogové okno nového webovém prostředku](./media/app-insights-sample-mscrm/07.png)

    Přiřaďte jí název, vyberte **skript (JScript)** a otevřete textový editor.

    ![Otevřete textový editor](./media/app-insights-sample-mscrm/00004.png)
2. Zkopírujte kód z Application Insights JavaScript SDK, ve kterém nakonfigurujete svůj Instrumentační klíč před. Při kopírování, ujistěte se, že chcete ignorovat značky skriptu. Podívejte se na snímku obrazovky:

    ![Nastavte klíč instrumentace](./media/app-insights-sample-mscrm/000005.png)

    Tento kód obsahuje klíč instrumentace, který identifikuje váš prostředek Application insights.
3. Uložit a publikovat.

    ![Uložit a publikovat](./media/app-insights-sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Nástroj formulářů
1. V Microsoft CRM Online otevřete formulář účtu

    ![Formulář účtu](./media/app-insights-sample-mscrm/00007.png)
2. Otevřete vlastnosti formuláře

    ![Vlastnosti formuláře](./media/app-insights-sample-mscrm/00008.png)
3. Přidat webovém prostředku jazyka JavaScript, který jste vytvořili

    ![Přidat nabídku](./media/app-insights-sample-mscrm/13.png)

4. Uložení a publikování vlastní formulář.

## <a name="metrics-captured"></a>Metriky zachycena
Nyní jste nastavili zachycování telemetrie pro formulář. Pokaždé, když se používá, data se odešlou do prostředku Application Insights.

Tady jsou ukázky data, která se zobrazí.

#### <a name="application-health"></a>Stav aplikace
![Příklad čas načítání stránky](./media/app-insights-sample-mscrm/15.png)

![Příklad stránky zobrazení grafu](./media/app-insights-sample-mscrm/16.png)

Výjimky prohlížečů:

![Graf výjimky prohlížeče](./media/app-insights-sample-mscrm/17.png)

Klepněte na graf, abyste získali více podrobností:

![Seznam výjimek](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Využití
![Uživatelé, relace a zobrazení stránek](./media/app-insights-sample-mscrm/19.png)

![Grafy relace](./media/app-insights-sample-mscrm/20.png)

![Verze prohlížeče](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Prohlížeče
![Rozpis čas načítání stránky](./media/app-insights-sample-mscrm/22.png)

![Počet relací podle verze prohlížeče](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Zeměpisná poloha
![Počet relací podle země](./media/app-insights-sample-mscrm/24.png)

![Relace a uživatelé podle země](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Požadavek na vnitřní stránku zobrazení
![Stránka zobrazení souhrnu](./media/app-insights-sample-mscrm/26.png)

![Hledat na stránky zobrazení událostí](./media/app-insights-sample-mscrm/27.png)

![Podobná zobrazení stránek](./media/app-insights-sample-mscrm/28.png)

![Zobrazení vlastností stránky](./media/app-insights-sample-mscrm/29.png)

![Počet stránek při relaci](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Ukázka kódu
[Procházet ukázky kódu](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Můžete provést i hlubší analýzu, pokud jste [export dat do Microsoft Power BI](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Ukázkové aplikace Microsoft Dynamics CRM řešení
[Tady je ukázka řešení implementované v aplikaci Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Další informace
* [Co je Application Insights?](app-insights-overview.md)
* [Application Insights pro webové stránky](app-insights-javascript.md)
* [Další ukázky a návody](app-insights-overview.md)

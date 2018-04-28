---
title: Aplikace Microsoft Dynamics CRM a Azure Application Insights | Microsoft Docs
description: Získáte telemetrická data z Microsoft Dynamics CRM Online pomocí Application Insights. Postup instalace, získávání dat, vizualizace a export.
services: application-insights
documentationcenter: ''
author: mazharmicrosoft
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: mbullwin
ms.openlocfilehash: ced4f62b4f27ede28c61e8d2670432ddd7b76f9b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Návod: Povolení Telemetrie pro aplikaci Microsoft Dynamics CRM Online pomocí Application Insights
Tento článek ukazuje, jak získat telemetrická data z [Microsoft Dynamics CRM Online](https://www.dynamics.com/) pomocí [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Budeme zabývat dokončení proces přidávání skript Application Insights do vaší aplikace, zaznamenávání dat a vizualizace dat sady.

> [!NOTE]
> [Procházet ukázkové řešení](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Přidejte Application Insights do nové nebo stávající instance CRM Online
Do monitorování vaší aplikace, přidejte Application Insights SDK do aplikace. Sada SDK odesílá telemetrii do [portál Application Insights](https://portal.azure.com), kde můžete používat naše efektivní analýzu a diagnostické nástroje, nebo exportovat data do úložiště.

### <a name="create-an-application-insights-resource-in-azure"></a>Vytvořte prostředek Application Insights v Azure
1. Získat [účet ve službě Microsoft Azure](http://azure.com/pricing). 
2. Přihlaste se k [portál Azure](https://portal.azure.com) a přidat nový prostředek Application Insights. Toto je, kde bude zpracována a zobrazí data.

    ![Klikněte na tlačítko +, služby pro vývojáře, Application Insights.](./media/app-insights-sample-mscrm/01.png)

    Vyberte jako typ aplikace ASP.NET.
3. Otevřete stránku Začínáme a otevřete "monitorování a diagnostikovat na straně klienta".

    ![Fragment kódu pro vložení do webové stránky](./media/app-insights-sample-mscrm/03.png)

**Znaková stránka nechat otevřený** zatímco pracujete na další krok v jiném okně prohlížeče. Kód budete potřebovat brzy k dispozici. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Vytvořte prostředek JavaScript webové aplikace Microsoft Dynamics CRM
1. Otevřete CRM Online instance a přihlaste se s oprávněními správce.
2. Otevřete Microsoft Dynamics CRM nastavení, vlastní nastavení, přizpůsobení systému

    ![Nastavení aplikace Microsoft Dynamics CRM](./media/app-insights-sample-mscrm/00001.png)

    ![Nastavení > Přizpůsobení](./media/app-insights-sample-mscrm/00002.png)

1. Vytvořte prostředek JavaScript.

    ![Dialogové okno Nový webový prostředek](./media/app-insights-sample-mscrm/07.png)

    Zadejte jeho název, vyberte **skriptu (JScript)** a otevřete textový editor.

    ![Otevřete textový editor](./media/app-insights-sample-mscrm/00004.png)
2. Zkopírujte kód z Application Insights. Při kopírování, nezapomeňte ignorovat značek skriptu. Naleznete níže – snímek obrazovky:

    ![Nastavte klíč instrumentace](./media/app-insights-sample-mscrm/000005.png)

    Tento kód obsahuje klíč instrumentace, který identifikuje prostředek vaší aplikace statistiky.
3. Uložte a publikovat.

    ![Uložte a publikování](./media/app-insights-sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Nástrojích formulářů
1. V aplikaci Microsoft CRM Online otevřete formulář účtu

    ![Účet formuláře](./media/app-insights-sample-mscrm/00007.png)
2. Otevřete vlastnosti formuláře

    ![Vlastnosti formuláře](./media/app-insights-sample-mscrm/00008.png)
3. Přidání webové prostředky JavaScript, který jste vytvořili

    ![Nabídka Přidat](./media/app-insights-sample-mscrm/13.png)

4. Uložte a publikujte vlastní nastavení formuláře.

## <a name="metrics-captured"></a>Metriky zachycení
Nyní jste nastavili zachycování telemetrie pro daný formulář. Vždy, když se používají data se odešlou do zdroje Application Insights.

Tady jsou vzorků dat, která se zobrazí.

#### <a name="application-health"></a>Stav aplikace
![Příklad čas načítání stránky](./media/app-insights-sample-mscrm/15.png)

![Příklad stránky zobrazení grafu](./media/app-insights-sample-mscrm/16.png)

Výjimky prohlížečů:

![Grafu výjimek prohlížeče](./media/app-insights-sample-mscrm/17.png)

Klikněte na graf zobrazíte další podrobnosti:

![Seznamu výjimek](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Využití
![Uživatelů, relací a zobrazení stránek](./media/app-insights-sample-mscrm/19.png)

![Grafy relace](./media/app-insights-sample-mscrm/20.png)

![Verze prohlížeče](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Prohlížeče
![Rozdělení čas načítání stránky](./media/app-insights-sample-mscrm/22.png)

![Počet relací podle verze prohlížeče](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Informace o zeměpisné poloze
![Počet relací podle země](./media/app-insights-sample-mscrm/24.png)

![Relace a uživatelů podle země](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Požadavek na uvnitř stránku zobrazení
![Stránka zobrazení souhrnu](./media/app-insights-sample-mscrm/26.png)

![Vyhledávání na stránky zobrazení událostí](./media/app-insights-sample-mscrm/27.png)

![Podobně jako zobrazení stránky](./media/app-insights-sample-mscrm/28.png)

![Zobrazení vlastností stránky](./media/app-insights-sample-mscrm/29.png)

![Stránky na relaci](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Ukázka kódu
[Procházet ukázkový kód](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Můžete provést i hlubší analysis, pokud jste [export dat k Microsoft Power BI](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Ukázkové aplikace Microsoft Dynamics CRM řešení
[Zde je ukázka řešení implementována v aplikaci Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Další informace
* [Co je Application Insights?](app-insights-overview.md)
* [Application Insights pro webové stránky](app-insights-javascript.md)
* [Další ukázky a návody](app-insights-code-samples.md)

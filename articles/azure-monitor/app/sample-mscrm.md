---
title: Microsoft Dynamics CRM a Azure Application Insights | Microsoft Docs
description: Získejte telemetrii z Microsoft Dynamics CRM Online pomocí Application Insights. Návod k instalaci, získávání dat, vizualizaci a exportu.
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
ms.openlocfilehash: 470f723782ca29409549e0df8e900edf86cd446e
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534294"
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Průvodce: Povolení telemetrie pro Microsoft Dynamics CRM Online pomocí Application Insights
V tomto článku se dozvíte, jak získat data telemetrie z [Microsoft Dynamics CRM Online](https://www.dynamics.com/) pomocí [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Po dokončení procesu přidávání skriptu Application Insights do vaší aplikace, zachytávání dat a vizualizace dat vás provedeme.

> [!NOTE]
> [Projděte si ukázkové řešení](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Přidat Application Insights do nové nebo existující instance CRM Online
Chcete-li monitorovat aplikaci, přidejte do aplikace sadu Application Insights SDK. Sada SDK odesílá telemetrii na [portál Application Insights](https://portal.azure.com), kde můžete využít naše výkonné nástroje pro analýzu a diagnostiku nebo exportovat data do úložiště.

### <a name="create-an-application-insights-resource-in-azure"></a>Vytvoření prostředku Application Insights v Azure
1. Získat [účet v Microsoft Azure](https://azure.com/pricing). 
2. Přihlaste se k [Azure Portal](https://portal.azure.com) a přidejte nový prostředek Application Insights. Slouží k tomu, aby se data zpracovala a zobrazila.

    ![Klikněte na +, Vývojářské služby Application Insights.](./media/sample-mscrm/01.png)

    Vyberte jako typ aplikace ASP.NET.
3. Postupujte podle pokynů pro [získání skriptu JavaScript SDK pro vaši aplikaci](../../azure-monitor/app/javascript.md), zkopírujte fragment kódu JavaScriptu a nezapomeňte nahradit klíč instrumentace správnou hodnotou pro váš prostředek Application Insights.

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Vytvoření webového prostředku v JavaScriptu v Microsoft Dynamics CRM
1. Otevřete vaši instanci CRM Online a přihlaste se s oprávněními správce.
2. Otevřete nastavení Microsoft Dynamics CRM, přizpůsobení, přizpůsobení systému.

    ![Nastavení aplikace Microsoft Dynamics CRM](./media/sample-mscrm/00001.png)

    ![Nastavení > Přizpůsobení](./media/sample-mscrm/00002.png)

1. Vytvořte prostředek JavaScriptu.

    ![Dialog Nový webový prostředek](./media/sample-mscrm/07.png)

    Zadejte název, vyberte **skript (JScript)** a otevřete textový editor.

    ![Otevřete textový editor.](./media/sample-mscrm/00004.png)
2. Zkopírujte kód z Application Insights JavaScript SDK, ve které jste předtím nakonfigurovali klíč instrumentace. Při kopírování nezapomeňte ignorovat značky skriptu. Podívejte se na snímek obrazovky níže:

    ![Nastavení klíče instrumentace](./media/sample-mscrm/000005.png)

    Kód zahrnuje klíč instrumentace, který identifikuje prostředek Application Insights.
3. Uložit a publikovat.

    ![Uložit a publikovat](./media/sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Formuláře instrumentace
1. V aplikaci Microsoft CRM online otevřete formulář účtu.

    ![Formulář účtu](./media/sample-mscrm/00007.png)
2. Otevřete vlastnosti formuláře.

    ![Vlastnosti formuláře](./media/sample-mscrm/00008.png)
3. Přidat webový prostředek JavaScriptu, který jste vytvořili

    ![Přidat nabídku](./media/sample-mscrm/13.png)

4. Uložte a publikujte vlastní nastavení formuláře.

## <a name="metrics-captured"></a>Zaznamenané metriky
Pro tento formulář jste teď nastavili zachycení telemetrie. Pokaždé, když se použije, data se odešlou do vašeho prostředku Application Insights.

Zde jsou příklady dat, která se zobrazí.

#### <a name="application-health"></a>Stav aplikace
![Příklad času načtení stránky](./media/sample-mscrm/15.png)

![Příklad grafu zobrazení stránky](./media/sample-mscrm/16.png)

Výjimky prohlížeče:

![Graf výjimek prohlížeče](./media/sample-mscrm/17.png)

Další podrobnosti získáte kliknutím na graf:

![Seznam výjimek](./media/sample-mscrm/18.png)

#### <a name="usage"></a>Použití
![Uživatelé, relace a zobrazení stránek](./media/sample-mscrm/19.png)

![Grafy relací](./media/sample-mscrm/20.png)

![Verze prohlížeče](./media/sample-mscrm/21.png)

#### <a name="browsers"></a>Prohlížeče
![Rozpis doby načítání stránky](./media/sample-mscrm/22.png)

![Počet relací podle verze prohlížeče](./media/sample-mscrm/23.png)

#### <a name="geolocation"></a>Zeměpisná poloha
![Počet relací podle země](./media/sample-mscrm/24.png)

![Relace a uživatelé podle země](./media/sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Vnitřní požadavek na zobrazení stránky
![Souhrn zobrazení stránky](./media/sample-mscrm/26.png)

![Vyhledat události zobrazení stránky](./media/sample-mscrm/27.png)

![Podobná zobrazení stránek](./media/sample-mscrm/28.png)

![Zobrazení vlastností stránky](./media/sample-mscrm/29.png)

![Počet stránek na relaci](./media/sample-mscrm/30.png)

## <a name="sample-code"></a>Ukázka kódu
[Projděte si vzorový kód](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Pokud [exportujete data do Microsoft Power BI](../../azure-monitor/app/export-power-bi.md ), můžete provést ještě hlubší analýzu.

## <a name="sample-microsoft-dynamics-crm-solution"></a>Ukázka řešení Microsoft Dynamics CRM
[Tady je ukázkové řešení implementované v Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Víc se uč
* [Co je Application Insights?](../../azure-monitor/app/app-insights-overview.md)
* [Application Insights pro webové stránky](../../azure-monitor/app/javascript.md)
* [Další ukázky a návody](../../azure-monitor/app/app-insights-overview.md)

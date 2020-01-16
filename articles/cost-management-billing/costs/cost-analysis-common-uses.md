---
title: Běžná analýza nákladů používá v Azure Cost Management
description: Tento článek vysvětluje, jak můžete získat výsledky pro úlohy běžné analýzy nákladů v Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: ''
ms.openlocfilehash: 756e2f275a92a31a99604aaf7ef880bfdce8149b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988510"
---
# <a name="common-cost-analysis-uses"></a>Běžné analýzy nákladů používá

Azure Cost Management uživatelé často chtějí zodpovědět otázky, na které se zeptat mnoho dalších. Tento článek vás provede procesem získání výsledků pro úlohy běžné analýzy nákladů v Cost Management.

## <a name="view-cost-breakdown-by-azure-service"></a>Zobrazit rozpis nákladů podle služby Azure

Zobrazení nákladů pomocí služby Azure vám pomůže lépe porozumět částem vaší infrastruktury, která je pro vás nejdůležitější. Například náklady na výpočetní prostředky virtuálních počítačů můžou být malé. Z důvodu množství informací, které se z virtuálních počítačů generují, se zatím můžou výrazně snížit náklady na síť. Princip primárních nákladových ovladačů služeb Azure je nezbytný, takže můžete podle potřeby upravit využití služeb.

1. V Azure Portal přejděte k analýze nákladů pro váš obor. Například: **cost management + > fakturace** **cost management** > **analýzy nákladů**.
1. Vyberte **náklady podle služby** a pak seskupte podle **úrovně služeb**.
1. Změňte zobrazení na **tabulka**.

![Rozpis nákladů podle služby Azure](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Zobrazení rozpisu nákladů podle prostředku Azure

Vaše služby jsou sestavené s prostředky Azure. Kontroly nákladů na základě prostředků vám pomůžou rychle identifikovat vaše primární přispěvatele nákladů. Pokud služba obsahuje prostředky, které jsou moc nákladné, zvažte provedení změn, abyste snížili náklady.

1. V Azure Portal přejděte k analýze nákladů pro váš obor. Například: **cost management + > fakturace** **cost management** > **analýzy nákladů**.
1. Vyberte **náklady podle prostředku**.
1. Změňte zobrazení na **tabulka**.

![Zobrazení rozpisu nákladů podle prostředku Azure](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Zobrazit rozdělení nákladů podle vybraných dimenzí

Dimenze umožňují organizovat náklady na základě různých hodnot metadat zobrazených v rámci poplatků. Můžete například seskupit náklady podle umístění.

1. V Azure Portal přejděte k analýze nákladů pro váš obor. Například: **cost management + > fakturace** **cost management** > **analýzy nákladů**.
1. Vyberte filtr **Seskupit podle** .  
    ![vybrat položku Seskupit podle](./media/cost-analysis-common-uses/group-by.png)
1. Volitelně můžete zobrazení uložit pro pozdější použití.
1. Kliknutím na výsečový graf pod grafem zobrazíte podrobnější data.  
    ![Zobrazit rozdělení nákladů podle vybraných dimenzí](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>Zobrazit náklady za den nebo po měsících

Každodenní a měsíční náklady vám pomůžou lépe pochopit, jestli máte čas v týdnu nebo roce, kde jsou vaše náklady vyšší. Pokud máte v době volno více zákaznických přenosů, povede to k odpovídajícímu nárůstu nákladů na Azure? Má pátek více dražších dní než pondělí?

1. V Azure Portal přejděte k analýze nákladů pro váš obor. Například: **cost management + > fakturace** **cost management** > **analýzy nákladů**.
1. Nastavte **členitost** na **měsíční** nebo **denní**.

![Zobrazit náklady za den](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>Zobrazit náklady na konkrétní značku

Mnoho uživatelů Azure používá značky pro své prostředky, jako je například nákladové středisko nebo vývojové prostředí (výroba a testování), aby lépe kategorizoval poplatky. Značky se zobrazí jako dimenze při analýze nákladů. Dimenzi můžete použít k získání přehledu o vlastních kategorizacích značek.

Podpora značek se vztahuje na použití hlášené *po* použití značky u prostředku. Značky se nepoužijí zpětně pro kumulativní náklady.

1. V Azure Portal přejděte k analýze nákladů pro váš obor. Například: **cost management + > fakturace** **cost management** > **analýzy nákladů**.
1. Pro značku vyberte **Group by** .

![Zobrazit náklady na konkrétní značku](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>Stažení podrobností o použití

Soubor sestavy s podrobnostmi o využití ve formátu CSV poskytuje rozpis všech poplatků, které se účtují vůči faktuře. Sestavu můžete použít k porovnání a k lepšímu pochopení vaší faktury. Každý účtovaný poplatek na faktuře odpovídá za rozdělené poplatky v sestavě využití.

1. V Azure Portal přejděte na kartu **využití a poplatky** pro fakturační účet nebo předplatné. Například: **cost management + fakturace** ** > fakturace** > **využití a poplatky**.
1. Vyberte položku řádku, ze které se má stáhnout, a pak klikněte na symbol pro stažení.  
    ![Stažení využití a poplatků](./media/cost-analysis-common-uses/download1.png)
1.  Vyberte soubor využití, který se má stáhnout.  
    ![Zvolit soubor využití ke stažení](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Zobrazit měsíční rozpis nákladů na EA

Vaše registrace EA narůstá náklady na celou organizaci. Porozumět tomu, jak se účtují a účtují náklady v průběhu času, vám pomůže zapojit příslušné zúčastněné strany, aby se zajistilo, že náklady budou spravované zodpovědnou.

1. V Azure Portal přejděte na **přehled** > **cost management + fakturace** .
1. Klikněte na **rozdělení** pro aktuální měsíc a podívejte se na vypalování peněžních závazků.  
    Přehled nákladů na ![EA – souhrn rozpisu](./media/cost-analysis-common-uses/breakdown1.png)
1.  Klikněte na kartu **využití a poplatky** a zobrazte rozpis předchozího měsíce ve zvoleném časovém rámci.  
    karta využití ![a poplatky](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>Zobrazit měsíční náklady na registraci podle termínu

Pomocí grafického zobrazení měsíčních nákladů na registraci můžete pochopit trendy nákladů a fakturované částky za dané období.

1. V Azure Portal přejděte k analýze nákladů pro váš obor. Například: **cost management + > fakturace** **cost management** > **analýzy nákladů**.
1. Vyberte registraci a nastavte termín registrace.
1. Nastavte členitost na měsíčně a pak nastavte zobrazení na **sloupec (skládané)** .

Můžete seskupit podle a filtrovat data pro podrobnější analýzu.

![Měsíční náklady na registraci podle termínu](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>Zobrazit akumulované náklady registrace EA

Zobrazení čistých kumulovaných poplatků v průběhu času pro pochopení celkových výdajů vaší organizace po určitou dobu.

1. V Azure Portal přejděte k analýze nákladů pro váš obor. Například: **cost management + > fakturace** **cost management** > **analýzy nákladů**.
1. Vyberte si registraci a pak zobrazte aktuální akumulované náklady.

![Akumulované náklady registrace](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>Další kroky
- Pokud jste ještě nedokončili první tohoto rychlého startu Cost Management, přečtěte si ho na [začít analýza nákladů](quick-acm-cost-analysis.md).
- Přečtěte si [dokumentaci cost management](../index.yml).

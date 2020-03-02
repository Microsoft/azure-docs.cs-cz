---
title: Běžné způsoby analýzy nákladů ve službě Azure Cost Management
description: Tento článek vysvětluje, jak získat potřebné výsledky prostřednictvím běžných způsobů analýzy nákladů ve službě Azure Cost Management.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/21/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 1d9a498185e76f9771ec98656b1cad914b0f3214
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562577"
---
# <a name="common-cost-analysis-uses"></a>Běžné způsoby analýzy nákladů

Uživatelé služby Azure Cost Management hledají často odpověď na otázky, které zajímají i mnoho dalších osob. Tento článek vás provede hledáním výsledků prostřednictvím běžných způsobů analýzy nákladů ve službě Cost Management.

## <a name="view-cost-breakdown-by-azure-service"></a>Zobrazení rozpisu nákladů podle služeb Azure

Tím, že si zobrazíte náklady podle služeb Azure, budete schopni lépe porozumět, které části vaší infrastruktury vás stojí nejvíce. Například náklady na výpočetní výkon zajišťovaný virtuálními počítači můžou být nízké. Ale množství informací, které virtuální počítače generují, může vést k významnému nárůstu nákladů na síťové zajištění. Pochopení primárních generátorů nákladů mezi vašimi službami Azure má zásadní význam pro následné doladění způsobu jejich využívání.

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.
1. Vyberte **Náklady podle služeb** a pak je seskupte volbou **Úroveň služby**.
1. Zobrazení změňte na možnost **Tabulka**.

![Rozpis nákladů podle služeb Azure](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Zobrazení rozpisu nákladů podle prostředků Azure

Vámi používané služby se skládají z prostředků Azure. Zobrazení nákladů podle prostředků vám umožní rychle identifikovat jejich primární zdroje. Pokud služba obsahuje prostředky, které jsou moc drahé, zvažte změny, kterými náklady snížíte.

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.
1. Vyberte **Náklady podle prostředků**.
1. Zobrazení změňte na možnost **Tabulka**.

![Zobrazení rozpisu nákladů podle prostředků Azure](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Zobrazení rozpisu nákladů podle vybraných dimenzí

Díky dimenzím můžete náklady uspořádat na základě hodnot různých metadat uvedených v rámci poplatků. Náklady můžete například seskupit podle míst.

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.
1. Vyberte filtr **Seskupit podle**.  
    ![Výběr položky Seskupit podle](./media/cost-analysis-common-uses/group-by.png)
1. Zobrazení si také můžete uložit pro pozdější použití.
1. Kliknutím na výsečový graf pod hlavním grafem si zobrazíte podrobnější data.  
    ![Zobrazení rozpisu nákladů podle vybraných dimenzí](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>Zobrazení nákladů po dnech nebo měsících

Díky zobrazení denních nebo měsíčních nákladů můžete snáze odhalit období v rámci týdne nebo roku, kdy jsou náklady vyšší. Pokud komunikace se zákazníky narůstá v období před svátky, vede to k odpovídajícímu nárůstu nákladů na Azure? Bývají pátky nákladnější než pondělky?

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.
1. **Úroveň podrobností** nastavte na **Měsíční** nebo **Denní**.

![Zobrazení nákladů po dnech](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>Zobrazení nákladů pro konkrétní značku

Mnoho uživatelů Azure používá u svých prostředků, což můžou být třeba nákladová střediska nebo vývojová prostředí (produkce a testování), značky, které jim usnadňují kategorizaci poplatků. Značky se při analýze nákladů zobrazují jako dimenze. Díky těmto dimenzím můžete získat potřebný přehled na základě vlastní kategorizace značek.

Údaje vázané na značky zahrnují data o využití nahlášená až *po* nastavení značky u prostředku. Značky se nedají použít pro retroaktivní shrnutí.

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.
1. Vyberte **Seskupit podle** a konkrétní značku.

![Zobrazení nákladů pro konkrétní značku](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>Stažení podrobností o využití

Soubor ve formátu CSV s podrobnou sestavou využití obsahuje rozpis všech poplatků vyúčtovaných na faktuře. Sestavu můžete porovnat s fakturou, abyste jí lépe porozuměli. Jednotlivé poplatky vyúčtované na faktuře odpovídají poplatkům rozepsaným v sestavě využití.

1. Na webu Azure Portal přejděte na kartu **Využití a poplatky** pro konkrétní fakturační účet nebo předplatné. Příklad: **Správa nákladů a fakturace** > **Fakturace** > **Využití a poplatky**.
1. Vyberte řádek, pro který si chcete stáhnout data, a pak klikněte na symbol pro stažení.  
    ![Stažení dat o využití a poplatcích](./media/cost-analysis-common-uses/download1.png)
1.  Vyberte soubor s údaji o využití, který se má stáhnout.  
    ![Volba souboru o využití ke stažení](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Zobrazení měsíčního rozpisu nákladů pro smlouvu Enterprise (EA)

U vaší registrace EA se načítají náklady za celou vaši organizaci. Když pochopíte, jak náklady průběžně nabíhají a jak se účtují, snáze zapojíte ostatní zainteresované osoby do úsilí o odpovědné řízení nákladů.

Náklady se zobrazují jenom pro aktivní registraci. Pokud jste převedli registraci (neaktivní) na novou (aktivní), v Cost Managementu se nezobrazují náklady na předchozí registraci.


1. Na webu Azure Portal přejděte na **Správa nákladů a fakturace** > **Přehled**.
1. Klikněte na **Rozpis** pro aktuální měsíc a podívejte se na čerpání peněžního závazku.  
    ![Přehled nákladů na EA – souhrn rozpisu](./media/cost-analysis-common-uses/breakdown1.png)
1.  Klikněte na kartu **Využití a náklady** a podívejte se na rozpis předchozího měsíce ve zvoleném časovém rozsahu.  
    ![Karta Využití a náklady](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>Zobrazení měsíčních nákladů na registraci podle období

Díky grafickému zobrazení měsíčních nákladů u vaší registrace můžete pochopit trendy nákladů a fakturované částky pro konkrétní období.

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.
1. Vyberte registraci a nastavte období registrace.
1. Úroveň podrobností nastavte na měsíční a pak jako zobrazení zvolte **Sloupcový (skládaný)** .

Data můžete dál seskupovat a filtrovat, abyste získali ještě podrobnější analýzu.

![Měsíční náklady u registrace podle období](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>Zobrazení kumulovaných nákladů u registrace EA

Pokud budete sledovat čisté kumulované poplatky v průběhu času, snáze pochopíte celkové výdaje vaší organizace v určitém období.

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.
1. Vyberte svou registraci a podívejte se na aktuální kumulované náklady.

![Kumulované náklady u registrace](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>Další kroky
- Pokud jste si ještě neprošli úvodní příručku pro Cost Management, najdete ji v tématu [Začínáme s analýzou nákladů](quick-acm-cost-analysis.md).
- Projděte si [dokumentaci pro Cost Management](../index.yml).

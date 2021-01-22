---
title: Běžné způsoby analýzy nákladů ve službě Azure Cost Management
description: Tento článek vysvětluje, jak získat potřebné výsledky prostřednictvím běžných způsobů analýzy nákladů ve službě Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 72b371fd1184c9c9e69593c5ad7e57310abf1de2
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600030"
---
# <a name="common-cost-analysis-uses"></a>Běžné způsoby analýzy nákladů

Uživatelé služby Azure Cost Management hledají často odpověď na otázky, které zajímají i mnoho dalších osob. Tento článek vás provede hledáním výsledků prostřednictvím běžných způsobů analýzy nákladů ve službě Cost Management.

## <a name="view-forecasted-costs"></a>Zobrazení předpokládaných nákladů

Předpokládané náklady se zobrazují v oblastech analýzy nákladů pro zobrazení plošných a skládaných sloupcových grafů. Prognóza vychází z historického využití prostředků. Na předpokládané náklady mají vliv změny využití prostředků.

Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.

Horní graf ve výchozím zobrazení obsahuje oddíly se skutečnými/amortizovanými náklady a s předpokládanými náklady. Plnou barvou jsou v grafu zobrazené vaše skutečné/amortizované náklady. Stínovaná barva znázorňuje předpokládané náklady.

[![Předpokládané náklady](./media/cost-analysis-common-uses/enrollment-forecast.png)](./media/cost-analysis-common-uses/enrollment-forecast.png#lightbox)

## <a name="view-forecasted-costs-grouped-by-service"></a>Zobrazení předpokládaných nákladů seskupených podle služeb

Výchozí zobrazení nezobrazuje předpokládané náklady podle služeb, takže je nutné přidat seskupení.

Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.

Vyberte **Seskupit podle** > **Název služby**.

Zobrazení ukazuje náklady seskupené pro jednotlivé služby. Předpokládané náklady nejsou vypočítané pro každou službu. Jsou rozplánované pro všechny vaše služby jako **celek**.

[![Seskupení předpokládaných nákladů](./media/cost-analysis-common-uses/forecast-group-by-service.png)](./media/cost-analysis-common-uses/forecast-group-by-service.png#lightbox)

## <a name="view-forecasted-costs-for-a-service"></a>Zobrazení předpokládaných nákladů pro službu

Předpokládané náklady je možné zúžit na jednu službu. Můžete si například chtít zobrazit předpokládané náklady jenom pro virtuální počítače.

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.
1. Vyberte **Přidat filtr** a potom vyberte **Název služby**.
1. V seznamu pro **výběr** zvolte službu. Můžete například vybrat **virtuální počítače**.

Projděte si skutečné a předpokládané náklady pro vybranou službu.

Zobrazení můžete dál upravovat.

1. Přidejte druhý filtr pro **měřič** a vyberte hodnotu pro filtrování konkrétního typu měřiče pod vybraným názvem služby.
1. Seskupení podle **prostředků** vám umožní zobrazit konkrétní prostředky, u kterých se účtují náklady. Předpokládané náklady nejsou vypočítané pro každou službu. Jsou rozplánované pro všechny vaše prostředky jako **celek**.

[![Předpokládané náklady pro službu](./media/cost-analysis-common-uses/forecast-by-service.png)](./media/cost-analysis-common-uses/forecast-by-service.png#lightbox)

## <a name="view-your-azure-and-aws-costs-together"></a>Zobrazení nákladů na Azure a AWS najednou  

K zobrazení nákladů na Azure a AWS najednou můžete využít rozsahy skupin pro správu v Azure.

1. Vytvořte skupinu pro správu nebo vyberte existující.
1. Do skupiny pro správu přiřaďte požadovaná předplatná Azure.
1. *Tutéž* skupinu pro správu přiřaďte k propojenému účtu konektoru.
1. Přejděte do části Analýza nákladů a vyberte **Kumulované náklady**.
1. Vyberte **Seskupit podle** - **Poskytovatel**.

## <a name="view-cost-breakdown-by-azure-service"></a>Zobrazení rozpisu nákladů podle služeb Azure

Tím, že si zobrazíte náklady podle služeb Azure, budete schopni lépe porozumět, které části vaší infrastruktury vás stojí nejvíce. Například náklady na výpočetní výkon zajišťovaný virtuálními počítači můžou být nízké. Ale množství informací, které virtuální počítače generují, může vést k významnému nárůstu nákladů na síťové zajištění. Pochopení primárních generátorů nákladů mezi vašimi službami Azure má zásadní význam pro následné doladění způsobu jejich využívání.

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.
1. Vyberte **Náklady podle služeb** a pak je seskupte volbou **Úroveň služby**.
1. Zobrazení změňte na možnost **Tabulka**.

[![Rozpis nákladů podle služeb Azure](./media/cost-analysis-common-uses/breakdown-by-service.png)](./media/cost-analysis-common-uses/breakdown-by-service.png#lightbox)

## <a name="review-invoiced-charges-in-cost-analysis"></a>Kontrola fakturovaných poplatků při analýze nákladů

Pokud si chcete prohlédnout podrobnosti o vaší faktuře na webu Azure Portal, přejděte k analýze nákladů pro rozsah přidružený k faktuře, kterou analyzujete. Vyberte **Podrobnosti o faktuře**. V podrobnostech o faktuře jsou uvedené stejné poplatky jako na faktuře.

[![Příklad znázorňující výběr možnosti Podrobnosti o faktuře](./media/cost-analysis-common-uses/invoice-details.png)](./media/cost-analysis-common-uses/invoice-details.png#lightbox)

Při prohlížení podrobností o faktuře můžete určit službu, která má neočekávané náklady, a zjistit, které prostředky jsou přímo přidružené k prostředku v analýze nákladů. Pokud například chcete analyzovat poplatky za službu Virtual Machines, přejděte k zobrazení **Kumulované náklady**. Potom nastavte úroveň podrobností na **denní**, vyfiltrujte poplatky **Název služby: Virtual Machines** a seskupte je podle **prostředku**.

[![Příklad znázorňující kumulované náklady pro virtuální počítače](./media/cost-analysis-common-uses/virtual-machines.png)](./media/cost-analysis-common-uses/virtual-machines.png#lightbox)

## <a name="view-cost-breakdown-by-azure-resource"></a>Zobrazení rozpisu nákladů podle prostředků Azure

Vámi používané služby se skládají z prostředků Azure. Zobrazení nákladů podle prostředků vám umožní rychle identifikovat jejich primární zdroje. Pokud služba obsahuje prostředky, které jsou moc drahé, zvažte změny, kterými náklady snížíte.

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.
1. Vyberte **Náklady podle prostředků**.
1. Zobrazení změňte na možnost **Tabulka**.

[![Zobrazení rozpisu nákladů podle prostředků Azure](./media/cost-analysis-common-uses/cost-by-resource.png)](./media/cost-analysis-common-uses/cost-by-resource.png#lightbox)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Zobrazení rozpisu nákladů podle vybraných dimenzí

Díky dimenzím můžete náklady uspořádat na základě hodnot různých metadat uvedených v rámci poplatků. Náklady můžete například seskupit podle míst.

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.
1. Vyberte filtr **Seskupit podle**.  
    [![Výběr položky Seskupit podle](./media/cost-analysis-common-uses/group-by.png)](./media/cost-analysis-common-uses/group-by.png#lightbox)
1. Zobrazení si také můžete uložit pro pozdější použití.
1. Kliknutím na výsečový graf pod hlavním grafem si zobrazíte podrobnější data.  
    [![Zobrazení rozpisu nákladů podle vybraných dimenzí](./media/cost-analysis-common-uses/drill-down.png)](./media/cost-analysis-common-uses/drill-down.png#lightbox)

## <a name="view-costs-per-day-or-by-month"></a>Zobrazení nákladů po dnech nebo měsících

Díky zobrazení denních nebo měsíčních nákladů můžete snáze odhalit období v rámci týdne nebo roku, kdy jsou náklady vyšší. Pokud komunikace se zákazníky narůstá v období před svátky, vede to k odpovídajícímu nárůstu nákladů na Azure? Bývají pátky nákladnější než pondělky?

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.
1. **Úroveň podrobností** nastavte na **Měsíční** nebo **Denní**.

[![Zobrazení nákladů po dnech](./media/cost-analysis-common-uses/daily-granularity.png)](./media/cost-analysis-common-uses/daily-granularity.png#lightbox)


## <a name="view-your-spot-vm-charges"></a>Zobrazení poplatků za spotové virtuální počítače

Spotové virtuální počítače můžou zajistit značné úspory nákladů na úlohy, kterým nevadí přerušování. Úlohy se spouštějí s využitím nevyužité kapacity Azure. Vzhledem k tomu, že se spotové virtuální počítače můžou kdykoli vyřadit, platí pro ně výrazná sleva. Pokud chcete zobrazit poplatky za spotové virtuální počítače, postupujte následovně.

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Například **Cost Management a fakturace** > **Správa náklad**  > **Analýza nákladů**.
2. Přidejte filtr pro **Cenový model: Spotový**.

![Příklad ukazující filtr spotových virtuálních počítačů](./media/cost-analysis-common-uses/spot-vm-filter.png)

Dimenze Cenový model slouží také k zobrazení poplatků za vyžádání a rezervace.

## <a name="view-your-reservation-charges"></a>Zobrazení poplatků za rezervace

Rezervované instance představují způsob, jak ušetřit v Azure. S rezervacemi platíte předem za daný počet prostředků v průběhu času. Analýza nákladů zobrazuje poplatky tak, jak se zobrazují na faktuře. Poplatky se zobrazují jako skutečné náklady nebo amortizované náklady v průběhu období rezervace.

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Například **Cost Management a fakturace** > **Správa náklad**  > **Analýza nákladů**.
1. Přidejte filtr pro **Cenový model: Rezervace**.
1. V části **Rozsah** vedle zobrazených nákladů klikněte na symbol šipky dolů a vyberte metriku **Skutečné náklady** nebo **Amortizované náklady**.

![Výběr metriky nákladů](./media/cost-analysis-common-uses/metric-cost.png)

Každá metrika má vliv na zobrazení dat pro poplatky za rezervace.

**Skutečné náklady** –⁠ Ukazuje nákup tak, jak se zobrazuje na faktuře. Pokud jste například v lednu koupili roční rezervaci za 1 200 USD, v analýze nákladů se v měsíci lednu zobrazí náklady na rezervaci ve výši 1 200 USD. Za další měsíce v daném roce se náklady na rezervaci nezobrazí. Pokud seskupíte skutečné náklady podle virtuálního počítače, u virtuálního počítače, který pro určitý měsíc obdržel výhodu rezervace se za daný měsíc zobrazí nulové náklady.

**Amortizované náklady** –⁠ Ukazuje nákup rezervace rozdělený na amortizované náklady v průběhu období rezervace. Při použití stejného příkladu jako výše se v analýze nákladů zobrazí náklady ve výši 100 USD za každý měsíc v roce, pokud jste v lednu zakoupili roční rezervaci za 1 200 USD. Pokud v tomto příkladu seskupíte náklady podle virtuálního počítače, zobrazí se náklady přiřazené k jednotlivým virtuálním počítačům, které obdržely výhodu rezervace.

## <a name="view-your-reservation-utilization"></a>Zobrazení využití rezervace

Po zakoupení rezervace je důležité sledovat její využití, abyste dostávali to, za co jste zaplatili. Pokud jste například na rok zakoupili 10 virtuálních počítačů a využíváte jich pouze pět, polovina nákupu v podstatě přijde nazmar. Využití můžete vyhodnotit dvěma různými způsoby:

### <a name="view-unused-ri-costs-in-cost-analysis"></a>Zobrazení nákladů na nevyužité rezervované instance v analýze nákladů

Pokud chcete zjistit, jak velká část nákladů na nákup vaší rezervace se aktuálně každý měsíc plýtvá, postupujte následovně.

1. Na webu Azure Portal přejděte na analýzu nákladů pro rozsah vaší rezervace. Například **Cost Management a fakturace** > **Správa náklad**  > **Analýza nákladů**.
1. Přidejte filtr pro **Cenový model: Rezervace**.
1. Vyberte zobrazení **Amortizované náklady**.
1. Nastavte úroveň podrobností na **Měsíční**.
1. Nastavte časové období na aktuální rok nebo období vaší rezervace.
1. Nastavte typ grafu na **Sloupcový (skládaný)** .
1. Seskupte poplatky podle **Typu poplatku**.
1. Ve výsledcích zkontrolujte hodnoty `unusedreservation`.

[![Příklad ukazující využití rezervace](./media/cost-analysis-common-uses/view-reservation-cost.png)](./media/cost-analysis-common-uses/view-reservation-cost.png#lightbox)

### <a name="view-utilization-in-reservations"></a>Zobrazení využití v rámci rezervací

Podrobné pokyny najdete v tématu [Optimalizace využití rezervace](../reservations/manage-reserved-vm-instance.md#optimize-reservation-use).

## <a name="view-costs-for-a-specific-tag"></a>Zobrazení nákladů pro konkrétní značku

Mnoho uživatelů Azure používá u svých prostředků, což můžou být třeba nákladová střediska nebo vývojová prostředí (produkce a testování), značky, které jim usnadňují kategorizaci poplatků. Značky se při analýze nákladů zobrazují jako dimenze. Díky těmto dimenzím můžete získat potřebný přehled na základě vlastní kategorizace značek.

Údaje vázané na značky zahrnují data o využití nahlášená až *po* nastavení značky u prostředku. Značky se nedají použít pro retroaktivní shrnutí.

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.
1. Vyberte **Seskupit podle** a konkrétní značku.

[![Zobrazení nákladů pro konkrétní značku](./media/cost-analysis-common-uses/tag.png)](./media/cost-analysis-common-uses/tag.png#lightbox)

## <a name="download-your-usage-details"></a>Stažení podrobností o využití

Soubor ve formátu CSV s podrobnou sestavou využití obsahuje rozpis všech poplatků vyúčtovaných na faktuře. Sestavu můžete porovnat s fakturou, abyste jí lépe porozuměli. Jednotlivé poplatky vyúčtované na faktuře odpovídají poplatkům rozepsaným v sestavě využití.

1. Na webu Azure Portal přejděte na kartu **Využití a poplatky** pro konkrétní fakturační účet nebo předplatné. Příklad: **Správa nákladů a fakturace** > **Fakturace** > **Využití a poplatky**.
1. Vyberte řádek, pro který si chcete stáhnout data, a pak klikněte na symbol pro stažení.  
    [![Stažení dat o využití a poplatcích](./media/cost-analysis-common-uses/download1.png)](./media/cost-analysis-common-uses/download1.png#lightbox)
1.  Vyberte soubor s údaji o využití, který se má stáhnout.  
    ![Volba souboru o využití ke stažení](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Zobrazení měsíčního rozpisu nákladů pro smlouvu Enterprise (EA)

U vaší registrace EA se načítají náklady za celou vaši organizaci. Když pochopíte, jak náklady průběžně nabíhají a jak se účtují, snáze zapojíte ostatní zainteresované osoby do úsilí o odpovědné řízení nákladů.

Náklady se zobrazují jenom pro aktivní registraci. Pokud jste převedli registraci (neaktivní) na novou (aktivní), v Cost Managementu se nezobrazují náklady na předchozí registraci.


1. Na webu Azure Portal přejděte na **Správa nákladů a fakturace** > **Přehled**.
1. Klikněte na **Rozpis** pro aktuální měsíc a podívejte se na čerpání Azure Prepaymentu (dřív označovaný jako peněžní závazek).  
    [![Přehled nákladů na EA – souhrn rozpisu](./media/cost-analysis-common-uses/breakdown1.png)](./media/cost-analysis-common-uses/breakdown1.png#lightbox)
1.  Klikněte na kartu **Využití a náklady** a podívejte se na rozpis předchozího měsíce ve zvoleném časovém rozsahu.  
    [![Karta Využití a náklady](./media/cost-analysis-common-uses/breakdown2.png)](./media/cost-analysis-common-uses/breakdown2.png#lightbox)

## <a name="view-enrollment-monthly-cost-by-term"></a>Zobrazení měsíčních nákladů na registraci podle období

Díky grafickému zobrazení měsíčních nákladů na vaši registraci můžete pochopit trendy nákladů a fakturované částky pro konkrétní období.

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.
1. Vyberte registraci a nastavte období registrace.
1. Úroveň podrobností nastavte na měsíční a pak jako zobrazení zvolte **Sloupcový (skládaný)** .

Data můžete dál seskupovat a filtrovat, abyste získali ještě podrobnější analýzu.

[![Měsíční náklady na registraci podle období](./media/cost-analysis-common-uses/enrollment-term1.png)](./media/cost-analysis-common-uses/enrollment-term1.png#lightbox)

## <a name="view-ea-enrollment-accumulated-costs"></a>Zobrazení kumulovaných nákladů u registrace EA

Pokud budete sledovat čisté kumulované poplatky v průběhu času, snáze pochopíte celkové výdaje vaší organizace v určitém období.

1. Na webu Azure Portal přejděte na analýzu nákladů pro konkrétní rozsah. Příklad: **Správa nákladů a fakturace** > **Správa nákladů** > **Analýza nákladů**.
1. Vyberte svou registraci a podívejte se na aktuální kumulované náklady.

[![Kumulované náklady na registraci](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)](./media/cost-analysis-common-uses/cost-analysis-enrollment.png#lightbox)

## <a name="next-steps"></a>Další kroky
- Pokud jste si ještě neprošli úvodní příručku pro Cost Management, najdete ji v tématu [Začínáme s analýzou nákladů](quick-acm-cost-analysis.md).
- Projděte si [dokumentaci pro Cost Management](../index.yml).

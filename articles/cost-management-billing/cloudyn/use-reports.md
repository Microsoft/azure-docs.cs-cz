---
title: Použití sestav Cloudynu v Azure
description: Tento článek popisuje účel sestav Cloudynu, které jsou součástí portálu Cloudyn, a pomůže vám je efektivně používat.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 4bc68e851c4f3ba85f8ea18a91d86906e1f87887
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685217"
---
# <a name="reports-available-in-the-cloudyn-portal"></a>Sestavy dostupné na portálu Cloudyn

Tento článek popisuje účel sestav Cloudynu, které jsou součástí portálu Cloudyn. Popisuje rovněž způsob, jak tyto sestavy efektivně používat. Většina sestav je intuitivní a má jednotný vzhled a chování. Většinu akcí, které můžete dělat v jedné sestavě, můžete dělat také v jiných sestavách. Přehled o používání sestav Cloudynu včetně postupu jejich přizpůsobení, uložení a plánování, najdete v článku [Vysvětlení sestav nákladů](understanding-cost-reports.md).

Azure Cost Management nabízí podobné funkce jako Cloudyn. Azure Cost Management je nativní řešení Azure pro správu nákladů. Umožní vám analyzovat náklady, vytvářet a spravovat rozpočty, exportovat data, kontrolovat optimalizační doporučení k úspoře peněz a reagovat na ně. Další informace najdete v tématu [Azure Cost Management](../cost-management-billing-overview.md).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="report-types"></a>Typy sestav

Rozlišují se tři typy sestav Cloudynu:

- Sestavy v průběhu času: Příkladem je sestava nákladů v průběhu času. Sestavy v průběhu času zobrazují časovou řadu dat za vybraný interval s předdefinovaným rozlišením a zobrazují týdenní rozlišení za poslední dva měsíce. Pomocí seskupení a filtrování se můžete blíže podívat na různé datové body.
  - Sestavy v průběhu času vám pomáhají zobrazit trendy a odhalit špičky nebo anomálie.
- Sestavy analýzy: Příkladem je sestava analýzy nákladů. Tyto sestavy zobrazují agregovaná data za vámi definované období a umožňují seskupení a filtrování dat.
  - Sestavy analýzy vám pomáhají znázornit špičky, určit hlavní příčiny anomálie a zobrazit odstupňovaný rozpis dat.
- Tabulkové sestavy: Jako tabulku můžete zobrazit libovolnou sestavu, některé sestavy se ale zobrazují jen ve formě tabulky. Tyto sestavy poskytují podrobné seznamy položek.
  - Mezi tabulkové sestavy patří doporučení – pro doporučení neexistují žádné vizualizace. Můžete ale vizualizovat výsledky doporučení. Příkladem jsou úspory v průběhu času.
  - Tabulkové sestavy jsou užitečné jako seznamy akcí nebo pro export dat za účelem dalšího zpracování. Příkladem je sestava zpětného zúčtování.

V sestavách nákladů se zobrazují buď _skutečné_, nebo _amortizované_ náklady.

Sestavy skutečných nákladů zobrazují platby provedené během vybraného časového období. Například všechny jednorázové poplatky, jako jsou nákupy rezervovaných instancí (RI), se v sestavách skutečných nákladů zobrazují jako špičky v nákladech.

V sestavách amortizovaných nákladů jsou jednorázové poplatky rozloženy přes období, na které se vztahují. Například jednorázové poplatky za nákupy rezervovaných instancí jsou rozloženy přes období rezervace a nezobrazují se jako špička. Amortizované zobrazení je jediným způsobem, jak vidět skutečné trendy a provádět plánování nákladů.

V některých případech se amortizace prezentuje jako samostatná sestava. Příkladem jsou sestavy analýzy nákladů a analýzy amortizovaných nákladů. V jiných případech slouží amortizace jako zásada sestavy, například u sestav alokace nákladů a analýzy nákladů.

U jakékoli sestavy můžete naplánovat pravidelné doručování. U sestav nákladů lze nastavit prahovou hodnotu, takže jsou užitečné pro upozornění.

## <a name="cost-analysis-vs-cost-allocation"></a>Analýza nákladů versus alokace nákladů

Sestavy _analýzy nákladů_ zobrazují fakturační údaje od vašich poskytovatelů cloudu. Pomocí těchto sestav můžete seskupit a zobrazit podrobnosti různých segmentů dat položkově rozepsaných z fakturačního souboru. Tyto sestavy umožňují podrobné procházení nákladů v nezpracovaných fakturačních údajích dodavatelů cloudu.

V některých sestavách _analýzy nákladů_ nejsou náklady seskupeny podle značek prostředků. Fakturační údaje založené na značkách se v sestavách zobrazí až po alokaci nákladů vytvořením nákladového modelu pomocí [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

Sestavy _alokace nákladů_ jsou dostupné po vytvoření nákladového modelu pomocí [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs). Cloudyn zpracovává nákladové a fakturační údaje a _páruje_ je s údaji o využití a značkách vašich cloudových účtů. Kvůli tomuto párování vyžaduje Cloudyn přístup k datům o využití. Pokud máte účty, které nemají přihlašovací údaje, jsou označené jako _prostředky nezařazené do kategorie_.

## <a name="dashboards"></a>Řídicí panely

Řídicí panely v Cloudynu zobrazují sestavy z vyšší perspektivy. Řídicí panely se skládají z widgetů, přičemž každý widget je v podstatě miniatura sestavy. Když si [sestavy přizpůsobíte](understanding-cost-reports.md#save-and-schedule-reports), uloží se do oblasti My Reports (Moje sestavy) a přidají se na řídicí panel. Další informace o řídicích panelech najdete v článku [Zobrazení klíčových nákladových metrik pomocí řídicích panelů](dashboards.md).

## <a name="budget-information-in-reports"></a>Rozpočtové informace v sestavách

Po ručním vytvoření rozpočtu se ve spoustě sestav Cloudynu zobrazují rozpočtové informace. Rozpočtové informace se tedy v sestavách nezobrazí, dokud nevytvoříte rozpočet. Další informace najdete v článku [Nastavení správy rozpočtu](#budget-management-settings).

## <a name="reports-and-reporting-features"></a>Sestavy a vykazovací funkce

Cloudyn obsahuje následující sestavy a vykazovací funkce.

### <a name="cost-navigator-report"></a>Sestava nákladového navigátoru

Sestava nákladového navigátoru představuje rychlý způsob, jak zobrazit fakturované využití pomocí zobrazení řídicího panelu. Obsahuje podmnožinu filtrů a základních zobrazení, s jejichž pomocí okamžitě vidíte souhrnné zobrazení nákladů organizace. Náklady se zobrazují podle data. Protože účelem této sestavy je prvotní zobrazení nákladů, není tak flexibilní ani obsáhlá jako řada jiných sestav nebo vlastních řídicích panelů, které si sami vytvoříte.

Hlavní zobrazení této sestavy standardně obsahují:

- Náklady v průběhu času znázorněné v pruhovém grafu pracovních týdnů. Pomocí **rozsahu kalendářních dat** můžete změnit období pruhového grafu.
- Výdaje podle služby ve výsečovém grafu
- Kategorizace prostředků podle značek ve výsečovém grafu
- Výdaje podle nákladových entit ve výsečovém grafu
- Celkové náklady podle data v zobrazení seznamu

### <a name="cost-analysis-report"></a>Sestava analýzy nákladů

Sestava analýzy nákladů je výpočet zpětného zúčtování a zpětné kalkulace na základě vašich zásad. Agreguje využití cloudu během vybraného časového období poté, co jsou na náklady aplikována všechna alokační pravidla. Vypočítá například náklady podle značek, změní přiřazení nákladů na neoznačené prostředky a volitelně alokuje využití rezervovaných instancí.

V sestavě analýzy nákladů se používají zásady nastavené v [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs), přičemž výsledky se následně zkombinují s informacemi z nezpracovaných dat dodavatele cloudu.

Jak se tato sestava vypočítá? Služba Cloudyn zajišťuje zachování integrity alokace jednotlivých propojených účtů pomocí _spřažení účtů_. Spřažení zajišťuje, že na účet, který nepoužívá konkrétní službu, nejsou alokovány žádné náklady na tuto službu. Náklady naběhlé na tomto účtu zůstávají na tomto účtu a nevypočítávají se zásadami alokace. Můžete mít například pět propojených účtů. Pokud jen tři z nich používají služby úložiště, alokují se náklady na tyto služby úložiště jen do značek v těchto třech účtech.

Sestava analýzy nákladů umožňuje:

- Vypočítat zpětné zúčtování / zpětnou kalkulaci v organizaci
- Kategorizovat všechny náklady
- Znázornit agregované zobrazení celého nasazení za určité časové období
- Zobrazit náklady podle kategorií značek na základě zásad vytvořených v nákladovém modelu

Sestava analýzy nákladů se používá takto:

1. Vyberte rozsah kalendářních dat.
2. Podle potřeby přidejte značky.
3. Přidejte skupiny.
4. Zvolte dříve vytvořený nákladový model.

### <a name="cost-over-time-report"></a>Sestava nákladů v průběhu času

Sestava nákladů v průběhu času zobrazuje výsledky alokace nákladů ve formě časové řady. Umožňuje pozorovat trendy a odhalovat nesrovnalosti v nasazení. Zobrazuje v podstatě náklady rozúčtované přes definované období. Tato sestava ukazuje hlavní přispěvatele nákladů včetně průběžných nákladů a jednorázových poplatků za rezervované instance, které se využívají ve vybraném časovém období. V této sestavě se používají zásady nastavené v [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

Sestava nákladů v průběhu času umožňuje:

- Vidět změny v průběhu času a co ovlivňuje změnu od jednoho dne (nebo rozsahu kalendářních dat) do dalšího
- Analyzovat náklady v průběhu času pro konkrétní instanci
- Zjistit, proč došlo ke zvýšení nákladů na konkrétní instanci

Sestava nákladů v průběhu času se používá takto:

1. Vyberte rozsah kalendářních dat.
2. Podle potřeby přidejte značky.
3. Přidejte skupiny.
4. Zvolte dříve vytvořený nákladový model.
5. Vyberte skutečné náklady nebo amortizované náklady.
6. Zvolte, jestli chcete aplikovat pravidla alokace a zobrazit nezpracované fakturační údaje nebo přepočítané náklady.

### <a name="actual-cost-analysis-report"></a>Sestava analýzy skutečných nákladů

Sestava analýzy skutečných nákladů zobrazuje náklady na poskytovatele bez jakýchkoli úprav. Ukazuje hlavní přispěvatele nákladů včetně průběžných nákladů a jednorázových poplatků.

Tuto sestavu můžete použít k zobrazení údajů o nákladech na svá předplatná. Předplatná Azure se v této sestavě zobrazují jako **název účtu** a **číslo účtu**. **Propojené účty** zobrazují předplatná AWS. Pokud chcete vidět náklady na předplatná v podobě rozpisu pro jednotlivé účty, vyberte typ předplatného v oblasti **Skupiny**.

Sestava analýzy skutečných nákladů umožňuje:

- Analyzovat a monitorovat nezpracované náklady na poskytovatele využité během zadaného časového období
- Naplánovat prahovou hodnotu upozornění
- Analyzovat naběhlé neupravené náklady podle účtů a entit

### <a name="actual-cost-over-time-report"></a>Sestava skutečných nákladů v průběhu času

Sestava skutečných nákladů v průběhu času je standardní sestava analýzy nákladů s náklady rozúčtovanými přes definované časové rozlišení. Tato sestava zobrazuje útratu v průběhu času a umožňuje pozorovat trendy a odhalit nesrovnalosti v útratě. Tato sestava ukazuje hlavní přispěvatele nákladů včetně průběžných nákladů a jednorázových poplatků za rezervované instance, které se využívají ve vybraném časovém období.

Sestava skutečných nákladů v průběhu času umožňuje:

- Zobrazit trendy nákladů v průběhu času
- Najít nesrovnalosti v nákladech
- Najít odpovědi na všechny otázky ohledně nákladů související s poskytovateli cloudu

### <a name="amortized-cost-reports"></a>Sestavy amortizovaných nákladů

Tato sada sestav amortizovaných nákladů ukazuje linearizované poplatky za služby nesouvisející s využitím, neboli jednorázově splatné náklady rovnoměrně rozložené přes dobu jejich životnosti. Mezi jednorázové poplatky například patří:

- Roční poplatky za podporu
- Roční poplatky za komponentu zabezpečení
- Poplatky za nákup rezervovaných instancí
- Některé položky z Azure Marketplace

Ve fakturačním souboru jsou jednorázové poplatky charakterizovány tím, že datum zahájení a ukončení využití služby (časové razítko) je stejné. Služba Cloudyn je pak rozpozná jako jednorázové náklady, které jsou amortizované. Jiné služby založené na využití s jednorázovými náklady amortizovány nejsou.

Mezi sestavy amortizovaných nákladů patří:

- Analýza amortizovaných nákladů
- Amortizované náklady v průběhu času

### <a name="cost-analysis-report"></a>Sestava analýzy nákladů

Sestava analýzy nákladů poskytuje přehled o využití cloudu a útratě během vybraného časového období. V sestavě analýzy nákladů se používají zásady nastavené v [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

Jak Cloudyn tuto sestavu počítá?

Cloudyn zajišťuje zachování integrity alokace jednotlivých propojených účtů pomocí _spřažení účtů_. Spřažení zajišťuje, že na účet, který nepoužívá konkrétní službu, také nejsou alokovány žádné náklady na tuto službu. Náklady naběhlé na tomto účtu zůstávají na tomto účtu a nevypočítávají se zásadami alokace. Můžete mít například pět propojených účtů. Pokud jen tři z nich používají služby úložiště, alokují se náklady na tyto služby úložiště jen do značek v těchto třech účtech.

Sestava analýzy nákladů umožňuje:

- Znázornit agregované zobrazení celého nasazení za určité časové období
- Zobrazit náklady podle kategorií značek na základě zásad vytvořených v nákladovém modelu

### <a name="cost-over-time-report"></a>Sestava nákladů v průběhu času

Sestava nákladů v průběhu času zobrazuje útratu v průběhu času, takže můžete sledovat trendy a odhalit nesrovnalosti v nasazení. Zobrazuje v podstatě náklady rozúčtované přes definované období. Tato sestava ukazuje hlavní přispěvatele nákladů včetně průběžných nákladů a jednorázových poplatků za rezervované instance, které se využívají ve vybraném časovém období. V této sestavě se používají zásady nastavené v [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

Sestava nákladů v průběhu času umožňuje:

- Vidět změny v průběhu času a co ovlivňuje změnu od jednoho dne (nebo rozsahu kalendářních dat) do dalšího
- Analyzovat náklady v průběhu času pro konkrétní instanci
- Zjistit, proč došlo ke zvýšení nákladů na konkrétní instanci

### <a name="custom-charges-report"></a>Sestava vlastních poplatků

Uživatelé z řad podniků a poskytovatelů cloudových řešení poskytují svým externím nebo interním zákazníkům kromě využití vlastních cloudových prostředků často také přidané služby. Pro přidané služby definujete vlastní poplatky nebo slevy, které se do sestav fakturace nebo zpětného zúčtování pro zákazníky přidají jako vlastní řádkové položky.

Vlastní poplatky za služby vyjadřují služby, které se na faktuře normálně nezobrazují. Vlastní poplatky, které vytvoříte, se pak zobrazují v sestavách nákladů.

*Vlastní poplatky nejsou vlastní ceny*. V seznamu vlastních poplatků se nezobrazují různé sazby, které můžete účtovat. Například fakturační poplatky za AWS se zobrazují tak, jak jsou účtovány.

Vlastní poplatek vytvoříte takto:

1. V oblasti **Custom Charges** (Vlastní poplatky) klikněte na **Add New** (Přidat nový). Zobrazí se dialogové okno _Add New Custom Charge_ (Přidat nový vlastní poplatek).
2. Do pole **Provider Name** (Název poskytovatele) zadejte název poskytovatele.
3. Do pole **Service Name** (Název služby) zadejte typ služby.
4. Do pole **Description** (Popis) přidejte popis vlastního poplatku.
5. V poli **Type** (Typ) zadejte vybrané **Percentage** (Procento) a v rozevíracím seznamu Services (Služby) vyberte služby, které mají být do sestav nákladů zahrnuty jako vlastní poplatky.
6. V poli **Payment** (Platba) vyberte, jestli se jedná o One-Time Fee (Jednorázový poplatek) nebo Recurring Fee (Opakovaný poplatek). U opakovaného poplatku zaškrtněte Amortized (Amortizováno), pokud se má poplatek amortizovat, a vyberte počet měsíců.
7. U jednorázového poplatku zadejte v oblasti **Dates** (Kalendářní data) do pole **Effective Date** (Datum účinnosti) datum, kdy je poplatek zaplacen. Pokud je vybrán opakovaný poplatek, zadejte rozsah kalendářních dat včetně počátečního a koncového data poplatku.
8. Ve **stromu entit** vyberte entity, u kterých chcete poplatek použít, a pak vyberte **On** (Zapnuto).

_Když jsou poplatky přiřazeny k entitě, nemohou je uživatelé změnit. Poplatky přidané správcem k nadřazené entitě jsou určené jen pro čtení._

Vlastní poplatky zobrazíte takto:

Vlastní poplatky se zobrazují v sestavách nákladů. Otevřete například sestavu analýzy skutečných nákladů a v oblasti **Extended Filters** (Rozšířené filtry) vyberte **Standalone** (Samostatný). Pomocí filtru pak zobrazte **Custom Charges** (Vlastní poplatky).

### <a name="cost-allocation-360"></a>Cost Allocation 360

Cost Allocation 360 umožňuje vytvořit vlastní modely alokace nákladů pro přiřazení nákladů k využívaným cloudovým prostředkům. Spousta sestav zobrazuje informace z vlastních nákladových modelů, které jste vytvořili pomocí vlastních nákladových modelů. Některé sestavy navíc zobrazují informace až potom, co vytvoříte vlastní nákladový model s alokací nákladů.

Další informace o vytváření vlastních nákladových modelů najdete v článku [Kurz: Správa nákladů pomocí Cloudynu](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>Sestava nákladů oproti rozpočtu v průběhu času

Sestava nákladů oproti rozpočtu v průběhu času umožňuje porovnat hlavní přispěvatele nákladů vůči rozpočtu. Přiřazený rozpočet se zobrazuje v sestavě, takže si můžete prohlédnout útratu rozpočtu (nad/pod/rovno) v průběhu času. Pomocí volby Show/Hide Fields (Zobrazit/skrýt pole) v horní části sestavy můžete vybrat, jestli se mají zobrazovat náklady, rozpočet, kumulované náklady a celkový rozpočet.

### <a name="current-month-projected-cost-report"></a>Sestava plánovaných nákladů pro aktuální měsíc

Sestava plánovaných nákladů pro aktuální měsíc nabízí přehled souhrnných nákladů od začátku aktuálního měsíce. Tato sestava zobrazuje náklady od začátku měsíce, od předchozího měsíce a celkové plánované náklady pro aktuální měsíc. Plánované náklady pro aktuální měsíc se vypočítají jako součet dosavadních měsíčních nákladů a plánu na základě nákladů monitorovaných za posledních 30 dnů.

Sestava plánovaných nákladů pro aktuální měsíc umožňuje:

- Plánovat měsíční náklady podle služby
- Plánovat měsíční náklady podle účtu

### <a name="annual-projected-cost-report"></a>Sestava ročních plánovaných nákladů

Sestava ročních plánovaných nákladů umožňuje zobrazit roční plánované náklady na základě předchozích trendů útraty. Zobrazuje příštích 12 měsíců celkových plánovaných nákladů. K plánování se používá funkce trendu extrapolovaná přes příštích 12 měsíců vycházející z nákladů přidružených k posledním 30 dnům používání.

### <a name="budget-management-settings"></a>Nastavení správy rozpočtu

Správa rozpočtu umožňuje nastavit rozpočet pro fiskální rok.

K entitě přidáte rozpočet takto:

1. Na stránce Budget Management (Správa rozpočtu) v oblasti **Entities** (Entity) vyberte entitu, ve které chcete vytvořit rozpočet.
2. V roce rozpočtu vyberte rok, ve kterém chcete vytvořit rozpočet.
3. Nastavte rozpočet v jednotlivých měsících a pak klikněte na **Save** (Uložit).

Soubor pro roční rozpočet naimportujete takto:

1. Výběrem možnosti **Export** (Exportovat) v oblasti **Actions** (Akce) stáhněte prázdnou šablonu CSV, kterou použijete jako základ pro rozpočet.
2. Vyplňte tento soubor CSV položkami rozpočtu a místně ho uložte.
3. V oblasti **Actions** (Akce) vyberte **Import** (Importovat).
4. Vyberte uložený soubor a klikněte na **OK**.

Pokud chcete vyplněný rozpočet exportovat jako soubor CSV, výběrem možnosti **Export** (Exportovat) v oblasti **Actions** (Akce) tento soubor stáhněte.

Po dokončení se rozpočet zobrazuje v sestavách analýzy nákladů a nákladů oproti rozpočtu v průběhu času. Sestavy můžete také plánovat na základě prahových hodnot rozpočtu.

### <a name="azure-resource-explorer-report"></a>Sestava Azure Resource Exploreru

Sestava Azure Resource Exploreru zobrazuje seznam všech prostředků Azure dostupných v Cloudynu. Kvůli efektivnímu používání této sestavy by u vašich účtů Azure měly být povolené rozšířené metriky. Rozšířené metriky poskytují Cloudynu přístup k virtuálním počítačům Azure. Další informace najdete v článku [Přidání rozšířených metrik pro virtuální počítače Azure](azure-vm-extended-metrics.md).

### <a name="azure-resources-over-time-report"></a>Sestava prostředků Azure v průběhu času

Sestava prostředků Azure v průběhu času zobrazuje rozpis všech prostředků spuštěných za konkrétní období. Kvůli efektivnímu používání této sestavy by u vašich účtů Azure měly být povolené rozšířené metriky. Rozšířené metriky poskytují Cloudynu přístup k virtuálním počítačům Azure. Další informace najdete v článku [Přidání rozšířených metrik pro virtuální počítače Azure](azure-vm-extended-metrics.md).

### <a name="instance-explorer-report"></a>Sestava průzkumníka instancí

Sestava průzkumníka instancí slouží k zobrazení různých metrik pro prostředky virtuálních počítačů. Při přechodu na podrobnosti konkrétních instancí můžete například zobrazit:
- Intervaly spuštění instance
- Životní cyklus ve vybraném období
- Využití procesoru
- Vstup sítě
- Výstupní provoz
- Aktivní disky

Sestava průzkumníka instancí shromažďuje všechny intervaly spuštění v definovaném rozsahu kalendářních dat a odpovídajícím způsobem agreguje data. Rozbalením instance zobrazíte všechny intervaly spuštění v daném rozsahu kalendářních dat. Náklady na jednotlivé instance se pro vybraný rozsah kalendářních dat počítají z ceníkových cen AWS a Azure. Nejsou uplatněny žádné slevy. Pomocí možnosti Show/Hide Fields (Zobrazit/skrýt pole) můžete do sestavy přidat další pole.

Sestava průzkumníka instancí umožňuje:

- Vypočítat odhadované náklady na počítač
- Vytvořit úplný seznam všech počítačů (včetně agregovaných hodin běhu), které byly aktivní v nějakém časovém rozsahu
- Vytvořit seznam podle poskytovatele cloudových služeb nebo účtu
- Zobrazit počítače vytvořené nebo ukončené v nějakém časovém rozsahu
- Zobrazit všechny aktuálně zastavené počítače
- Zobrazit značky jednotlivých počítačů

### <a name="instances-over-time-report"></a>Sestava instancí v průběhu času

Pomocí sestavy instancí v průběhu času můžete zobrazit maximální počet počítačů, které byly aktivní během vybraného časového rozsahu. Pokud je definováno rozlišení podle týdnů nebo měsíců, je výsledkem maximální počet počítačů aktivních v libovolném dni v průběhu tohoto měsíce. Výběrem rozsahu kalendářních dat vyberte filtry, které chcete v sestavě zobrazit.

### <a name="instance-utilization-over-time-report"></a>Sestava využití instancí v průběhu času

Tato sestava zobrazuje rozpis využití procesoru nebo paměti všech instancí v průběhu času.

### <a name="compute-power-cost-over-time-report"></a>Sestava nákladů na výpočetní výkon v průběhu času

Sestava výpočetního výkonu v průběhu času poskytuje rozpis výpočetního výkonu za zadaný rozsah kalendářních dat. Ačkoli jiné sestavy zobrazují počet spuštěných počítačů nebo hodiny běhu, tato sestava zobrazuje hodiny jádra, hodiny výpočetní jednotky nebo hodiny GB RAM.

Tato sestava umožňuje:

- Zkontrolovat výpočetní výkon v zadaném rozsahu kalendářních dat
- Zobrazit výpočetní časy na základě modelů alokace nákladů

Tato sestava je propojená se zásadami [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs), takže výsledky se zobrazují na základě značek a zásad definovaných ve vybraných zásadách nákladů. Pokud nemáte vytvořené zásady, výsledky se nezobrazí.

### <a name="compute-power-average-cost-over-time-report"></a>Sestava průměrných nákladů na výpočetní výkon v průběhu času

Sestava průměrných nákladů na výpočetní výkon v průběhu času zobrazuje více než pouhé náklady na jednotlivé spuštěné počítače. Tato sestava zobrazuje průměrné náklady na hodinu instance, hodinu jádra, hodinu výpočetní jednotky a hodinu GB RAM. Poskytuje tak přehled o efektivitě vašeho nasazení.

Tato sestava je propojená se zásadami [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs), takže výsledky se zobrazují na základě značek a zásad definovaných ve vybraných zásadách nákladů. Pokud nemáte vytvořené zásady, výsledky se nezobrazí.

### <a name="s3-cost-over-time-report"></a>Sestava nákladů na S3 v průběhu času

Sestava nákladů na S3 v průběhu času poskytuje rozpis nákladů na Amazon Simple Storage Service (S3) na kontejner v průběhu času za zadané časové období. Tato sestava vám pomůže najít kontejnery, které jsou hlavními nákladovými faktory, a zobrazuje trendy využití a útraty za S3.

### <a name="s3-distribution-of-cost-report"></a>Sestava rozúčtování nákladů na S3

Pomocí této sestavy můžete analyzovat náklady na S3 za poslední měsíc podle kontejneru a třídy úložiště. K nastavení prahové hodnoty viditelnosti můžete použít zobrazení výsečového grafu. Můžete také použít tabulkové zobrazení ukazující mezisoučty.

### <a name="s3-bucket-properties-report"></a>Sestava vlastností kontejneru S3

Tato sestava umožňuji zobrazit vlastnosti kontejneru S3. K nastavení prahové hodnoty viditelnosti můžete použít zobrazení výsečového grafu. Můžete také použít tabulkové zobrazení ukazující mezisoučty.

### <a name="rds-instances-over-time-report"></a>Sestava instancí RDS v průběhu času

Tato sestava zobrazuje rozpis všech instancí Amazon RDS (Relational Database Service) spuštěných během zadaného období.

### <a name="rds-active-instances-report"></a>Sestava aktivních instancí RDS

Pomocí toto sestavy můžete analyzovat aktivní instance RDS. Rozbalením řádkové položky sestavy zobrazíte další informace.

### <a name="azure-reserved-instances-report"></a>Sestava rezervovaných instancí Azure

Sestava rezervovaných instancí Azure poskytuje jediné zobrazení všech rezervovaných instancí Azure. Tato sestava zobrazuje každý nákup v samostatné řádkové položce. Poskytuje rovněž podrobnosti o daném nákupu, například účet, který ho koupil, typ nákupu a typ instance, zbývající dny atd. Data sestavy můžete zobrazit nebo skrýt pomocí možnosti Show/Hide Fields (Zobrazit/skrýt pole).

Sestava rezervovaných instancí Azure umožňuje zobrazit:

- Seznam všech rezervací podle data nákupu
- Čas zbývající do vypršení platnosti rezervované instance
- Jednorázové poplatky
- Účet, který rezervované instance zakoupil, a datum

### <a name="aws-reserved-instances-report"></a>Sestava rezervovaných instancí AWS

Sestava rezervovaných instancí AWS poskytuje jediné zobrazení všech rezervovaných instancí AWS. Tato sestava zobrazuje každý nákup v samostatné řádkové položce a podrobnosti o daném nákupu, například účet, který ho koupil, typ nákupu a typ instance, zbývající dny atd. Data sestavy můžete zobrazit nebo skrýt pomocí možnosti Show/Hide Fields (Zobrazit/skrýt pole).

Sestava rezervovaných instancí AWS umožňuje zobrazit:

- Seznam všech rezervací podle data nákupu
- Čas zbývající do vypršení platnosti rezervované instance
- Jednorázové poplatky
- ID původního nákupu (ID rezervace)
- Účet, který rezervované instance zakoupil, a datum

### <a name="ec2-ri-buying-recommendations-report"></a>Sestava doporučení k nákupu rezervovaných instancí EC2

Podstatou využívání cloudových prostředků je model na vyžádání, kdy u prostředků nabíhají náklady jen v případě, že se používají. Neplatíte nic předem, ale platíte jen za to, co používáte a za dobu, kdy to používáte.

AWS nabízí pro své služby Elastic Compute Cloud (EC2) alternativní cenový model – rezervované instance (RI). Tento cenový model po dobu trvání rezervované instance zaručuje uživatelům kapacitu, kdykoli ji potřebují. Rezervovaná instance nabízí významné cenové slevy oproti ceně na vyžádání. Na oplátku se uživatelé předem zavážou k používání virtuální instance. Po dobu závazku (jeden nebo tři roky) je tento závazek omezen na konkrétní rodinu, velikost, zónu dostupnosti a operační systém. Rezervovaná instance umožňuje AWS efektivně plánovat budoucí kapacitu a zavázat si uživatele k používání svých služeb.

U rezervovaných instancí lze použít tři možnosti platby předem:

- Celá suma v den pořízení, kdy je sleva nejvyšší.
- Bez platby předem – náklady na rezervovanou instanci se platí v měsíčních splátkách po dobu trvání rezervované instance, přičemž sleva je nejnižší.
- Částečná platba předem, kdy se předem zaplatí ¼–½ ceny a zbytek v měsíčních splátkách se slevou, která je nižší, ale blízká sazbě za celou platbu předem.

Cloudyn vyhodnocuje dobu provozu každého počítače za posledních 30 dnů. Cloudyn doporučuje nákup rezervovaných instancí, když je cenově výhodnější provozovat počítač pomocí rezervované instance při aktuální úrovni doby provozu.

Sestava obsahuje odůvodnění těchto doporučení, která vám ušetří nejvíce peněz v průběhu roku. Tato doporučení navrhují nahrazení instancí na vyžádání rezervovanými instancemi. Rezervované instance si můžete koupit přímo z této sestavy.

Každá karta se otevře jako úplná sestava. Mezi významné oddíly na kartách patří:

- **EC2 RI Purchase Impact** (Dopad nákupu rezervovaných instancí EC2) – tento oddíl simuluje rozdíl mezi instancemi na vyžádání a rezervovanými instancemi. Kliknutím na **Zoom in** (Přiblížit) zobrazíte úplnou sestavu dopadu nákupu rezervovaných instancí EC2 s filtry, které jsou pro doporučení už definované. Tato sestava zobrazuje dopad všech potenciálních nákupů rezervovaných instancí. Úpravou očekávané průměrné doby provozu zobrazíte potenciální úspory při nákupu rezervovaných instancí EC2.

- **Saving Analysis**  (Analýza úspor) – tento oddíl zobrazuje potenciální dosažené úspory a měsíc, ve kterém jsou úspory realizovány, pokud doporučení Cloudynu dodržíte. Skutečné úspory a uspořené procento jsou červeně zvýrazněny.

- **EC2 RI Type Comparison** (Srovnání typů rezervovaných instancí EC2) – tento oddíl zdůrazňuje návratnost investic nasazení doporučeného Cloudynem včetně všech relevantních možností. Výsledky v této sestavě předpokládají, že počítač běží 100% doby provozu. Kliknutím na **Zoom In** (Přiblížit) otevřete podrobnou sestavu.

- **Instances Over Time** (Instance v průběhu času) – tento oddíl zobrazuje rozpis všech instancí přidružených k doporučení, a sice instancí na vyžádání, rezervovaných instancí a náhradních instancí. Kliknutím na **Zoom In** (Přiblížit) otevřete podrobnou sestavu.
- **Breakeven Points** (Body návratnosti) – tento oddíl zobrazuje tabulku všech možných doporučených nasazení, návratnosti investic a měsíce, ve kterém dojde k návratnosti investic. Kliknutím na **Zoom In** (Přiblížit) otevřete podrobnou sestavu.

### <a name="ec2-reservations-over-time-report"></a>Sestava rezervací EC2 v průběhu času

Sestava rezervací EC2 v průběhu času sleduje stav využití zakoupených rezervovaných instancí EC2. Rozlišení sestavy můžete nastavit na hodinu, den nebo týden.

Tato sestava umožňuje:

- Zobrazit zakoupené rezervace, které se používají a nepoužívají
- Přejít na hodinové rozlišení a zobrazit využití rezervované instance po hodinách

### <a name="savings-over-time-report"></a>Sestava úspor v průběhu času

Sestava úspor v průběhu času zobrazuje úspory dosažené pomocí rezervovaných instancí a náhradních instancí. Ukazuje návratnost investic dosaženou v průběhu času, která vyplývá z nákupů rezervovaných instancí.

Úspory z rezervovaných instancí zobrazíte tak, že výsledky seskupíte podle **Price Model** (Cenový model) a vyberete **Reservation** (Rezervace). Pokud chcete vidět úspory z rezervovaných instancí dosažené u konkrétního účtu nebo typu instance, přidejte relevantní seskupení a filtrujte podle účtu nebo typu instance.

Úspory z použití náhradních instancí zobrazíte tak, že **Price Model** (Cenový model) vyfiltrujete na **Spot**. Výchozím filtrem této sestavy jsou rezervované instance a náhradní instance.

### <a name="rds-ri-buying-recommendations-report"></a>Sestava doporučení k nákupu rezervovaných instancí RDS

Sestava doporučení k nákupu rezervovaných instancí RDS doporučuje, kdy místo instancí na vyžádání použít rezervované instance RDS.

Každá karta se otevře jako úplná sestava. Mezi významné oddíly na kartách patří:

- **RDS RI Purchase Impact** (Dopad nákupu rezervovaných instancí RDS) – tento oddíl simuluje rozdíl mezi instancemi na vyžádání a rezervovanými instancemi. Kliknutím na **Zoom in** (Přiblížit) zobrazíte úplnou sestavu dopadu nákupu rezervovaných instancí RDS s filtry, které jsou pro doporučení už definované. Tato sestava umožňuje vidět dopad všech potenciálních nákupů rezervovaných instancí.  Úpravou očekávané průměrné doby provozu zobrazíte potenciální úsporu při zakoupení rezervovaných instancí.
- **Saving Analysis**  (Analýza úspor) – tento oddíl zobrazuje potenciální dosažené úspory a měsíc, ve kterém jsou úspory realizovány, pokud doporučení Cloudynu dodržíte. Skutečné úspory a uspořené procento jsou červeně zvýrazněny.

- **RDS RI Type Comparison** (Srovnání typů rezervovaných instancí RDS) – tento oddíl zdůrazňuje návratnost investic doporučeného nasazení včetně všech relevantních možností. Výsledky v této sestavě předpokládají, že počítač běží 100% doby provozu. Kliknutím na **Zoom In** (Přiblížit) otevřete podrobnou sestavu pro vybraný počítač.
- **Instances Over Time** (Instance v průběhu času) – tento oddíl zobrazuje rozpis všech instancí přidružených k doporučení, a sice instancí na vyžádání, rezervovaných instancí a náhradních instancí. Kliknutím na **Zoom In** (Přiblížit) otevřete podrobnou sestavu.

- **Breakeven Points** (Body návratnosti) – tento oddíl zobrazuje tabulku všech možných doporučených nasazení, návratnosti investic a měsíce, ve kterém dojde k návratnosti investic. Kliknutím na **Zoom In** (Přiblížit) otevřete podrobnou sestavu.

### <a name="rds-reservations-over-time-report"></a>Sestava rezervací RDS v průběhu času

Sestava rezervací RDS v průběhu času zobrazuje rozpis využitých a nevyužitých rezervací během zadaného období.

### <a name="reserved-instance-purchase-impact-report"></a>Sestava dopadu nákupu rezervované instance

Sestava dopadu nákupu rezervované instance EC2 simuluje srovnání nákladů na rezervovanou instanci a nákladů na instanci na vyžádání v průběhu času. Pomůže vám se lépe rozhodnout při nákupu. Úpravou filtrů, mezi které patří průměrná doba provozu, období, platforma a další, učiníte informované rozhodnutí při nákupu rezervovaných instancí.

### <a name="cost-effective-sizing-recommendations-report"></a>Sestava doporučení nákladově efektivní velikosti

Sestava doporučení nákladově efektivní velikosti poskytuje výsledky pro AWS a Azure. Pro uživatele AWS jsou zohledněny nákupy rezervovaných instancí, přičemž výsledky nezahrnují počítače provozované jako rezervované instance. Tato sestava obsahuje seznam nedostatečně využitých instancí, které jsou kandidáty na zmenšení velikosti. Doporučení vycházejí z údajů o využití a výkonu za posledních 30 dnů. Každé doporučení obsahuje seznam kandidátů na zmenšení velikosti včetně odůvodnění a odkazu na zobrazení kompletních podrobností a metrik výkonu dané instance. Zjistíte také, kdy relevantní doporučení radí změnu na novější generaci typů instancí.

Z této sestavy si nemůžete stáhnout seznam ID instancí, jejichž velikost se doporučuje zmenšit. K tomuto účelu použijte sestavu doporučení pro všechny velikosti.

Představte si následující příklad se zmenšením velikosti:

Provozujete šest instancí m3.xlarge. Analýza Cloudynu ukazuje, že pět z nich má nízké využití procesoru. Zvažte zmenšení jejich velikosti.

V oddílu Cost Impact (Dopad na náklady) je spočítán dopad na náklady. Rozbalením řádkové položky v tomto příkladu vidíte, že aktuální cena za jednu instanci m3.xlarge (Linux/Unix) je 0,266 USD za hodinu a za jednu instanci m3.large (Linux/UNIX) je 0,133 USD za hodinu. Roční náklady na pět instancí m3.xlarge provozovaných při 100% využití tedy činí 11 651 USD. Roční náklady na pět instancí m3.large provozovaných při 100% využití činí 5 825 USD. Potenciální úspory jsou 5 825 USD za rok.

Kliknutím na + rozbalíte řádkovou položku a zobrazíte odůvodnění nákladově efektivní velikosti. Oblast **Details** (Podrobnosti) obsahuje:

- Oddíl **Recommendation Justification** (Odůvodnění doporučení) zobrazuje aktuální nasazení a počet instancí, které je doporučeno zmenšit.
- Oddíl **Cost Impact** (Dopad na náklady) zobrazuje výpočet použitý k určení potenciálních úspor.
- Oddíl **Potential Annual Savings** (Potenciální roční úspory) zobrazuje potenciální roční úspory při zmenšení velikosti podle doporučení Cloudynu.

### <a name="all-sizing-recommendations-report"></a>Sestava doporučení pro všechny velikosti

Tato sestava obsahuje seznam nedostatečně využitých instancí, které jsou kandidáty na zmenšení velikosti. Tato doporučení vycházejí z údajů o využití a výkonu za posledních 30 dnů. U každého doporučení můžete vidět kompletní podrobnosti a metriky výkonu dané instance.

Pokud jste si zakoupili rezervované instance AWS, obsahuje tato sestava výsledky pro všechny instance včetně těch, které se provozují jako rezervované.

Sestava doporučení pro všechny velikosti umožňuje:

- Zobrazit seznam všech instancí, které jsou kandidáty na zmenšení velikosti
- Exportovat seznam obsahující názvy a ID instancí

Kliknutím na **+** rozbalíte podrobnosti doporučení pro konkrétní instanci. Oddíl Recommendation Details (Podrobnosti doporučení) nabízí přehled doporučení.

Oddíl **Tags** (Značky) obsahuje seznam klíčů a hodnot značek vybrané instance. Pomocí značek v levém podokně můžete tento oddíl filtrovat.

Oddíl **CPU Utilization** (Využití procesoru) zobrazuje využití procesoru instance za poslední měsíc den po dni.

Kliknutím na tento graf přejdete na podrobnosti a otevřete sestavu procesorů instancí v průběhu času, která zobrazuje rozpis instancí.

- Pomocí možnosti **Show/Hide Fields** (Zobrazit/skrýt pole) můžete přidat nebo odebrat pole: Timestamp (Časové razítko), Avg CPU (Průměrné využití procesoru), Min CPU (Minimální využití procesoru), Max CPU (Maximální využití procesoru).
- **Date Range** (Rozsah kalendářních dat) umožňuje zadat datum nebo rozsah kalendářních dat a přejít k podrobnostem konkrétního ID instance.
- **Extended Filters** (Rozšířené filtry) umožňují zobrazit všechny nebo konkrétní ID instance.
- Kliknutím na **Zoom in** (Přiblížit) otevřete sestavu využití procesoru.

Pokud instance není monitorována po dobu 30 dnů, zobrazují se neúplná data.

Oddíl **Memory Utilization (GB)** (Využití paměti v GB) nabízí informace o využité paměti. Pro uživatele AWS nejsou metriky paměti dostupné automaticky a je třeba je u každé instance přidat prostřednictvím AWS. AWS vám za povolení metrik paměti pro instance EC2 účtuje poplatek.

Oddíl **Memory Utilization (%)** (Využití paměti v procentech) zobrazuje procento využité paměti.

Oddíl **Network Input Traffic** (Vstupní síťový provoz) zobrazuje snímek síťového provozu v průběhu času, a sice průměr a maximum pro vybranou instanci. Najetím myši na čáry se zobrazí datum a maximální provoz v této době. Kliknutím na **Zoom In** (Přiblížit) otevřete sestavu vstupního síťového provozu.

Oddíl **Network Output Traffic** (Výstupní síťový provoz) zobrazuje snímek výstupního síťového provozu pro vybranou instanci. Najetím myši na čáry se zobrazí datum a maximální provoz v této době. Kliknutím na **Zoom In** (Přiblížit) otevřete sestavu výstupního síťového provozu.

### <a name="instance-metrics-explorer-report"></a>Sestava průzkumníka metrik instancí

Sestava průzkumníka metrik instancí zobrazuje metriky výkonu každé instance v různých cloudech. Tato sestava zobrazuje instance, které jsou nadměrně nebo nedostatečně využívány podle prahových hodnot metrik procesoru, paměti a sítě.

Výkon každé instance v různých cloudech zobrazíte takto:

1. V oblasti **Date Range** (Rozsah kalendářních dat) vyberte období, za které chcete zobrazit výkon.
2. V oblasti **Tags** (Značky) vyberte všechny značky, které chcete zobrazit.
3. V oblasti **Filters** (Filtry) vyberte filtry, které chcete v sestavě zobrazit.
4. V oblasti **Extended Filters** (Rozšířené filtry) upravte prahové hodnoty sestavy pro:
    - Avg CPU (průměrné využití procesoru)
    - Max CPU (maximální využití procesoru)
    - Avg Memory (průměrné využití paměti)
    - Max Memory (maximální využití paměti)
5. V oblasti **Extended Filters** (Rozšířené filtry) klikněte na **Show** (Zobrazit) a pak vyberte typ instancí, které se mají zobrazit.

Metriky konkrétní instance v průběhu času zobrazíte takto:

- Přejděte na sestavu průzkumníka metrik instancí a kliknutím na **+** zobrazte podrobnosti.

### <a name="rds-sizing-recommendations-report"></a>Sestava doporučení velikosti RDS

Sestava doporučení velikosti RDS poskytuje doporučení ohledně velikosti RDS pro optimalizaci využití cloudu. Obsahuje seznam nedostatečně využitých instancí, které jsou kandidáty na zmenšení velikosti. Doporučení Cloudynu vycházejí z údajů o využití a výkonu za posledních 30 dnů. Doporučení můžete filtrovat podle názvu účtu, oblasti, typu instance a stavu.

### <a name="sizing-threshold-manager-report"></a>Sestava správce prahových hodnot velikosti

K výpočtu doporučení velikosti používá Cloudyn složitý algoritmus, který poskytuje přesné návrhy velikosti. Prahové hodnoty doporučení pro zmenšení velikosti můžete upravit.

Tyto prahové hodnoty můžete ručně upravit takto:

1. Ve správci prahových hodnot velikosti upravte následující prahové hodnoty podle svých potřeb:
    - Average CPU % (průměrné procento využití procesoru)
    - Maximum CPU % (maximální procento využití procesoru)
    - Average Memory % (průměrné procento využití paměti)
    - Maximum Memory % (maximální procento využití paměti)
3. Kliknutím na **Apply** (Použít) uložte změny.
4. Změny se projeví okamžitě ve všech doporučeních.

Výchozí prahové hodnoty obnovíte takto:

- Ve správci prahových hodnot velikosti klikněte na **Restore Defaults** (Obnovit výchozí nastavení).

### <a name="compute-instance-types-report"></a>Sestava typů výpočetních instancí

Sestava typů výpočetních instancí umožňuje:

- Zobrazit typy instancí podle služby, rodiny, názvu rozhraní API a názvu.
- Zobrazit podrobnosti, například CPU, ECU, RAM a síť.

Pomocí možnosti **Search** (Hledat) můžete najít konkrétní řádkové položky.

## <a name="next-steps"></a>Další kroky

- Informace o používání sestav včetně postupu jejich přizpůsobení, uložení a plánování najdete v článku [Vysvětlení sestav nákladů](understanding-cost-reports.md).
- Informace o řídicích panelech, které jsou součástí Cloudynu, a o vytváření vlastních řídicích panelů najdete v článku [Zobrazení klíčových nákladových metrik pomocí řídicích panelů](dashboards.md).

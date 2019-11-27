---
title: Použití Cloudyn sestav v Azure | Dokumentace Microsoftu
description: Tento článek popisuje účel sestavách Cloudyn, které jsou součástí portálu Cloudyn můžete efektivněji používat je.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: f838091f4b4cdcb0535f58926a724074ef080463
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218859"
---
# <a name="reports-available-in-the-cloudyn-portal"></a>Sestavy jsou k dispozici na portálu Cloudyn

Tento článek popisuje účel sestavách Cloudyn, které jsou součástí portálu Cloudyn. Také popisuje, jak lze efektivně používat sestavy. Většina sestav jsou výsledkem je intuitivní a mít jednotný vzhled a chování. Většinu akcí, které vám pomůžou v jedné sestavě, můžete provést také v jiných sestavách. Přehled o tom, jak používat sestavy Cloudyn, včetně postupu přizpůsobení a uložení nebo plánování sestav, najdete v tématu [porozumění sestavám nákladů](understanding-cost-reports.md).

Azure Cost Management nabízí podobné funkce jako Cloudyn. Azure Cost Management je nativní řešení Azure pro správu nákladů. Umožní vám analyzovat náklady, vytvářet a spravovat rozpočty, exportovat data, kontrolovat optimalizační doporučení k úspoře peněz a reagovat na ně. Další informace najdete v tématu [Azure Cost Management](overview-cost-mgt.md).

## <a name="report-types"></a>Typy sestav

Existují tři typy Cloudyn sestav:

- Útoky over-pass-the sestav. Například sestava Cost Over Time. Útoky over-pass-the sestav zobrazit časové řadě dat. v intervalu vybraného předdefinovaného řešení a zobrazit týdenní řešení po dobu posledních dvou měsíců. Přiblížit k různým datovým bodům, můžete použít seskupování a filtrování.
  - Útoky over-pass-the sestav můžete zobrazit trendy a odhalte provozní špičky nebo anomálie.
- Sestavy analýzy. Například sestava analýzy nákladů. Tyto sestavy zobrazit agregovaná data po dobu, definovat a Povolit seskupování a filtrování podle data.
  - Analýza sestavy můžete zobrazit provozní špičky a určení příčiny anomálií a chcete-li zobrazit podrobné konec seznamu vaše data.
- Tabulkové sestavy. Můžete zobrazit všechny sestavu jako tabulku, ale některé sestavy jsou zobrazeny pouze jako tabulku. Tyto sestavy poskytují podrobné seznamy položek.
  - Doporučení jsou tabulkové sestavy – neexistují žádné vizualizace pro doporučení. Však můžete vizualizovat výsledky doporučení. Například úspory v čase.
  - Tabulkové zprávy jsou užitečné jako seznam akcí, nebo pro export dat pro další zpracování. Například sestavy vrácení peněz.

V sestavách nákladů se zobrazují buď _skutečné_ , nebo náklady na _amortizaci_ .

Skutečné náklady sestavy zobrazují platby během vybraného časového rámce. Například všechny jednorázové poplatky, jako je nákup rezervované instance (RI) jsou uvedeny v sestavách skutečných nákladů jako si poraďte se špičkami náklady.

Sestavy amortizovaných nákladů rozprostřené jednorázové poplatky za období, na které se vztahují. Například jednorázové poplatky za nákup rezervované instance jsou rozdělené do období rezervace a nejsou zobrazeny jako prudký nárůst. Amortizované zobrazení je jediný způsob, jak zobrazit trendy true a provádět odhadu nákladů.

V některých případech se zobrazí amortizace jako samostatnou sestavu. Mezi příklady patří sestavy analýzy nákladů a analýza Amortizovaných nákladů. V ostatních případech je amortizace sestavu zásad, například sestavy přidělování nákladů a analýzy nákladů.

Můžete naplánovat jakoukoli sestavu pro pravidelné doručení. Sestavy nákladů povolit nastavení prahové hodnoty, aby byly užitečné pro výstrahy.

## <a name="cost-analysis-vs-cost-allocation"></a>Analýza nákladů oproti přidělení nákladů

Sestavy _analýzy nákladů_ zobrazují fakturační data od poskytovatelů cloudu. Využití sestav, můžete seskupit a přejít k podrobnostem různých datových segmentech rozpis ze souboru fakturace. Sestavy povolení navigace detailní náklady napříč dodavatelem cloudu nezpracovaná fakturačních údajů.

Některé sestavy _analýzy nákladů_ neseskupují náklady podle značek prostředků. A informace o fakturaci na základě značek se v sestavách zobrazí jenom po přidělení nákladů vytvořením nákladového modelu pomocí [přidělení nákladů 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

Sestavy _přidělení nákladů_ jsou k dispozici po vytvoření nákladového modelu pomocí [přidělení nákladů 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs). Cloudyn zpracovává nákladová a fakturační data a _odpovídá_ na data o využití a označení vašich cloudových účtů. Cloudyn tak, aby odpovídaly data, vyžaduje přístup k data o využití. Pokud máte účty, u kterých chybí přihlašovací údaje, budou označeny jako _Nezařazeno do kategorie prostředky_.

## <a name="dashboards"></a>Řídicí panely

Řídicí panely v Cloudy poskytují souhrnný přehled sestav. Řídicí panely jsou tvořené widgetů a u každé pomůcky máte je v podstatě miniaturu sestavy. Při [přizpůsobování sestav](understanding-cost-reports.md#save-and-schedule-reports)je uložíte do mé sestavy a přidají se na řídicí panel. Další informace o řídicích panelech najdete v tématu [zobrazení metrik nákladů na klíč s řídicími panely](dashboards.md).

## <a name="budget-information-in-reports"></a>Informace o rozpočtu v sestavách

Mnoho sestavách Cloudyn zobrazit rozpočtu informace po jedné ruční vytvoření. Aby sestavy nebudou zobrazovat informace rozpočtu, dokud nevytvoříte rozpočtu. Další informace najdete v tématu [Nastavení správy rozpočtu](#budget-management-settings).

## <a name="reports-and-reporting-features"></a>Sestavy a funkce generování sestav

Cloudyn obsahuje následující sestavy a funkce pro vytváření sestav.

### <a name="cost-navigator-report"></a>Sestava nákladů Navigátor

Sestava náklady na navigátor je rychlý způsob, jak zobrazit vaše fakturační využití v zobrazení řídicího panelu. Obsahuje podmnožinu filtry a okamžitě zobrazit souhrnné zobrazení náklady organizace na základní zobrazení. Náklady jsou uvedeny podle data. Vzhledem k tomu sestavy slouží jako počáteční přehled nákladů, není tak pružná nebo jako komplexní jako mnoho dalších sestavy nebo vlastních řídicích panelů, které jste sami vytvořili.

Ve výchozím nastavení hlavní zobrazení v sestavě zobrazit:

- Ceny za časová zobrazující pracovního týdne panelu zobrazení grafu. Můžete změnit **Rozsah kalendářních dat** , aby se změnil pruhový graf rozsahu kalendářních dat.
- Výdaje podle služby, použití výsečového grafu.
- Prostředek kategorizace podle značek, použití výsečového grafu.
- Výdaje podle entit nákladů, použití výsečového grafu.
- Náklady celkem podle data v zobrazení seznamu.

### <a name="cost-analysis-report"></a>Sestava analýzy nákladů

Sestava analýzy nákladů se počítají za metod showback a chargeback, na základě vašich zásad. Agreguje vaše využití cloudu během vybraného časového rámce, po použití všechna pravidla přidělování na vaše náklady. Například se vypočítá náklady podle značek, znovu přiřadí náklady na neoznačených prostředky a volitelně přiděluje využití rezervovaných instancí.

Zásady nastavené v [přidělování nákladů 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) se používají v sestavě analýzy nákladů a výsledky se pak zkombinují s informacemi z nezpracovaných dat od dodavatele cloudu.

Jak se počítá tuto sestavu? Služba Cloudyn zajišťuje, že přidělení zachovává integritu každého propojeného účtu tím, že použije _spřažení účtů_. Spřažení zajistí, že účet, který nepoužívá konkrétní služba nemá žádné náklady na tuto službu přiřazen. Náklady na operace účet zůstane v tomto účtu a nejsou vypočítaných zásady přidělování. Například může mít pět propojené účty. Pokud jenom tři z nich používat služby storage, náklady na úložiště služby je přidělena pouze ve mezi značky v tři účty.

Pomocí sestavy analýzy nákladů na:

- Vypočítat vaší organizace vrácení peněz nebo kompletní přehled nákladů
- Kategorizace všechny nákladů
- Zobrazí souhrnný náhled na celé nasazení pro konkrétní časový rámec.
- Zobrazení nákladů podle značky kategorií na základě zásad, které jsou vytvořené v modelu nákladů.

Použití sestav Cost Analysis:

1. Vyberte konkrétní období.
2. Přidání značek, podle potřeby.
3. Přidání skupin.
4. Vyberte model nákladů, který jste vytvořili dříve.

### <a name="cost-over-time-report"></a>Sestava nákladů v průběhu času

Náklady na čas sestavě zobrazí výsledky přidělování nákladů jako časové řady. Umožňuje sledovat trendy a detekovat nesrovnalosti ve vašem nasazení. V podstatě ukazuje náklady v průběhu za definované období. Zpráva obsahuje vaše náklady na hlavní přispěvatelé, včetně průběžných nákladů a poplatky za jednorázové rezervované instance, které se právě spotřebovávají během vybraného časového rámce. V této sestavě se používají zásady nastavené v [přidělení nákladů 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) .

Použití sestav Cost Over Time, abyste:

- Podívejte se změny za dobu a které ovlivňuje změnit z jednoho dne (nebo rozsah dat) na další.
- Analyzujte náklady v čase pro konkrétní instanci.
- Pochopit, proč došlo zvýšení nákladů pro konkrétní instanci.

Použití sestav Cost Over Time:

1. Vyberte konkrétní období.
2. Přidání značek, podle potřeby.
3. Přidání skupin.
4. Vyberte model nákladů, který jste vytvořili dříve.
5. Vyberte skutečné náklady nebo amortizované náklady.
6. Zvolte, jestli se má použít pravidla přidělování na Zobrazit nezpracované fakturace zobrazení dat nebo k přepočítání zobrazení nákladů.

### <a name="actual-cost-analysis-report"></a>Skutečné sestavu analýzy nákladů

Analýza skutečných nákladů sestava obsahuje poskytovatele náklady bez možnosti úprav. Ukazuje náklady na hlavní přispěvatelů, včetně průběžných nákladů a jednorázové poplatky.

Sestavy můžete zobrazit informace o nákladech pro vaše předplatná. V sestavě se předplatná Azure zobrazují jako **název účtu** a **číslo účtu**. **Propojené účty** ukazují AWS předplatná. Pokud chcete zobrazit náklady na předplatné, pro každý účet v části **skupiny**vyberte typ předplatného, které máte.

Použijte sestavu Analýza skutečných nákladů na:

- Analyzovat a sledovat náklady nezpracovaná poskytovatele strávil během zadaného časového rámce.
- Naplánujte prahová hodnota upozornění.
- Analyzujte bez úprav náklady vzniklé v souvislosti s klienty a entity.

### <a name="actual-cost-over-time-report"></a>Skutečné náklady v čase sestavy

Sestavy Actual Cost Over Time je náklady na standardní sestavy analýzy distribuce náklady za definované časové řešení. Sestava zobrazí za čas, aby bylo možné sledovat trendy a zjišťovat útraty nesrovnalosti. Tato sestava zobrazí hlavních nákladových přispěvatelů včetně průběžných nákladů a poplatky za jednorázové rezervované instance, které se právě spotřebovávají během vybraného časového rámce.

Pomocí sestavy Actual Cost Over Time na:

- Zobrazení trendy nákladů v průběhu času.
- Najdete nesrovnalosti náklady.
- Najdete všechny otázky související náklady související s poskytovateli cloudu.

### <a name="amortized-cost-reports"></a>Sestavy amortizovaných nákladů

Tato sada amortizované náklady sestavy zobrazuje lineárním bez využití na základě poplatků za služby nebo jednorázové závazků náklady a náklady na jejich rozloženy rovnoměrně dobu jejich životnosti. Například může zahrnovat jednorázové poplatky:

- Roční poplatky za podporu
- Roční poplatky komponentu zabezpečení
- Poplatky za nákupy rezervovaných instancí
- Některé položky Azure Marketplace

V souboru fakturace jsou charakteristické jednorázové poplatky při používání služby počáteční a koncové datum (časové razítko) mají stejné hodnoty. Služba Cloudyn pak je pak rozezná jako jednorázové poplatky, které jsou amortizovaných. Nejsou amortizovaných jiné služby založenou na skutečné spotřebě s náklady na využití na vyžádání.

Sestavy amortizovaných nákladů patří:

- Analýza amortizovaných nákladů
- Amortizované náklady za časová období

### <a name="cost-analysis-report"></a>Sestava analýzy nákladů

Sestava analýzy nákladů poskytuje pohled na vaše využití cloudu a výdaje během vybraného časového rámce. V sestavě analýza nákladů se používají zásady nastavené v části [přidělení nákladů 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) .

Jak Cloudyn vypočítat tuto sestavu?

Cloudyn zajišťuje, že přidělení zachovává integritu každého propojeného účtu tím, že použije _spřažení účtů_. Spřažení zajistí, že účet, který nepoužívá konkrétní službu také nemá žádné náklady na tuto službu přiřazen. Náklady na operace účet zůstane v tomto účtu a nejsou vypočítaných zásady přidělování. Například může mít pět propojené účty. Pokud jenom tři z nich používat služby storage, náklady na úložiště služby je přidělena pouze ve mezi značky v tři účty.

Pomocí sestavy analýzy nákladů na:

- Zobrazí souhrnný náhled na celé nasazení pro konkrétní časový rámec.
- Zobrazení nákladů podle značky kategorií na základě zásad, které jsou vytvořené v modelu nákladů.

### <a name="cost-over-time-report"></a>Sestava nákladů v průběhu času

Sestava Cost Over Time zobrazí útraty v čase, která umožňují vysledovat trendy a Všimněte si, že nesrovnalosti ve vašem nasazení. V podstatě ukazuje náklady v průběhu za definované období. Zpráva obsahuje vaše náklady na hlavní přispěvatelé, včetně průběžných nákladů a poplatky za jednorázové rezervované instance, které se právě spotřebovávají během vybraného časového rámce. V této sestavě se používají zásady nastavené v [přidělení nákladů 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) .

Použití sestav Cost Over Time, abyste:

- Podívejte se změny za dobu a které ovlivňuje změnit z jednoho dne (nebo rozsah dat) na další.
- Analyzujte náklady v čase pro konkrétní instanci.
- Pochopit, proč došlo zvýšení nákladů pro konkrétní instanci.

### <a name="custom-charges-report"></a>Vlastní sestavu poplatky

Podniku a CSP uživatelé často dostat poskytuje přidání služby zákazníkům externí nebo interní kromě své vlastní využití prostředků cloudu. Můžete definovat vlastní poplatky za služby s přidanou ani slevy, které jsou přidány do sestav vrácení peněz jako vlastní položky řádku nebo fakturace zákazníka.

Vlastní poplatků za služby zahrnují služby, které se ve vyúčtování služeb obvykle nezobrazují. Vlastní poplatky, které vytvoříte, se pak zobrazí v sestavách nákladů.

*Vlastní poplatky nejsou vlastní ceny*. Seznam vlastních poplatky nezobrazí různé kurzy, vám může účtovat. Například AWS fakturační poplatky se zobrazí stejně jako se účtují.

Pokud chcete vytvořit vlastní poplatky:

1. V nabídce **vlastní poplatky**klikněte na **Přidat nový**. Zobrazí se dialogové okno _Přidat nový vlastní poplatek_ .
2. Do pole **název poskytovatele**zadejte název poskytovatele.
3. Do pole **název služby**zadejte typ služby.
4. Do pole **Popis**zadejte popis vlastního poplatku.
5. V poli **typ**zadejte **procento** výběru a potom v rozevíracím seznamu služby vyberte služby, které chcete zahrnout do sestav nákladů jako vlastní poplatky.
6. V části **platba**vyberte, jestli je poplatek jednorázovým poplatkem nebo opakovaným poplatkem. Pokud platí se poplatek se opakuje, vyberte Amortized, pokud chcete náklady na být amortizovaných a vybrat počet měsíců.
7. V části **data**v případě, že je vybrán jednorázový poplatek, zadejte do pole datum **účinnosti**datum, kdy je poplatek zaplacen. Pokud je vybrána opakované poplatek, zadejte rozsah dat, včetně počáteční datum a koncové datum pro příslušný poplatek.
8. Ve **stromové struktuře entit**vyberte entity, u kterých chcete použít poplatek, a pak vyberte **zapnuto**.

_Když se poplatky přiřazují k entitě, uživatelé je nemůžou měnit. Poplatky přidané správcem k nadřazené entitě jsou jen pro čtení._

Chcete-li zobrazit vlastní poplatky:

Vlastní poplatky se zobrazí v sestavách nákladů. Otevřete například sestavu skutečná analýza nákladů a potom v části **Rozšířené filtry**vyberte **samostatně**. Pak filtrováním zobrazíte **vlastní poplatky**.

### <a name="cost-allocation-360"></a>Cost Allocation 360

Cost Allocation 360 použijete k vytvoření vlastní nákladový model přidělování nákladů přiřadit využité cloudové prostředky. Mnoho sestav zobrazovat informace z vlastní nákladový model, které jste vytvořili pomocí vlastní nákladový model. A některé sestavy pouze zobrazit informace, až vytvoříte vlastní model nákladů s přidělováním nákladů.

Další informace o vytváření vlastních nákladových modelů najdete v tématu [kurz: Správa nákladů pomocí Cloudyn](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>Sestava nákladů vs. rozpočet v průběhu času

Sestava náklady vs. rozpočet v čase umožňuje porovnat hlavní přispěvatele nákladů s vaším rozpočtem. Přiřazené rozpočtu se zobrazí v sestavě tak, abyste mohli zobrazit vaše využití rozpočtu (nad nebo pod/pamětích) v čase. Pomocí Zobrazit/skrýt pole v horní části sestavy, můžete vybrat zobrazení nákladů, rozpočet, celkové náklady a celkový rozpočet.

### <a name="current-month-projected-cost-report"></a>Aktuální sestava plánovaných nákladů měsíc

Sestava plánovaných nákladů pro aktuální měsíc poskytuje přehled o aktuální souhrnné náklady za měsíc k datu. Tato sestava zobrazuje náklady od začátku měsíce, od předchozí měsíc, a celkový počet plánované náklady pro aktuální měsíc. Aktuální měsíc plánované náklady se počítají jako součet aktuální měsíční náklady a projekci na základě nákladů monitorovat za posledních 30 dní.

Sestava plánovaných nákladů pro aktuální měsíc použijte:

- Projekt měsíční náklady podle služeb
- Měsíční náklady na projektu podle účtu

### <a name="annual-projected-cost-report"></a>Sestava ročních plánovaných nákladů

Sestava roční plánované náklady můžete zobrazit roční plánované náklady podle předchozí výdajové trendy. Zobrazuje další 12 měsíců od celkové předpokládané náklady. Projekce jsou prováděna pomocí funkce trend extrapolovat během následujících 12 měsíců, podle nákladů spojených se za posledních 30 dní používání.

### <a name="budget-management-settings"></a>Nastavení Správa rozpočtu

Pomáhá při řízení rozpočtu umožňuje nastavit rozpočtu fiskálního roku.

Chcete-li přidat rozpočtu na entitu:

1. Na stránce Správa rozpočtu v části **entity**vyberte entitu, u které chcete vytvořit rozpočet.
2. V roce rozpočtu vyberte v roce, ve kterém chcete vytvořit rozpočtu.
3. V každém měsíci nastavte rozpočet a pak klikněte na **Uložit**.

Import souboru pro roční rozpočtu:

1. V části **Akce**vyberte **exportovat** a stáhněte prázdnou šablonu sdíleného svazku clusteru, kterou použijete jako základ pro rozpočet.
2. Zadejte soubor CSV s položky vašeho rozpočtu a uloží do místního prostředí.
3. V části **Akce**vyberte **importovat**.
4. Vyberte uložený soubor a klikněte na tlačítko **OK**.

Pokud chcete exportovat dokončený rozpočet jako soubor CSV, vyberte v části **Akce**možnost **exportovat** a Stáhněte soubor.

Po dokončení se váš rozpočet zobrazuje v sestavách analýzy nákladů a v sestavě náklady vs. rozpočet v čase. Můžete také plánovat sestavy na základě prahových hodnot rozpočtu.

### <a name="azure-resource-explorer-report"></a>Sestava Průzkumníka prostředků Azure

Azure Resource Exploreru sestava zobrazí seznam hromadně všechny prostředky Azure k dispozici ve službě Cloudyn. Efektivně používat sestavy, by měl mít účtů Azure rozšířené povolené metriky. Rozšířené metriky poskytovat přístup do Cloudyn virtuální počítače Azure. Další informace najdete v tématu [Přidání rozšířené metriky pro virtuální počítače Azure](azure-vm-extended-metrics.md).

### <a name="azure-resources-over-time-report"></a>Sestava Azure prostředků v čase

Prostředky Azure v čase sestava obsahuje rozpis všech prostředků s konkrétní období. Efektivně používat sestavy, by měl mít účtů Azure rozšířené povolené metriky. Rozšířené metriky poskytovat přístup do Cloudyn virtuální počítače Azure. Další informace najdete v tématu [Přidání rozšířené metriky pro virtuální počítače Azure](azure-vm-extended-metrics.md).

### <a name="instance-explorer-report"></a>Sestava instance Průzkumníka

Sestava Instance Průzkumníka se používá k zobrazení různých metrik pro prostředky virtuálních počítačů. Můžete přejít k podrobnostem konkrétních instancí zobrazíte informace:
- Instance spuštění intervaly
- Životní cyklus ve vybraném období
- Využití procesoru
- Vstup sítě
- Výstupní provoz
- Aktivní disky

Sestava Instance Explorer shromažďuje všechny spuštěné intervalech v rámci definované období a odpovídajícím způsobem agreguje data. Chcete-li zobrazit spuštěné intervaly během období, rozbalte instanci. Náklady na jednotlivých instancí se počítá pro rozsah vybrané na základě AWS a Azure ceny seznamu. Nevztahují žádné slevy. Můžete přidat další pole do sestavy pomocí Zobrazit/skrýt pole.

Pomocí Průzkumníka Instance sestavy do:

- Výpočet odhadované náklady na počítač.
- Vytvoří úplný seznam, včetně agregované spuštěné hodin, všech počítačů, které byly aktivní v časovém rozsahu.
- Vytvoření seznamu poskytovatele cloudové služby nebo účet.
- Zobrazit počítače vytvořen nebo byl ukončen během časového úseku.
- Zobrazte všechny aktuálně zastavené počítače.
- Zobrazte značky jednotlivých počítačů.

### <a name="instances-over-time-report"></a>Sestava instance v čase

Pomocí funkce nahlásit instance v čase, zobrazí se maximální počet počítačů, které bylo aktivních každý během vybraného časového úseku. Pokud týden nebo měsíc je definovaný rozlišení, výsledky se maximální počet počítačů aktivní každý den v daném měsíci. Vyberte rozsah dat vyberte filtry, které chcete zobrazit v sestavě.

### <a name="instance-utilization-over-time-report"></a>Sestava využití v čase instance

Tato sestava uvádí rozpis využití procesoru nebo paměti v čase pro všechny vaše instance.

### <a name="compute-power-cost-over-time-report"></a>Výpočetní výkon Cost Over Time sestavy

Výpočetní výkon v čase sestava uvádí výčet výpočetní výkon za určité časové období. I když další sestavy zobrazit počet spuštěných počítače nebo hodin modulu runtime, tato sestava zobrazuje – základní hodiny hodin výpočetních jednotek a GB paměti RAM hodin.

Sestavu, kterou chcete použijte:

- Zkontrolujte výpočetní výkon v rámci určité časové období.
- Zobrazení výpočetní čas podle modelů přidělování nákladů.

Tato sestava je propojená s vašimi zásadami pro [přidělování nákladů 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) , takže výsledky se zobrazují na základě definovaného označování a zásad, které jste vybrali pro zvolenou nákladovou zásadu. Pokud nemáte k dispozici zásady vytvořené, nejsou zobrazeny výsledky.

### <a name="compute-power-average-cost-over-time-report"></a>Sestava Power průměrné Cost Over Time COMPUTE

Sestava výpočetní výkon průměrné Cost Over Time slouží k zobrazení více než jen náklady na každý počítač spuštěný. Tato sestava zobrazuje vaše průměrné náklady na hodiny instance, hodina jádra, hodinu výpočetní jednotky a hodina GB paměti RAM. Tato sestava poskytuje přehled o tom, efektivitu vašeho nasazení.

Tato sestava je propojená s vašimi zásadami pro [přidělování nákladů 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) , takže se výsledky zobrazují na základě definovaného označování a zásad, na které jste vybrali náklady. Pokud nemáte k dispozici zásady vytvořené, nejsou zobrazeny výsledky.

### <a name="s3-cost-over-time-report"></a>S3 Cost Over Time sestavy

S3 Cost Over Time sestava uvádí výčet Amazon Simple Storage Service (S3) nákladů na kbelík v čase pro zadaného časového rámce. Sestavy vám pomůže najít bloků, které jsou ovladače hlavní náklady a zobrazuje trendy ve vašich S3 využití a výdajů.

### <a name="s3-distribution-of-cost-report"></a>Sestava S3 rozdělení nákladů

Pomocí sestavy k analýze S3 náklady za poslední měsíc třídou kontejneru a úložiště. Zobrazení výsečového grafu můžete použít k nastavení prahová hodnota viditelnosti. Nebo můžete použít zobrazení tabulky pro mezisoučtů naleznete v tématu.

### <a name="s3-bucket-properties-report"></a>Sestava vlastností sady S3

Chcete-li zobrazit vlastnosti kontejneru S3 pomocí sestavy. Zobrazení výsečového grafu můžete použít k nastavení prahová hodnota viditelnosti. Nebo můžete použít zobrazení tabulky pro mezisoučtů naleznete v tématu.

### <a name="rds-instances-over-time-report"></a>Sestavy licencí VP instance v čase

Pomocí sestavy chcete-li zobrazit rozpis všech instancí Amazon Relational Database Service (RDS) během zadaného období.

### <a name="rds-active-instances-report"></a>Sestava aktivních instancích vzdálené plochy

Pomocí sestavy k analýze aktivní instance vzdálené plochy. V sestavě rozbalte položku řádku zobrazíte další informace.

### <a name="azure-reserved-instances-report"></a>Sestava rezervované instance Azure

Sestavy Azure Reserved Instances vám poskytne ucelený přehled o všech Azure rezervovaných instancí. Tato sestava zobrazí každý nákup je vlastní položky na řádku. Sestava také zobrazuje podrobnosti o tento nákup, jako je například účet, který předplatné zakoupili, typ nákupu a typu instance, a tak dále zbývající počet dní. Můžete zobrazit nebo skrýt pomocí Zobrazit/skrýt pole data sestavy.

Sestava rezervované instance Azure slouží k zobrazení:

- Seznam všech rezervací datu nákupu.
- Dobu zbývající do vypršení platnosti rezervované instance.
- Jednorázové poplatky.
- Účet, který zakoupené rezervované instance, a kdy.

### <a name="aws-reserved-instances-report"></a>Rezervované instance AWS sestavy

Sestava rezervované instance AWS poskytuje že ucelený přehled o všech AWS vám rezervované instance. Tato sestava že zobrazí každý nákup je vlastní položky na řádku a podrobnosti o tento nákup, jako je například účet, který předplatné zakoupili, typ nákupu a typu instance, a tak dále zbývající počet dní. Můžete zobrazit nebo skrýt pomocí Zobrazit/skrýt pole data sestavy.

Rezervované instance AWS sestava slouží k zobrazení:

- Seznam všech rezervací datu nákupu.
- Dobu zbývající do vypršení platnosti rezervované instance.
- Jednorázové poplatky.
- ID původního nákupu (ID rezervace).
- Účet, který zakoupené rezervované instance a kdy.

### <a name="ec2-ri-buying-recommendations-report"></a>Sestava doporučení k nákupu rezervovaných Instancí EC2

Základ pro využití prostředků cloudu je model na vyžádání, ve kterém vám být naúčtovány prostředky náklady pouze při použití. Neexistují žádné předem dané závazky – platíte jenom za využité, když je používáte.

AWS nabízí alternativu cenový model pro jeho elastické cloudové výpočetní (EC2) služby – rezervované instance (RI). Tento cenový model zaručuje uživatelé kapacitu kdykoli to bude potřebovat po dobu trvání rezervované instance. Rezervované instance významné cen nabízí oproti cenám na vyžádání. Uživatelé provádět na oplátku počátečního závazku využívání služeb pro použití virtuální instance. Závazek je vázán na konkrétní rodina, velikost, zóna dostupnosti (AZ) a operačního systému, období závazku (jeden nebo tři roky). Rezervované instance umožňuje efektivně plánovat budoucí kapacity, a jde o získání závazku zákazníky pomocí svých služeb AWS.

Tři možnosti platby pro rezervované instance, které jsou všechny předem:

- Hromadné součet za den 0, nabízí nejvyšší slevu
- Žádné předem – ve kterém náklady na rezervovanou Instanci platí v měsíčních splátek za celou dobu rezervované instance, nabízí nejnižší slevu
- Částečné předem, ve které ¼ - ½ ceny platí ještě před zahájením a rest v měsíčních splátek s zvýhodněná sazba, která je nižší, ale zavřete, všechny předem rychlost

Cloudyn vyhodnotí dobu provozu jednotlivých počítačů za posledních 30 dní. Cloudyn doporučuje nákup rezervované instance, pokud bude cenově výhodnější ke spuštění počítače s rezervovanou Instanci na aktuální úrovni doby provozu.

Tato sestava zobrazuje zdůvodnění svá doporučení nejvíce ušetřit za rok. Doporučení navrhnout nahradíte rezervované instance na vyžádání. Rezervované instance si můžete zakoupit přímo od sestavy.

Každá karta se otevře jako úplnou sestavu. Významné části karty patří:

- **EC2 dopad na nákup** rezervovaných instancí – Tato část poskytuje simulaci rozdílu mezi rezervovanými instancemi na vyžádání vs. Klikněte na tlačítko **přiblížit**, abyste zobrazili úplný EC2 sestavu dopadu nákupu na rezervované instance s filtry, které jsou již definovány pro vaše doporučení. Tato sestava zobrazuje dopad nákupu všech potenciálních nákupy rezervovaných Instancí. Můžete upravit očekávané průměrné doby provozu zobrazíte potenciál ukládání při nákupu rezervovaných instancí EC2.

- **Ukládání analýz** – Tato část poskytuje potenciální úspory a měsíc, ve kterém jsou úspory v rámci následujících doporučení Cloudyn k dispozici. Skutečné úspory a procento uložili se zvýrazní červeně.

- **Porovnání typu EC2** na úrovni rezervovaný – v této části se zvýrazňují nejvyšší návratnosti investic z doporučeného nasazení Cloudyn, včetně všech relevantních možností. Výsledky v této sestavě se předpokládá, že je počítač se spouští ve 100 % dostupnost. Kliknutím na tlačítko **přiblížit** otevřete podrobnou sestavu.

- **Instance v průběhu času** – Tato část zobrazuje rozpis všech instancí přidružených k doporučení, OnDemand, rezervovaných instancí a místě. Kliknutím na tlačítko **přiblížit** otevřete podrobnou sestavu.
- **Breakeven body** – v této části se zobrazuje tabulka všech možných doporučených nasazení a návratnost investic a měsíc, kdy dojde k návratnosti investic. Kliknutím na tlačítko **přiblížit** otevřete podrobnou sestavu.

### <a name="ec2-reservations-over-time-report"></a>Sestava EC2 rezervace v čase

Sestava čase rezervace EC2 sleduje stav využití zakoupených rezervovaných instancí EC2. Rozlišení sestavy můžete nastavit na hodinu, dne nebo týdne.

Sestavu, kterou chcete použijte:

- Zobrazit koupit rezervace, které jsou používány a nepoužívá se.
- Když přejdete k podrobnostem na řešení po hodinách zobrazíte využití rezervovaných Instancí za hodinu.

### <a name="savings-over-time-report"></a>Sestava úspory v čase

Sestava průběhu čas slouží k zobrazení úspory dosažené, pomocí rezervované instance, jakož i spot instance. Tato sestava zobrazuje návratnost investic dosáhnout v čase vyplývající z nákupy rezervovaných Instancí.

Chcete-li zobrazit úspory z části RIs, seskupte výsledky podle **cenového modelu** a vyberte možnost **rezervace**. Zobrazit RI úspory dosažené podle určitého účtu nebo instanci typu, přidejte k účtu nebo instance typu relevantní seskupování a filtrování.

Chcete-li zobrazit úspory z instance bodu použití, vyfiltrujte **model cen** tak, aby byl **bodový**. Rezervované instance a Spot instance se jako výchozí filtr pro tuto sestavu.

### <a name="rds-ri-buying-recommendations-report"></a>Sestava doporučení k nákupu rezervovaných Instancí vzdálené plochy

Sestava doporučení k nákupu rezervovaných Instancí vzdálené plochy se doporučuje, abyste kdy použít rezervované instance RDS místo instance na vyžádání.

Každá karta se otevře jako úplnou sestavu. Významné části karty patří:

- **Dopad na nákup vzdálené plochy RDS** – Tato část poskytuje simulaci rozdílu mezi rezervovanými instancemi na vyžádání vs. Klikněte na tlačítko **přiblížit** . zobrazí se úplná sestava dopadu nákupu VP rezervovaný s filtry, které jsou už definované pro vaše doporučení. Tato sestava umožňuje zobrazit dopad nákupu všech potenciálních nákupy rezervovaných Instancí.  Můžete upravit očekávané průměrné doby provozu a zjistit potenciální ukládání nákupu rezervované instance.
- **Ukládání analýz** – Tato část poskytuje potenciální úspory a měsíc, ve kterém jsou úspory v rámci následujících doporučení Cloudyn k dispozici. Skutečné úspory a procento uložili se zvýrazní červeně.

- **Porovnání typu RDS** rezervované – v této části se zvýrazňují nejdůležitější nejdůležitější možnosti nasazení, včetně všech relevantních možností. Výsledky v této sestavě se předpokládá, že je počítač se spouští ve 100 % dostupnost. Kliknutím na tlačítko **přiblížit** otevřete podrobnou sestavu pro vybraný počítač.
- **Instance v průběhu času – v** této části se zobrazuje rozpis všech instancí přidružených k doporučení, OnDemand, rezervovaných instancí a místě. Kliknutím na tlačítko **přiblížit** otevřete podrobnou sestavu.

- **Breakeven body** – v této části se zobrazuje tabulka všech možných doporučených nasazení a návratnost investic a měsíc, kdy dojde k návratnosti investic. Kliknutím na tlačítko **přiblížit** otevřete podrobnou sestavu.

### <a name="rds-reservations-over-time-report"></a>Sestavy licencí VP rezervace v čase

Pomocí vzdálené plochy rezervace v čase sestavy zobrazit rozpis vaše používaných a nevyužité rezervace během zadaného období.

### <a name="reserved-instance-purchase-impact-report"></a>Rezervované Instance dopadu nákupu sestavy

Sestava dopadu nákupu rezervované instance EC2 umožňuje simulovat náklady na rezervovanou instanci a náklady na vyžádání v čase. Může pomoct při rozhodování lépe nákupu. Upravte filtry, např. Průměrná doba běhu, termín, platformy a ostatními a informovaně se rozhodovat při zvažování nákupy rezervovaných Instancí.

### <a name="cost-effective-sizing-recommendations-report"></a>Nákladově efektivní doporučení velikosti sestav

Sestava doporučení velikosti cenově efektivního obsahuje výsledky pro AWS a Azure. Pro uživatele AWS nákupy rezervovaných Instancí se berou v úvahu a výsledky nejsou zahrnuté počítače, které běží jako rezervované instance. Tato sestava obsahuje seznam instancí nedostatečně využité, které jsou kandidáty na downsize. Doporučení jsou založená na vaše data o využití a výkonu za posledních 30 dní. V každé doporučení je seznam kandidátů downsize, odůvodnění ke downsize a odkaz, chcete-li zobrazit podrobné informace a metriky výkonu instance. A když relevantní doporučení dokáží změnu typů instancí novější generace.

Nelze stáhnout seznam instance ID, která se doporučuje, aby downsize z této sestavy. Pokud chcete stáhnout ID Instance, použijte sestavu všech doporučení velikosti.

Podívejte se na následující příklad downsizing:

Můžete mít šest m3.xlarge spuštěné instance. Cloudyn analýza ukazuje, že pět z nich mají nízké využití procesoru. Vezměte v úvahu downsizing je.

V dopad náklady se počítá dopad náklady. V tomto příkladu tak, že rozbalíte položku řádku vidíte, že aktuální ceny pro jednu instanci m3.xlarge (Linux/Unix) stojí 0.266 $ za hodinu a náklady na instanci (Linux/Unix) jeden m3.large 0.133 $ za hodinu. Roční náklady tedy 11,651 pro pět instancí m3.xlarge spuštěné ve 100 % využití. Roční náklady jsou 5,825 pro pět instancí m3.large spuštěné ve 100 % využití. Potenciální úspory jsou 5,825 $.

K zobrazení justifications nákladově efektivního nastavení velikosti, klikněte na + rozbalte položky řádku. V **podrobnostech**:

- V části **odůvodnění doporučení** se zobrazuje aktuální nasazení a počet instancí doporučených pro klidnějších.
- Část **dopad nákladů** zobrazuje výpočet, který se používá k určení potenciální úspory.
- V části **možné roční úspory** se zobrazuje potenciální roční úspora při možnost doporučení na Cloudyn.

### <a name="all-sizing-recommendations-report"></a>Všechny sestavy doporučení velikosti

Tato sestava obsahuje seznam instancí nedostatečně využité, které jsou kandidáty na downsize. Doporučení jsou založené na datech využití a výkonu za posledních 30 dní. V jednotlivých doporučení uvidíte veškeré podrobnosti a metriky výkonu instance.

Pokud jste zakoupili AWS rezervované instance, tato sestava obsahuje výsledky pro všechny spuštěné instance, včetně instancí spuštěných jako rezervované instance.

Použijte všechna doporučení velikosti sestavu do:

- Zobrazit seznam všech instancí, které jsou kandidáty na downsize.
- Exportujte seznam sestav obsahující Instance názvy a identifikátory.

Chcete-li zobrazit podrobnosti o doporučení pro určitou instanci, kliknutím na tlačítko **+** rozbalte podrobnosti. V části Podrobnosti o doporučení poskytuje přehled doporučení.

V části **značky** najdete seznam klíčů a hodnot značek pro vybranou instanci. Použití značek k filtrování v části v levém podokně.

Část **využití procesoru** poskytuje využití CPU pro instanci v průběhu posledního měsíce za den.

Klikněte na graf tak, aby k podrobnostem a otevřete instanci procesoru nad čas sestavu pro zobrazení rozpisu instancí.

- Použijte **pole Zobrazit/skrýt** pro přidání nebo odebrání polí: časové razítko, prům. CPU, minimální procesor, maximální využití procesoru.
- Pomocí **rozsahu kalendářních dat** můžete zadat datum nebo rozsah dat a přejít k podrobnostem o konkrétní InstanceID.
- Použijte **Rozšířené filtry** k zobrazení všech nebo konkrétního ID instance.
- Kliknutím na tlačítko **přiblížit** otevřete sestavu využití CPU.

Pokud instance není sledována po dobu 30 dnů, zobrazí se neúplná data.

Část **využití paměti (GB)** poskytuje informace o využité paměti. Pro uživatele AWS paměti metriky nejsou automaticky k dispozici a potřeba přidat na instanci prostřednictvím AWS. Poplatky za AWS, můžete k zapnutí metrik paměti pro instance EC2.

V části **využití paměti (%)** se zobrazuje procento využité paměti.

Část **síťový vstup přenosů** zobrazuje snímek v čase provozu sítě, průměr a maximum pro vybranou instanci. Najeďte myší řádky se zobrazí datum a maximální provozu pro tento čas. Kliknutím na tlačítko **přiblížit** otevřete sestavu síťový vstupní provoz.

Část **síťový výstup přenosů** zobrazuje snímek výstupního provozu sítě pro vybranou instanci. Najeďte myší řádky se zobrazí datum a maximální provozu pro tento čas. Klikněte na tlačítko **přiblížit** a otevřete sestavu výstup síťového provozu.

### <a name="instance-metrics-explorer-report"></a>Sestava instance Průzkumník metrik

Průzkumník metrik Instance sestava zobrazuje metriky výkonu cloudu na jednu instanci. Použití sestav k zobrazení instance, které jsou nad nebo pod jiným podle procesoru, paměti a sítě prahové hodnoty metrik.

Chcete-li zobrazit výkon cloudu na instanci:

1. V **oblasti data**vyberte rozsah dat, pro který chcete zobrazit výkon.
2. V **tagu**vyberte všechny značky, které chcete zobrazit.
3. V části **filtry**vyberte filtry, které chcete zobrazit v sestavě.
4. V **rozšířených filtrech**upravte prahové hodnoty sestavy pro:
    - Průměrné využití procesoru
    - Maximální počet procesorů
    - Průměrná paměť
    - Maximální velikost paměti
5. V části **Rozšířené filtry**klikněte na tlačítko **Zobrazit** a vyberte typ instancí, které chcete zobrazit.

Pokud chcete zobrazit konkrétní instanci metriky v čase:

- Přejděte na instanci Průzkumník metrik sestavu a kliknutím na **+** zobrazte podrobnosti.

### <a name="rds-sizing-recommendations-report"></a>Sestava doporučení velikosti vzdálené plochy

Sestava doporučení velikosti vzdálené plochy poskytuje vzdálené plochy pro změnu velikosti doporučení k optimalizaci využití cloudu. Poskytuje seznam nedostatečně využité instancí, které jsou kandidáty na downsize. Cloudyn doporučení jsou založená na data o využití a výkonu z posledních 30 dní. Můžete filtrovat podle názvu účtu, oblasti, typu Instance a stav doporučení.

### <a name="sizing-threshold-manager-report"></a>Správce prahová hodnota velikosti sestav

Doporučení integrované velikosti od Cloudyn vypočítají na základě komplexní algoritmus k pošlete nám návrhy, určení správné velikosti. Můžete upravit prahové hodnoty pro downsizing doporučení.

Chcete-li ručně upravit doporučení velikosti prahové hodnoty:

1. Ve Správci prahová hodnota velikosti upravte následující prahové hodnoty, jak potřebujete:
    - Průměrné využití procesoru %
    - Maximální využití procesoru %
    - Průměrná paměť %
    - Maximální velikost paměti %
3. Kliknutím na **použít** uložte změny.
4. Změny se okamžitě použije pro všechna vaše doporučení.

Chcete-li obnovit výchozí prahové hodnoty:

- V nabídce Velikost správce prahové hodnoty klikněte na **Obnovit výchozí hodnoty**.

### <a name="compute-instance-types-report"></a>Výpočetní Instance typů sestav

Pomocí sestavy typy instancí na:

- Zobrazení typů instancí služby, rodina, název rozhraní API a název.
- Zobrazit podrobnosti, jako je procesor, ECU, RAM a síť.

Pomocí **vyhledávání** můžete najít konkrétní položky řádku.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak používat sestavy, včetně postupu přizpůsobení nebo ukládání a plánování sestav, najdete v tématu [porozumění sestavám nákladů](understanding-cost-reports.md).
- Přečtěte si o řídicích panelech, které jsou zahrnuté v Cloudyn, a o tom, jak vytvářet vlastní řídicí panely, najdete v tématu [zobrazení metriky klíčových nákladů pomocí řídicích panelů](dashboards.md).

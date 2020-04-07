---
title: Prevence a analýza neočekávaných poplatků s využitím služby Azure Cost Management a fakturace
description: Zjistěte, jak se vyhnout neočekávaným poplatkům na faktuře za Azure a jak u účtu Azure využívat funkce pro sledování a správu nákladů.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 3/30/2020
ms.author: banders
ms.openlocfilehash: 79af6f78e8e9bf93c49deafe79f6a421cbb77d1a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475257"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Jak zabránit neočekávaným poplatkům pomocí fakturace a řízení nákladů v Azure

Při registraci Azure máte k dispozici několik možností, které vám pomohou získat lepší představu o útratě:

- Zjistěte odhadované náklady před přidáním služeb s využitím [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) a ceníku Azure, případně při přidávání služeb na webu Azure Portal.
- Monitorujte náklady s využitím [rozpočtů](../costs/tutorial-acm-create-budgets.md), [upozornění](../costs/cost-mgt-alerts-monitor-usage-spending.md) a [analýzy nákladů](../costs/quick-acm-cost-analysis.md).
- Projděte si poplatky na vaší faktuře a porovnejte je se [soubory s podrobnými informacemi o využití](download-azure-invoice-daily-usage-date.md).
- Integrujte fakturační údaje a data o využití s vlastním systémem generování sestav s využitím rozhraní API pro [fakturaci](https://docs.microsoft.com/rest/api/billing/) a [spotřebu](https://docs.microsoft.com/rest/api/consumption/).
- Využijte další prostředky a nástroje pro zákazníky se smlouvou Enterprise (EA), zákazníky CSP (Cloud Solution Provider) a zákazníky s nabídkou Azure Sponsorship.
- Využijte [některé z nejoblíbenějších služeb Azure zdarma po dobu 12 měsíců](create-free-services.md), které jsou k dispozici v rámci [bezplatného účtu Azure](https://azure.microsoft.com/free/). Spolu s níže uvedenými doporučeními si přečtěte, jak se [vyhnout platbám za bezplatný účet](avoid-charges-free-account.md).

Pokud potřebujete zrušit vaše předplatné Azure, přečtěte si téma [Zrušení předplatného Azure](cancel-azure-subscription.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Zjištění odhadovaných nákladů před přidáním služeb Azure

K odhadu nákladů na používání služeb Azure použijte některý z následujících nástrojů:
- Cenová kalkulačka Azure
- Ceník Azure
- portál Azure

Na obrázcích v následujících částech jsou zobrazeny příklady cen v amerických dolarech.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Odhad nákladů online pomocí cenové kalkulačky

Pokud chcete zjistit odhadované měsíční náklady na službu, kterou chcete přidat, použijte [cenovou kalkulačku](https://azure.microsoft.com/pricing/calculator/). Můžete změnit měnu a získat odhad ve vaší místní měně.

![Snímek obrazovky s nabídkou cenové kalkulačky](./media/getting-started/pricing-calc.png)

Můžete zobrazit odhadované náklady na jakoukoli službu Azure. Například na následujícím snímku obrazovky jsou odhadované náklady na virtuální počítač A1 s Windows 66,96 USD za měsíc za výpočetní čas, pokud ho necháte běžet celou dobu:

![Snímek obrazovky s cenovou kalkulačkou, která ukazuje odhadované náklady na virtuální počítač A1 s Windows za měsíc](./media/getting-started/pricing-calcvm.png)

Další informace o cenách najdete v [nejčastějších dotazech ohledně cen](https://azure.microsoft.com/pricing/faq/). Pokud si chcete promluvit s prodejcem Azure, zavolejte na telefonní číslo uvedené na začátku stránky s nejčastějšími dotazy.

### <a name="view-and-download-azure-price-sheet"></a>Zobrazení a stažení ceníku Azure

Ceník pro váš účet Azure můžete zobrazit a sáhnout, pokud máte přístup k Azure prostřednictvím smlouvy Enterprise (EA) nebo smlouvy se zákazníkem Microsoftu (MCA). Ceník je soubor aplikace Excel, který obsahuje ceny všech služeb Azure. Další informace najdete v tématu [Zobrazení a stažení cen Azure](ea-pricing.md).

### <a name="review-estimated-costs-in-the-azure-portal"></a>Zjištění odhadovaných nákladů na webu Azure Portal

Při přidávání služeb můžete zobrazit odhadované měsíční náklady na webu Azure Portal. Když například zvolíte velikost virtuálního počítače s Windows, uvidíte odhadované měsíční náklady za výpočetní čas:

![Příklad: virtuální počítač A1 s Windows zobrazující odhadované náklady za měsíc](./media/getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>Monitorování nákladů při používání služeb Azure
Náklady můžete monitorovat pomocí následujících nástrojů:

- Rozpočty a upozornění na náklady
- Analýza nákladů

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Sledování nákladů s využitím rozpočtů a upozornění na náklady

Pro účely řízení nákladů vytvořte [rozpočty](../costs/tutorial-acm-create-budgets.md) a nastavte [upozornění](../costs/cost-mgt-alerts-monitor-usage-spending.md), která vás a účastníky automaticky upozorní na anomálie ve výdajích a nadměrné výdaje.

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a>Zkoumání a analýza nákladů s využitím analýzy nákladů

Po spuštění služeb Azure sledujte svou útratu v Azure tím, že budete pravidelně kontrolovat poplatky. Pomocí analýzy nákladů můžete zjistit původ nákladů na využití Azure.

1. Navštivte stránku [Cost Management a fakturace na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

2. Kliknutím na **Analýza nákladů** na levé straně obrazovky zobrazíte aktuální náklady rozdělené podle různých pivotů, jako je služba, umístění nebo předplatné. Po přidání nějaké služby nebo provedení nákupu počkejte 24 hodin, než se údaje zobrazí. Ve výchozím nastavení se v analýze nákladů zobrazí náklady pro obor, ve kterém se právě nacházíte. Například na následujícím snímku obrazovky se zobrazují náklady pro fakturační účet Contoso. Pomocí možnosti Obor můžete v analýze nákladů přepnout na jiný obor. Další informace o oborech najdete v tématu [Principy oborů a práce s nimi](../costs/understand-work-scopes.md#scopes).

    ![Snímek obrazovky se zobrazením analýzy nákladů na webu Azure Portal](./media/getting-started/cost-analysis.png)

4. Můžete filtrovat podle různých vlastností, jako jsou značky, typ prostředku nebo časový rozsah. Pokud chcete přidat filtr pro nějakou vlastnost, klikněte na **Přidat filtr** a vyberte hodnoty, podle kterých chcete filtrovat. Výběrem možnosti **Exportovat** můžete zobrazení exportovat do souboru hodnot oddělených čárkami (.csv).

5. Kliknutím na popisky grafu navíc můžete zobrazit historii denní útraty pro daný popisek. Příklad: Na následujícím snímku obrazovky se po kliknutí na virtuální počítače zobrazí denní náklady na provoz virtuálních počítačů.

    ![Snímek obrazovky se zobrazením historie výdajů na webu Azure Portal](./media/getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>Optimalizace a snížení nákladů
Pokud nejste obeznámeni s principy řízení nákladů, přečtěte si článek [Využití investice do cloudu na maximum se službou Azure Cost Management](../costs/cost-mgt-best-practices.md).

Také na webu Azure Portal můžete optimalizovat a snížit náklady na Azure pomocí automatického vypínání virtuálních počítačů a doporučení Advisoru (Poradce).

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Zvážení funkcí pro snížení nákladů, jako je automatické vypínání virtuálních počítačů

Podle situace můžete na webu Azure Portal nakonfigurovat automatické vypínání virtuálních počítačů. Další informace najdete v článku [Automatické vypínání virtuálních počítačů pomocí Azure Resource Manageru](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Snímek obrazovky s možností automatického vypínání na portálu](./media/getting-started/auto-shutdown.png)

Automatické vypnutí není totéž jako vypnutí virtuálního počítače pomocí možností napájení. Při automatickém vypnutí se virtuální počítače zastaví a zruší se jejich přidělení, takže nenabíhají další poplatky za využití. Další informace najdete v nejčastějších dotazech ke stavům virtuálních počítačů pro [virtuální počítače s Linuxem](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Další funkce pro snížení nákladů u vývojových a testovacích prostředí popisuje [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Zapnutí a kontrola doporučení Azure Advisoru (Poradce)

[Azure Advisor](../../advisor/advisor-overview.md) (Poradce) pomáhá snižovat náklady tím, že identifikuje prostředky s nízkým využitím. Na webu Azure Portal vyhledejte **Advisor**:

![Snímek obrazovky s tlačítkem Advisor (Poradce) na webu Azure Portal](./media/getting-started/advisor-button.png)

Na levé straně vyberte **Náklady**. Na kartě **Náklady** se zobrazí užitečná doporučení:

![Snímek obrazovky s příkladem doporučení Advisoru (Poradce) ohledně nákladů](./media/getting-started/advisor-action.png)

Absolvujte výukový kurz [Optimalizace nákladů na základě doporučení](../costs/tutorial-acm-opt-recommendations.md), který se věnuje doporučením Advisoru (Poradce) ohledně úspor nákladů.


## <a name="integrate-with-billing-and-consumption-apis"></a>Integrace s využitím rozhraní API pro fakturaci a spotřebu

Pomocí rozhraní API pro [fakturaci](https://docs.microsoft.com/rest/api/billing/) a [spotřebu](https://docs.microsoft.com/rest/api/consumption/) Azure můžete fakturační údaje a data o nákladech získat programově. Využití, které je vám účtováno, získáte zkombinováním rozhraní RateCard API a Usage API. Další informace najdete v článku [Získání přehledu o spotřebě prostředků Microsoft Azure](usage-rate-card-overview.md).

## <a name="additional-resources-and-special-cases"></a><a name="other-offers"></a> Další prostředky a speciální případy

### <a name="ea-csp-and-sponsorship-customers"></a>Zákazníci využívající EA, CSP a Sponsorship
Začněte tím, že kontaktujete svého account manažera nebo partnera Azure.

| Nabídka | Zdroje a prostředky |
|-------------------------------|-----------------------------------------------------------------------------------|
| Smlouva Enterprise (EA) | [Portál EA](https://ea.azure.com/), [nápověda](https://ea.azure.com/helpdocs) a [sestava Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| CSP (Cloud Solution Provider) | Kontaktujte svého poskytovatele. |
| Azure Sponsorship | [Portál Sponsorship](https://www.microsoftazuresponsorships.com/) |

Pokud se staráte o IT ve velké organizaci, doporučujeme, abyste si přečetli článek o [používání Azure v podnicích](/azure/architecture/cloud-adoption-guide/subscription-governance) a [dokument white paper o podnikovém IT](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (soubor .pdf ke stažení, jen v angličtině).

### <a name="enterprise-agreement-cost-views-in-the-azure-portal"></a><a name="EA"></a> Zobrazení nákladů u smlouvy Enterprise na webu Azure Portal

Zobrazení nákladů u smluv Enterprise je v současnosti ve veřejné verzi Preview. Měli byste vědět:

- Náklady na předplatné vycházejí z využití a nezahrnují předplacené částky, nadlimitní využití, zahrnuté množství, opravy a daně. Skutečné poplatky se počítají na úrovni prováděcí smlouvy.
- Částky zobrazené na webu Azure Portal se mohou lišit od částek na portálu Enterprise. Aktualizace na portálu Enterprise se na webu Azure Portal mohou projevit až za několik minut.
- Pokud nevidíte žádné náklady, může to mít jeden z následujících důvodů:
    - Nemáte oprávnění na úrovni předplatného. Abyste viděli zobrazení podnikových nákladů, musíte být čtenářem fakturace, čtenářem, přispěvatelem nebo vlastníkem na úrovni předplatného.
    - Jste vlastníkem účtu a váš správce prováděcí smlouvy zakázal nastavení pro zobrazení nákladů vlastníkovi účtu.  Požádejte správce prováděcí smlouvy o přístup k nákladům.
    - Jste správcem oddělení a váš správce prováděcí smlouvy zakázal nastavení pro **zobrazení nákladů správci oddělení**.  Požádejte správce prováděcí smlouvy o přístup.
    - Koupili jste Azure přes distribučního partnera a tento partner neuvolnil informace o ceně.  
- Pokud na portálu Enterprise aktualizujete nastavení související s přístupem k nákladům, projeví se změny na webu Azure Portal se zpožděním několika minut.
- Limit útraty a pokyny k fakturaci se nevztahují na předplatná EA.

### <a name="check-your-subscription-and-access"></a>Kontrola předplatného a přístupu

K zobrazení nákladů potřebujete přístup k informacím o nákladech nebo fakturačním údajům na úrovni účtu nebo předplatného. Úroveň přístupu se liší podle typu fakturačního účtu. Další informace o fakturačních účtech a kontrole typu fakturačního účtu najdete v tématu [Zobrazení fakturačních účtů na webu Azure Portal](view-all-accounts.md).

Pokud máte přístup k Azure prostřednictvím fakturačního účtu programu Microsoft Online Service (MOSP), projděte si téma [Správa přístupu k fakturačním údajům pro Azure](manage-billing-access.md).

Pokud máte přístup k Azure prostřednictvím fakturačního účtu pro smlouvu Enterprise (EA), projděte si téma [Principy rolí pro správu smlouvy Azure Enterprise v Azure](understand-ea-roles.md).

Pokud máte přístup k Azure prostřednictvím fakturačního účtu pro smlouvu se zákazníkem Microsoftu (MCA), projděte si téma [Principy rolí pro správu smlouvy se zákazníkem Microsoftu v Azure](understand-mca-roles.md).

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Vyžádání kreditu smlouvy o úrovni služeb za servisní incident

Smlouva o úrovni služeb (SLA) popisuje závazky Microsoftu týkající se dostupnosti a možností připojení. Servisní incident je nahlášen, když se služby Azure potýkají s problémem, který má dopad na dostupnost nebo konektivitu, a často se označuje jako *výpadek*. Pokud nedosáhneme a neudržíme úrovně jednotlivých služeb, jak je popsáno ve smlouvě SLA, můžete mít nárok na kredit části vašich měsíčních poplatků za služby.

Kredit si vyžádáte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/). Pokud máte více účtů, nezapomeňte použít ten, který byl ovlivněn výpadkem Azure.
2. Vytvořte novou žádost o podporu.
3. V seznamu **Typ problému** vyberte **Fakturace**.
4. V seznamu **Typ problému** vyberte **Žádost o refundaci**.
5. Popište, že žádáte o kredit na základě smlouvy SLA, uveďte datum/čas/časové pásmo a dotčené služby (virtuální počítače, weby atd.).
6. Ověřte své kontaktní údaje a výběrem možnosti **Vytvořit** žádost odešlete.

Prahové hodnoty smlouvy SLA se liší podle služby. Například SQL na úrovni Web má smlouvu SLA na 99,9 %, virtuální počítače mají smlouvu SLA na 99,95 % a SQL úrovně Standard má smlouvu SLA na 99,99 %.

U některých služeb musí být splněny určité předpoklady, aby šla smlouva SLA uplatnit. Například virtuální počítače musí mít nasazené nejméně dvě instance ve stejné skupině dostupnosti.

Další informace najdete v dokumentaci ke [smlouvám o úrovni služeb](https://azure.microsoft.com/support/legal/sla/) a [souhrnu smluv SLA pro služby Azure](https://azure.microsoft.com/support/legal/sla/summary/).

## <a name="analyze-unexpected-charges"></a>Analýza neočekávaných poplatků

Infrastruktura cloudových prostředků, kterou jste pro vaši organizaci vytvořili, je pravděpodobně velmi složitá. Řada typů prostředků Azure může mít různé typy poplatků. Prostředky Azure můžou vlastnit různé týmy ve vaší organizaci a ty můžou mít pro různé prostředky různé typy modelu fakturace. Pokud chcete lépe porozumět poplatkům, zahajte analýzu s využitím jedné nebo několika strategií v následujících částech.

### <a name="review-your-invoice-and-identify-the-resource-that-is-responsible-for-the-charge"></a>Kontrola faktury a identifikace prostředků zodpovědných za poplatky

Způsob, jakým nakupujete služby Azure, vám pomůže určit, jakou metodologii a jaké nástroje máte k dispozici pro identifikaci prostředků přidružených k poplatkům. Pokud chcete zjistit, která metodologie se vás týká, nejprve [určete typ vaší nabídky Azure](../costs/understand-cost-mgt-data.md#determine-your-offer-type). Pak identifikujte kategorii zákazníka v seznamu [podporovaných nabídek Azure](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

Následující články obsahují podrobný postup s vysvětlením, jak zkontrolovat fakturu na základě typu zákazníka. Jednotlivé články obsahují pokyny ke stažení souboru CSV s podrobnostmi o využití a nákladech za dané fakturační období.

- [Proces kontroly faktury za průběžné platby](../understand/review-individual-bill.md#compare-invoiced-charges-with-usage-file)
- [Proces kontroly faktury za smlouvu Enterprise](../understand/review-enterprise-agreement-bill.md)
- [Proces kontroly faktury za smlouvu se zákazníkem Microsoftu](../understand/review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Proces kontroly faktury za smlouvu s partnerem Microsoftu](../understand/review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

Faktura za Azure obsahuje agregované poplatky za měsíc podle jednotlivých _měřičů_. Měřiče slouží ke sledování využití prostředků v průběhu času a používají se k výpočtu faktury. Když vytvoříte určitý prostředek Azure, například virtuální počítač, vytvoří se pro něj jedna nebo více instancí měřiče.

Soubor CSV s informacemi o využití můžete filtrovat podle _názvu měřiče_, který se zobrazuje na faktuře, kterou chcete analyzovat, a zobrazit tak všechny řádkové položky vztahující se k danému měřiči. _ID instance_ u řádkové položky odpovídá skutečnému prostředku Azure, který je za poplatek zodpovědný.

Jakmile daný prostředek identifikujete, můžete pomocí analýzy nákladů ve službě Azure Cost Management pokračovat v analýze nákladů souvisejících s tímto prostředkem. Další informace o používání analýzy nákladů najdete v tématu [Začínáme s analýzou nákladů](../costs/quick-acm-cost-analysis.md).

### <a name="identify-spikes-in-cost-over-time"></a>Identifikace špiček v nákladech v průběhu času

V některých případech se může stát, že nevíte, jaké nedávné náklady způsobily změny účtovaných poplatků. Pokud chcete pochopit, co se změnilo, můžete pomocí analýzy nákladů [zobrazit denní nebo měsíční rozpis nákladů v průběhu času](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month). Po vytvoření příslušného zobrazení můžete poplatky seskupit podle **služby** nebo **prostředku** a identifikovat změny. Pokud chcete získat lepší vizualizaci dat, můžete také změnit zobrazení na **spojnicový** graf.

![Příklad ukazující náklady v průběhu času v analýze nákladů](./media/getting-started/costs-over-time.png)

### <a name="determine-resource-pricing-and-understand-its-billing-model"></a>Určení cen prostředků a vysvětlení modelu jejich fakturace

Za jeden prostředek se můžou účtovat poplatky napříč několika produkty a službami Azure. Další informace o cenách jednotlivých služeb Azure najdete na stránce s [cenami Azure podle konkrétního produktu](https://azure.microsoft.com/pricing/#product-pricing). Například u jednoho virtuálního počítače vytvořeného v Azure se můžou vytvořit následující měřiče sledující jeho využití. Pro každý z nich můžou platit jiné ceny.

- Výpočetní hodiny
- Hodiny IP adresy
- Přenos příchozích dat
- Přenos odchozích dat
- Spravovaný disk úrovně Standard
- Operace spravovaných disků úrovně Standard
- Standardní V/V – disk
- Standardní V/V – čtení objektů blob bloku
- Standardní V/V – zápis objektů blob bloku
- Standardní V/V – odstranění objektů blob bloku

Po vytvoření virtuálního počítače začne každý měřič generovat záznamy o využití. Využití a cena z měřiče se sledují v měřicím systému Azure. Na měřiče, které byly použity k výpočtu faktury, se můžete podívat v souboru CSV s využitím.

### <a name="find-the-people-responsible-for-the-resource-and-engage-them"></a>Vyhledání lidí zodpovědných za konkrétní prostředky a jejich zapojení

Týmy zodpovědné za konkrétní prostředky často ví o změnách, které se s prostředky prováděly. Jejich zapojení vám může pomoct zjistit, proč se určité poplatky účtovaly. Vlastnící tým například mohl nedávno vytvořit prostředek, aktualizovat jeho skladovou položku (a tím změnit sazbu za prostředek) nebo zvýšit zatížení prostředku kvůli změnám kódu. Pokračujte a přečtěte si následující části, ve kterých najdete informace o dalších technikách umožňujících určit, kdo je vlastníkem prostředku.

#### <a name="analyze-the-audit-logs-for-the-resource"></a>Analýza protokolů auditu pro prostředek

Pokud máte oprávnění k zobrazení prostředku, měli byste mít přístup k jeho protokolům auditu. Projděte si protokoly a zjistěte, který uživatel byl zodpovědný za poslední změny prostředku. Další informace najdete v tématu [Zobrazení a načtení událostí protokolu aktivit Azure](../../azure-monitor/platform/activity-log-view.md).

#### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>Analýza uživatelských oprávnění k nadřazenému oboru prostředku

Lidé, kteří mají k předplatnému nebo skupině prostředků přístup pro zápis, obvykle mají informace o vytvořených prostředcích. Měli by být schopni vysvětlit účel daného prostředku nebo vás odkázat na osobu, která ho zná. Pokud chcete zjistit, kteří lidé mají oprávnění k určitému oboru předplatného, přečtěte si téma [Zobrazení přiřazení rolí](../../role-based-access-control/check-access.md#view-role-assignments). Podobný postup můžete použít i u skupin prostředků.

### <a name="get-help-to-identify-charges"></a>Získání nápovědy k identifikaci poplatků

Pokud jste použili výše uvedené strategie, ale stále nechápete, proč se vám účtovaly některé poplatky, nebo pokud potřebujete pomoc s jinými problémy s fakturací, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- Seznamte se s [limity útraty](spending-limit.md), abyste neutráceli víc, než musíte.
- Začněte [analyzovat náklady na Azure](../costs/quick-acm-cost-analysis.md).

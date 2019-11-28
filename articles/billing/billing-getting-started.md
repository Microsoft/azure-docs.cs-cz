---
title: Jak zabránit neočekávaným nákladům a spravovat fakturaci v Azure
description: Zjistěte, jak se vyhnout neočekávaným poplatkům ve faktuře za Azure. U účtu Azure můžete využít funkce pro sledování a řízení nákladů.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e5cdd7181e680dbc7406118afeb4aeb837d3aaf2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223912"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Jak zabránit neočekávaným poplatkům pomocí fakturace a řízení nákladů v Azure

Při registraci Azure máte k dispozici několik možností, které vám pomohou získat lepší představu o útratě:

- Zjistěte odhadované náklady před přidáním služeb s využitím [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) a ceníku Azure, případně při přidávání služeb na webu Azure Portal.
- Monitorujte náklady s využitím [rozpočtů](../cost-management/tutorial-acm-create-budgets.md), [upozornění](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) a [analýzy nákladů](../cost-management/quick-acm-cost-analysis.md).
- Projděte si poplatky na vaší faktuře a porovnejte je se [soubory s podrobnými informacemi o využití](billing-download-azure-invoice-daily-usage-date.md).
- Integrujte fakturační údaje a data o využití s vlastním systémem generování sestav s využitím rozhraní API pro [fakturaci](https://docs.microsoft.com/rest/api/billing/) a [spotřebu](https://docs.microsoft.com/rest/api/consumption/).
- Využijte další prostředky a nástroje pro zákazníky se smlouvou Enterprise (EA), zákazníky CSP (Cloud Solution Provider) a zákazníky s nabídkou Azure Sponsorship.
- Využijte [některé z nejoblíbenějších služeb Azure zdarma po dobu 12 měsíců](billing-create-free-services-included-free-account.md), které jsou k dispozici v rámci [bezplatného účtu Azure](https://azure.microsoft.com/free/). Spolu s níže uvedenými doporučeními si přečtěte, jak se [vyhnout platbám za bezplatný účet](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Zjištění odhadovaných nákladů před přidáním služeb Azure

K odhadu nákladů na používání služeb Azure použijte některý z následujících nástrojů:
- Cenová kalkulačka Azure
- Ceník Azure
- portál Azure

Na obrázcích v následujících částech jsou zobrazeny příklady cen v amerických dolarech.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Odhad nákladů online pomocí cenové kalkulačky

Pokud chcete zjistit odhadované měsíční náklady na službu, kterou chcete přidat, použijte [cenovou kalkulačku](https://azure.microsoft.com/pricing/calculator/). Můžete změnit měnu a získat odhad ve vaší místní měně.

![Snímek obrazovky s nabídkou cenové kalkulačky](./media/billing-getting-started/pricing-calc.png)

Můžete zobrazit odhadované náklady na jakoukoli službu Azure. Například na následujícím snímku obrazovky jsou odhadované náklady na virtuální počítač A1 s Windows 66,96 USD za měsíc za výpočetní čas, pokud ho necháte běžet celou dobu:

![Snímek obrazovky s cenovou kalkulačkou, která ukazuje odhadované náklady na virtuální počítač A1 s Windows za měsíc](./media/billing-getting-started/pricing-calcvm.png)

Další informace o cenách najdete v [nejčastějších dotazech ohledně cen](https://azure.microsoft.com/pricing/faq/). Pokud si chcete promluvit s prodejcem Azure, zavolejte na telefonní číslo uvedené na začátku stránky s nejčastějšími dotazy.

### <a name="view-and-download-azure-price-sheet"></a>Zobrazení a stažení ceníku Azure

Ceník pro váš účet Azure můžete zobrazit a sáhnout, pokud máte přístup k Azure prostřednictvím smlouvy Enterprise (EA) nebo smlouvy se zákazníkem Microsoftu (MCA). Ceník je soubor aplikace Excel, který obsahuje ceny všech služeb Azure. Další informace najdete v tématu [Zobrazení a stažení cen Azure](billing-ea-pricing.md).

### <a name="review-estimated-costs-in-the-azure-portal"></a>Zjištění odhadovaných nákladů na webu Azure Portal

Při přidávání služeb můžete zobrazit odhadované měsíční náklady na webu Azure Portal. Když například zvolíte velikost virtuálního počítače s Windows, uvidíte odhadované měsíční náklady za výpočetní čas:

![Příklad: virtuální počítač A1 s Windows zobrazující odhadované náklady za měsíc](./media/billing-getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>Monitorování nákladů při používání služeb Azure
Náklady můžete monitorovat pomocí následujících nástrojů:

- Rozpočty a upozornění na náklady
- Analýza nákladů

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Sledování nákladů s využitím rozpočtů a upozornění na náklady

Pro účely řízení nákladů vytvořte [rozpočty](../cost-management/tutorial-acm-create-budgets.md) a nastavte [upozornění](../cost-management/cost-mgt-alerts-monitor-usage-spending.md), která vás a účastníky automaticky upozorní na anomálie ve výdajích a nadměrné výdaje.

### <a name="costs"></a>Zkoumání a analýza nákladů s využitím analýzy nákladů

Po spuštění služeb Azure sledujte svou útratu v Azure tím, že budete pravidelně kontrolovat poplatky. Pomocí analýzy nákladů můžete zjistit původ nákladů na využití Azure.

1. Navštivte stránku [Cost Management a fakturace na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

2. Kliknutím na **Analýza nákladů** na levé straně obrazovky zobrazíte aktuální náklady rozdělené podle různých pivotů, jako je služba, umístění nebo předplatné. Po přidání nějaké služby nebo provedení nákupu počkejte 24 hodin, než se údaje zobrazí. Ve výchozím nastavení se v analýze nákladů zobrazí náklady pro obor, ve kterém se právě nacházíte. Například na následujícím snímku obrazovky se zobrazují náklady pro fakturační účet Contoso. Pomocí možnosti Obor můžete v analýze nákladů přepnout na jiný obor. Další informace o oborech najdete v tématu [Principy oborů a práce s nimi](../cost-management/understand-work-scopes.md#scopes).

    ![Snímek obrazovky se zobrazením analýzy nákladů na webu Azure Portal](./media/billing-getting-started/cost-analysis.png)

4. Můžete filtrovat podle různých vlastností, jako jsou značky, typ prostředku nebo časový rozsah. Pokud chcete přidat filtr pro nějakou vlastnost, klikněte na **Přidat filtr** a vyberte hodnoty, podle kterých chcete filtrovat. Výběrem možnosti **Exportovat** můžete zobrazení exportovat do souboru hodnot oddělených čárkami (.csv).

5. Kliknutím na popisky grafu navíc můžete zobrazit historii denní útraty pro daný popisek. Příklad: Na následujícím snímku obrazovky se po kliknutí na virtuální počítače zobrazí denní náklady na provoz virtuálních počítačů.

    ![Snímek obrazovky se zobrazením historie výdajů na webu Azure Portal](./media/billing-getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>Optimalizace a snížení nákladů
Pokud nejste obeznámeni s principy řízení nákladů, přečtěte si článek [Využití investice do cloudu na maximum se službou Azure Cost Management](../cost-management/cost-mgt-best-practices.md).

Také na webu Azure Portal můžete optimalizovat a snížit náklady na Azure pomocí automatického vypínání virtuálních počítačů a doporučení Advisoru (Poradce).

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Zvážení funkcí pro snížení nákladů, jako je automatické vypínání virtuálních počítačů

Podle situace můžete na webu Azure Portal nakonfigurovat automatické vypínání virtuálních počítačů. Další informace najdete v článku [Automatické vypínání virtuálních počítačů pomocí Azure Resource Manageru](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Snímek obrazovky s možností automatického vypínání na portálu](./media/billing-getting-started/auto-shutdown.png)

Automatické vypnutí není totéž jako vypnutí virtuálního počítače pomocí možností napájení. Při automatickém vypnutí se virtuální počítače zastaví a zruší se jejich přidělení, takže nenabíhají další poplatky za využití. Další informace najdete v nejčastějších dotazech ke stavům virtuálních počítačů pro [virtuální počítače s Linuxem](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Další funkce pro snížení nákladů u vývojových a testovacích prostředí popisuje [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Zapnutí a kontrola doporučení Azure Advisoru (Poradce)

[Azure Advisor](../advisor/advisor-overview.md) (Poradce) pomáhá snižovat náklady tím, že identifikuje prostředky s nízkým využitím. Na webu Azure Portal vyhledejte **Advisor**:

![Snímek obrazovky s tlačítkem Advisor (Poradce) na webu Azure Portal](./media/billing-getting-started/advisor-button.png)

Na levé straně vyberte **Náklady**. Na kartě **Náklady** se zobrazí užitečná doporučení:

![Snímek obrazovky s příkladem doporučení Advisoru (Poradce) ohledně nákladů](./media/billing-getting-started/advisor-action.png)

Absolvujte výukový kurz [Optimalizace nákladů na základě doporučení](../cost-management/tutorial-acm-opt-recommendations.md), který se věnuje doporučením Advisoru (Poradce) ohledně úspor nákladů.

## <a name="review-charges-against-your-latest-invoice"></a>Kontrola poplatků na nejnovější faktuře

Vaše faktura je k dispozici na konci fakturačního období. Pokud chcete zkontrolovat správnost vyúčtování, můžete si [stáhnout faktury a soubory s podrobnými informacemi o využití](billing-download-azure-invoice-daily-usage-date.md) a porovnat je. Další informace o porovnání denního využití s fakturou najdete v článku [Informace o vyúčtování služeb Microsoft Azure](billing-understand-your-bill.md).

Pokud používáte Azure prostřednictvím smlouvy se zákazníkem Microsoftu (MCA) a chcete porozumět poplatkům na vaší faktuře, můžete [fakturu porovnat také s transakcemi](billing-mca-understand-your-bill.md#view-transactions-for-an-invoice-in-the-azure-portal).

## <a name="integrate-with-billing-and-consumption-apis"></a>Integrace s využitím rozhraní API pro fakturaci a spotřebu

Pomocí rozhraní API pro [fakturaci](https://docs.microsoft.com/rest/api/billing/) a [spotřebu](https://docs.microsoft.com/rest/api/consumption/) Azure můžete fakturační údaje a data o nákladech získat programově. Využití, které je vám účtováno, získáte zkombinováním rozhraní RateCard API a Usage API. Další informace najdete v článku [Získání přehledu o spotřebě prostředků Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Další prostředky a speciální případy

### <a name="ea-csp-and-sponsorship-customers"></a>Zákazníci využívající EA, CSP a Sponsorship
Začněte tím, že kontaktujete svého account manažera nebo partnera Azure.

| Nabídka | Zdroje a prostředky |
|-------------------------------|-----------------------------------------------------------------------------------|
| Smlouva Enterprise (EA) | [Portál EA](https://ea.azure.com/), [nápověda](https://ea.azure.com/helpdocs) a [sestava Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| CSP (Cloud Solution Provider) | Kontaktujte svého poskytovatele. |
| Azure Sponsorship | [Portál Sponsorship](https://www.microsoftazuresponsorships.com/) |

Pokud se staráte o IT ve velké organizaci, doporučujeme, abyste si přečetli článek o [používání Azure v podnicích](/azure/architecture/cloud-adoption-guide/subscription-governance) a [dokument white paper o podnikovém IT](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (soubor .pdf ke stažení, jen v angličtině).

### <a name="EA"></a> Zobrazení nákladů u smlouvy Enterprise na webu Azure Portal

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

K zobrazení nákladů potřebujete přístup k informacím o nákladech nebo fakturačním údajům na úrovni účtu nebo předplatného. Úroveň přístupu se liší podle typu fakturačního účtu. Další informace o fakturačních účtech a kontrole typu fakturačního účtu najdete v tématu [Zobrazení fakturačních účtů na webu Azure Portal](billing-view-all-accounts.md).

Pokud máte přístup k Azure prostřednictvím fakturačního účtu programu Microsoft Online Service (MOSP), projděte si téma [Správa přístupu k fakturačním údajům pro Azure](billing-manage-access.md).

Pokud máte přístup k Azure prostřednictvím fakturačního účtu pro smlouvu Enterprise (EA), projděte si téma [Principy rolí pro správu smlouvy Azure Enterprise v Azure](billing-understand-ea-roles.md).

Pokud máte přístup k Azure prostřednictvím fakturačního účtu pro smlouvu se zákazníkem Microsoftu (MCA), projděte si téma [Principy rolí pro správu smlouvy se zákazníkem Microsoftu v Azure](billing-understand-mca-roles.md).

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Vyžádání kreditu smlouvy o úrovni služeb za servisní incident

Smlouva o úrovni služeb (SLA) popisuje závazky Microsoftu týkající se dostupnosti a konektivity. Servisní incident je nahlášen, když se služby Azure potýkají s problémem, který má dopad na dostupnost nebo konektivitu, a často se označuje jako *výpadek*. Pokud nedosáhneme a neudržíme úrovně jednotlivých služeb, jak je popsáno ve smlouvě SLA, můžete mít nárok na kredit části vašich měsíčních poplatků za služby.

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

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- Seznamte se s [limity útraty](billing-spending-limit.md), abyste neutráceli víc, než musíte.
- Začněte [analyzovat náklady na Azure](../cost-management/quick-acm-cost-analysis.md).

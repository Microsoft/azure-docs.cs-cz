---
title: Jak zabránit neočekávaným nákladům a spravovat fakturaci v Azure
description: Zjistěte, jak se vyhnout neočekávaným poplatkům ve faktuře za Azure. U předplatného Azure můžete využít funkce pro sledování a řízení nákladů.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: d6c287d5ead0095a4f7bb5ad754212b134f7103c
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719806"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Jak zabránit neočekávaným poplatkům pomocí fakturace a řízení nákladů v Azure

Při registraci Azure máte k dispozici několik možností, které vám pomohou získat lepší představu o útratě:

- [Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/) dokáže odhadnout náklady ještě před vytvořením prostředku Azure. 

- [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nabízí rozpis aktuálních nákladů a prognózu pro vaše předplatné. 

- Pokud chcete seskupit a zjistit náklady na různé projekty nebo týmy, můžete využít [označování prostředků](../azure-resource-manager/resource-group-using-tags.md). Pokud vaše organizace používá vykazovací systém, kterému dáváte přednost, podívejte se na [fakturační rozhraní API](billing-usage-rate-card-overview.md).

- Pokud bylo vaše předplatné vytvořeno ze smlouvy Enterprise (EA), můžete si své náklady zobrazit na webu Azure Portal. Pokud jste si předplatné pořídili přes poskytovatele CSP (Cloud Solution Provider) nebo Azure Sponsorship, nemusí se na vás vztahovat některé z následujících funkcí. Více informací najdete v [dalších materiálech pro EA, CSP a Sponsorship](#other-offers).

- Pokud jako předplatné používáte bezplatnou zkušební verzi, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Systém Azure v rámci licenčního programu Open (AIO) nebo BizSpark, vaše předplatné se při využití všech kreditů automaticky zakáže. Přečtěte si o [limitech útraty](#spending-limit), aby vaše předplatné nebylo neočekávaně zakázáno.

- Pokud jste si zaregistrovali [bezplatný účet Azure](https://azure.microsoft.com/free/), [můžete využít některé z nejoblíbenějších služeb Azure zdarma po dobu 12 měsíců](billing-create-free-services-included-free-account.md). Spolu s níže uvedenými doporučeními si přečtěte, jak se [vyhnout platbám za bezplatný účet](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Zjištění odhadovaných nákladů před přidáním služeb Azure

Zde najdete další informace o odhadu nákladů pomocí následujících nástrojů:
- Cenová kalkulačka Azure
- portál Azure
- Limit útraty

Na obrázcích v následujících částech jsou zobrazeny příklady cen v amerických dolarech.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Odhad nákladů online pomocí cenové kalkulačky

Pokud chcete zjistit odhadované měsíční náklady na službu, o kterou máte zájem, použijte [cenovou kalkulačku](https://azure.microsoft.com/pricing/calculator/). Odhadované náklady zjistíte pro jakýkoli prostředek Azure poskytovaný první stranou. V cenové kalkulačce můžete změnit typ měny.

![Snímek obrazovky s nabídkou cenové kalkulačky](./media/billing-getting-started/pricing-calc.png)

Cenová kalkulačka například u virtuálního počítače A1 s Windows odhadne určitou částku za měsíc za výpočetní čas, pokud ho necháte běžet celou dobu:

![Snímek obrazovky s cenovou kalkulačkou, která ukazuje odhadované náklady na virtuální počítač A1 s Windows za měsíc](./media/billing-getting-started/pricing-calcvm.png)

Další informace o cenách najdete v [nejčastějších dotazech ohledně cen](https://azure.microsoft.com/pricing/faq/). Pokud si chcete promluvit s prodejcem Azure, zavolejte na telefonní číslo uvedené na začátku stránky s nejčastějšími dotazy.

### <a name="review-estimated-costs-in-the-azure-portal"></a>Zjištění odhadovaných nákladů na webu Azure Portal

Když na webu Azure Portal přidáte nějakou službu, můžete se zpravidla podívat na odhadovanou cenu za měsíc ve své účtované měně. Když například zvolíte velikost virtuálního počítače s Windows, uvidíte odhadované měsíční náklady za výpočetní čas:

![Příklad: virtuální počítač A1 s Windows zobrazující odhadované náklady za měsíc](./media/billing-getting-started/vm-size-cost.png)

### <a name="spending-limit"></a> Zjištění, jestli máte zapnutý limit útraty

Pokud máte předplatné, které používá kredity, je limit útraty standardně zapnutý. Díky tomu se vám při utracení všech kreditů nezačnou strhávat poplatky z platební karty. Prohlédněte si [úplný seznam nabídek Azure a dostupnost limitu útraty](https://azure.microsoft.com/support/legal/offer-details/).

Když ale dosáhnete limitu útraty, vaše služby se zakážou. To znamená, že se zruší přidělení vašich virtuálních počítačů. Abyste se vyhnuli výpadkům služby, musíte limit útraty vypnout. Jakékoli nadlimitní využití bude strženo z vaší zaregistrované platební karty.

Pokud chcete zjistit, jestli máte limit útraty zapnutý, přejděte na [zobrazení Předplatná v Centru účtů](https://account.windowsazure.com/Subscriptions). Pokud je limit útraty zapnutý, zobrazí se informační zpráva podobná následující:

![Snímek obrazovky zobrazující upozornění na zapnutý limit útraty v Centru účtů](./media/billing-getting-started/spending-limit-banner.png)

Klikněte na tuto informační zprávu a podle zobrazených pokynů limit útraty odeberte. Pokud jste při registraci nezadali informace o platební kartě, musíte je kvůli odebrání limitu útraty zadat. Další informace najdete v článku [Limit útraty Azure – jak funguje a jak ho aktivovat nebo odebrat](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="use-budgets-and-cost-alerts"></a>Použití rozpočtů a upozornění na náklady

Pro účely řízení nákladů můžete vytvořit [rozpočty](../cost-management/tutorial-acm-create-budgets.md) a nastavit [upozornění](../cost-management/cost-mgt-alerts-monitor-usage-spending.md), která účastníky automaticky upozorňují na anomálie a nebezpečí nadměrných výdajů. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů.

## <a name="monitor-costs-when-using-azure-services"></a>Monitorování nákladů při používání služeb Azure
Náklady můžete monitorovat pomocí následujících nástrojů:

- Značky
- Rozpis nákladů a pracovní tempo
- Analýza nákladů

### <a name="tags"></a> Seskupení fakturačních údajů přidáním značek k prostředkům

Pomocí značek můžete seskupit fakturační údaje za podporované služby. Pokud například provozujete několik virtuálních počítačů pro různé týmy, můžete pomocí značek kategorizovat náklady podle nákladového střediska (například pro osobní, marketingové a finanční oddělení) nebo prostředí (například pro produkční, předprodukční a testovací).

![Snímek obrazovky, který ukazuje nastavení značek na portálu](./media/billing-getting-started/tags.png)

Značky jsou vidět v různých zobrazeních s výkazy nákladů. Po prvním fakturačním období jsou například vidět v [zobrazení analýzy nákladů](#costs) a v souboru CSV s podrobným využitím.

Další informace najdete v článku [Použití značek k uspořádání prostředků Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Monitorování rozpisu nákladů a pracovního tempa

Po spuštění služeb Azure pravidelně kontrolujte poplatky. Aktuální útratu a pracovní tempo si můžete prohlédnout na webu Azure Portal.

1. Přejděte na [Předplatná na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a vyberte předplatné.

2. Pokud to vaše předplatné podporuje, zobrazí se rozpis nákladů a pracovní tempo.

    ![Snímek obrazovky s pracovním tempem a rozpisem na webu Azure Portal](./media/billing-getting-started/burn-rate.PNG)

3. Kliknutím na [Analýza nákladů](../cost-management/quick-acm-cost-analysis.md) v seznamu nalevo zobrazíte rozpis nákladů podle prostředků. Po přidání nějaké služby počkejte 24 hodin, než se údaje zobrazí.

    ![Snímek obrazovky se zobrazením analýzy nákladů na webu Azure Portal](./media/billing-getting-started/cost-analysis.png)

4. Můžete filtrovat podle různých vlastností, jako jsou [značky](#tags), typ prostředku, skupina prostředků a časový rozsah. Kliknutím na **Použít** filtry potvrďte a pak klikněte na **Stáhnout**, pokud chcete toto zobrazení exportovat do textového souboru s oddělovači (.csv).

5. Kromě toho můžete kliknutím na prostředek zobrazit historii denní útraty a zjistit, kolik vás tento prostředek každý den stojí.

    ![Snímek obrazovky se zobrazením historie výdajů na webu Azure Portal](./media/billing-getting-started/costhistory.png)

Náklady, které vidíte, porovnejte s odhady, které jste viděli při výběru služeb. Pokud se náklady od odhadů významně liší, zkontrolujte cenový plán, který jste pro své prostředky vybrali.

## <a name="optimize-and-reduce-costs"></a>Optimalizace a snížení nákladů
Pokud nejste obeznámeni s principy řízení nákladů, přečtěte si článek [Využití investice do cloudu na maximum se službou Azure Cost Management](../cost-management/cost-mgt-best-practices.md).

Také na webu Azure Portal můžete optimalizovat a snížit náklady na Azure pomocí automatického vypínání virtuálních počítačů a doporučení Advisoru (Poradce).

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Zvážení funkcí pro snížení nákladů, jako je automatické vypínání virtuálních počítačů

Podle situace můžete na webu Azure Portal nakonfigurovat automatické vypínání virtuálních počítačů. Další informace najdete v článku [Automatické vypínání virtuálních počítačů pomocí Azure Resource Manageru](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Snímek obrazovky s možností automatického vypínání na portálu](./media/billing-getting-started/auto-shutdown.png)

Automatické vypnutí není totéž jako vypnutí virtuálního počítače pomocí možností napájení. Při automatickém vypnutí se virtuální počítače zastaví a zruší se jejich přidělení, takže nenabíhají další poplatky za využití. Další informace najdete v nejčastějších dotazech ke stavům virtuálních počítačů pro [virtuální počítače s Linuxem](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Další funkce pro snížení nákladů u vývojových a testovacích prostředí popisuje [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Zapnutí a kontrola doporučení Azure Advisoru (Poradce)

[Azure Advisor](../advisor/advisor-overview.md) (Poradce) pomáhá snižovat náklady tím, že identifikuje prostředky s nízkým využitím. Přejděte na Advisor (Poradce) na webu Azure Portal:

![Snímek obrazovky s tlačítkem Advisor (Poradce) na webu Azure Portal](./media/billing-getting-started/advisor-button.png)

Užitečná doporučení můžete získat na kartě **Náklady** na řídicím panelu Advisoru (Poradce):

![Snímek obrazovky s příkladem doporučení Advisoru (Poradce) ohledně nákladů](./media/billing-getting-started/advisor-action.png)

Absolvujte výukový kurz [Optimalizace nákladů na základě doporučení](../cost-management/tutorial-acm-opt-recommendations.md), který se věnuje doporučením Advisoru (Poradce) ohledně úspor nákladů.

## <a name="review-costs-against-your-latest-invoice"></a>Kontrola nákladů na nejnovější faktuře

Na konci fakturačního cyklu je k dispozici vaše nejnovější faktura. Můžete si také [stáhnout faktury a soubory s podrobnými informacemi o využití](billing-download-azure-invoice-daily-usage-date.md) a zkontrolovat správnost vyúčtování. Další informace o porovnání denního využití s fakturou najdete v článku [Informace o vyúčtování služeb Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>Rozhraní API pro fakturaci

Pomocí rozhraní API pro fakturaci Azure můžete data o využití získat programově. Využití, které je vám účtováno, získáte zkombinováním rozhraní RateCard API a Usage API. Další informace najdete v článku [Získání přehledu o spotřebě prostředků Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Další prostředky a speciální případy

### <a name="ea-csp-and-sponsorship-customers"></a>Zákazníci využívající EA, CSP a Sponsorship
Začněte tím, že kontaktujete svého account manažera nebo partnera Azure.

| Nabídka | Zdroje a prostředky |
|-------------------------------|-----------------------------------------------------------------------------------|
| Smlouva Enterprise (EA) | [Portál EA](https://ea.azure.com/), [nápověda](https://ea.azure.com/helpdocs) a [sestava Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| CSP (Cloud Solution Provider) | Kontaktujte svého poskytovatele. |
| Azure Sponsorship | [Portál Sponsorship](https://www.microsoftazuresponsorships.com/) |

Pokud se staráte o IT ve velké organizaci, doporučujeme, abyste si přečetli článek o [používání Azure v podnicích](/azure/architecture/cloud-adoption-guide/subscription-governance) a [dokument white paper o podnikovém IT](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (soubor .pdf ke stažení, jen v angličtině).

#### <a name="EA"></a> Zobrazení nákladů u smlouvy Enterprise na webu Azure Portal

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

Abyste viděli náklady, musíte mít [přístup k fakturačním údajům na úrovni předplatného](billing-manage-access.md). K [Centru účtů](https://account.azure.com/Subscriptions), změně fakturačních údajů a správě předplatných má přístup jen správce účtu. Správce účtu je osoba, která prošla procesem registrace. Další informace najdete v článku [Jak přidat nebo změnit role správce Azure, které spravují předplatné nebo služby](billing-add-change-azure-subscription-administrator.md).

Pokud chcete zjistit, jestli jste správcem účtu, přejděte na [Předplatná na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Zobrazte seznam předplatných a najděte sloupec **Moje role**. Pokud je zde uvedeno *Správce účtu*, pak máte úplná oprávnění. Pokud je zde uvedeno něco jiného, například *Vlastník*, pak úplná oprávnění nemáte.

![Snímek obrazovky vaší role v zobrazení Předplatná na webu Azure Portal](./media/billing-getting-started/sub-blade-view.PNG)

Pokud chcete spravovat předplatná a měnit fakturační údaje, [najděte správce účtu](billing-subscription-transfer.md#whoisaa). Požádejte správce účtu o provedení příslušných úkolů nebo o [převod předplatného na vás](billing-subscription-transfer.md).

Pokud správce účtu už nepracuje ve vaší organizaci a potřebujete spravovat fakturaci, [kontaktujte nás](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Vyžádání kreditu smlouvy o úrovni služeb za servisní incident

Smlouva o úrovni služeb (SLA) popisuje závazky Microsoftu týkající se dostupnosti a konektivity. Servisní incident je nahlášen, když se služby Azure potýkají s problémem, který má dopad na dostupnost nebo konektivitu, a často se označuje jako *výpadek*. Pokud nedosáhneme a neudržíme úrovně jednotlivých služeb, jak je popsáno ve smlouvě SLA, můžete mít nárok na kredit části vašich měsíčních poplatků za služby.

Kredit si vyžádáte takto:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/). Pokud máte více účtů, nezapomeňte použít ten, který byl ovlivněn výpadkem Azure. 
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

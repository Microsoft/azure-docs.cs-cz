---
title: Prevence neočekávaných nákladů a správa fakturace v Azure
description: Přečtěte si, jak se vyhnout neočekávaným poplatkům za váš účet Azure. Využijte funkce pro sledování nákladů a správu pro předplatné Azure.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: b64e84c3fff27675029ff35f27972a4aca014ec3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68612091"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Zabránění neočekávaným poplatkům s využitím fakturace a správy nákladů Azure

Při registraci k Azure máte k dispozici několik věcí, které vám pomůžou získat lepší představu o útratě:

- [Cenová Kalkulačka](https://azure.microsoft.com/pricing/calculator/) může poskytnout odhad nákladů ještě před vytvořením prostředku Azure. 

- [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) poskytuje aktuální rozpis nákladů a prognózu pro vaše předplatné. 

- Pokud chcete seskupit a pochopit náklady na různé projekty nebo týmy, podívejte se na [označení prostředků](../azure-resource-manager/resource-group-using-tags.md). Pokud má vaše organizace systém vytváření sestav, který chcete použít, podívejte se na [fakturační rozhraní API](billing-usage-rate-card-overview.md).

- Pokud se vaše předplatné vytvořilo z smlouva Enterprise (EA), můžete si své náklady zobrazit v Azure Portal. Pokud je vaše předplatné prostřednictvím poskytovatele Cloud Solution Provider (CSP) nebo Azure Sponsorship, pak se na vás nemusí vztahovat některé z následujících funkcí. Další informace najdete v tématu [Další zdroje informací pro EA, CSP a sponzorství](#other-offers).

- Pokud je vaše předplatné bezplatnou zkušební verzí, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), systém Azure V rámci licenčního programu Open (AIO) nebo BizSpark, vaše předplatné se automaticky zakáže, když se použijí všechny vaše kredity. Přečtěte [](#spending-limit) si o limitech útraty, abyste se vyhnuli neočekávanému zákazu

- Pokud jste si zaregistrovali [bezplatný účet Azure](https://azure.microsoft.com/free/), [můžete využít některé z nejoblíbenějších služeb Azure zdarma po dobu 12 měsíců](billing-create-free-services-included-free-account.md). Spolu s níže uvedenými doporučeními se můžete podívat, jak si neúčtují [poplatky za bezplatný účet](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Získat odhadované náklady před přidáním služeb Azure

Tady jsou některé další informace o odhadu nákladů pomocí následujících nástrojů:
- Cenová kalkulačka Azure
- portál Azure
- Limit útraty

Obrázky v následujících částech ukazují příklad ceny v amerických dolarech.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Odhad nákladů online pomocí cenové kalkulačky

Podívejte se na [cenovou kalkulačku](https://azure.microsoft.com/pricing/calculator/) , kde získáte Odhadované měsíční náklady na službu, které vás zajímá. Můžete přidat libovolný prostředek Azure First stran, abyste získali odhadované náklady. V cenové kalkulačkě můžete změnit typ měny.

![Snímek nabídky cenové kalkulačky](./media/billing-getting-started/pricing-calc.png)

Například v cenové kalkulačkě je virtuální počítač s Windows (VM) s Windows odhadem na náklady a určitou částku za měsíc v výpočetních hodinách, pokud necháte běžet celý čas:

![Snímek obrazovky s cenovou kalkulačkou, která ukazuje odhadované náklady na virtuální počítač s Windows a1 za měsíc](./media/billing-getting-started/pricing-calcvm.png)

Další informace o cenách najdete v tématu s [cenami – Nejčastější dotazy](https://azure.microsoft.com/pricing/faq/). Pokud se chcete spojit s prodejcem Azure, zavolejte telefonní číslo uvedené v horní části stránky Nejčastější dotazy.

### <a name="review-estimated-costs-in-the-azure-portal"></a>Přečtěte si odhadované náklady na Azure Portal

Když v Azure Portal přidáte službu, zobrazí se vám zobrazení odhadovaná cena za měsíc v účtované měně. Když například zvolíte velikost virtuálního počítače s Windows, zobrazí se vám Odhadované měsíční náklady za výpočetní hodiny:

![Příklad: virtuální počítač s Windows a1 zobrazující odhadované náklady za měsíc](./media/billing-getting-started/vm-size-cost.png)

### <a name="spending-limit"></a>Podívejte se, jestli máte limit útraty zapnutý.

Pokud máte předplatné, které používá kredity, je limit útraty ve výchozím nastavení zapnutý. Tím se vám při útratě všech svých kreditů neúčtují poplatky za platební kartu. Podívejte se na [úplný seznam nabídek Azure a dostupnost limitu útraty](https://azure.microsoft.com/support/legal/offer-details/).

Když ale dosáhnete limitu útraty, vaše služby se zablokují. To znamená, že vaše virtuální počítače se nadělují. Abyste se vyhnuli výpadkům služby, musíte vypnout limit útraty. Všechna nadlimitní využití se účtují na platební kartu v souboru.

Pokud chcete zjistit, jestli máte limit útraty, přejděte do [zobrazení předplatná v centru účtů](https://account.windowsazure.com/Subscriptions). Pokud je limit útraty zapnutý, zobrazí se banner podobný následujícímu:

![Snímek obrazovky zobrazující upozornění týkající se limitu útraty v centru účtů](./media/billing-getting-started/spending-limit-banner.png)

Klikněte na banner a podle zobrazených výzev odeberte limit útraty. Pokud jste při registraci nezadali informace o kreditních kartách, je nutné ji zadat, chcete-li odebrat limit útraty. Další informace najdete v tématu [limit útraty Azure – jak to funguje a jak ho povolit nebo odebrat](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="use-budgets-and-cost-alerts"></a>Použití rozpočtů a cenových výstrah

Můžete vytvářet [rozpočty](../cost-management/tutorial-acm-create-budgets.md) pro správu nákladů a vytvářet [výstrahy](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) , které automaticky upozorňují zúčastněné strany na anomálie a rizika plynoucí z výdajů. Výstrahy vycházejí z útraty v porovnání s rozpočtovými a nákladovou prahovou hodnotou.

## <a name="monitor-costs-when-using-azure-services"></a>Monitorování nákladů při používání služeb Azure
Náklady můžete monitorovat pomocí následujících nástrojů:

- Tags
- Rozpis nákladů a rychlost vypálení
- Analýza nákladů

### <a name="tags"></a>Přidání značek k prostředkům pro seskupení fakturačních dat

Pomocí značek můžete seskupit fakturační data pro podporované služby. Například pokud spustíte několik virtuálních počítačů pro různé týmy, pak můžete pomocí značek kategorizovat náklady podle nákladového střediska (například: HR, marketing, finance atd.) nebo prostředí (například: produkční, předprodukční, test).

![Snímek obrazovky, který ukazuje nastavení značek na portálu](./media/billing-getting-started/tags.png)

Značky se zobrazí v různých zobrazeních pro generování sestav nákladů. Například jsou viditelné v [zobrazení analýza nákladů](#costs) hned a v souboru CSV s podrobným využitím po vašem prvním fakturačním období.

Další informace najdete v tématu [použití značek k uspořádání prostředků Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a>Sledovat rozpis nákladů a rychlost vypálení

Po spuštění služeb Azure budou pravidelně kontrolovat poplatky. Aktuální útratu a pracovní tempo můžete vidět v Azure Portal.

1. Přejděte na [předplatná v Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a vyberte předplatné.

2. Pokud je pro vaše předplatné podporovaná, zobrazí se rozpis nákladů a rychlost vypalování.

    ![Snímek obrazovky s počtem a rozpisem vypálení v Azure Portal](./media/billing-getting-started/burn-rate.PNG)

3. Kliknutím na [Analýza nákladů](../cost-management/quick-acm-cost-analysis.md) v seznamu nalevo zobrazíte rozpis nákladů podle prostředků. Po přidání služby počkejte 24 hodin na zobrazení dat.

    ![Snímek obrazovky zobrazení analýzy nákladů v Azure Portal](./media/billing-getting-started/cost-analysis.png)

4. Můžete filtrovat podle různých vlastností, jako jsou [značky](#tags), typ prostředku, skupina prostředků a časový rozsah. Kliknutím na **použít** potvrďte filtry a **Stáhněte si** , jestli chcete exportovat zobrazení do souboru hodnot oddělených čárkami (. csv).

5. Kromě toho můžete kliknutím na prostředek zobrazit historii denních výdajů a množství nákladů na prostředky.

    ![Snímek obrazovky s zobrazením historie výdajů v Azure Portal](./media/billing-getting-started/costhistory.png)

Porovnejte náklady, které vidíte, u odhadů, které jste viděli při výběru služeb. Pokud se náklady významně liší od odhadů, podívejte se do cenového plánu, který jste vybrali pro vaše prostředky.

## <a name="optimize-and-reduce-costs"></a>Optimalizace a snížení nákladů
Pokud nejste obeznámeni se zásadami správy nákladů, přečtěte si, [jak optimalizovat cloudovou investici pomocí Azure cost management](../cost-management/cost-mgt-best-practices.md).

V Azure Portal můžete také optimalizovat a snížit náklady na Azure pomocí automatického vypnutí pro virtuální počítače a doporučení poradce.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Zvažte funkce, jako je automatické vypnutí pro virtuální počítače.

V závislosti na vašem scénáři můžete automatické vypnutí pro virtuální počítače nakonfigurovat v Azure Portal. Další informace najdete v tématu [Automatické vypnutí virtuálních počítačů pomocí Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Snímek obrazovky s možností automatického vypnutí na portálu](./media/billing-getting-started/auto-shutdown.png)

Automatické vypnutí není stejné jako při vypnutí na virtuálním počítači s možností napájení. Automatické vypnutí se zastaví a zruší přidělení virtuálních počítačů, aby se zastavily další poplatky za využití. Další informace najdete v tématu VĚNOVANÉm cenám pro virtuální počítače se systémem [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) o stavech virtuálních počítačů.

Další cenové funkce pro vývojová a testovací prostředí najdete [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Zapnutí a kontrola Azure Advisor doporučení

[Azure Advisor](../advisor/advisor-overview.md) pomáhá snižovat náklady tím, že identifikuje prostředky s nízkým využitím. Navštivte službu Advisor v Azure Portal:

![Snímek obrazovky s tlačítkem Azure Advisor v Azure Portal](./media/billing-getting-started/advisor-button.png)

Užitečná doporučení můžete získat na kartě **náklady** na řídicím panelu Poradce:

![Snímek obrazovky s doporučením pro náklady na poradce](./media/billing-getting-started/advisor-action.png)

Kurz průvodce [optimalizace nákladů z doporučení](../cost-management/tutorial-acm-opt-recommendations.md) najdete v kurzu s podrobnými informacemi o úsporách nákladů a šetřících náklady.

## <a name="review-costs-against-your-latest-invoice"></a>Kontrola nákladů na nejnovější fakturu

Na konci fakturačního cyklu je k dispozici vaše nejnovější faktury. Můžete si také [Stáhnout faktury a podrobné soubory použití](billing-download-azure-invoice-daily-usage-date.md) , abyste se ujistili, že jste se vyrovnali správně. Další informace o porovnání denního využití s vaší fakturou najdete v tématu [vysvětlení faktury za Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>Rozhraní API pro fakturaci

Pomocí rozhraní API pro fakturaci Azure můžete programově získat data o využití. Využijte rozhraní API pro RateCard a rozhraní API pro využití a získejte vaše fakturované využití. Další informace najdete v tématu [Získání přehledů o spotřebě prostředků Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a>Další zdroje informací a zvláštní případy

### <a name="ea-csp-and-sponsorship-customers"></a>Zákazníci se smlouvou EA, CSP a sponzorství
Začněte tím, že budete kontaktovat svého správce účtů nebo partnera Azure.

| Nabídka | Zdroje a prostředky |
|-------------------------------|-----------------------------------------------------------------------------------|
| Smlouva Enterprise (EA) | [Portál EA](https://ea.azure.com/), [dokumentace k nápovědě](https://ea.azure.com/helpdocs)a [Power BI sestava](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | Kontaktujte svého poskytovatele |
| Azure Sponsorship | [Portál sponzorství](https://www.microsoftazuresponsorships.com/) |

Pokud ho spravujete pro velkou organizaci, doporučujeme, abyste si přečetli [Azure Enterprise weblešení](/azure/architecture/cloud-adoption-guide/subscription-governance) a [Podnikový IT dokument White Paper](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (soubor. PDF stažením, jenom v angličtině).

#### <a name="EA"></a>smlouva Enterprise zobrazení nákladů v Azure Portal

Podniková zobrazení nákladů jsou v současnosti ve Public Preview. Položky, které se mají poznamenat:

- Náklady na předplatné jsou založené na využití a nezahrnují předplacené částky, překročení limitů, zahrnuté množství, úpravy a daně. Skutečné poplatky jsou vypočítány na úrovni registrace.
- Částky zobrazené v Azure Portal se mohou lišit od toho, co je na portálu Enterprise. Aktualizace na portálu Enterprise může trvat několik minut, než se změny zobrazí v Azure Portal.
- Pokud nevidíte žádné náklady, může to být z jednoho z následujících důvodů:
    - Nemáte oprávnění na úrovni předplatného. Pokud chcete zobrazit zobrazení podnikových nákladů, musíte být čtečkou fakturace, čtenář, přispěvatel nebo vlastník na úrovni předplatného.
    - Jste vlastníkem účtu a správce registrace zakázal nastavení "poplatky za zobrazení".  Pokud chcete získat přístup k nákladům, obraťte se na správce registrace.
    - Jste správcem vašeho oddělení a správce registrace zakázal nastavení **Zobrazit poplatky za zobrazení da** .  Pro získání přístupu se obraťte na správce registrace.
    - Nakoupili jste Azure prostřednictvím partnera pro kanály a partner neuvolnil informace o cenách.  
- Pokud aktualizujete nastavení související s přístupem k nákladům na portálu Enterprise, nastane zpoždění několik minut, než se změny zobrazí v Azure Portal.
- Limit útraty a pokyny k fakturaci se nevztahují na předplatná EA.

### <a name="check-your-subscription-and-access"></a>Kontrolovat předplatné a přístup

Chcete-li zobrazit náklady, je nutné mít [přístup na úrovni předplatného k fakturačním údajům](billing-manage-access.md). Pouze správce účtu má přístup k [centru účtů](https://account.azure.com/Subscriptions), změnám fakturačních údajů a správě předplatných. Správce účtu je osoba, která se stala procesem registrace. Další informace najdete v tématu [Přidání nebo změna rolí správce Azure, které spravují předplatné nebo služby](billing-add-change-azure-subscription-administrator.md).

Pokud se chcete podívat, jestli jste správcem účtu, přejděte na [předplatná v Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Zobrazte seznam předplatných a najděte **moji roli**. Pokud je *správcem účtu* role, pak máte úplná oprávnění. Pokud se říká něco jiného, jako je *vlastník*, nemáte úplná oprávnění.

![Snímek obrazovky vaší role v zobrazení předplatných v Azure Portal](./media/billing-getting-started/sub-blade-view.PNG)

Pokud chcete spravovat předplatná a měnit informace o fakturaci, [Najděte správce účtu](billing-subscription-transfer.md#whoisaa). Požádejte správce účtu, aby dokončil úkoly nebo [přeneste předplatné za vás](billing-subscription-transfer.md).

Pokud správce účtu už není ve vaší organizaci a potřebujete spravovat fakturaci, [kontaktujte nás](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Požádat o smlouva SLA kredit pro incident služby

Smlouva o úrovni služeb (SLA) popisuje závazky společnosti Microsoft týkající se dostupnosti a konektivity. Incident služby se oznamuje, když se služby Azure setkávají s problémem, který má dopad na provozuschopnost nebo konektivitu, často se označuje jako výpadek. Pokud pro každou službu nedosáhneme a udržujeme úrovně služeb, jak je popsáno v smlouvě SLA, můžete mít nárok na kredit na část svých měsíčních poplatků za služby.

Požadavek na kredit:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/). Pokud máte více účtů, ujistěte se, že používáte tu, kterou ovlivnila výpadky Azure. 
2. Vytvořte novou žádost o podporu.
3. V části **typ problému**vyberte **fakturace**.
4. V části **typ problému**vyberte **žádost o refundaci**.
5. Přidejte podrobnosti, abyste určili, že žádáte o kredit smlouvy SLA, zmiňujete si datum, čas, časové pásmo a také ovlivněné služby (virtuální počítače, weby atd.).
6. Ověřte své kontaktní údaje a vyberte **vytvořit** a odešlete žádost.

Prahové hodnoty smlouvy SLA se liší podle služby. Například webová vrstva SQL má smlouvu SLA 99,9%, virtuální počítače mají smlouvu SLA o 99,95% a úroveň Standard SQL má smlouvu SLA na 99,99%.

U některých služeb jsou k dispozici předpoklady pro použití smlouvy SLA. Například virtuální počítače musí mít nasazené dvě nebo více instancí ve stejné skupině dostupnosti.

Další informace najdete v dokumentaci ke [smlouvám o úrovni služeb](https://azure.microsoft.com/support/legal/sla/) a [souhrnu SLA pro služby Azure](https://azure.microsoft.com/support/legal/sla/summary/) .

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- Přečtěte si [](billing-spending-limit.md) o používání limitů útraty, abyste zabránili nadměrnému využití.
- Začněte [analyzovat náklady na Azure](../cost-management/quick-acm-cost-analysis.md).

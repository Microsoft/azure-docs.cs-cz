---
title: Zabránit neočekávané náklady, spravovat fakturace - Azure | Microsoft Docs
description: Zjistěte, jak neočekávané náklady na faktury Azure. Pomocí funkce sledování nákladů a správy pro předplatné Microsoft Azure.
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: tonguyen
ms.openlocfilehash: 47a0f2e1cdb873dc907748ed2f3def29efccaa98
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Zabránit neočekávané poplatky s Azure fakturace a náklady na správu

Při registraci v Azure existuje několik věcí, které vám pomohou získat lepší představu o vaší výdaji. [Cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) může poskytnout odhad nákladů, než vytvoříte prostředek služby Azure. [Portál Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) poskytuje aktuální rozdělení nákladů a Prognóza pro vaše předplatné. Pokud chcete skupinu a pochopit náklady na různých projektů nebo skupin, podívejte se na [označování prostředků](../azure-resource-manager/resource-group-using-tags.md). Pokud má vaše organizace reporting systém, který byste radši chtěli použít, podívejte se [fakturace rozhraní API](billing-usage-rate-card-overview.md). 

- Pokud je vaše předplatné Enterprise Agreement (EA), je k dispozici verzi public preview pro zobrazuje náklady na portálu Azure. Pokud je vaše předplatné přes Cloud Solution Provider (CSP) nebo Azure Sponsorship, pak některé z následujících funkcí na vás nemusí vztahovat. V tématu [další prostředky pro EA, CSP a sponzorství](#other-offers) Další informace.

- Pokud je vaše předplatné bezplatnou zkušební verzi, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)Azure v otevřené (AIO) nebo BizSpark, vaše předplatné automaticky zakázán, když vaše kredity se používají. Další informace o [limitů útraty](#spending-limit) -li se vyhnout předplatného unexpectantly zakázána.

- Pokud jste se zaregistrovali k [bezplatný účet Azure](https://azure.microsoft.com/free/), [mohou používat některé z nejčastěji používané služby Azure zadarmo po dobu 12 měsíců](billing-create-free-services-included-free-account.md). Společně s doporučení uvedených níže, najdete v části [vyhnout, získávání účtovat zdarma účet](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Získat odhadované náklady před přidáním služeb Azure

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Odhad náklady online pomocí cenové kalkulačky

Podívejte se [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) získat odhadované měsíční náklady služby vás zajímá. Přidáním jakékoli první strany prostředků Azure. Chcete-li získat nákladů na odhad.

![Snímek obrazovky cenové kalkulačky nabídky](./media/billing-getting-started/pricing-calc.png)

Například virtuální počítač A1 systému Windows (VM) by mělo náklady 66.96 USD za měsíc v výpočetní hodiny, pokud je necháte spuštěné po celou dobu:

![Snímek obrazovky zobrazující, že virtuální počítač A1 Windows by mělo náklady 66.96 USD měsíčně cenové kalkulačky](./media/billing-getting-started/pricing-calcVM.png)

Další informace o cenách najdete [– nejčastější dotazy](https://azure.microsoft.com/pricing/faq/). Nebo pokud chcete, aby komunikoval s Azure prodejce, kontaktujte 1-800-867-1389.

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>Zkontrolujte odhadované náklady na portálu Azure

Obvykle Pokud přidáte službu na portálu Azure, je zobrazení, které ukazuje, podobně jako odhadované náklady za měsíc. Například pokud zvolíte velikost virtuálního počítače Windows, zobrazí odhadované měsíční náklady pro výpočetní hodiny:

![Příklad: virtuální počítač A1 Windows by mělo náklady 66.96 USD za měsíc](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="set-up-billing-alerts"></a>Nastavení upozornění fakturace

Nastavení výstrah fakturace získat e-mailů, když vaše náklady na použití překročit částku, kterou zadáte. Pokud máte měsíční kredity, nastavení oznámení pro při použití až po zadanou dobu. Další informace najdete v tématu [nastavit výstrahy pro vaše předplatné Microsoft Azure billing](billing-set-up-alerts.md).

![Snímek obrazovky fakturace výstrahy e-mailu](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> Tato funkce je stále ve verzi preview, takže byste měli zkontrolovat, pravidelně vašeho využití.

Můžete chtít použít odhad náklady z cenové kalkulačky jako vodítko pro první výstraha.

### <a name="spending-limit"></a> Zkontrolujte, pokud máte limitu útraty a automaticky

Pokud máte předplatné, které používá kredity, pak limit útraty je pro vás ve výchozím nastavení zapnuta. Tímto způsobem, když tráví vaše kredity platební karty není získat účtovat. Najdete v článku [úplný seznam Azure nabízí a dostupnost útrat](https://azure.microsoft.com/support/legal/offer-details/).

Pokud jste dosáhl svého limitu útraty, získat zakázána vašim službám. To znamená, že virtuální počítače jsou navrácena. Výpadky služby, je nutné vypnout limitu útraty. Získá všechny Nadlimitní účtovat na platební karty u souboru. 

Pokud chcete zobrazit, pokud jste jste získali útrat na, přejděte na [odběry zobrazit v centru účtů](https://account.windowsazure.com/Subscriptions). Pokud limit útraty na se zobrazí nápis informující o:

![Snímek obrazovky, který zobrazí upozornění o výdaje limit používán centra účtů](./media/billing-getting-started/spending-limit-banner.PNG)

Klikněte na informační zprávě a postupujte podle výzev a odeberte limit útraty. Pokud se informace o kreditní kartě nezadali jste při registraci, musíte zadat ho odeberte limit útraty. Další informace najdete v tématu [Azure výdaje omezit – jak to funguje a jak povolit nebo ji odeberte](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Způsoby, jak sledovat vaše náklady při použití služby Azure

### <a name="tags"></a> Přidání značek k prostředkům na fakturační data seskupit

Značky, které skupiny fakturační údaje můžete použít pro podporované služby. Například pokud spustíte několik virtuálních počítačů pro různé týmy, pak můžete značek ke kategorizaci náklady nákladové středisko (HR, marketing, finance) nebo prostředí (test předprodukční, produkčním prostředí). 

![Snímek obrazovky, který ukazuje nastavení značky na portálu](./media/billing-getting-started/tags.PNG)

Značky zobrazí v rámci různých náklady na vytváření sestav zobrazení. Například jsou viditelné v vaše [náklady analysis zobrazení](#costs) hned a [podrobností využití .csv](#invoice-and-usage) po vaší první fakturačního období.

Další informace najdete v tématu [použití značek k uspořádání prostředků Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Pravidelně zkontrolujte na portálu a rozdělení nákladů a vypálíte rychlost

Po získání vaší služby spuštěné pravidelně kontrolovat, kolik je jste nákladů. Můžete zobrazit aktuální výdaji a zápis míry v portálu Azure. 

1. Přejděte [okno předplatná na portálu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a vyberte předplatné.

2. By měl zobrazit rozpis nákladů a zápis rychlost v místním okně. Nemusí být podporována pro vaši nabídku (upozornění se zobrazí v horní).

    ![Snímek obrazovky s pracovní tempo a rozdělení na portálu Azure](./media/billing-getting-started/burn-rate.PNG)

3. Klikněte na tlačítko **analýza nákladů** v seznamu nalevo zobrazit rozpis nákladů prostředkem. Počkejte, než 24 hodin, po přidání služby pro data k naplnění.

    ![Snímek obrazovky zobrazení analýza nákladů na portálu Azure](./media/billing-getting-started/cost-analysis.PNG)

4. Můžete filtrovat podle různých vlastnosti, například [značky](#tags), skupinu prostředků a časový interval. Klikněte na tlačítko **použít** potvrďte filtry a **Stáhnout** Pokud budete chtít exportovat do souboru (CSV) Comma-Separated hodnoty zobrazení.

5. Kromě toho můžete kliknout na prostředek zobrazíte denně tráví historie a kolik stojí prostředek každý den.

    ![Snímek obrazovky zobrazení historie výdaji na portálu Azure](./media/billing-getting-started/costhistory.PNG)

Doporučujeme zkontrolovat náklady, které se zobrazí s odhad, které jste viděli, pokud jste vybrali služby. Pokud náklady na velkým liší od odhady, Překontrolujte cenový plán (vs A1 virtuální počítač A0, např.), který jste vybrali pro vaše prostředky. 

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Zvažte povolení náklady vyjímání funkcí, jako je automatické vypnutí pro virtuální počítače

V závislosti na scénáři může nakonfigurovat automatické vypnutí pro virtuální počítače na portálu Azure. Další informace najdete v tématu [automaticky vypnutí pro virtuální počítače pomocí Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Snímek obrazovky možnost Automatické vypnutí na portálu](./media/billing-getting-started/auto-shutdown.PNG)

Vypnutí automatického není stejný jako při vypnutí virtuálního počítače pomocí možnosti napájení. Vypnutí automatického zastaví a zruší přidělení virtuální počítače k zastavení náklady. Další informace najdete v tématu Nejčastější dotazy týkající se ceny [virtuální počítače s Linuxem](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [virtuálních počítačů Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) o stavech virtuálních počítačů.

Pro další náklady vyjímání funkce pro vývojové a testovací prostředí, podívejte se na [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Zapnout a podívejte se na doporučení služby Advisor Azure

[Azure Advisor](../advisor/advisor-overview.md) je funkce preview, která vám pomůže snížit náklady tím, že identifikujete zdroje s nízkou využití. Aktivujte ji na portálu Azure:

![Snímek obrazovky Azure Advisor tlačítka na portálu Azure](./media/billing-getting-started/advisor-button.PNG)

Potom můžete získat řešitelné doporučení **náklady** kartě v řídicím panelu Advisor:

![Snímek obrazovky Advisor náklady na doporučení příklad](./media/billing-getting-started/advisor-action.PNG)

Další informace najdete v tématu [doporučení služby Advisor náklady](../advisor/advisor-cost-recommendations.md).

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>Kontrola náklady na konci fakturačního cyklu

Na konci fakturačního cyklu faktury bude k dispozici. Můžete také [stáhnout po faktury a soubory využití podrobností](billing-download-azure-invoice-daily-usage-date.md) a ujistěte se vám měla účtovat správně. Další informace o porovnávání s faktury vašeho denní využití najdete v tématu [porozumět vaší faktuře pro Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>Rozhraní API pro fakturaci

Pomocí našich fakturace rozhraní API prostřednictvím kódu programu získat data o využití. Použijte RateCard rozhraní API a rozhraní API využití společně k získání vašeho fakturovaná využití. Další informace najdete v tématu [proniknout do vaší spotřeby prostředků Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Další zdroje informací a zvláštních případech

### <a name="ea-csp-and-sponsorship-customers"></a>EA, CSP a sponzorství zákazníků
Obraťte se na vašeho account manažera nebo partnera Azure, abyste mohli začít.

| Nabídka | Zdroje a prostředky |
|-------------------------------|-----------------------------------------------------------------------------------|
| Smlouva Enterprise (EA) | [Portál EA](https://ea.azure.com/), [pomoci dokumentace](https://ea.azure.com/helpdocs), a [sestavy Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | Obraťte se na svého poskytovatele |
| Sponzorství Azure | [Sponzorství portálu](https://www.microsoftazuresponsorships.com/) |

Pokud spravujete IT ve velkých organizacích doporučujeme čtení [vygenerované uživatelské rozhraní Azure enterprise](../azure-resource-manager/resource-manager-subscription-governance.md) a [enterprise IT dokumentu white paper](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (.pdf ke stažení, pouze v angličtině).

#### <a name="EA"></a> Náklady zobrazení v portálu Azure Preview Enterprise Agreement 

Zobrazení náklady Enterprise jsou aktuálně ve verzi Public Preview. Všimněte si položek:
- Předplatné náklady jsou založená na využití a nespadá zálohách, poplatky za použití, zahrnuté množství, úpravy a daně. Skutečné poplatky se vypočítávají na úrovni registrace. 
- Objemy zobrazit v rámci portálu Azure může být zpoždění porovnání hodnoty v podnikovém portálu.  
- Pokud nevidíte náklady, může to být kvůli jednomu z následujících důvodů:
    - Nemáte dostatečná oprávnění na úrovni předplatného. Zobrazení náklady enterprise najdete musí být čtečky fakturace, čtečky, Přispěvatel nebo vlastníka na úrovni předplatného.
    - Jsou vlastníka účtu a správce registrace zakázal "AO zobrazení poplatků" nastavení.  Obraťte se na správce registrace získat přístup k náklady. 
    - Oddělení správce a správce registrace zakázal "DA zobrazení poplatků" nastavení.  Obraťte se na správce registrace k získání přístupu. 
    - Jste koupili prostřednictvím kanálu partnera Azure a partnerský nevydala informace o cenách.  
- Když jsou aktualizovány nastavení související s náklady na přístup v rámci portálu Enterprise dochází ke zpoždění o několik minut, než se projeví na portálu Azure.
- Útrat, fakturace výstrahy a faktury pokyny se netýkají EA odběry.

### <a name="check-your-subscription-and-access"></a>Zkontrolujte předplatné a přístup

Náklady na zobrazení vyžadují [přístup na úrovni odběry na fakturační informace](billing-manage-access.md), ale přístup pouze správce účtu [centra účtů](https://account.azure.com/Subscriptions), změňte fakturační informace a spravovat odběry. Správce účtu je osoba, která se prostřednictvím procesu registrace. Další informace najdete v tématu [přidání nebo změna role Správce služby Azure, které spravují předplatné nebo služby](billing-add-change-azure-subscription-administrator.md).

Pokud chcete zobrazit, pokud jste správce účtu, přejděte na [okno předplatná na portálu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a prohlédněte si seznam odběrů, máte přístup k. Podívejte se do části **Moje role**. Pokud se říká *správce účtu*, pak jste ok. Pokud se říká něco jiného jako *vlastníka*, pak nemáte s úplnými právy.

![Snímek obrazovky vaše role při zobrazení odběry na portálu Azure](./media/billing-getting-started/sub-blade-view.PNG)

Pokud si nejste správce účtu, pak někdo pravděpodobně vám Dal částečný přístup prostřednictvím [řízení přístupu na základě Role v Azure Active Directory](../role-based-access-control/role-assignments-portal.md) (RBAC). Správa předplatných a změnu fakturační údaje, [najít správce účtu](billing-subscription-transfer.md#whoisaa) a požádejte je o provádět úlohy nebo [převést předplatné vám](billing-subscription-transfer.md).

Pokud váš správce účtu je už ve vaší organizaci a potřebujete spravovat fakturace, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 
## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.

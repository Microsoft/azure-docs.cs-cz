---
title: Jak zabránit neočekávaným nákladům, správy fakturace v Azure | Dokumentace Microsoftu
description: Zjistěte, jak se vyhnout neočekávaným poplatkům na faktuře Azure. Pomocí funkce sledování nákladů a správy předplatného Microsoft Azure.
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
ms.openlocfilehash: 9396e954305ecc9ff6cbdd664c6cb71846bd5bfa
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42919122"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Vám nenaúčtovaly neočekávané poplatky se správou nákladů a fakturací Azure

Při registraci Azure existuje několik věcí, které vám pomůžou získat lepší představu o vaše útrata. [Cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) může poskytnout odhad nákladů, před vytvořením prostředku Azure. [Webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) vám poskytne aktuální rozpis nákladů a prostředků pro vaše předplatné. Pokud chcete seskupovat a pochopit i celkové náklady pro různé projekty nebo týmy, podívejte se na [označování prostředků](../azure-resource-manager/resource-group-using-tags.md). Pokud vaše organizace má vytváření sestav systém, který chcete použít, podívejte se [rozhraní API pro fakturaci](billing-usage-rate-card-overview.md). 

- Pokud vaše předplatné se smlouvou Enterprise (EA), verze public preview pro zobrazení nákladů na webu Azure Portal je k dispozici. Pokud je vaše předplatné přes Cloud Solution Provider (CSP) nebo Azure Sponsorship, pak některé z následujících funkcí na vás nemusí vztahovat. Zobrazit [další zdroje informací pro EA, CSP a nabídky Sponsorship](#other-offers) pro další informace.

- Pokud je vaše předplatné bezplatné zkušební verze, [sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)Azure v Open (AIO) nebo BizSpark, vaše předplatné se automaticky zakáže při vaše kredity se uplatňují. Další informace o [limitech útraty](#spending-limit) vyhnout předplatného unexpectantly zakázán.

- Pokud nemáte registrovanou službu [bezplatný účet Azure](https://azure.microsoft.com/free/), [můžete použít některé z nejoblíbenějších služeb Azure zdarma po dobu 12 měsíců](billing-create-free-services-included-free-account.md). Spolu s doporučení uvedených níže, naleznete v tématu [vyhnuli účtování zdarma účet](billing-avoid-charges-free-account.md).

> [!div class="nextstepaction"]
> [Pomozte nám vylepšit Azure fakturační dokumentace](https://go.microsoft.com/fwlink/p/?linkid=2010091) 

## <a name="get-estimated-costs-before-adding-azure-services"></a>Získejte odhadované náklady před přidáním služeb Azure

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Odhad nákladů na online pomocí cenové kalkulačky

Podívejte se [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) zobrazíte odhadované měsíční náklady na služby, které vás zajímají. Můžete přidat jakékoli první strany prostředků Azure k získání odhadu nákladů.

![Snímek obrazovky nabídky cenová Kalkulačka](./media/billing-getting-started/pricing-calc.png)

Například virtuální počítač Windows A1 (VM) je odhad nákladů 66.96 USD/měsíc za výpočetních hodin, pokud je necháte spuštěné po celou dobu:

![Snímek obrazovky ukazující, že k virtuálnímu počítači s Windows A1 odhadem nákladů 66.96 USD za měsíc cenové kalkulačky](./media/billing-getting-started/pricing-calcVM.png)

Další informace o cenách najdete v tomto [nejčastější dotazy k](https://azure.microsoft.com/pricing/faq/). Nebo pokud chcete ke komunikaci s Azure prodejce, obraťte se na 1-800-867-1389.

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>Zkontrolujte odhadované náklady na webu Azure Portal

Obvykle po přidání služby na webu Azure Portal je zobrazení, které obsahuje podobné odhadované náklady za měsíc. Například při výběru velikosti virtuálního počítače Windows se zobrazí odhadované měsíční náklady pro výpočetní hodiny:

![Příklad: Virtuálním počítači Windows A1 odhadem nákladů 66.96 USD za měsíc](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="set-up-billing-alerts"></a>Nastavení upozornění fakturace

Nastavení upozornění fakturace k odeslání e-mailů, když vaše náklady na využití přesáhnout částku, kterou zadáte. Pokud máte zdarma měsíční kredit, nastavení upozornění pro po spotřebování zadanou velikost. Další informace najdete v tématu [nastavení upozornění pro předplatná Microsoft Azure fakturace](billing-set-up-alerts.md).

![Snímek obrazovky s fakturační upozornění e-mailu](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> Tato funkce je stále ve verzi preview, abyste měli pravidelně kontrolovat využití.

Můžete chtít použít odhadu nákladů z cenovou kalkulačku jako vodítko pro první výstraha.

### <a name="spending-limit"></a> Zaškrtněte, pokud máte nastavený limit útraty

Pokud máte předplatné, používající kreditů, pak limit útraty je pro vás ve výchozím nastavení zapnutá. Tímto způsobem, při tráví vaše kredity vaší platební karty nebude účtovat. Zobrazit [úplný seznam nabídek Azure a dostupnost limitu útraty](https://azure.microsoft.com/support/legal/offer-details/).

Nicméně pokud dosáhnete limitu útraty, vaše služby zakázán. To znamená, že jsou vaše virtuální počítače uvolní. Výpadky služby, musíte vypnout limit útraty. Získá všechna překročení limitu účtovat na vaší platební karty strženy. 

Pokud chcete zobrazit, pokud jste jste získali limit útraty ve, přejděte na [zobrazit předplatná v centru účtů](https://account.windowsazure.com/Subscriptions). Pokud svůj limit útraty zapnutý, zobrazí se banner:

![Snímek obrazovky s upozorněním útraty, že omezení v centru účtů](./media/billing-getting-started/spending-limit-banner.PNG)

Kliknutím na banner a postupujte podle pokynů k odebrání limitu útraty. Pokud jste nezadali informace o platební kartě při registraci, musíte zadat jeho odebrání limitu útraty. Další informace najdete v tématu [limit útraty Azure – jak funguje a jak ho aktivovat nebo odebrat](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Způsoby, jak sledovat svoje náklady při používání služeb Azure

### <a name="tags"></a> Přidání značek k vašim prostředkům k seskupení fakturačních dat

U podporovaných služeb můžete použít značky do fakturačních dat skupiny. Například při spuštění několika virtuálních počítačů pro různé týmy, pak vám pomůže značky kategorizace nákladů nákladové středisko (HR, marketingu, financí) nebo prostředí (test předprodukčním prostředí, produkčním prostředí). 

![Snímek obrazovky zobrazující nastavení značky na portálu](./media/billing-getting-started/tags.PNG)

Značky se zobrazí v rámci různých náklady na vytváření sestav zobrazení. Například jsou viditelné ve vaší [cost analysis zobrazení](#costs) okamžitě a [podrobně popisují použití CSV](#invoice-and-usage) po jste první fakturační období.

Další informace najdete v tématu [použití značek k uspořádání prostředků Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Pravidelně podívejte se na portál pro rozpis nákladů a pracovního tempa

Po získání svoje služby pravidelně kontrolovat, kolik je už nákladů. Můžete zobrazit aktuální výdaje za a pracovní tempo na webu Azure portal. 

1. Přejděte [okně předplatná na webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a vyberte předplatné.

2. By měl zobrazit rozpis nákladů a pracovní tempo v místním okně. Nemusí být podporované pro vaši nabídku (upozornění bude zobrazen v horní části).

    ![Snímek obrazovky pracovní tempo a rozdělení na webu Azure Portal](./media/billing-getting-started/burn-rate.PNG)

3. Klikněte na tlačítko **analýza nákladů** v seznamu na levé straně si zobrazit rozpis náklady podle prostředků. Počkejte 24 hodin, po přidání služby pro naplní agregovanými daty.

    ![Snímek obrazovky zobrazení analýzy nákladů na webu Azure portal](./media/billing-getting-started/cost-analysis.PNG)

4. Můžete filtrovat podle jiné vlastnosti, jako je [značky](#tags), skupinu prostředků a časový rozsah. Klikněte na tlačítko **použít** potvrďte filtry a **Stáhnout** Pokud chcete exportovat do souboru (CSV) Comma-Separated hodnoty zobrazení.

5. Kromě toho můžete kliknout na prostředek zobrazíte denní věnovat historie a kolik prostředků náklady na každý den.

    ![Snímek obrazovky zobrazení historie nákladů na webu Azure Portal](./media/billing-getting-started/costhistory.PNG)

Doporučujeme zkontrolovat, zda náklady, které se zobrazí odhady, které jste viděli při výběru služby. Pokud náklady na nečekaně liší od odhady, pečlivě zkontrolujte cenový plán (vs A1 virtuální počítač A0, například), kterou jste vybrali pro vaše prostředky. 

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Zvažte povolení náklady společné funkce, jako je automatické vypínání pro virtuální počítače

V závislosti na vašem scénáři můžete nakonfigurovat automatické vypínání pro virtuální počítače na webu Azure Portal. Další informace najdete v tématu [automatické vypínání pro virtuální počítače pomocí Azure Resource Manageru](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Snímek obrazovky s možností automatického vypínání v portálu](./media/billing-getting-started/auto-shutdown.PNG)

Automatického vypínání není stejný jako při vypnutí ve virtuálním počítači s možnosti napájení. Automatické vypnutí, zastaví a uvolní vaše virtuální počítače zastavit poplatky za další využití. Další informace najdete v tématu Nejčastější dotazy týkající se cen [virtuální počítače s Linuxem](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) o stavech virtuálních počítačů.

Pro další náklady na společné funkce pro vývojová a testovací prostředí, projděte si [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Zapnutí a projděte si doporučení Azure Advisoru

[Azure Advisor](../advisor/advisor-overview.md) je funkce, která vám pomůže snížit náklady díky identifikaci prostředky s nízkým využitím. Navštivte Advisor na webu Azure Portal:

![Snímek obrazovky Azure Advisoru tlačítko na webu Azure portal](./media/billing-getting-started/advisor-button.PNG)

Potom získejte užitečná doporučení **náklady** karty na řídicím panelu služby Advisor:

![Příklad doporučení náklady na snímku obrazovky služby Advisor](./media/billing-getting-started/advisor-action.PNG)

Další informace najdete v tématu [doporučení Advisoru náklady](../advisor/advisor-cost-recommendations.md).

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>Kontrola nákladů na konci fakturačního období

Faktuře bude k dispozici na konci fakturačního cyklu. Můžete také [si stáhnout předchozí faktury a podrobnosti o použití souborů](billing-download-azure-invoice-daily-usage-date.md) k Ujistěte se, že se vám účtovat správně. Další informace o porovnávání denním využitím vaše faktura najdete v tématu [vysvětlení vašeho vyúčtování služeb Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>Rozhraní API pro fakturaci

Použijte naše rozhraní API fakturace prostřednictvím kódu programu získat data o využití. Pomocí rozhraní API využití a RateCard API společně se získat účtované využití. Další informace najdete v tématu [získání přehledů o spotřebě prostředků Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Další zdroje informací a zvláštní případy

### <a name="ea-csp-and-sponsorship-customers"></a>Zákazníci EA, CSP a sponzorství
Obraťte se na vašeho account manažera nebo partnera Azure, abyste mohli začít.

| Nabídka | Zdroje a prostředky |
|-------------------------------|-----------------------------------------------------------------------------------|
| Smlouva Enterprise (EA) | [Portál EA](https://ea.azure.com/), [pomáhají dokumentace](https://ea.azure.com/helpdocs), a [sestavy Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | Obraťte se na svého poskytovatele |
| Sponzorství Azure | [Sponzorství portálu](https://www.microsoftazuresponsorships.com/) |

Pokud spravujete IT ve velkých organizacích doporučujeme čtení [Základní kostra Azure enterprise](/azure/architecture/cloud-adoption-guide/subscription-governance) a [podnikové IT dokument white paper](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (PDF ke stažení, jenom v angličtině).

#### <a name="EA"></a> Smlouvy Enterprise Preview nákladů zobrazení v rámci webu Azure Portal 

Zobrazení nákladů Enterprise jsou aktuálně ve verzi Public Preview. Položky k mějte na paměti:
- Náklady na předplatné se na základě využití a nespadá předplacené částky, překročení limitu, zahrnuté množství, úprav a daní. Skutečné náklady se počítají na úrovni registrace. 
- Můžou být zpožděné porovnávané hodnoty na webu Enterprise Portal částky zobrazené na webu Azure portal.  
- Pokud se nezobrazují náklady, může to být způsobené jedním z následujících důvodů:
    - Nemáte dostatečná oprávnění RBAC na úrovni předplatného. Zobrazení nákladů enterprise najdete musí být čtenář fakturace, čtenář, Přispěvatel nebo vlastník na úrovni předplatného.
    - Jste vlastníkem účtu a správce registrace zakázal "AO zobrazení poplatky" nastavení.  Obraťte se na správce registrace se získat přístup k náklady. 
    - Jste správcem oddělení a registraci správce zakázal "DA zobrazení poplatky" nastavení.  Obraťte se na správce registrace k získání přístupu. 
    - Azure zakoupené prostřednictvím partnera kanálu a partnera nevydala informace o cenách.  
- Při aktualizaci nastavení související s náklady na přístup na webu Enterprise portal, je trvat několik minut, než se změny projeví na webu Azure Portal.
- Limit útraty, upozornění fakturace a pokyny k faktuře se netýkají předplatných EA.

### <a name="check-your-subscription-and-access"></a>Zkontrolujte předplatné a přístup

Zobrazení nákladů vyžadují [přístup na úrovni předplatného na fakturační informace](billing-manage-access.md), ale jenom správce účtu může získat přístup k [centra pro účty](https://account.azure.com/Subscriptions), změňte fakturační informace a správě předplatných. Správce účtu je osoba, která prostřednictvím procesu registrace se nepovedlo. Další informace najdete v tématu [přidání nebo změna role Správce služby Azure, které spravují předplatné nebo služby](billing-add-change-azure-subscription-administrator.md).

Pokud chcete zobrazit, pokud jste správce účtu, přejděte na [okně předplatná na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a podívejte se na seznam předplatných, kterým máte přístup. Podívejte se do části **Moje role**. Při stavu *správce účtu*, pak je to ok. Při stavu něco jako *vlastníka*, pak není nutné úplná oprávnění.

![Snímek obrazovky role v okně předplatná na webu Azure Portal](./media/billing-getting-started/sub-blade-view.PNG)

Pokud si nejste správce účtu, pak někdo pravděpodobně zobrazila částečný přístup prostřednictvím [řízení přístupu na základě Role v Azure Active Directory](../role-based-access-control/role-assignments-portal.md) (RBAC). Ke správě předplatných a změnit informace o, fakturaci [Najít účet správce](billing-subscription-transfer.md#whoisaa) a požádejte ho o provedení úlohy nebo [převést toto předplatné se vám](billing-subscription-transfer.md).

Pokud správce účtu už není ve vaší organizaci a potřebujete spravovat fakturace, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 
## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

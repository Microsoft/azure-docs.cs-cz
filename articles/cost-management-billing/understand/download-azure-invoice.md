---
title: Zobrazení a stažení faktury za Azure
description: Popisuje, jak zobrazit a stáhnout fakturu za Azure.
keywords: billing invoice, invoice download, azure invoice, azure usage
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: banders
ms.openlocfilehash: 16534343a831f0802a60a9214f567742153360e0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479009"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Zobrazit a stáhnout fakturu Microsoft Azure

Fakturu si můžete stáhnout na webu [Azure Portal](https://portal.azure.com/) nebo si ji nechat poslat e-mailem. Pokud jste zákazníkem Azure se smlouvou Enterprise (zákazníkem EA), nemůžete stahovat faktury vaší organizace. Faktury se místo toho odesílají osobě, která je nastavená jako příjemce faktur pro danou smlouvu.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="when-invoices-are-generated"></a>Kdy se generují faktury

Faktury se generují na základě typu vašeho fakturačního účtu. Faktury se vytvářejí pro fakturační účty pro program Microsoft Online Service (MOSP), smlouvu se zákazníkem Microsoftu (MCA) a smlouvu s partnerem Microsoftu (MPA). Faktury se generují také pro fakturační účty pro smlouvu Enterprise (EA). Faktury pro fakturační účty EA se však nezobrazují na webu Azure Portal.

Další informace o fakturačních účtech a určení typu fakturačního účtu najdete v tématu [Zobrazení fakturačních účtů na webu Azure Portal](../manage/view-all-accounts.md).

## <a name="invoices-for-mosp-billing-accounts"></a>Faktury pro fakturační účty MOSP

Fakturační účet MOSP se vytvoří při registraci Azure prostřednictvím webu Azure. Když si například zaregistrujete [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [účet s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo jste [předplatitelem sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Fakturační účet pro smlouvu MCA můžou mít zákazníci ve vybraných oblastech, kteří si prostřednictvím webu Azure zaregistrují [účet s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).

Pokud si nejste jistí typem vašeho fakturačního účtu, než budete postupovat podle pokynů v tomto článku, projděte si část [Kontrola typu fakturačního účtu](../manage/view-all-accounts.md#check-the-type-of-your-account). 

Pro fakturační účet MOSP můžou být k dispozici následující faktury:

**Poplatky za služby Azure** –⁠ Faktura se vygeneruje za každé předplatné Azure obsahující prostředky Azure, které předplatné využívá. Faktura obsahuje poplatky za fakturační období. Fakturační období se určuje podle dne v měsíci, kdy se předplatné vytvořilo.

Jan například 5. března vytvoří *předplatné Azure 01* a 10. března vytvoří *předplatné Azure 02*. Faktura za *předplatné Azure 01* bude obsahovat poplatky od pátého dne měsíce do čtvrtého dne následujícího měsíce. Faktura za *předplatné Azure 02* bude obsahovat poplatky od desátého dne měsíce do devátého dne následujícího měsíce. Faktury za všechna předplatná Azure se normálně generují ke dni v měsíci, kdy se účet vytvořil, ale můžou se generovat až o dva dny později. Pokud by Jan v tomto příkladu vytvořil účet 2. února, faktury za *předplatné Azure 01* i *předplatné Azure 02* by se normálně generovaly k druhému dni každého měsíce, ale mohly by se generovat až o dva dny později.

**Azure Marketplace, rezervace a spotové virtuální počítače** –⁠ Faktura se generuje za rezervace, produkty z Marketplace a spotové virtuální počítače zakoupené prostřednictvím předplatného. Tato faktura obsahuje příslušné poplatky za předchozí měsíc. Jan například 1. března zakoupil rezervaci a 30. března další rezervaci. V dubnu se za obě rezervace vygeneruje jedna faktura. Faktury za Azure Marketplace, rezervace a spotové virtuální počítače se vždy generují kolem devátého dne v měsíci.

Pokud za Azure platíte platební kartou a zakoupíte si rezervaci, Azure okamžitě vygeneruje příslušnou fakturu. Pokud však platíte pomocí faktury, rezervace se vám bude účtovat na další měsíční faktuře.

**Plán podpory Azure** –⁠ Faktura za předplatné plánu podpory se generuje každý měsíc. První faktura se vygeneruje v den nákupu nebo až o dva dny později. Následující faktury za plán podpory se normálně generují ke stejnému dni v měsíci, kdy se účet vytvořil, ale můžou se generovat až o dva dny později.

## <a name="download-your-mosp-azure-subscription-invoice"></a>Stažení faktury za předplatné Azure MOSP

Faktura se vygeneruje pouze za předplatné, které patří do fakturačního účtu pro program MOSP. [Zkontrolujte svůj přístup k účtu MOSP](../manage/view-all-accounts.md#check-the-type-of-your-account). 

Ke stažení faktury za předplatné musíte mít roli správce účtu daného předplatného. Uživatelé s rolí vlastníka, přispěvatele nebo čtenáře si můžou danou fakturu stáhnout, pokud jim k tomu správce účtu udělí oprávnění. Další informace najdete v části [Povolení stahování faktur uživatelům](../manage/manage-billing-access.md#opt-in).

1. Vyberte své předplatné na [stránce Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure Portal.
1. V části Fakturace vyberte **Faktury**.  
    ![Snímek obrazovky zachycující výběr možnosti Faktury pro předplatné](./media/download-azure-invoice/select-subscription-invoice.png)
1. Pokud chcete stáhnout fakturu ve verzi PDF, vyberte **Stáhnout** a pak v části Faktura vyberte **Stáhnout**.  
    [![Snímek obrazovky ukazující fakturační období, možnost stažení a celkové poplatky za každé fakturační období](./media/download-azure-invoice/download-invoice-subscription.png)](./media/download-azure-invoice/download-invoice-subscription-zoomed.png#lightbox)
1. Můžete si také stáhnout denní rozpis spotřebovaných prostředků a poplatků, a to výběrem možnosti **Stáhnout** v části Podrobnosti o využití. Příprava souboru CSV může trvat několik minut.  
    ![Snímek obrazovky zachycující stránku pro stažení faktury a dat o využití](./media/download-azure-invoice/usage-and-invoice-subscription.png)

Další informace o své faktuře najdete v tématu [Vysvětlení informací na faktuře za Microsoft Azure](../understand/review-individual-bill.md). Pokud potřebujete pomoc se správou nákladů, přečtěte si, [jak zabránit neočekávaným nákladům v rámci fakturace Azure a jak používat správu nákladů](../manage/getting-started.md).

## <a name="download-your-mosp-support-plan-invoice"></a>Stažení faktury za plán podpory MOSP

Faktura se vygeneruje pouze za předplatné plánu podpory, které patří do fakturačního účtu MOSP. [Zkontrolujte svůj přístup k účtu MOSP](../manage/view-all-accounts.md#check-the-type-of-your-account). 

Ke stažení faktury za předplatné plánu podpory musíte mít roli správce účtu daného předplatného.

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1.  Vyhledejte **Cost Management a fakturace**.  
    ![Snímek obrazovky zachycující hledání položky Cost Management a fakturace na portálu](./media/download-azure-invoice/search-cmb.png)
1.  Na levé straně vyberte **Faktury**.
1.  Vyberte své předplatné plánu podpory a pak vyberte **Stáhnout**.  
    [![Snímek obrazovky se seznamem fakturačních profilů](./media/download-azure-invoice/cmb-invoices.png)](./media/download-azure-invoice/cmb-invoices-zoomed-in.png#lightbox)
1.  Pokud chcete stáhnout fakturu ve verzi PDF, vyberte **Stáhnout**.  
    ![Snímek obrazovky ukazující fakturační období, možnost stažení a celkové poplatky za každé fakturační období](./media/download-azure-invoice/download-invoice-support-plan.png)

## <a name="allow-others-to-download-an-mosp-subscription-invoice"></a>Povolení stahování faktur za předplatné MOSP ostatním uživatelům

Verze PDF faktury obsahuje osobní údaje správce účtu, takže aby si fakturu mohli stáhnout ostatní uživatelé, správce účtu předplatného jim k tomu musí udělit oprávnění. Po udělení oprávnění můžou faktury za předplatné stahovat následující lidé a uživatelé s odpovídajícími rolemi:

- Uživatelé
- Skupiny
- Instanční objekty s vlastníkem
- Přispěvatel
- Čtenář
- Správce přístupu uživatelů
- Čtenář fakturace
- Spolusprávce
- Správce služeb

Stažení faktury:

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako správce účtu předplatného.
1.  Vyhledejte **Cost Management a fakturace**.  
1.  Na levé straně vyberte **Faktury**.
1.  Vyberte své předplatné Azure a pak vyberte **Přístup k faktuře**.  
    [![Snímek obrazovky znázorňující výběr možnosti Přístup k faktuře](./media/download-azure-invoice/cmb-select-access-to-invoice.png)](./media/download-azure-invoice/cmb-select-access-to-invoice-zoomed-in.png#lightbox)
1.  Vyberte **Zapnuto** a pak v horní části stránky vyberte **Uložit**.  
    ![Snímek obrazovky znázorňující výběr možnosti Zapnuto v části Přístup k faktuře](./media/download-azure-invoice/cmb-access-to-invoice.png)

## <a name="get-mosp-subscription-invoice-in-email"></a>Zaslání faktury za předplatné MOSP e-mailem

Pokud se chcete přihlásit k zasílání faktur za předplatné nebo plán podpory e-mailem, musíte mít roli správce účtu daného předplatného nebo plánu podpory. Zasílání faktur e-mailem je dostupné pouze pro předplatná a plány podpory, ne pro rezervace nebo nákupy na Azure Marketplace. Po přihlášení k zasílání můžete přidat další příjemce, kterým se také budou zasílat faktury e-mailem.

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1.  Vyhledejte **Cost Management a fakturace**.  
1.  Na levé straně vyberte **Faktury**.
1.  Vyberte své předplatné Azure nebo předplatné plánu podpory a pak vyberte **Poslat fakturu e-mailem**.  
    [![Snímek obrazovky se seznamem fakturačních profilů](./media/download-azure-invoice/cmb-email-invoice.png)](./media/download-azure-invoice/cmb-email-invoice-zoomed-in.png#lightbox)
1. Vyberte **Přihlásit** a přijměte podmínky.  
    ![Snímek obrazovky, který ukazuje průběh přihlášení k zasílání, krok 2](./media/download-azure-invoice/invoice-article-step02.png)
1. Faktura se odešle na e-mailovou adresu, kterou upřednostňujete pro komunikaci. Tuto e-mailovou adresu můžete aktualizovat v [kontaktních údajích vašeho fakturačního účtu](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/Properties).  
    ![Snímek obrazovky, který ukazuje průběh přihlášení k zasílání, krok 3](./media/download-azure-invoice/invoice-article-step03-verify-email.png)

## <a name="share-your-mosp-invoices-with-others-by-email"></a>Sdílení faktur MOSP s ostatními e-mailem

Možná budete chtít každý měsíc sdílet faktury za vaše předplatné a plán podpory s vaším účetním týmem nebo si je poslat na jinou e-mailovou adresu.

1. Postupujte podle pokynů v části [Zasílání faktur za předplatné a plán podpory e-mailem](#get-mosp-subscription-invoice-in-email) a vyberte **Konfigurovat příjemce**.  
    ![Snímek obrazovky zachycující výběr možnosti Konfigurovat příjemce](./media/download-azure-invoice/invoice-article-step03.png)
1. Zadejte e-mailovou adresu a pak vyberte **Přidat příjemce**. Můžete přidat několik e-mailových adres.  
    ![Snímek obrazovky zachycující přidání dalších příjemců](./media/download-azure-invoice/invoice-article-step04.png)
1. Po přidání všech e-mailových adres v dolní části obrazovky vyberte **Hotovo**.

## <a name="invoices-for-mca-and-mpa-billing-accounts"></a>Faktury pro fakturační účty MCA a MPA

Fakturační účet MCA se vytvoří, když vaše organizace ve spolupráci se zástupcem Microsoftu podepíše smlouvu MCA. Fakturační účet pro smlouvu MCA můžou mít i někteří zákazníci ve vybraných oblastech, kteří si prostřednictvím webu Azure zaregistrují [účet s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/). Další informace najdete v tématu [Začínáme s fakturačním účtem MCA](../understand/mca-overview.md).

Fakturační účty MPA se vytváří pro partnery CSP (Cloud Solution Provider), aby mohli spravovat své zákazníky v novém komerčním prostředí. Aby mohli partneři spravovat svůj fakturační účet na webu Azure Portal, musí mít nejméně jednoho zákazníka s [plánem Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan). Další informace najdete v tématu [Začínáme s fakturačním účtem MPA](../understand/mpa-overview.md).

Na začátku kalendářního měsíce se pro každý fakturační profil na vašem účtu vygeneruje měsíční faktura. Ta obsahuje příslušné poplatky za všechna předplatná Azure a další nákupy z předchozího měsíce. Jan například 5. března vytvořil *předplatné Azure 01* a 10. března vytvořil *předplatné Azure 02*. 28. března zakoupil předplatné *podpory Azure 01* s použitím *fakturačního profilu 01*. Jan na začátku dubna obdrží jednu fakturu, která bude obsahovat poplatky za obě předplatná Azure i plán podpory.

##  <a name="download-an-mca-or-mpa-billing-profile-invoice"></a>Stažení faktury pro fakturační profil MCA nebo MPA

Ke stažení faktury pro fakturační profil na webu Azure Portal musíte mít roli vlastníka, přispěvatele, čtenáře nebo správce faktur daného fakturačního profilu. Uživatelé s rolí vlastníka, přispěvatele nebo čtenáře fakturačního účtu můžou stahovat faktury pro všechny fakturační profily v daném účtu.

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1.  Vyhledejte **Cost Management a fakturace**.  
1. Na levé straně vyberte **Faktury**.
    [![Snímek obrazovky zachycující stránku s fakturami pro fakturační účet MCA](./media/download-azure-invoice/mca-billing-profile-invoices.png)](./media/download-azure-invoice/mca-billing-profile-invoices-zoomed.png#lightbox)
1. V tabulce s fakturami vyberte fakturu, kterou chcete stáhnout.
1. V horní části stránky vyberte **Stáhnout fakturu v PDF**.  
    ![Snímek obrazovky znázorňující stažení faktury v PDF](./media/download-azure-invoice/mca-billing-profile-download-invoice.png)
1. Můžete si také stáhnout soubor CSV s denním rozpisem spotřebovaných prostředků a odhadovaných poplatků, a to výběrem možnosti **Stáhnout data o využití Azure**. Příprava souboru CSV může trvat několik minut.

## <a name="get-your-billing-profiles-invoice-in-email"></a>Zaslání faktury pro váš fakturační profil e-mailem

K aktualizaci předvolby zasílání faktury e-mailem pro fakturační profil musíte mít roli vlastníka nebo přispěvatele daného fakturačního profilu nebo jeho fakturačního účtu. Po přihlášení k zasílání pak fakturu pro fakturační profil obdrží e-mailem všichni uživatelé s rolí vlastník, přispěvatel, čtenář nebo správce faktur daného fakturačního profilu. 

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1.  Vyhledejte **Cost Management a fakturace**.  
1.  Na levé straně vyberte **Faktury** a pak v horní části stránky vyberte **Poslat fakturu e-mailem**.  
    [![Snímek obrazovky zachycující stránku s fakturami pro fakturační účet MCA](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Pokud máte více fakturačních profilů, vyberte fakturační profil a pak vyberte **Přihlásit**.  
    ![Snímek obrazovky zachycující stránku s fakturami pro fakturační účet MCA](./media/download-azure-invoice/mca-billing-profile-email-invoice.png)
1.  Vyberte **Aktualizovat**.

## <a name="give-others-access-to-mca-or-mpa-invoices"></a>Udělení přístup k fakturám MCA nebo MPA ostatním uživatelům

I jiným uživatelům můžete umožnit zobrazování, stahování a placení faktur tím, že jim pro daný fakturační profil MCA nebo MPA přiřadíte roli správce faktur. Pokud jste se rozhodli pro zasílání faktur e-mailem, dostanou je i tito uživatelé stejným způsobem.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Cost Management a fakturace**.  
1. Na levé straně vyberte **Fakturační profily**. V seznamu fakturačních profilů vyberte profil, u kterého chcete přiřadit roli správce faktur.  
   ![Snímek obrazovky se seznamem fakturačních profilů](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)
1. Na levé straně vyberte **Řízení přístupu (IAM)** a pak v horní části stránky vyberte **Přidat**.  
    ![Snímek obrazovky zachycující stránku řízení přístupu](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)
1. V rozevíracím seznamu Role vyberte roli **Správce faktur**. Zadejte e-mailovou adresu uživatele, kterému chcete udělit přístup. Přiřaďte roli výběrem možnosti **Uložit**.  
   ![Snímek obrazovky zachycující přidání uživatele jako správce faktur](./media/download-azure-invoice/mca-added-invoice-manager.png)

## <a name="why-you-might-not-see-an-invoice"></a><a name="noinvoice"></a>Proč nemůžete zobrazit fakturu

Faktura se nemusí zobrazovat z několika důvodů:

- Od vytvoření vašeho předplatného Azure uplynulo méně než 30 dnů. 
- Azure se fakturuje několik dnů po konci fakturačního období. Proto se faktura možná ještě nevygenerovala.
- Nemáte oprávnění k zobrazení faktur.
    Pokud máte fakturační účet MCA nebo MPA, k zobrazení faktur musíte mít roli vlastníka, přispěvatele, čtenáře nebo správce faktur fakturačního profilu nebo roli vlastníka, přispěvatele nebo čtenáře fakturačního účtu. U jiných předplatných se může stát, že pokud nejste správcem účtu, nemusí se vám zobrazovat faktury. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](../manage/manage-billing-access.md).
- Pokud máte fakturační účet MOSP a zaregistrovali jste si bezplatný účet Azure nebo předplatné s měsíčním kreditem, faktura se vám vystaví pouze po překročení částky měsíčního kreditu. Pokud máte fakturační účet pro smlouvu MCA nebo MPA, faktura se vám vystaví vždy.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o vaší faktuře a poplatcích najdete tady:

- [Zobrazení a stažení informací o využití a poplatcích Microsoft Azure](download-azure-daily-usage.md)
- [Vysvětlení informací na faktuře za Microsoft Azure](review-individual-bill.md)
- [Vysvětlení výrazů na faktuře Azure](understand-invoice.md)

Pokud máte smlouvu MCA, přečtěte si:

- [Vysvětlení poplatků na faktuře pro váš fakturační profil](review-customer-agreement-bill.md)
- [Vysvětlení výrazů na faktuře pro váš fakturační profil](mca-understand-your-invoice.md)
- [Vysvětlení souboru s informacemi o využití a poplatcích za Azure pro váš fakturační profil](mca-understand-your-usage.md)



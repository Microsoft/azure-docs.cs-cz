---
title: Zobrazení a stažení faktury za Azure
description: Přečtěte si, jak zobrazit a stáhnout fakturu Azure. Fakturu si můžete stáhnout do Azure Portal nebo ji nechat odeslat e-mailem.
keywords: billing invoice, invoice download, azure invoice, azure usage
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 37ce1a292b6ff2efe0abecdb2ab934f096689f87
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "105560565"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Zobrazit a stáhnout fakturu Microsoft Azure

Fakturu si můžete stáhnout na webu [Azure Portal](https://portal.azure.com/) nebo si ji nechat poslat e-mailem. Pokud jste zákazníkem Azure se smlouvou Enterprise (zákazníkem EA), nemůžete stahovat faktury vaší organizace. Faktury se místo toho odesílají osobě, která je nastavená jako příjemce faktur pro danou smlouvu.

## <a name="when-invoices-are-generated"></a>Kdy se generují faktury

Faktury se generují na základě typu vašeho fakturačního účtu. Faktury se vytvářejí pro fakturační účty pro program Microsoft Online Service (MOSP), smlouvu se zákazníkem Microsoftu (MCA) a smlouvu s partnerem Microsoftu (MPA). Faktury se generují také pro fakturační účty pro smlouvu Enterprise (EA). Faktury pro fakturační účty EA se však nezobrazují na webu Azure Portal.

Další informace o fakturačních účtech a určení typu fakturačního účtu najdete v tématu [Zobrazení fakturačních účtů na webu Azure Portal](../manage/view-all-accounts.md).

### <a name="invoice-status"></a>Stav faktury

Při kontrole stavu faktury v Azure Portal má každá faktura jeden z následujících symbolů stavu.

|  Symbol stavu | Description  |
|---|---|
| ![Symbol stavu termínu splnění](./media/download-azure-invoice/due.svg) | *Termín splnění* se zobrazí při vygenerování faktury, ale ještě nebyl zaplacen. |
| ![Symbol stavu po termínu splnění](./media/download-azure-invoice/past-due.svg)  | Po *termínu splnění* se služba Azure snažila účtovat způsob platby, ale platba byla odmítnuta. |
| ![Symbol placeného stavu](./media/download-azure-invoice/paid.svg)  | *Placený* stav se zobrazí, když Azure úspěšně účtuje způsob platby. |

Při vytvoření faktury se zobrazí v Azure Portal se stavem *splnění* . Termín splnění je normální a byl očekáván.  

Když faktura není placená, stav se zobrazí jako *po splatnosti*. Předplatné po splatnosti se zakáže, pokud faktura není placená.

## <a name="invoices-for-mosp-billing-accounts"></a>Faktury pro fakturační účty MOSP

Fakturační účet MOSP se vytvoří při registraci Azure prostřednictvím webu Azure. Když si například zaregistrujete [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [účet s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo jste [předplatitelem sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Fakturační účet pro smlouvu MCA můžou mít zákazníci ve vybraných oblastech, kteří si prostřednictvím webu Azure zaregistrují [účet s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).

Pokud si nejste jistí typem vašeho fakturačního účtu, než budete postupovat podle pokynů v tomto článku, projděte si část [Kontrola typu fakturačního účtu](../manage/view-all-accounts.md#check-the-type-of-your-account). 

Pro fakturační účet MOSP můžou být k dispozici následující faktury:

**Poplatky za služby Azure** –⁠ Faktura se vygeneruje za každé předplatné Azure obsahující prostředky Azure, které předplatné využívá. Faktura obsahuje poplatky za fakturační období. Fakturační období se určuje podle dne v měsíci, kdy se předplatné vytvořilo.

Jan například 5. března vytvoří *předplatné Azure 01* a 10. března vytvoří *předplatné Azure 02*. Faktura za *předplatné Azure 01* bude obsahovat poplatky od pátého dne měsíce do čtvrtého dne následujícího měsíce. Faktura za *předplatné Azure 02* bude obsahovat poplatky od desátého dne měsíce do devátého dne následujícího měsíce. Faktury za všechna předplatná Azure se normálně generují ke dni v měsíci, kdy se účet vytvořil, ale můžou se generovat až o dva dny později. Pokud by Jan v tomto příkladu vytvořil účet 2. února, faktury za *předplatné Azure 01* i *předplatné Azure 02* by se normálně generovaly k druhému dni každého měsíce, ale mohly by se generovat až o dva dny později.

**Azure Marketplace, rezervace a spotové virtuální počítače** –⁠ Faktura se generuje za rezervace, produkty z Marketplace a spotové virtuální počítače zakoupené prostřednictvím předplatného. Tato faktura obsahuje příslušné poplatky za předchozí měsíc. Jan například 1. března zakoupil rezervaci a 30. března další rezervaci. V dubnu se za obě rezervace vygeneruje jedna faktura. Faktury za Azure Marketplace, rezervace a spotové virtuální počítače se vždy generují kolem devátého dne v měsíci.

Pokud za Azure platíte platební kartou a zakoupíte si rezervaci, Azure okamžitě vygeneruje příslušnou fakturu. Pokud však platíte pomocí faktury, rezervace se vám bude účtovat na další měsíční faktuře.

**Plán podpory Azure** –⁠ Faktura za předplatné plánu podpory se generuje každý měsíc. První faktura se vygeneruje v den nákupu nebo až o dva dny později. Další faktury za plán podpory se normálně generují ke stejnému dni v měsíci, kdy se účet vytvořil, ale můžou se generovat až o dva dny později.

## <a name="download-your-mosp-azure-subscription-invoice"></a>Stažení faktury za předplatné Azure MOSP

Faktura se vygeneruje pouze za předplatné, které patří do fakturačního účtu pro program MOSP. [Zkontrolujte svůj přístup k účtu MOSP](../manage/view-all-accounts.md#check-the-type-of-your-account). 

Ke stažení faktury za předplatné musíte mít roli správce účtu daného předplatného. Uživatelé s rolí vlastníka, přispěvatele nebo čtenáře si můžou danou fakturu stáhnout, pokud jim k tomu správce účtu udělí oprávnění. Další informace najdete v části [Povolení stahování faktur uživatelům](../manage/manage-billing-access.md#opt-in).

1. Vyberte své předplatné na [stránce Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure Portal.
1. V části Fakturace vyberte **Faktury**.  
    ![Snímek obrazovky zachycující výběr možnosti Faktury pro předplatné](./media/download-azure-invoice/select-subscription-invoice.png)
1. Vyberte fakturu, kterou chcete stáhnout, a pak klikněte na **Stáhnout faktury**.  
    ![Snímek obrazovky s možností stažení MOSP faktury](./media/download-azure-invoice/downloadinvoice-subscription.png)
1. Můžete si také stáhnout denní rozpis spotřebovaných množství a poplatků kliknutím na ikonu stáhnout a potom kliknutím na tlačítko **připravit soubor využití Azure** v části Podrobnosti o využití. Příprava souboru CSV může trvat několik minut.  
    ![Snímek obrazovky zachycující stránku pro stažení faktury a dat o využití](./media/download-azure-invoice/usage-and-invoice-subscription.png)

Další informace o své faktuře najdete v tématu [Vysvětlení informací na faktuře za Microsoft Azure](../understand/review-individual-bill.md). Nápovědu k identifikaci neobvyklých nákladů najdete v tématu [Analýza neočekávaných poplatků](analyze-unexpected-charges.md).

## <a name="download-your-mosp-support-plan-invoice"></a>Stažení faktury za plán podpory MOSP

Faktura se vygeneruje pouze za předplatné plánu podpory, které patří do fakturačního účtu MOSP. [Zkontrolujte svůj přístup k účtu MOSP](../manage/view-all-accounts.md#check-the-type-of-your-account).

Ke stažení faktury za předplatné plánu podpory musíte mít roli správce účtu daného předplatného.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Cost Management a fakturace**.  
    ![Snímek obrazovky zachycující hledání položky Cost Management a fakturace na portálu](./media/download-azure-invoice/search-cmb.png)
1. Na levé straně vyberte **Faktury**.
1. Vyberte předplatné plánu podpory.  
    [![Snímek obrazovky zobrazující seznam fakturačního profilu faktury plánu MOSP support](./media/download-azure-invoice/cmb-invoices.png)](./media/download-azure-invoice/cmb-invoices-zoomed-in.png#lightbox)
1. Vyberte fakturu, kterou chcete stáhnout, a pak klikněte na **Stáhnout faktury**.  
    ![Snímek obrazovky zobrazující možnost stažení pro fakturu plánu podpory MOSP ](./media/download-azure-invoice/download-invoice-support-plan.png)

## <a name="allow-others-to-download-your-subscription-invoice"></a>Umožnit ostatním stažení vaší faktury předplatného

Stažení faktury:

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako správce účtu předplatného.

2.  Vyhledejte **Cost Management a fakturace**.

    ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace](./media/download-azure-invoice/search-cmb.png)

3.  Na levé straně vyberte **Faktury**.

4.  Vyberte předplatné Azure a potom klikněte na **Allow others to download invoice** (Umožnit ostatním stáhnout fakturu).

    [![Snímek obrazovky znázorňující výběr možnosti Přístup k faktuře](./media/download-azure-invoice/cmb-select-access-to-invoice.png)](./media/download-azure-invoice/cmb-select-access-to-invoice-zoomed-in.png#lightbox)

5.  Vyberte **Zapnuto** a pak v horní části stránky vyberte **Uložit**.  
    ![Snímek obrazovky znázorňující výběr možnosti Zapnuto v části Přístup k faktuře](./media/download-azure-invoice/cmb-access-to-invoice.png)
    
> [!NOTE]
> Společnost Microsoft nedoporučuje sdílet žádné důvěrné nebo identifikovatelné osobní údaje s třetími stranami. Toto doporučení se týká sdílení faktury za Azure nebo faktury s třetí stranou pro optimalizaci nákladů. Další informace naleznete v tématech https://azure.microsoft.com/support/legal/ a https://www.microsoft.com/trust-center.

## <a name="get-mosp-subscription-invoice-in-email"></a>Zaslání faktury za předplatné MOSP e-mailem

Pokud se chcete přihlásit k zasílání faktur za předplatné nebo plán podpory e-mailem, musíte mít roli správce účtu daného předplatného nebo plánu podpory. Zasílání faktur e-mailem je dostupné pouze pro předplatná a plány podpory, ne pro rezervace nebo nákupy na Azure Marketplace. Po přihlášení k zasílání můžete přidat další příjemce, kterým se také budou zasílat faktury e-mailem.

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2.  Vyhledejte **Cost Management a fakturace**.  
3.  Na levé straně vyberte **Faktury**.
4.  Vyberte své předplatné Azure nebo předplatné plánu podpory a pak vyberte **Receive invoice by email** (Dostat fakturu e-mailem).  
    [![Snímek obrazovky, který ukazuje možnost přijetí faktury e-mailem](./media/download-azure-invoice/cmb-email-invoice.png)](./media/download-azure-invoice/cmb-email-invoice-zoomed-in.png#lightbox)
5. Klikněte na **Email invoice** (Faktura e-mailem) a přijměte podmínky.  
    ![Snímek obrazovky, který ukazuje průběh přihlášení k zasílání, krok 2](./media/download-azure-invoice/invoicearticlestep02.png)
6. Faktura se odešle na e-mailovou adresu, kterou upřednostňujete pro komunikaci. Pokud chcete e-mailovou adresu aktualizovat, vyberte **Aktualizovat profil**.  
    ![Snímek obrazovky, který ukazuje průběh přihlášení k zasílání, krok 3](./media/download-azure-invoice/invoicearticlestep03-verifyemail.png)

## <a name="share-subscription-and-support-plan-invoice"></a>Sdílet fakturu s plánem pro předplatné a podporu

Můžete chtít sdílet fakturu pro předplatné a plán podpory každý měsíc v rámci svého účetního týmu nebo je odeslat na jednu z vašich jiných e-mailových adres.

1. Postupujte podle pokynů v části [Zasílání faktur za předplatné a plán podpory e-mailem](#get-mosp-subscription-invoice-in-email) a vyberte **Konfigurovat příjemce**.  
    [![Snímek obrazovky zobrazující uživatele, který vybere možnost konfigurovat příjemce](./media/download-azure-invoice/invoice-article-step03.png)](./media/download-azure-invoice/invoice-article-step03-zoomed.png#lightbox)
1. Zadejte e-mailovou adresu a pak vyberte **Přidat příjemce**. Můžete přidat několik e-mailových adres.  
    ![Snímek obrazovky zachycující přidání dalších příjemců](./media/download-azure-invoice/invoice-article-step04.png)
1. Po přidání všech e-mailových adres v dolní části obrazovky vyberte **Hotovo**.

## <a name="invoices-for-mca-and-mpa-billing-accounts"></a>Faktury pro fakturační účty MCA a MPA

Fakturační účet MCA se vytvoří, když vaše organizace ve spolupráci se zástupcem Microsoftu podepíše smlouvu MCA. Fakturační účet pro smlouvu MCA můžou mít i někteří zákazníci ve vybraných oblastech, kteří si prostřednictvím webu Azure zaregistrují [účet s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/). Další informace najdete v tématu [Začínáme s fakturačním účtem MCA](../understand/mca-overview.md).

Fakturační účty MPA se vytváří pro partnery CSP (Cloud Solution Provider), aby mohli spravovat své zákazníky v novém komerčním prostředí. Aby mohli partneři spravovat svůj fakturační účet na webu Azure Portal, musí mít nejméně jednoho zákazníka s [plánem Azure](/partner-center/purchase-azure-plan). Další informace najdete v tématu [Začínáme s fakturačním účtem MPA](../understand/mpa-overview.md).

Na začátku kalendářního měsíce se pro každý fakturační profil na vašem účtu vygeneruje měsíční faktura. Ta obsahuje příslušné poplatky za všechna předplatná Azure a další nákupy z předchozího měsíce. Jan například 5. března vytvořil *předplatné Azure 01* a 10. března vytvořil *předplatné Azure 02*. 28. března zakoupil předplatné *podpory Azure 01* s použitím *fakturačního profilu 01*. Jan na začátku dubna obdrží jednu fakturu, která bude obsahovat poplatky za obě předplatná Azure i plán podpory.

##  <a name="download-an-mca-or-mpa-billing-profile-invoice"></a>Stažení faktury pro fakturační profil MCA nebo MPA

Ke stažení faktury pro fakturační profil na webu Azure Portal musíte mít roli vlastníka, přispěvatele, čtenáře nebo správce faktur daného fakturačního profilu. Uživatelé s rolí vlastníka, přispěvatele nebo čtenáře fakturačního účtu můžou stahovat faktury pro všechny fakturační profily v daném účtu.

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2.  Vyhledejte **Cost Management a fakturace**.

    ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace](./media/download-azure-invoice/search-cmb.png)

3. Na levé straně vyberte **Faktury**.

    [![Snímek obrazovky zachycující stránku s fakturami pro fakturační účet MCA](./media/download-azure-invoice/mca-billingprofile-invoices.png)](./media/download-azure-invoice/mca-billingprofile-invoices-zoomed-in.png#lightbox)

4. V tabulce s fakturami vyberte fakturu, kterou chcete stáhnout.

5. V horní části stránky klikněte na tlačítko **Download invoice pdf** (Stáhnout fakturu v PDF).

    [![Snímek obrazovky znázorňující stažení faktury v PDF](./media/download-azure-invoice/mca-billingprofile-download-invoice.png)](./media/download-azure-invoice/mca-billingprofile-download-invoice-zoomed-in.png#lightbox)

6. Můžete si také stáhnout denní rozpis spotřebovaných prostředků a odhadované poplatky, a to kliknutím na **Download Azure usage** (Stáhnout využití Azure). Příprava souboru CSV může trvat několik minut.

## <a name="get-your-billing-profiles-invoice-in-email"></a>Zaslání faktury pro váš fakturační profil e-mailem

K aktualizaci předvolby zasílání faktury e-mailem pro fakturační profil musíte mít roli vlastníka nebo přispěvatele daného fakturačního profilu nebo jeho fakturačního účtu. Po přihlášení k zasílání pak fakturu pro fakturační profil obdrží e-mailem všichni uživatelé s rolí vlastník, přispěvatel, čtenář nebo správce faktur daného fakturačního profilu. 

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1.  Vyhledejte **Cost Management a fakturace**.  
1.  Na levé straně vyberte **faktury** a pak v horní části stránky vyberte **Předvolby e-mailu faktury** .  
    [![Snímek obrazovky zobrazující možnost faktury e-mailu pro faktury](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Pokud máte více fakturačních profilů, vyberte profil fakturace a pak vyberte **Ano**.  
    [![Snímek obrazovky zobrazující možnost výslovného souhlasu](./media/download-azure-invoice/mca-billing-profile-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Vyberte **Uložit**.

I jiným uživatelům můžete umožnit zobrazování, stahování a placení faktur tím, že jim pro daný fakturační profil MCA nebo MPA přiřadíte roli správce faktur. Pokud jste se rozhodli pro zasílání faktur e-mailem, dostanou je i tito uživatelé stejným způsobem.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Cost Management a fakturace**.  
1. Na levé straně vyberte **Fakturační profily**. V seznamu fakturačních profilů vyberte profil, u kterého chcete přiřadit roli správce faktur.  
   ![Snímek obrazovky, který zobrazuje seznam profilů fakturace, kde vyberete profil fakturace](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)
1. Na levé straně vyberte **Řízení přístupu (IAM)** a pak v horní části stránky vyberte **Přidat**.  
    ![Snímek obrazovky zachycující stránku řízení přístupu](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)
1. V rozevíracím seznamu Role vyberte roli **Správce faktur**. Zadejte e-mailovou adresu uživatele, kterému chcete udělit přístup. Přiřaďte roli výběrem možnosti **Uložit**.  
    [![Snímek obrazovky zachycující přidání uživatele jako správce faktur](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)
   

## <a name="share-your-billing-profiles-invoice"></a>Sdílení faktury vašeho fakturačního profilu

Fakturu můžete sdílet každý měsíc s vaším účetním týmem nebo je odeslat na jednu z vašich jiných e-mailových adres, aniž byste k vašemu fakturačnímu profilu museli udělit svůj účetní tým nebo jiné oprávnění e-mailu.

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1.  Vyhledejte **Cost Management a fakturace**.  
1.  Na levé straně vyberte **faktury** a pak v horní části stránky vyberte **Předvolby e-mailu faktury** .  
    [![Snímek obrazovky zobrazující možnost faktury e-mailu pro faktury](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Pokud máte více fakturačních profilů, vyberte fakturační profil.
1.  V části Další příjemci přidejte e-mailové adresy, na které se mají dostávat faktury.
    [![Snímek obrazovky zobrazující další příjemce e-mailové faktury](./media/download-azure-invoice/mca-billing-profile-add-invoice-recipients.png)](./media/download-azure-invoice/mca-billing-profile-add-invoice-recipients-zoomed.png#lightbox)
1.  Vyberte **Uložit**.

##  <a name="why-you-might-not-see-an-invoice"></a>Proč nemůžete zobrazit fakturu?

<a name="noinvoice"></a>

Faktura se nemusí zobrazovat z několika důvodů:

- Faktura ještě není připravená.
    
    - Od vytvoření vašeho předplatného Azure uplynulo méně než 30 dnů. 

    - Azure se fakturuje několik dnů po konci fakturačního období. Proto se faktura možná ještě nevygenerovala.

- Nemáte oprávnění k zobrazení faktur. 
    
    - Pokud máte fakturační účet MCA nebo MPA, k zobrazení faktur musíte mít roli vlastníka, přispěvatele, čtenáře nebo správce faktur fakturačního profilu nebo roli vlastníka, přispěvatele nebo čtenáře fakturačního účtu. 
    
    - U jiných fakturačních účtů se může stát, že pokud nejste správcem účtu, nemusí se vám zobrazovat faktury.

- Váš účet nepodporuje faktury.

    - Pokud máte fakturační účet pro MOSP (Microsoft Online Services Program) a zaregistrovali jste si bezplatný účet Azure nebo předplatné s měsíčním kreditem, faktura se vám vystaví pouze po překročení částky měsíčního kreditu.

    - Pokud máte fakturační účet pro Smlouvu se zákazníkem Microsoftu (MCA) nebo Smlouvu s partnerem Microsoftu (MPA), faktura se vám vystaví vždy.

- K faktuře máte přístup prostřednictvím některého z vašich dalších účtů.

    - K této situaci obvykle dochází, když v e-mailu kliknete na odkaz pro zobrazení faktury na portálu. Kliknete na odkaz a zobrazí se chybová zpráva: `We can't display your invoices. Please try again`. Ověřte, že jste přihlášeni pomocí e-mailové adresy, která má oprávnění k zobrazení faktur.

- K faktuře máte přístup prostřednictvím jiné identity. 

    - Někteří zákazníci mají dvě identity se stejnou e-mailovou adresou – pracovní účet a účet Microsoft. K zobrazení faktur ale obvykle má oprávnění jenom jedna z těchto identit. Pokud se přihlásí k identitě, která nemá příslušné oprávnění, faktury neuvidí. Ověřte, že pro přihlášení používáte správnou identitu.

- Přihlásili jste se k nesprávnému tenantovi Azure Active Directory (Azure AD). 

    - Fakturační účet je přidružený k tenantovi služby Azure AD. Pokud se přihlásíte k nesprávnému klientovi, ve fakturačním účtu se vám nezobrazí faktura za předplatná. Ověřte, že jste přihlášeni ke správnému tenantovi služby Azure AD. Pokud nejste přihlášení ke správnému tenantovi, můžete pro přepnutí tenanta na webu Azure Portal použít následující postup:

        1. V pravém horním rohu stránky vyberte váš e-mail.

        2. Vyberte **Přepnout adresář**.

           ![Snímek obrazovky zachycující výběr položky pro přepnutí adresáře na portálu](./media/download-azure-invoice/select-switch-directory.png)

        3. V části **Všechny adresáře** vyberte požadovaný adresář.

           ![Snímek obrazovky zachycující výběr adresáře na portálu](./media/download-azure-invoice/select-directory.png)

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
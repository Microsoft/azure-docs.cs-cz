---
title: Stažení faktury za Azure a dat o denním využití
description: Vysvětluje, jak stáhnout nebo zobrazit fakturu za Azure a data o denním využití.
keywords: billing invoice,invoice download,azure invoice,azure usage
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/28/2020
ms.author: banders
ms.openlocfilehash: e380aa1f4d50e6ced34254ceca9d899022142f6d
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911286"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Stažení nebo zobrazení faktury za Azure a dat o denním využití

U většiny předplatných si můžete fakturu stáhnout z webu [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nebo si ji nechat poslat e-mailem. Pokud jste zákazníkem Azure se smlouvou Enterprise (zákazníkem EA), nemůžete stahovat faktury vaší organizace. Faktury se odesílají osobě, která je nastavená jako příjemce faktur pro danou smlouvu.

Pokud jste zákazníkem se smlouvou Enterprise nebo máte [smlouvu se zákazníkem Microsoftu](#check-access-to-a-microsoft-customer-agreement), můžete si z webu [Azure Portal](https://portal.azure.com/) stáhnout údaje o využití.

K získání faktury a informací o využití mají oprávnění jenom některé role, třeba správce účtu nebo podnikový správce. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](manage-billing-access.md).

Pokud máte smlouvu se zákazníkem Microsoftu a chcete zobrazit informace o fakturaci a využití, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktur daného fakturačního profilu. Další informace o fakturačních rolích u smluv se zákazníkem Microsoftu najdete v tématu [Role a úlohy související s fakturačním profilem](understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Stahování faktur za Azure (PDF)

U většiny předplatných si můžete fakturu stáhnout z webu Azure Portal. Pokud máte smlouvu se zákazníkem Microsoftu, projděte si téma Stahování faktur pro fakturační profil.

### <a name="download-invoices-for-an-individual-subscription"></a>Stahování faktur pro jedno předplatné

1. Vyberte své předplatné na stránce [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure Portal jako [uživatel s přístupem k fakturám](manage-billing-access.md).

2. Vyberte **Faktury**.

    ![Snímek obrazovky znázorňující možnost Využití a fakturace](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. Pokud si chcete stáhnout kopii faktury ve formátu PDF, klikněte na tlačítko pro stažení si a potom vyberte **Stáhnout fakturu**. Pokud se zobrazí **Není k dispozici** , přečtěte si téma [Proč se mi nezobrazuje faktura za poslední fakturační období?](#noinvoice)

    ![Snímek obrazovky ukazující fakturační období, možnost stažení a celkové poplatky za každé fakturační období](./media/download-azure-invoice-daily-usage-date/downloadinvoice.png)

4. Můžete si také stáhnout denní rozpis spotřebovaných prostředků a odhadované poplatky, a to kliknutím na **Stáhnout CSV**.

    ![Screenshot zobrazující stránku využití a stažení faktury](./media/download-azure-invoice-daily-usage-date/usageandinvoice.png)

Další informace o své faktuře najdete v tématu [Vysvětlení informací na faktuře za Microsoft Azure](../understand/review-individual-bill.md). Pokud potřebujete pomoc se správou nákladů, přečtěte si téma [Analýza neočekávaných poplatků](../understand/analyze-unexpected-charges.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Stahování faktur pro smlouvu se zákazníkem Microsoftu

Faktury se generují pro každý [fakturační profil](../understand/mca-overview.md#billing-profiles) ve smlouvě se zákazníkem Microsoftu. Pokud chcete stahovat faktury z webu Azure Portal, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktur daného fakturačního profilu.

1. Vyhledejte **Cost Management a fakturace**.
2. Vyberte fakturační profil.
3. Vyberte **Faktury**.
4. V tabulce faktur najděte řádek s fakturou, kterou chcete stáhnout.
5. Klikněte na tlačítko pro stažení na konci řádku.
6. V místní nabídce pro stažení vyberte možnost **Faktura**.

Pokud nevidíte fakturu za poslední fakturační období, přečtěte si **Další informace**. <!-- Fix this -->
### <a name="why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> Proč se mi nezobrazuje faktura za poslední fakturační období?

Faktura se nemusí zobrazovat z několika důvodů:

- Od vytvoření vašeho předplatného Azure uplynulo méně než 30 dnů.

- Faktura se zatím nevygenerovala. Počkejte na konec fakturačního období.

- Nemáte oprávnění k zobrazení faktur. Pokud máte smlouvu se zákazníkem Microsoftu, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktur daného fakturačního profilu. U jiných předplatných se může stát, že pokud nejste správcem účtu, nemusí se vám zobrazovat staré faktury. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](manage-billing-access.md).

- Pokud máte bezplatnou zkušební verzi nebo máte v rámci svého předplatného měsíční kredit, který jste ještě nevyčerpali, a zároveň nemáte smlouvu se zákazníkem Microsoftu, nedostanete žádnou fakturu.

## <a name="get-your-invoice-in-email-pdf"></a>Zaslání faktury e-mailem (PDF)

Můžete se přihlásit k zasílání a nakonfigurovat další příjemce, kteří mají dostat vaši fakturu za Azure e-mailem. Tato funkce nemusí být u některých předplatných dostupná, například u nabídek podpory, smluv Enterprise nebo Azure v rámci licenčního programu Open. Pokud máte smlouvu se zákazníkem Microsoftu, přečtěte si téma Zasílání faktur pro fakturační profil e-mailem.

### <a name="get-your-subscriptions-invoices-in-email"></a>Zasílání faktur pro předplatné e-mailem

1. Na stránce [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) vyberte své předplatné. Pro každé předplatné, které vlastníte, se budete muset přihlásit k zasílání. Klikněte na **Faktury** a pak na **Zaslat fakturu e-mailem**.

    ![Snímek obrazovky, který ukazuje průběh přihlášení k zasílání](./media/download-azure-invoice-daily-usage-date/invoicesdeeplink01.png)

2. Klikněte na **Přihlásit** a přijměte podmínky.

    ![Snímek obrazovky, který ukazuje průběh přihlášení k zasílání, krok 2](./media/download-azure-invoice-daily-usage-date/invoicearticlestep02.png)

3. Po přijetí smlouvy můžete nakonfigurovat další příjemce. Když určitého příjemce odeberete, jeho e-mailová adresa se odstraní. Pokud si tento krok později rozmyslíte, je potřeba příjemce znovu přidat.

    ![Snímek obrazovky, který ukazuje průběh přihlášení k zasílání, krok 3](./media/download-azure-invoice-daily-usage-date/invoicearticlestep03.png)

Pokud vám po provedení těchto kroků nepřijde žádný e-mail, ujistěte se, že je v [předvolbách komunikace ve vašem profilu](https://account.windowsazure.com/profile) uvedená správná e-mailová adresa.

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Odhlášení ze zasílání faktur pro předplatné e-mailem

Pokud chcete vyjádřit výslovný nesouhlas s příjmem faktur e-mailem, proveďte výše uvedený postup a klikněte na **Odhlásit ze zasílání faktur e-mailem**. Tato možnost odebere všechny e-mailové adresy, pro které je nastavené zasílání faktur e-mailem. Pokud se k zasílání znovu přihlásíte, můžete znovu nakonfigurovat příjemce.

 ![Snímek obrazovky, který ukazuje průběh odhlášení ze zasílání](./media/download-azure-invoice-daily-usage-date/invoicearticlestep04.png)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Zasílání faktur pro smlouvu se zákazníkem Microsoftu e-mailem

Pokud máte smlouvu se zákazníkem Microsoftu, můžete se přihlásit k zasílání faktur e-mailem. Faktura se odešle e-mailem všem vlastníkům, přispěvatelům, čtenářům a správcům faktur daného fakturačního profilu. Čtenáři nemůžou aktualizovat předvolby zasílání faktur e-mailem.

1. Vyhledejte **Cost Management a fakturace**.
1. Vyberte fakturační profil.
1. V části **Nastavení** vyberte **Vlastnosti**.
1. V části **Poslat fakturu e-mailem** vyberte **Aktualizovat předvolbu odesílání faktury e-mailem**.
1. Zvolte **Přihlásit**.
1. Klikněte na **Aktualizovat**.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Odhlášení ze zasílání faktur pro fakturační profil e-mailem

Pokud chcete vyjádřit výslovný nesouhlas s příjmem faktur e-mailem, proveďte výše uvedený postup a klikněte na **Odhlásit**. Ze zasílání faktur e-mailem budou odhlášeni i všichni vlastníci, přispěvatelé, čtenáři a správci faktur. Pokud jste čtenář, nemůžete měnit předvolby zasílání faktury e-mailem.

## <a name="download-usage-in-azure-portal"></a>Stažení využití na webu Azure Portal

 Pro většinu předplatných můžete při zjišťování denního využití postupovat takto:

1. Vyberte své předplatné na stránce [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure Portal jako [uživatel s přístupem k fakturám](manage-billing-access.md).

2. Vyberte **Faktury**.

    ![Snímek obrazovky znázorňující možnost Využití a fakturace](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. Klikněte na tlačítko pro stažení fakturačního období, které chcete zkontrolovat.

4. Stáhněte si denní rozpis spotřebovaných prostředků a odhadované poplatky, a to kliknutím na **Stáhnout CSV**.  Příprava tohoto souboru CSV může trvat několik minut.

### <a name="download-usage-for-ea-customers"></a>Stažení informací o využití pro zákazníky se smlouvou Enterprise

Pokud si chcete zobrazit a stáhnout data o využití jako zákazník se smlouvou Enterprise, musíte být podnikový správce, vlastník účtu nebo správce oddělení s povolenou zásadou zobrazení poplatků.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte *Cost Management a fakturace*.
1. Pokud máte přístup k více fakturačním účtům, vyberte rozsah fakturace pro váš fakturační účet EA.
1. Vyberte **Využití a poplatky**.
1. U měsíce, který chcete zobrazit, vyberte **Stáhnout**.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Stažení informací o využití pro smlouvu se zákazníkem Microsoftu

Pokud chcete zobrazit a stáhnout informace o využití pro určitý fakturační profil, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktur daného fakturačního profilu.

#### <a name="download-usage-for-billed-charges"></a>Stažení informací o využití u účtovaných poplatků

1. Vyhledejte **Cost Management a fakturace**.
2. Vyberte fakturační profil.
3. Vyberte **Faktury**.
4. V tabulce faktur najděte řádek s fakturou odpovídající informacím o využití, které chcete stáhnout.
5. Klikněte na tři tečky (`...`) na konci řádku.
6. V místní nabídce pro stažení vyberte **Využití a poplatky Azure**.

#### <a name="download-usage-for-open-charges"></a>Stažení informací o využití u otevřených poplatků

Můžete si taky stáhnout informace o využití od začátku měsíce za aktuální fakturační období, tedy poplatky, které ještě nejsou vyfakturované.

1. Vyhledejte **Cost Management a fakturace**.
2. Vyberte fakturační profil.
3. V okně **Přehled** klikněte na **Stáhnout využití a poplatky Azure**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu k zákaznické smlouvě Microsoftu
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>Další kroky

Další informace o vaší faktuře a poplatcích najdete tady:

- [Vysvětlení informací na faktuře za Microsoft Azure](../understand/review-individual-bill.md)
- [Vysvětlení výrazů na faktuře Azure](../understand/understand-invoice.md)
- [Vysvětlení výrazů v podrobných informacích o využití Microsoft Azure](../understand/understand-usage.md)
- [Zobrazení cen Azure pro vaši organizaci](ea-pricing.md)

Pokud máte smlouvu se zákazníkem Microsoftu, projděte si tato témata:

- [Vysvětlení poplatků na faktuře pro váš fakturační profil](../understand/review-customer-agreement-bill.md)
- [Vysvětlení výrazů na faktuře pro váš fakturační profil](../understand/mca-understand-your-invoice.md)
- [Vysvětlení souboru s informacemi o využití a poplatcích za Azure pro váš fakturační profil](../understand/mca-understand-your-usage.md)
- [Zobrazení a stažení daňových dokumentů pro váš fakturační profil](../understand/mca-download-tax-document.md)
- [Zobrazení cen Azure pro vaši organizaci](ea-pricing.md)

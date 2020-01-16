---
title: Zobrazit a stáhnout fakturu Microsoft Azure
description: Vysvětluje, jak zobrazit a stáhnout fakturu za Microsoft Azure.
keywords: billing invoice, invoice download, azure invoice, azure usage
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: banders
ms.openlocfilehash: 0f413d38565202d379c81570b5cb169c2ed8effe
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987821"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Zobrazit a stáhnout fakturu Microsoft Azure

U většiny předplatných si můžete fakturu stáhnout z webu [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nebo si ji nechat poslat e-mailem. Pokud jste zákazníkem Azure se smlouvou Enterprise (zákazníkem EA), nemůžete stahovat faktury vaší organizace. Faktury se odesílají osobě, která je nastavená jako příjemce faktur pro danou smlouvu.

K zobrazení faktur mají oprávnění pouze určité role. Například správce účtu nebo podnikový správce. Další informace o získání přístupu k fakturačním údajům najdete v tématu [Správa přístupu k fakturaci Azure pomocí rolí](../manage/manage-billing-access.md).

Pokud máte smlouvu se zákazníkem Microsoftu (MCA) a chcete získat faktury, musíte mít jednu z následujících rolí:

- Vlastník fakturačního profilu
- Přispěvatel
- Čtenář
- Správce faktur

Pokud máte smlouvu s partnerem Microsoftu (MPA) a chcete zobrazit nebo stáhnout faktury za Azure, musíte být globálním správcem nebo agentem správy v partnerské organizaci. Pokud chcete zjistit, jaká oprávnění potřebujete, [zkontrolujte typ svého fakturačního účtu](#check-your-billing-account-type).

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a>Proč nemůžete získat fakturu

Faktura se nemusí zobrazovat z několika důvodů:

- Od vytvoření vašeho předplatného Azure uplynulo méně než 30 dnů.

- Azure se fakturuje na konci fakturačního období. Proto se faktura možná ještě nevygenerovala. Počkejte na konec fakturačního období.

- Nemáte oprávnění k zobrazení faktur. Pokud máte smlouvu MCA nebo MPA, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktura daného fakturačního profilu. U jiných předplatných se může stát, že pokud nejste správcem účtu, nemusí se vám zobrazovat staré faktury. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](../manage/manage-billing-access.md).

- Pokud máte bezplatnou zkušební verzi předplatného nebo předplatné s měsíčním kreditem, faktura se vám vystaví pouze v případě, že překročíte měsíční kredit. Pokud máte smlouvu se zákazníkem Microsoftu nebo smlouvu s partnerem Microsoftu, faktura se vám vystaví vždy.

## <a name="download-invoices-in-the-azure-portal"></a>Stahování faktur na webu Azure Portal

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Vyhledejte *Cost Management a fakturace*.
1. V závislosti na úrovni vašeho přístupu možná budete muset vybrat fakturační účet nebo fakturační profil.
1. V nabídce vlevo v části **Fakturace** vyberte **Faktury**.
1. V tabulce faktur najděte řádek s fakturou, kterou chcete stáhnout.
1. Klikněte na ikonu stahování nebo tři tečky (`...`) na konci řádku.
1. V nabídce Stáhnout vyberte **Faktura**.

Další informace o své faktuře najdete v tématu [Vysvětlení informací na faktuře za Microsoft Azure](review-individual-bill.md). Pokud potřebujete pomoc se správou nákladů, přečtěte si, [jak zabránit neočekávaným nákladům v rámci fakturace Azure a jak používat správu nákladů](../manage/getting-started.md).

## <a name="get-your-subscriptions-invoices-in-email"></a>Zasílání faktur pro předplatné e-mailem

Můžete se přihlásit k zasílání a nakonfigurovat další příjemce, kteří mají dostat vaši fakturu za Azure e-mailem. Tato funkce nemusí být u některých předplatných dostupná, například u nabídek podpory, smluv Enterprise nebo Azure v rámci licenčního programu Open.

1. Na stránce [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) vyberte své předplatné. Pro každé předplatné, které vlastníte, se budete muset přihlásit k zasílání. Klikněte na **Faktury** a pak na **Zaslat fakturu e-mailem**.

    ![Snímek obrazovky, který ukazuje průběh přihlášení k zasílání](./media/download-azure-invoice/invoicesdeeplink01.png)

2. Klikněte na **Přihlásit** a přijměte podmínky.

    ![Snímek obrazovky, který ukazuje průběh přihlášení k zasílání, krok 2](./media/download-azure-invoice/invoicearticlestep02.png)

3. Po přijetí smlouvy můžete nakonfigurovat další příjemce. Když určitého příjemce odeberete, jeho e-mailová adresa se odstraní. Pokud si tento krok později rozmyslíte, je potřeba příjemce znovu přidat.

    ![Snímek obrazovky, který ukazuje průběh přihlášení k zasílání, krok 3](./media/download-azure-invoice/invoicearticlestep03.png)

Pokud vám po provedení těchto kroků nepřijde žádný e-mail, ujistěte se, že je v [předvolbách komunikace ve vašem profilu](https://account.windowsazure.com/profile) uvedená správná e-mailová adresa.

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Odhlášení ze zasílání faktur pro předplatné e-mailem

Pokud se chcete odhlásit ze zasílání faktur e-mailem, proveďte výše uvedený postup a klikněte na **Odhlásit ze zasílání faktur e-mailem**. Tato možnost odebere všechny e-mailové adresy, pro které je nastavené zasílání faktur e-mailem. Pokud se k zasílání znovu přihlásíte, můžete znovu nakonfigurovat příjemce.

 ![Snímek obrazovky, který ukazuje průběh odhlášení ze zasílání](./media/download-azure-invoice/invoicearticlestep04.png)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Zasílání faktur pro smlouvu se zákazníkem Microsoftu e-mailem

Pokud máte fakturační účet pro zákaznickou smlouvu od Microsoftu, můžete vyjádřit výslovný souhlas s tím, že svou fakturu získáte v e-mailu. Všichni uživatelé s rolí vlastník, přispěvatel, čtenář nebo správce faktury ve fakturačním profilu obdrží svou fakturu v e-mailu. 

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky, který zobrazuje hledání předplatného na portálu](./media/download-azure-invoice/search-cmb.png)

1. Na levé straně vyberte **profily fakturace** . V seznamu profily fakturace vyberte fakturační profil, abyste získali jeho faktury v e-mailu.

   [![snímek obrazovky, který zobrazuje seznam profilů fakturace](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. Na levé straně vyberte **vlastnosti** a pak vyberte **aktualizovat e-mailovou fakturu předvolby**.

   [![snímek obrazovky, který zobrazuje seznam profilů fakturace](./media/download-azure-invoice/mca-select-update-email-preferences.png)](./media/download-azure-invoice/mca-select-update-email-preferences.png#lightbox)

1. Vyberte možnost **Přihlásit se** a potom klikněte na tlačítko **aktualizovat**.

   [![snímek obrazovky, který zobrazuje seznam profilů fakturace](./media/download-azure-invoice/mca-select-email-opt-in.png)](./media/download-azure-invoice/mca-select-email-opt-in.png#lightbox)

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Odhlášení ze zasílání faktur pro smlouvu se zákazníkem Microsoftu e-mailem

Pokud se chcete odhlásit od získání faktury e-mailem, postupujte podle předchozích kroků **a klikněte na Odhlásit.** K získání faktury v e-mailu se na všechny uživatele s rolí vlastník, přispěvatel, čtenář nebo správce faktury nesouhlasí. 

## <a name="give-others-access-to-your-microsoft-customer-agreement-invoices"></a>Poskytnutí přístupu ostatním k fakturám zákaznických smluv Microsoftu

Ostatním uživatelům můžete umožnit, aby si mohli zobrazit, stáhnout a zaplatit faktury tím, že jim přiřadí roli správce faktur pro fakturační profil. Pokud jste se rozhodli pro získání faktury v e-mailu, tito uživatelé také získají faktury v e-mailu. 

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky, který zobrazuje hledání předplatného na portálu](./media/download-azure-invoice/search-cmb.png)

1. Na levé straně vyberte **profily fakturace** . V seznamu profily fakturace vyberte profil fakturace, pro který chcete přiřadit roli manažera faktury.

   [![snímek obrazovky, který zobrazuje seznam profilů fakturace](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. Na levé straně vyberte **Access Control (IAM)** a pak v horní části stránky vyberte **Přidat** .

   [![snímek obrazovky, který zobrazuje stránku řízení přístupu](./media/download-azure-invoice/mca-select-access-control.png)](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png#lightbox)

1. V rozevíracím seznamu role vyberte **správce faktur**. Zadejte e-mailovou adresu uživatele, kterému chcete udělit přístup. Přiřaďte roli výběrem možnosti **Uložit**.

   [![snímek obrazovky, který ukazuje přidání uživatele jako správce faktury](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)

## <a name="check-your-billing-account-type"></a>Kontrola typu fakturačního účtu
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o vaší faktuře a poplatcích najdete tady:

- [Zobrazení a stažení využití a poplatků za Microsoft Azure](download-azure-daily-usage.md)
- [Vysvětlení informací na faktuře za Microsoft Azure](review-individual-bill.md)
- [Vysvětlení výrazů na faktuře Azure](understand-invoice.md)
- [Vysvětlení výrazů v podrobných informacích o využití Microsoft Azure](understand-usage.md)
- [Zobrazení cen Azure pro vaši organizaci](../manage/ea-pricing.md)

Pokud máte smlouvu se zákazníkem Microsoftu, projděte si tato témata:

- [Vysvětlení poplatků na faktuře pro váš fakturační profil](review-customer-agreement-bill.md)
- [Vysvětlení výrazů na faktuře pro váš fakturační profil](mca-understand-your-invoice.md)
- [Vysvětlení souboru s informacemi o využití a poplatcích za Azure pro váš fakturační profil](mca-understand-your-usage.md)
- [Zobrazení a stažení daňových dokumentů pro váš fakturační profil](mca-download-tax-document.md)
- [Zobrazení cen Azure pro vaši organizaci](../manage/ea-pricing.md)

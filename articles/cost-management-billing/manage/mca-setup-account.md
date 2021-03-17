---
title: Nastavení fakturace pro smlouvu se zákazníkem Microsoftu – Azure
description: Přečtěte si, jak nastavit fakturační účet pro smlouvu se zákazníkem Microsoftu. Projděte si požadavky na nastavení a další dostupné zdroje informací.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/20/2020
ms.author: banders
ms.openlocfilehash: 7e930ca938b2531fb001c327132f79f703b2ea74
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316415"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Nastavení fakturačního účtu pro smlouvu se zákazníkem Microsoftu

Pokud platnost vaší přímé registrace smlouvy Enterprise vypršela nebo brzy vyprší, můžete ji prodloužit podepsáním Smlouvy se zákazníkem Microsoftu. Tento článek popisuje změny ve stávající fakturaci po tomto nastavení a provede vás nastavením nového fakturačního účtu. Platnost nepřímých smluv Enterprise momentálně nejde prodloužit prostřednictvím Smlouvy se zákazníkem Microsoftu.

Prodloužení zahrnuje tyto kroky:

1. Přijetí nové smlouvy se zákazníkem Microsoftu. Ve spolupráci se zástupcem Microsoftu se seznámíte s podrobnostmi a potvrdíte novou smlouvu.
2. Nastavení nového fakturačního účtu, který se vytvoří pro novou smlouvu se zákazníkem Microsoftu.

Při nastavení fakturačního účtu musíte fakturaci předplatných Azure převést z registrace smlouvy Enterprise na nový účet. Toto nastavení nemá vliv na služby Azure používané ve vašich předplatných. Mění se ale způsob, jak budete spravovat fakturaci vašich předplatných.

- Služby Azure a fakturaci už nebudete spravovat na webu [EA Portal](https://ea.azure.com), ale na webu [Azure Portal](https://portal.azure.com).
- Každý měsíc budete dostávat elektronickou fakturu s vašimi poplatky. Fakturu můžete zobrazit a analyzovat na stránce Azure Cost Management a fakturace.
- Místo oddělení a účtu z registrace smlouvy Enterprise budete pro správu a uspořádání fakturace používat strukturu a rozsahy fakturace z nového účtu.

Než s tímto nastavením začnete, doporučujeme provést následující akce:

- **Seznamte se s novým fakturačním účtem**
  - Nový účet zjednoduší fakturaci pro vaši organizaci. [Projděte si rychlý přehled nového fakturačního účtu.](../understand/mca-overview.md)
- **Ověřte, že váš přístup umožňuje toto nastavení provést**
  - Toto nastavení můžou provést pouze uživatelé s určitými oprávněními pro správu. Zkontrolujte, jestli máte [přístup nutný k provedení tohoto nastavení](#access-required-to-complete-the-setup).
- **Seznamte se se změnami v hierarchii fakturace**
  - Nový fakturační účet je uspořádán jinak než v registraci smlouvy Enterprise. [Seznamte se se změnami v hierarchii fakturace u nového účtu.](#understand-changes-to-your-billing-hierarchy)
- **Seznamte se se změnami v přístupu správců fakturace**
  - Správci z registrace smlouvy Enterprise získají přístup k rozsahům fakturace v novém účtu. [Seznamte se se změnami v jejich přístupu.](#changes-to-billing-administrator-access)
- **Podívejte se, které funkce smlouvy Enterprise nový účet nahrazuje**
  - Podívejte se, které funkce smlouvy Enterprise nahrazují funkce v novém účtu.
- **Podívejte se na odpovědi na nejčastější dotazy**
  - Podívejte se na [další informace](#additional-information), abyste se o nastavení dozvěděli více.

## <a name="access-required-to-complete-the-setup"></a>Přístup nutný k provedení tohoto nastavení

K provedení tohoto nastavení potřebujete mít tyto úrovně přístupu:

- Vlastník fakturačního profilu, který byl vytvořen po podepsání smlouvy se zákazníkem Microsoftu. Další informace o fakturačních profilech najdete ve [vysvětlení fakturačních profilů](../understand/mca-overview.md#billing-profiles).

- Podnikový správce v registraci, která se prodlužuje.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Pokud nejste podnikovým správcem v registraci

Můžete požádat podnikové správce registrace, aby provedli nastavení vašeho fakturačního účtu.

1. Přihlaste se k webu Azure Portal pomocí odkazu v e-mailu, který vám byl odeslán při podepsání smlouvy se zákazníkem Microsoftu.

2. Pokud tento e-mail nemáte, přihlaste se pomocí následujícího odkazu. Nahraďte `<enrollmentNumber>` číslem registrace vaší smlouvy Enterprise, která se prodlužuje.

   `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

3. Vyberte podnikové správce, kterým chcete tuto žádost odeslat.

   ![Snímek obrazovky s žádostí pro podnikové správce](./media/mca-setup-account/ea-mca-invite-admins.png)

4. Vyberte **Odeslat žádost** .

   Správci obdrží e-mail s pokyny k provedení nastavení.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Pokud nejste vlastníkem fakturačního profilu

Uživatel ve vaší organizaci, který smlouvu se zákazníkem Microsoftu podepsal, je do fakturačního profilu přidán jako vlastník. Požádejte tohoto uživatele, aby vás přidal jako vlastníka, abyste mohli provést toto nastavení.

## <a name="understand-changes-to-your-billing-hierarchy"></a>Vysvětlení změn v hierarchii fakturace

Nový fakturační účet zjednodušuje fakturaci vaší organizace a přitom poskytuje rozšířené možnosti fakturace a správy nákladů. Následující diagram vysvětluje uspořádání fakturace v novém fakturačním účtu.

![Obrázek změn v hierarchii po přechodu](./media/mca-setup-account/mca-post-transition-hierarchy.png)

1. Ke správě fakturace u smlouvy se zákazníkem Microsoftu používáte fakturační účet. Podnikoví správci se stanou vlastníky fakturačního účtu. Další informace o fakturačním účtu najdete ve [vysvětlení fakturačního účtu](../understand/mca-overview.md#your-billing-account).
2. Ke správě fakturace ve vaší organizaci používáte fakturační profil, podobně jako u registrace smlouvy Enterprise. Podnikoví správci se stanou vlastníky fakturačního profilu. Další informace o fakturačních profilech najdete ve [vysvětlení fakturačních profilů](../understand/mca-overview.md#billing-profiles).
3. K uspořádání nákladů podle svých potřeb používáte oddíl faktury, podobně jako oddělení v registraci smlouvy Enterprise. Z oddělení se stanou oddíly faktury a správci oddělení se stanou vlastníky odpovídajících oddílů faktury. Další informace o oddílech faktury najdete ve [vysvětlení oddílů faktury](../understand/mca-overview.md#invoice-sections).
4. Účty, které byly vytvořené u smlouvy Enterprise, se v novém fakturačním účtu nepodporují. Předplatná účtu patří do odpovídajícího oddílu faktury vytvořeného pro jejich oddělení. Vlastníci účtu můžou vytvářet a spravovat předplatná pro svoje oddíly faktury.

## <a name="changes-to-billing-administrator-access"></a>Změny v přístupu správců fakturace

V závislosti na svém přístupu získají správci fakturace z registrace smlouvy Enterprise přístup k rozsahům fakturace v novém účtu. Následující tabulka objasňuje změny v přístupu po nastavení:

| Stávající role | Role po přechodu |
| --- | --- |
| **Podnikový správce (jen pro čtení = ne)** | **– Vlastník fakturačního účtu** </br> Spravuje všechny aspekty fakturačního účtu. </br> **- Vlastník fakturačního profilu** </br> Spravuje všechny aspekty fakturačního profilu. </br> **– Vlastník oddílu faktury u všech oddílů faktury** </br> Spravuje všechny aspekty oddílů faktury. |
| **Podnikový správce (jen pro čtení = ano)** | **– Čtenář fakturačního účtu** </br> Může zobrazit všechno ve fakturačním účtu, ale jenom pro čtení.</br> **- Čtenář fakturačního profilu** </br> Může zobrazit všechno ve fakturačním profilu, ale jenom pro čtení.</br>**- Čtenář oddílu faktury u všech oddílů faktury**</br> Může zobrazit všechno v oddílech faktury, ale jenom pro čtení.|
| **Správce oddělení (jen pro čtení = ne)** |**- Vlastník oddílu faktury u oddílu faktury vytvořeného pro jeho odpovídající oddělení** </br>Spravuje všechny aspekty oddílu faktury.|
| **Správce oddělení (jen pro čtení = ano)**|**- Čtenář oddílu faktury u oddílu faktury vytvořeného pro jeho odpovídající oddělení**</br> Může zobrazit všechno v oddílu faktury, ale jenom pro čtení.|
| **Vlastník účtu** | **- Tvůrce předplatného Azure u oddílu faktury vytvořeného pro jeho odpovídající oddělení** </br>  Může vytvářet předplatná Azure pro svůj oddíl faktury.|

Při přijetí smlouvy se zákazníkem Microsoftu se pro nový fakturační účet vybere tenant Azure Active Directory (AD). Pokud tenant pro vaši organizaci neexistuje, vytvoří se nový tenant. Tenant představuje vaši organizaci v rámci Azure Active Directory. Globální správci tenanta ve vaší organizaci používají tenanta ke správě přístupu k aplikacím a datům ve vaší organizaci.

Váš nový účet podporuje uživatele jenom z tenanta, který byl vybrán při podepsání smlouvy se zákazníkem Microsoftu. Pokud jsou uživatelé s oprávněním správce v rámci vaší smlouvy Enterprise součástí tenanta, získají během tohoto nastavení přístup k novému fakturačnímu účtu. Pokud nejsou součástí tenanta, budou mít k novému fakturačnímu účtu přístup jen v případě, že je pozvete.

Pokud uživatele pozvete, budou do tenanta přidáni jako uživatelé typu host a získají přístup k fakturačnímu účtu. Aby bylo možné uživatele pozvat, musí být u tenanta zapnutý přístup hostů. Další informace najdete v článku o [řízení přístupu hostů v Azure Active Directory](/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory). Pokud je přístup hostů vypnutý, požádejte globální správce vašeho tenanta, aby ho zapnuli. <!-- Todo - How can they find their global administrator -->

## <a name="view-replaced-features"></a>Zobrazení nahrazených funkcí

Následující funkce ze smlouvy Enterprise jsou ve fakturačním účtu pro smlouvu se zákazníkem Microsoftu nahrazeny novými funkcemi.

### <a name="enterprise-agreement-accounts"></a>Účty smlouvy Enterprise

Účty, které byly vytvořeny v registraci smlouvy Enterprise, se v novém fakturačním účtu nepodporují. Předplatná účtu patří do oddílu faktury vytvořeného pro jejich odpovídající oddělení. Vlastníci účtu se stanou tvůrci předplatného Azure a můžou vytvářet a spravovat předplatná pro svoje oddíly faktury.

### <a name="notification-contacts"></a>Kontakty pro oznámení

Kontaktům pro oznámení se odesílá e-mailová komunikace týkající se smlouvy Azure Enterprise. V novém fakturačním účtu se kontakty pro oznámení nepodporují. E-maily týkající se kreditů Azure a faktur se odesílají uživatelům, kteří mají přístup k fakturačním profilům ve fakturačním účtu.

### <a name="spending-quotas"></a>Kvóty útrat

Kvóty útraty, které byly nastaveny pro oddělení v registraci smlouvy Enterprise, jsou v novém fakturačním účtu nahrazeny rozpočty. Pro každou kvótu útraty nastavenou u oddělení v rámci registrace se vytvoří rozpočet. Další informace o rozpočtech najdete v tématu [Kurz: Vytváření a správa rozpočtů Azure](../costs/tutorial-acm-create-budgets.md).

### <a name="cost-centers"></a>Nákladová střediska

Nákladová střediska, která bylo nastavena u předplatných Azure v rámci registrace smlouvy Enterprise, se přenesou do nového fakturačního účtu. Nákladová střediska pro oddělení a účty smlouvy Enterprise se ale nepodporují.

## <a name="additional-information"></a>Další informace

Následující oddíly obsahují další informace týkající se nastavení fakturačního účtu.

### <a name="no-service-downtime"></a>Žádné výpadky služby

Služby Azure ve vašem předplatném poběží i nadále bez přerušení. Převedeme pouze fakturační vztah pro vaše předplatná Azure. Stávající prostředky, skupiny prostředků ani skupiny pro správu to neovlivní.

### <a name="user-access-to-azure-resources"></a>Přístup uživatelů k prostředkům Azure

Přístup k prostředkům Azure, který byl nastaven pomocí řízení přístupu na základě role v Azure (Azure RBAC), nebude při přechodu nijak ovlivněn.

### <a name="azure-reservations"></a>Rezervace Azure

Všechny rezervace Azure ve vaší registraci smlouvy Enterprise se převedou na nový fakturační účet. Během přechodu nedojde k žádným změnám slev za rezervace, které se u vašich předplatných uplatňují.

### <a name="azure-marketplace-products"></a>Produkty z Azure Marketplace

Všechny produkty z Azure Marketplace ve vaší registraci smlouvy Enterprise se převedou spolu s předplatnými. Během přechodu nedojde k žádným změnám v přístupu služeb k produktům z Marketplace.

### <a name="support-plan"></a>Plán podpory

Výhody podpory se při přechodu nepřenášejí. Získat výhody pro předplatná Azure v novém fakturačním účtu můžete zakoupením nového plánu podpory.

### <a name="past-charges-and-balance"></a>Minulé poplatky a zůstatek

Poplatky a zůstatek kreditů před přechodem lze zobrazit ve vaší registraci smlouvy Enterprise na webu Azure Portal. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>Kdy by se mělo toto nastavení provést?

Nastavení fakturačního účtu proveďte ještě před tím, než vyprší platnost vaší registrace smlouvy Enterprise. Když platnost vaší registrace vyprší, služby ve vašich předplatných Azure poběží i nadále bez přerušení. Za tyto služby se vám ale budou účtovat průběžné platby.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Změny v registraci smlouvy Enterprise po tomto nastavení

Předplatná Azure, která byla vytvořena v rámci registrace smlouvy Enterprise po přechodu, lze ručně převést na nový fakturační účet. Další informace najdete v článku o [získání vlastnictví fakturace předplatných Azure od jiných uživatelů](mca-request-billing-ownership.md). Pokud chcete převést rezervace Azure, které byly zakoupeny po přechodu, [obraťte se na podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Po přechodu můžete uživatelům poskytnout i přístup k fakturačnímu účtu. Další informace najdete v článku o [správě fakturačních rolí na webu Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="revert-the-transition"></a>Vrácení přechodu zpět

Přechod nelze vrátit zpět. Jakmile se fakturace vašich předplatných Azure převede na nový fakturační účet, nemůžete se vrátit zpět ke své registraci smlouvy Enterprise.

### <a name="closing-your-browser-during-setup"></a>Zavření prohlížeče během tohoto nastavení

Než vyberete **Zahájit převod** , můžete prohlížeč zavřít. Pomocí odkazu, který jste dostali v e-mailu, se můžete k tomuto nastavení vrátit a zahájit převod. Pokud po zahájení převodu zavřete prohlížeč, převod dále poběží. Když chcete zkontrolovat nejnovější stav převodu, vraťte se na stránku Stav převodu. Po dokončení převodu obdržíte e-mail.

## <a name="complete-the-setup-in-the-azure-portal"></a>Provedení nastavení na webu Azure Portal

K provedení tohoto nastavení potřebujete přístup k novému fakturačnímu účtu i k registraci smlouvy Enterprise. Další informace viz [přístup nutný k provedení nastavení fakturačního účtu](#access-required-to-complete-the-setup).

1. Přihlaste se k webu Azure Portal pomocí odkazu v e-mailu, který vám byl odeslán při podepsání smlouvy se zákazníkem Microsoftu.

2. Pokud tento e-mail nemáte, přihlaste se pomocí následujícího odkazu. Nahraďte `<enrollmentNumber>` číslem registrace vaší smlouvy Enterprise, která se prodlužuje.

   `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

3. V posledním kroku nastavení vyberte **Zahájit převod** . Jakmile vyberete Zahájit převod:

    ![Snímek obrazovky, který ukazuje průvodce nastavením](./media/mca-setup-account/ea-mca-set-up-wizard.png)

    - V novém fakturačním účtu se vytvoří hierarchie fakturace odpovídající hierarchii vaší smlouvy Enterprise. Další informace najdete ve [vysvětlení změn v hierarchii fakturace](#understand-changes-to-your-billing-hierarchy).
    - Správcům z vaší registrace smlouvy Enterprise se udělí přístup k novému fakturačnímu účtu, aby mohli nadále spravovat fakturaci vaší organizace.
    - Fakturace vašich předplatných Azure se převede na nový účet. **Během tohoto přechodu nedojde k žádnému ovlivnění vašich služeb Azure. Tyto služby poběží i nadále bez přerušení.**
    - Pokud máte rezervace Azure, převedou se na váš nový fakturační účet beze změny výhod nebo termínu.

4. Stav převodu můžete sledovat na stránce **Stav převodu** .

   ![Snímek obrazovky zobrazující stav převodu](./media/mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-billing-account-setup"></a>Ověření nastavení fakturačního účtu

 Proveďte následující ověření, že nový fakturační účet je správně nastaven:

### <a name="azure-subscriptions"></a>Předplatná Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace** .

   ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/mca-setup-account/search-cmb.png)

3. Vyberte fakturační účet. Typ fakturačního účtu bude **Smlouva se zákazníkem Microsoftu** .

4. Na levé straně vyberte **Předplatná Azure** .

   ![Snímek obrazovky znázorňující seznam předplatných](./media/mca-setup-account/mca-subscriptions-post-transition.png)

Předplatná Azure, která se převedou z vaší registrace smlouvy Enterprise na nový fakturační účet, se zobrazují na stránce Předplatná Azure. Pokud se domníváte, že nějaké předplatné chybí, převeďte fakturaci daného předplatného ručně na webu Azure Portal. Další informace najdete v článku o [získání vlastnictví fakturace předplatných Azure od jiných uživatelů](mca-request-billing-ownership.md).

### <a name="azure-reservations"></a>Rezervace Azure

Rezervace Azure ve vaší registraci smlouvy Enterprise se převedou na nový fakturační účet beze změny výhod nebo termínu. Transakce dokončené před převodem se v novém fakturačním účtu nezobrazí. Na [stránce Rezervace Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) však můžete ověřit, že se výhody vašich rezervací uplatňují na vaše předplatná.

### <a name="access-of-enterprise-administrators-on-the-billing-account"></a>Přístup podnikových správců ve fakturačním účtu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace** .

   ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/mca-setup-account/search-cmb.png)

3. Vyberte fakturační účet pro vaši **smlouvu se zákazníkem Microsoftu** .

4. Na levé straně vyberte **Řízení přístupu (IAM)** .

   ![Snímek obrazovky znázorňující přístup podnikových správců uvedených jako vlastníci fakturačního účtu po dokončení přechodu](./media/mca-setup-account/mca-ea-admins-ba-access-post-transition.png)

Podnikoví správci jsou uvedeni jako vlastníci fakturačního účtu, zatímco podnikoví správci s oprávněními jen pro čtení jsou uvedeni jako čtenáři fakturačního účtu. Pokud se domníváte, že přístup některých podnikových správců chybí, můžete jim přístup udělit na webu Azure Portal. Další informace najdete v článku o [správě fakturačních rolí na webu Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Přístup podnikových správců ve fakturačním profilu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace** .

   ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/mca-setup-account/search-cmb.png)

3. Vyberte fakturační profil, který se vytvořil pro vaši registraci. V závislosti na úrovni vašeho přístupu možná budete muset vybrat fakturační účet. Ve fakturačním účtu vyberte Fakturační profily a pak vyberte fakturační profil.

4. Na levé straně vyberte **Řízení přístupu (IAM)** .

   ![Snímek obrazovky znázorňující přístup podnikových správců uvedených jako vlastníci fakturačního profilu po dokončení přechodu](./media/mca-setup-account/mca-ea-admins-bp-access-post-transition.png)

Podnikoví správci jsou uvedeni jako vlastníci fakturačního profilu, zatímco podnikoví správci s oprávněními jen pro čtení jsou uvedeni jako čtenáři fakturačního profilu. Pokud se domníváte, že přístup některých podnikových správců chybí, můžete jim přístup udělit na webu Azure Portal. Další informace najdete v článku o [správě fakturačních rolí na webu Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Přístup podnikových správců, správců oddělení a vlastníků účtů v oddílech faktury

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace** .

   ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/mca-setup-account/search-cmb.png).

3. Vyberte oddíl faktury. Oddíly faktury mají stejný název jako jejich odpovídající oddělení v registracích smlouvy Enterprise. V závislosti na úrovni vašeho přístupu možná budete muset vybrat fakturační účet. Ve fakturačním účtu vyberte **Fakturační profily** a pak vyberte **Oddíly faktury** . V seznamu oddílů faktury vyberte oddíl faktury.

   ![Snímek obrazovky znázorňující seznam oddílů faktury po přechodu](./media/mca-setup-account/mca-invoice-sections-post-transition.png)

4. Na levé straně vyberte **Řízení přístupu (IAM)** .

    ![Snímek obrazovky znázorňující přístup správců oddělení a účtů po přechodu](./media/mca-setup-account/mca-department-account-admins-access-post-transition.png)

Podnikoví správci a správci oddělení jsou uvedeni jako vlastníci oddílu faktury nebo čtenáři oddílu faktury, zatímco vlastníci účtů v oddělení jsou uvedeni jako tvůrci předplatného Azure. Opakováním tohoto kroku pro všechny oddíly faktury zkontrolujte přístup u všech oddělení ve vaší registraci smlouvy Enterprise. Vlastníci účtů, kteří nebyli součástí žádného oddělení, získají oprávnění k oddílu faktury s názvem **Výchozí oddíl faktury** . Pokud se domníváte, že přístup některých správců chybí, můžete jim přístup udělit na webu Azure Portal. Další informace najdete v článku o [správě fakturačních rolí na webu Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.

## <a name="next-steps"></a>Další kroky

- [Začínáme s novým fakturačním účtem](../understand/mca-overview.md)

- [Provádění úkolů ze smlouvy Enterprise ve fakturačním účtu pro smlouvu se zákazníkem Microsoftu](mca-enterprise-operations.md)

- [Správa přístupu k fakturačnímu účtu](understand-mca-roles.md)
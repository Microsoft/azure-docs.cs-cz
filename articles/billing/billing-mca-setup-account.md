---
title: Nastavení fakturace účtu Microsoft zákaznické smlouvy – Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit fakturační účet Microsoft zákaznické smlouvy.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 389a8ac729cf5f4f95aa37654434245d08ecc87e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533625"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Nastavení účtu fakturační zákaznické smlouvy Microsoft

Pokud vaše smlouva Enterprise Agreement enrollment vypršela nebo chystáte vypršela platnost, se můžete přihlásit k obnovení vaší registrace zákaznické smlouvy Microsoft. Obnovení zahrnuje následující kroky:

1. Přijměte podmínky smlouvy nového zákazníka Microsoftu. Práce se zástupcem Microsoftu pole podrobnosti a přijmout nové smlouvy.
2. Nastavení nové fakturační účet, který je vytvořen pro nové smlouvy zákazníka Microsoftu.

Tento článek popisuje změny existující fakturace po nastavení a provede vás instalace nového fakturačního účtu.

Pokud chcete nastavit fakturační účet, musí přechod ze smlouva Enterprise Agreement enrollment fakturací předplatných Azure, do nového účtu. Instalační program nebude mít vliv na služby Azure, které jsou spuštěny v rámci vašich předplatných. Ale změní způsob, jak budete spravovat fakturace pro vaše předplatná.

- Místo [portál EA](https://ea.azure.com), v budete spravovat služby Azure a fakturace, [webu Azure portal](https://portal.azure.com).
- Získáte měsíční, digitální faktury pro poplatky. Můžete zobrazit a analyzovat fakturace v Azure Cost Management a fakturace stránky.
- Místo oddělení a účet v registraci smlouvy Enterprise budete používat fakturační strukturu a obory z nový účet pro správu a uspořádání fakturace.

Před zahájením instalace, doporučujeme, že abyste provedli následující:

- **Pochopení vašeho nového účtu fakturace**
  - Váš nový účet zjednodušuje fakturaci pro vaši organizaci. [Získat rychlý přehled o vaší nové fakturační účet](billing-mca-overview.md)
- **Ověřte váš přístup k dokončení instalace**
  - Jenom uživatelé s určitými oprávněními pro správu může dokončení instalace. Zkontrolujte, jestli máte [na úroveň požadovanou k dokončení instalace](#access-required-to-complete-the-setup).
- **Změny fakturace hierarchii porozumět**
  - Jste nový fakturační účet je uspořádaný odlišně než smlouva Enterprise Agreement enrollment. [Vysvětlení změny fakturace hierarchii na nový účet](#understand-changes-to-your-billing-hierarchy).
- **Principy změn pro přístup k vaší fakturační správci**
  - Správce z smlouvu Enterprise získáte přístup k fakturačním oborů v rámci nového účtu. [Pochopit změny v jejich přístup](#understand-changes-to-your-billing-administrators-access).
- **Funkce Enterprise Agreement zobrazení, které nahrazují nový účet**
  - Zobrazit funkce smlouva Enterprise Agreement enrollment, které jsou [nahrazuje funkce v rámci nového účtu](#review-features-replaced-by-the-new-billing-account).
- **Zobrazit odpovědi na nejčastější dotazy**
  - Zobrazení [Další informace o](#additional-information) Další informace o nastavení.

## <a name="access-required-to-complete-the-setup"></a>Na úroveň požadovanou k dokončení instalace

K dokončení instalace, budete potřebovat následující přístup:

- Vlastník fakturační profil, který byl vytvořen při podepsání smlouvy zákazníka Microsoftu. Další informace o fakturaci profilů najdete v tématu [pochopit fakturaci profily](billing-mca-overview.md#understand-billing-profiles).

- Správce podnikové sítě na registrace, který je obnoven.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Pokud si nejste správce rozlehlé sítě v zápisu

Můžete požádat o podnikovým správcům registraci dokončete nastavení fakturačnímu účtu.

1. Přihlaste se k webu Azure portal pomocí odkazu v e-mailu, který jste dostali při podepsání smlouvy zákazníka Microsoftu.

2. Pokud někdo jiný ve vaší organizaci podepsané smlouvy nebo nemáte k dispozici v e-mailu, přihlaste se pomocí následujícího odkazu. Nahraďte **enrollmentNumber** s registrační číslo smlouvy enterprise, který byl obnoven.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Vyberte správce organizace, které chcete odeslat žádost.

   ![Snímek obrazovky, který ukazuje pozvání správcům](./media/billing-mca-setup-account/ea-mca-invite-admins.png)

4. Vyberte **požadavků na odeslání**.

   Správci dostanou e-mail s pokyny k dokončení instalace.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Pokud nejste vlastníkem fakturační profil

Uživatel ve vaší organizaci, kdo podepsal smlouvy zákazníka Microsoftu se přidá jako vlastník na fakturační profil. Požádat uživatele, aby vás přidal jako vlastníka tak, aby dokončení nastavení.  <!-- Todo Are there any next steps -->

## <a name="understand-changes-to-your-billing-hierarchy"></a>Informace o změnách hierarchie fakturace

Vaše nové fakturační účet zjednodušuje fakturaci pro vaši organizaci při poskytování rozšířeného fakturace a možnosti správy nákladů. Následující diagram popisuje, jakým způsobem je organizována fakturace v nové fakturační účet.

![Obrázek ea mca-post přechodu – hierarchie](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

1. Fakturační účet používáte ke správě fakturace pro smlouvy Microsoft zákazníka. Další informace o fakturačním účtu, najdete v článku [pochopit fakturační účet](billing-mca-overview.md#understand-billing-account).
2. Fakturační profil, který používáte ke správě fakturace pro vaši organizaci, podobné smlouva Enterprise Agreement enrollment. Tito správci budou vlastníky fakturační profil. Další informace o fakturaci profilů najdete v tématu [pochopit fakturaci profily](billing-mca-overview.md#understand-billing-profiles).
3. Pomocí oddíl faktury uspořádat svoje náklady podle svých potřeb, podobně jako oddělením ve vaší registraci smlouvy Enterprise. Oddělení stane části faktury a správci oddělení budou vlastníky příslušných faktury oddílů. Další informace o části faktury, naleznete v tématu [vysvětlení části faktury](billing-mca-overview.md#understand-invoice-sections).
4. Nové fakturační účet nepodporují účty, které byly vytvořeny ve vaší smlouvě Enterprise. Předplatná účtu patří do části příslušných faktury pro jejich oddělení. Vlastníci účtu můžete vytvářet a spravovat odběry pro příslušné oddíly faktury.

## <a name="understand-changes-to-your-billing-administrators-access"></a>Principy změn pro přístup k vaší fakturační správci

V závislosti na jejich přístup správci fakturace na smlouvu Enterprise získáte přístup k fakturačním obory na nový účet. Následující tabulka popisuje změnu v hodnotě přístup v průběhu instalace:

| Existující roli | Role přechod příspěvku |
| --- | --- |
| **Správce rozlehlé sítě (pro čtení jenom = No)** | **– Vlastníka profilu fakturace** </br> Spravovat vše na fakturační profil </br> - **Vlastník části faktury na všechny části faktury** </br> Správu všech položek v části faktury |
| **Správce rozlehlé sítě (pro čtení jenom = Ano)** | **– Čtenář profilu fakturace** </br> – Zobrazení jen pro čtení všeho na fakturační účet</br>**– Čtenář faktury část na všechny části faktury**</br> – Zobrazení jen pro čtení všeho v části faktury|
| **Správce oddělení (čtení jenom = No)** |**– Vlastník části faktury v části faktury vytvořené pro oddělení na jejich odpovídajících** </br>Správu všech položek v části faktury|
| **Správce oddělení (čtení jenom = Ano)**|**-Čtenář oddílu faktury v části faktury vytvořené pro oddělení na jejich odpovídajících**</br> Zobrazení jen pro čtení všeho v části faktury|
| **Vlastník účtu** | **-Předplatné creator v části faktury vytvořené pro oddělení na jejich odpovídajících** </br>  Vytvářet předplatná Azure pro jejich části faktury|

Nové fakturační účet tenanta služby Azure Active Directory vybraná při podepsání smlouvy zákazníka Microsoftu. Pokud tenanta neexistuje pro vaši organizaci, je vytvořit nového tenanta. Klient představuje vaše organizace v Azure Active Directory. Globálního tenanta správci ve vaší organizaci pomocí tenanta můžete spravovat přístup k aplikacím a datům ve vaší organizaci.

Váš nový účet podporuje pouze uživatelé z tenanta, který byl vybrán při podepsání smlouvy zákazníka Microsoftu. Pokud uživatelé s oprávněním správce v rámci smlouvy Enterprise jsou součástí klienta, budete vývojáři získají přístup k nové fakturační účet během instalace. Pokud nejsou součástí klienta, nebudou moct přistupovat k nové fakturační účet, pokud jste pozvánku.

Při zvaní uživatelů jsou přidány do tenanta jako uživatele typu Host a získejte přístup k fakturačnímu účtu. Pozvat uživatele, musí být zapnuta přístup hosta pro příslušného tenanta. Další informace najdete v tématu [řízení přístupu hostů v Azure Active Directory](https://docs.microsoft.com/en-us/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory). Pokud přístup hosta je vypnutý, obraťte se na globální správci tenanta ho chcete zapnout. <!-- Todo - How can they find their global administrator -->

## <a name="view-features-replaced-by-the-new-billing-account"></a>Zobrazit funkce nahrazuje nové fakturační účet

Následující smlouvy Enterprise funkce se nahradí s novými funkcemi v fakturační účet zákaznické smlouvy Microsoft.

### <a name="enterprise-agreement-accounts"></a>Účty smlouvy Enterprise

Nové fakturační účet nepodporují účty, které byly vytvořeny ve vaší registraci smlouvy Enterprise. Předplatné na účet patří do části faktury pro oddělení na jejich odpovídajících vytvořit. Vlastníci účtu stát creators předplatného Azure a můžete vytvářet a spravovat odběry pro příslušné oddíly faktury.

### <a name="notification-contacts"></a>Oznámení kontakty

Kontakty oznámení se odesílají e-mailové komunikace o smlouvy Azure Enterprise. Nejsou podporovány v nové fakturační účet. Informace o kreditech Azure a faktury mailů uživatelům, kteří mají přístup k fakturačním profily ve vašem fakturačním účtu.

### <a name="spending-quotas"></a>Kvóty útraty

Kvóty útraty, které byly nastavené pro oddělením ve vaší registraci smlouvy Enterprise jsou nahrazeny rozpočty v nové fakturační účet. Rozpočtu se vytvoří pro každou útraty kvótu nastavenou pro oddělením ve vaší registraci. Další informace o rozpočty, naleznete v tématu [vytvářet a spravovat Azure rozpočty](../cost-management/manage-budgets.md).

### <a name="cost-centers"></a>Cost center

Nákladové středisko, které byly nastaveny na předplatných Azure na smlouvu Enterprise se přenesou na nový fakturační účet. Nákladových středisek pro oddělení a účty smlouvy Enterprise, ale podporované nejsou.

## <a name="additional-information"></a>Další informace

Následující části obsahují další informace o fakturačním účtu.

### <a name="no-service-downtime"></a>Žádný výpadek služby

Služby Azure ve vašem předplatném poběží i nadále bez přerušení. Převedeme pouze fakturační vztah pro vaše předplatná Azure. Stávající prostředky, skupiny prostředků ani skupiny pro správu to neovlivní.

### <a name="user-access-to-azure-resources"></a>Přístup uživatelů k prostředkům Azure

Přístup k prostředkům Azure, která byla nastavena pomocí Azure RBAC (řízení přístupu na základě role) nemá vliv během přechodu.

### <a name="azure-reservations"></a>Azure Reservations

Žádné rezervace Azure v smlouvu Enterprise se přesune do nového fakturačního účtu. Během přechodu nedojde k žádným změnám slev za rezervace, které se u vašich předplatných uplatňují.

### <a name="azure-marketplace-products"></a>Produkty Azure Marketplace

Jakékoli produkty Azure Marketplace v registraci smlouvy Enterprise se přesouvají společně s předplatným. Během přechodu nebude potřeba přístup k službě produktů Marketplace.

### <a name="support-plan"></a>Plán podpory

V rámci přechodu se nepřevádějí výhody podpory. Koupit nový plán podpory, chcete-li získat výhody za odběr služeb Azure v nové fakturačnímu účtu.

### <a name="past-charges-and-balance"></a>Uplynula poplatky a vyrovnání

Vyrovnávání nákladů a kredity před přechodu lze zobrazit v registraci smlouvy Enterprise na webu Azure portal. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>Pokud by měla být dokončena instalace?

Dokončete nastavení fakturačnímu účtu před vypršením platnosti smlouva Enterprise Agreement enrollment. Pokud vyprší registraci služeb ve vašem předplatném Azure stále na poběží dál bez přerušení. Nicméně se vám účtovat prodejními sazbami pro služby.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Smlouva Enterprise Agreement enrollment po nastavení změny

Předplatná Azure, které jsou vytvořeny pro smlouva Enterprise Agreement enrollment po přechodu je ručně přesunout do nové fakturační účet. Další informace najdete v tématu [získat vlastnictví předplatného Azure od jiných uživatelů fakturace](billing-mca-request-billing-ownership.md). Pro přesun rezervací Azure, které jsou po přechodu, zakoupeny [obraťte se na podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Můžete také poskytnout uživatelům přístup k fakturačnímu účtu po přechodu. Další informace najdete v tématu [Správa fakturace rolí na portálu Azure portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>Vrátit zpět na přechod

Přechod nelze vrátit zpět. Jakmile fakturaci vašeho předplatného Azure je převeden na nové fakturační účet, nejde vrátit zpět zpět k vaší registraci smlouvy Enterprise.

### <a name="closing-your-browser-during-setup"></a>Zavření prohlížeče během instalace

Předtím, než kliknete na **Start přechod**, můžete prohlížeč zavřít. Můžete se vrátit k instalaci pomocí odkazu, který jste získali v e-mailu a zahajte přechod. Pokud zavřete prohlížeč, po spuštění přechodu, přechod na poběží dál. Vraťte se na stránku přechodu stavu ke sledování nejnovější stav přechod. Získáte e-mailu, když se dokončí přechod.

## <a name="complete-the-setup-in-the-azure-portal"></a>Dokončit nastavení na portálu Azure portal

K dokončení instalace, musíte mít přístup k nové fakturační účet a smlouva Enterprise Agreement enrollment. Další informace najdete v tématu [na úroveň požadovanou k dokončení nastavení vašeho fakturačního účtu](#access-required-to-complete-the-setup).

1. Přihlaste se k webu Azure portal pomocí odkazu v e-mailu, který jste dostali při podepsání smlouvy zákazníka Microsoftu.

2. Pokud někdo jiný ve vaší organizaci podepsané smlouvy nebo nemáte k dispozici v e-mailu, přihlaste se pomocí následujícího odkazu. Nahraďte **enrollmentNumber** s registrační číslo smlouvy Enterprise, který byl obnoven.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Vyberte **Start přechod** v posledním kroku instalace. Jakmile vyberete start přechodu:

    ![Snímek obrazovky zobrazující Průvodce instalací](./media/billing-mca-setup-account/ea-mca-set-up-wizard.png)

    - V nové fakturační účet se vytvoří hierarchii fakturační odpovídající vaší smlouvy Enterprise hierarchii. Další informace najdete v tématu [změny fakturace hierarchii porozumět](#understand-changes-to-your-billing-hierarchy).
    - Správce z smlouva Enterprise Agreement enrollment získávali přístup k nové fakturační účet tak, aby se i nadále spravují fakturaci pro vaši organizaci.
    - Fakturaci vašeho předplatného Azure je převeden do nového účtu. **Nebudou existovat žádný vliv na služby Azure během tohoto přechodu. Budete udržovat spuštěna bez jakéhokoli přerušení**.
    - Pokud máte Azure rezervace, přesunou se k novému fakturačního účtu se stejnými slevy a termín. Vaše sleva za rezervaci bude dál uplatňovat během přechodu.

4. Můžete monitorovat stav přechodu na **přechod stavu** stránky.

   ![Snímek obrazovky zobrazující přechod stavu](./media/billing-mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-the-billing-account-is-set-up-properly"></a>Ověření fakturace účtu je správně nastavený.

 Ověřte následující zajistit, že je správně nastavený nové fakturačnímu účtu:

### <a name="azure-subscriptions"></a>Předplatná Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Hledat na **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Vyberte fakturační profil. V závislosti na přístup budete muset vybrat fakturační účet. Fakturační účet, vyberte **fakturace profily** a potom fakturační profil.

4. Vyberte **předplatná Azure** na levé straně.

   ![Snímek obrazovky zobrazující seznam předplatných](./media/billing-mca-setup-account/billing-mca-subscriptions-post-transition.png)

Předplatná Azure, které jsou přešel ze smlouvu Enterprise k nové fakturační účet se zobrazí na stránce předplatná Azure. Pokud se domníváte, že je jakékoli předplatné chybí, přechod fakturace předplatného ručně na webu Azure portal. Další informace najdete v tématu [získat fakturační vlastnictví předplatného Azure od jiných uživatelů](billing-mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Azure Reservations

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Hledat na **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Vyberte oddíl faktury. V závislosti na přístup budete muset vybrat fakturační účet nebo fakturační profil.  Fakturační účet nebo fakturační profil, vyberte **fakturovat oddíly** a potom část faktury.

    ![Snímek obrazovky zobrazující seznam přechod příspěvek části faktury](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Vyberte **všechny produkty** na levé straně.

5. Hledat na **vyhrazené**.

    ![Snímek obrazovky zobrazující seznam předplatných příspěvek přechodu](./media/billing-mca-setup-account/billing-mca-azure-reservations-post-transition.png)

Azure rezervace, které jsou přesunuty z smlouva Enterprise Agreement enrollment do nové fakturační účet se zobrazí na stránce všechny produkty. Opakujte kroky pro všechny části faktury k ověření, že všechny rezervace Azure jsou přemístěné z smlouva Enterprise Agreement enrollment. Pokud si myslíte, že chybí všechny rezervace Azure [požádejte podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) rezervace přesunout do nové fakturační účet.

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Přístup k podnikovým správcům na fakturační profil

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Hledat na **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Vyberte fakturační profil, který vytvořili pro vaši registraci. V závislosti na přístup budete muset vybrat fakturační účet.  Fakturační účet, vyberte **fakturace profily** a potom fakturační profil.

4. Vyberte **řízení přístupu (IAM)** z levé strany.

   ![Snímek obrazovky zobrazující přístup enterprise administrators příspěvek přechodu](./media/billing-mca-setup-account/billing-mca-ea-admins-access-post-transition.png)

Tito správci jsou uvedené jako vlastníky profil při organizace, které jsou uvedeny správci s oprávněními jen pro čtení fakturačních podmínek jako u fakturace čtenáři profilu. Pokud si myslíte, že chybí přístup pro libovolný správce organizace, můžete přiřadit přístup na webu Azure Portal. Další informace najdete v tématu [Správa fakturace rolí na portálu Azure portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Přístup k podnikovým správcům, správcům oddělení a vlastníci účtu v části faktury

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Hledat na **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-mca-setup-account/billing-search-cost-management-billing.png).

3. Vyberte oddíl faktury. Faktury sekce mají stejný název jako jejich odpovídajících odděleních v registraci smlouvy Enterprise. V závislosti na přístup budete muset vybrat fakturační profil nebo fakturační účet. Fakturační profil nebo fakturační účet, vyberte **fakturovat oddíly** a pak vyberte oddíl faktury.

   ![Snímek obrazovky zobrazující seznam přechod příspěvek části faktury](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Vyberte **řízení přístupu (IAM)** z levé strany.

    ![Snímek obrazovky zobrazující přístup z oddělení a účet správce přístup k přechodu příspěvek](./media/billing-mca-setup-account/billing-mca-department-account-admins-access-post-transition.png)

Tito správci a správci oddělení jsou uvedené jako vlastníky části faktury nebo čtenáři části faktury Ačkoli vlastníky účtů v oddělení jsou uvedeny jako tvůrce předplatného Azure. Opakujte krok pro všechny oddíly faktury ke kontrole přístupu pro všechny oddělením ve vaší registraci smlouvy Enterprise. Vlastníky účtů, které nebyly součástí oddělení získají oprávnění na oddíl faktury s názvem **části faktury výchozí**. Pokud se domníváte, že chybí přístupu pro všechny správce, můžete udělit přístup k webu Azure Portal. Další informace najdete v tématu [Správa fakturace rolí na portálu Azure portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

## <a name="next-steps"></a>Další postup

- [Začínáme s vaší nové fakturační účet](billing-mca-overview.md)

- [Provádění úloh smlouvy Enterprise ve vašem fakturačním účtu Microsoft zákaznické smlouvy](billing-mca-enterprise-operations.md)

- [Správa přístupu k fakturačnímu účtu](billing-understand-mca-roles.md)

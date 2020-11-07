---
title: 'Kurz: Konfigurace Tic-Tac Mobile pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po Tic-Tac Mobile.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: 99fe2f8356b53b5d8e459dbf62534ddb4f0019b2
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357262"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>Kurz: Konfigurace Tic-Tac Mobile pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v Tic-Tac Mobile i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odhodnotí uživatele a skupiny s využitím služby Azure AD Provisioning v rámci služby [Tic-Tac Mobile](https://www.tictacmobile.com/) . Informace o tom, jak tato služba funguje, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro aplikace SaaS (software jako služba) s Azure AD](../manage-apps/user-provisioning.md).


## <a name="capabilities-supported"></a>Podporované funkce

> [!div class="checklist"]
> * Umožňuje vytvořit uživatele v Tic-Tac Mobile.
> * Odeberte uživatele v Tic-Tac Mobile, když už nevyžadují přístup.
> * Udržujte atributy uživatelů synchronizované mezi Azure AD a Tic-Tac Mobile.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenanta Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování. Příkladem může být správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce.
* [Mobilní účet Tic-Tac](https://www.tictacmobile.com/) s rolí správce super.


## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování

1. Seznamte se s [fungováním služby zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
1. Zjistěte, kdo bude v [rozsahu zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
1. Určete, jaká data se mají [mapovat mezi Azure AD a Tic-Tac Mobile](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Tic-Tac Mobile na podporu zřizování s Azure AD

Kontaktujte support@tictacmobile.com pro získání **adresy URL tenanta** a **tajného tokenu**. Abyste mohli získat token, musíte mít v Tic-Tac mobilní roli správce role Super. Token se zadává do pole **token tajného klíče** na kartě **zřizování** Tic-Tac mobilní aplikace v Azure Portal.

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Tic-Tac mobilní aplikace z Galerie aplikací Azure AD

Přidejte Tic-Tac mobilní zařízení z Galerie aplikací Azure AD a začněte spravovat zřizování pro Tic-Tac Mobile. Pokud jste dříve nastavili Tic-Tac Mobile pro jednotné přihlašování, můžete použít stejnou aplikaci. Když nejprve otestujete integraci, vytvořte samostatnou aplikaci. Další informace o tom, jak přidat aplikaci z Galerie, najdete v tématu [zřizování aplikace založené na atributech s filtry oborů](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování

Pomocí služby Azure AD Provisioning můžete nastavit obor, který se zřídí v závislosti na přiřazení aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se bude zřizovat pro vaši aplikaci na základě přiřazení, postupujte podle kroků v části [Správa přiřazení uživatelů pro aplikaci v Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) k přiřazení uživatelů a skupin k aplikaci. Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, použijte filtr oboru, jak je popsáno v tématu [zřizování aplikací založené na atributech s filtry oborů](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

* Když přiřadíte uživatele a skupiny k Tic-Tac mobilní, musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat tak další role.
* Začněte v malém. Před zavedením všech uživatelů proveďte test pomocí malé sady uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete spravovat řízení přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastaven na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro Tic-Tac Mobile

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Tic-Tac Mobile ve službě Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**  >  **všechny aplikace**.

    ![Snímek obrazovky, který zobrazuje podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikace vyberte možnost **Tic-Tac Mobile**.

    ![Snímek obrazovky zobrazující Tic-Tac mobilní odkaz v seznamu aplikací](common/all-applications.png)

1. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky zobrazující kartu zřizování](common/provisioning.png)

1. Nastavte **režim zřizování** na **automaticky**.

    ![Snímek obrazovky zobrazující automatickou možnost karty zřizování](common/provisioning-automatic.png)

1. V části **přihlašovací údaje správce** zadejte Tic-Tac **Adresa URL mobilního klienta** a **tajný token**. Vyberte **Test připojení** , aby se služba Azure AD mohla připojit k Tic-Tac Mobile. Pokud se připojení nepovede, ujistěte se, že váš mobilní účet Tic-Tac má oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky, který zobrazuje pole tajného tokenu](common/provisioning-testconnection-tenanturltoken.png)

1. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování. Zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![Snímek obrazovky zobrazující okno s oznámením e-mailem](common/provisioning-notification-email.png)

1. Vyberte **Uložit**.

1. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele a Tic-Tac mobilní zařízení**.

1. Zkontrolujte atributy uživatelů synchronizované z Azure AD a Tic-Tac Mobile v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Tic-Tac Mobile pro operace aktualizace. Pokud změníte [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), je nutné zajistit, aby Tic-Tac mobilní rozhraní API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |externalId|Řetězec|
   |title|Řetězec|
   |e-maily [typ EQ "Work"]. Value|Řetězec|
   |preferredLanguage|Řetězec|
   |externalId|Řetězec|
   |userType|Řetězec|
   |locale|Řetězec|
   |timezone|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: employeeNumber|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: costCenter|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Organization|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: divize|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec|

1. Pokud chcete nakonfigurovat filtry oborů, přečtěte si pokyny v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pokud chcete povolit službu Azure AD Provisioning pro Tic-Tac Mobile, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Snímek obrazovky, na kterém se zobrazuje stav zřizování zapnuto.](common/provisioning-toggle-on.png)

1. Definujte uživatele nebo skupiny, které chcete zřídit pro Tic-Tac mobilní, výběrem požadovaných hodnot v části **Rozsah** **Nastavení** .

    ![Snímek obrazovky zobrazující obor zřizování](common/provisioning-scope.png)

1. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Snímek obrazovky, který ukazuje uložení konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení

Po nakonfigurování zřizování použijte následující prostředky k monitorování vašeho nasazení.

1. Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně zřídili.
1. Zkontrolujte indikátor [průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) a zobrazte stav cyklu zřizování a způsob, jakým se má dokončit.
1. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [v tématu zřizování aplikací ve stavu karantény](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../manage-apps/check-status-user-account-provisioning.md).

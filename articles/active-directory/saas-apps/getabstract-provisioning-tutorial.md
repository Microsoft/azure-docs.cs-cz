---
title: 'Kurz: Konfigurace getabstract pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů od Azure Active Directory až po getabstract.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 1d1b2417750b917f5b09bb53ee980887218a785c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102616107"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>Kurz: Konfigurace getabstract pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v rámci metody getabstract a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny pro [getabstract](https://www.getabstract.com) pomocí služby zřizování Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro aplikace SaaS (software jako služba) s Azure AD](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Podporované funkce

> [!div class="checklist"]
> * Umožňuje vytvořit uživatele v getabstract.
> * Odeberte uživatele v getabstract, když už nevyžadují přístup.
> * Udržujte atributy uživatele synchronizované mezi Azure AD a getabstract.
> * Zřizování skupin a členství ve skupinách v getabstract.
> * Povolit [jednotné přihlašování (SSO)](getabstract-tutorial.md) k getabstract (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenanta Azure AD](../develop/quickstart-create-new-tenant.md)
* Uživatelský účet ve službě Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování. Příkladem může být správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce.
* Klient getabstract (licence getabstract Corporate).
* Jednotné přihlašování je povolené v tenantovi Azure AD a v tenantovi getabstract.
* Schválení a systém pro správu identit mezi doménami (SCIM), povolení pro getabstract (Odeslat e-mail na adresu b2b.itsupport@getabstract.com .)

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování

1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
1. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Určete, jaká data se mají [mapovat mezi Azure AD a getabstract](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace getabstract pro podporu zřizování pomocí Azure AD

1. Přihlaste se k getabstract.
1. Vyberte ikonu nastavení umístěnou v pravém horním rohu a vyberte možnost **My Central admin** .

    ![Snímek obrazovky, který zobrazuje getabstract my Central admin.](media/getabstract-provisioning-tutorial/my-account.png)

1. Vyhledejte a vyberte možnost **správce SCIM** .

    ![Snímek obrazovky, který zobrazuje správce getabstract SCIM](media/getabstract-provisioning-tutorial/scim-admin.png)

1. Vyberte **Přejít**.

    ![Snímek obrazovky, který zobrazuje ID klienta getabstract SCIM](media/getabstract-provisioning-tutorial/scim-client-go.png)

1. Vyberte možnost **generovat nový token**.

    ![Snímek obrazovky, který ukazuje SCIM token getabstract 1.](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

1. Pokud jste si jisti, vyberte možnost **generovat nový token**. V opačném případě vyberte **Zrušit**.

    ![Snímek obrazovky, který ukazuje token getabstract SCIM 2.](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

1. Buď vyberte ikonu kopírování do schránky, nebo vyberte celý token a zkopírujte jej. Také si všimněte, že je tenant nebo základní adresa URL `https://www.getabstract.com/api/scim/v2` . Tyto hodnoty se zadají do polí **tajný token** a **Adresa URL tenanta** na kartě **zřizování** aplikace getabstract v Azure Portal.

    ![Snímek obrazovky, který ukazuje SCIM token getabstract 3.](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)

## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>Krok 3. Přidat getabstract z Galerie aplikací Azure AD

Přidejte getabstract z Galerie aplikací Azure AD a začněte spravovat zřizování pro getabstract. Pokud jste dříve nastavili getabstract pro jednotné přihlašování, můžete použít stejnou aplikaci. Při počátečním testování integrace doporučujeme vytvořit samostatnou aplikaci. Další informace o tom, jak přidat aplikaci z Galerie, najdete v [tomto rychlém](../manage-apps/add-application-portal.md)startu.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování

Službu zřizování Azure AD můžete použít k určení oboru, který se zřídí v závislosti na přiřazení aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oboru, jak je popsáno v tématu [zřizování aplikací s filtry oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Když přiřadíte uživatele a skupiny k getabstract, musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat tak další role.

* Začněte v malém. Před zavedením všech uživatelů proveďte test pomocí malé sady uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete tuto možnost řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastaven na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro getabstract

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro funkci getabstract v Azure AD

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**  >  **všechny aplikace**.

    ![Snímek obrazovky, který zobrazuje podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **getabstract**.

    ![Snímek obrazovky, který zobrazuje odkaz getabstract v seznamu aplikací.](common/all-applications.png)

1. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky zobrazující kartu zřizování](common/provisioning.png)

1. Nastavte **režim zřizování** na **automaticky**.

    ![Snímek obrazovky, který ukazuje režim zřizování nastavený na automatické.](common/provisioning-automatic.png)

1. V části **přihlašovací údaje správce** zadejte **adresu URL klienta** getabstract a informace o **tajném tokenu** . Vyberte **Test připojení** , aby se služba Azure AD mohla připojit k getabstract. Pokud se připojení nepovede, zajistěte, aby měl účet getabstract oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky zobrazující pole Adresa URL tenanta a tajný klíč tokenu](common/provisioning-testconnection-tenanturltoken.png)

1. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování. Zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![Snímek obrazovky zobrazující okno s oznámením e-mailem](common/provisioning-notification-email.png)

1. Vyberte **Uložit**.

1. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatelů do getabstract**.

1. Zkontrolujte atributy uživatele synchronizované z Azure AD do getabstract v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v getabstract pro operace aktualizace. Pokud změníte [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), je nutné zajistit, aby getabstract API podporoval filtrování uživatelů na základě tohoto atributu. Vyberte **Uložit** a potvrďte všechny změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |userName|Řetězec|&check;|
   |active|Logická hodnota|
   |emails[type eq "work"].value|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |externalId|Řetězec|
   |preferredLanguage|Řetězec|

1. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do getabstract**.

1. Zkontrolujte atributy skupiny synchronizované z Azure AD do getabstract v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v getabstract pro operace aktualizace. Vyberte **Uložit** a potvrďte všechny změny.

    |Atribut|Typ|Podporováno pro filtrování|
    |---|---|---|
    |displayName|Řetězec|&check;|
    |externalId|Řetězec|
    |členy|Referenční informace|

1. Pokud chcete nakonfigurovat filtry oborů, přečtěte si pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pokud chcete povolit službu Azure AD Provisioning pro getabstract, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Snímek obrazovky, na kterém se zobrazuje stav zřizování zapnuto.](common/provisioning-toggle-on.png)

1. Definujte uživatele nebo skupiny, které chcete zřídit pro getabstract, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **rozsahu** .

    ![Snímek obrazovky zobrazující obor zřizování](common/provisioning-scope.png)

1. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Snímek obrazovky zobrazující tlačítko Uložit](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než u dalších cyklů, ke kterým dojde každých 40 minut, pokud je služba zřizování Azure AD spuštěná.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení

Po dokončení konfigurace zřizování použijte následující prostředky k monitorování nasazení:

* Pomocí [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně zřídili.
* Zkontrolujte indikátor [průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) a zobrazte stav cyklu zřizování a způsob, jakým se má dokončit.
* Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete v tématu [stav zřizování aplikace v karanténě](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

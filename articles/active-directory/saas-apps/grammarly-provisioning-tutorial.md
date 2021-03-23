---
title: 'Kurz: Konfigurace automatického zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD do gramaticky.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: cd2dd9d7-4901-40c8-8888-98850557b072
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2021
ms.author: Zhchia
ms.openlocfilehash: ca01289ce66afe642081e5be17373e640dd1e46d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864801"
---
# <a name="tutorial-configure-grammarly-for-automatic-user-provisioning"></a>Kurz: Konfigurace gramatiky pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v gramatické i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny pro [gramatické](https://www.grammarly.com/) používání služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v gramaticky
> * Odebrat uživatele v gramatickém případě, že už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a gramaticky

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet v Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (např. správce aplikací, správce cloudových aplikací, vlastník aplikací nebo globální správce) 
* Gramatický obchodní účet s přístupem správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
1. Zjistěte, kdo bude v [rozsahu zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
1. Určete, jaká data se mají [mapovat mezi Azure AD a gramaticky](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-grammarly-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace pro zajištění gramatiky pro podporu zřizování pomocí Azure AD

Přihlaste se k vašemu Gramatickému zástupce nebo ho zapište do <support@grammarly.com> žádosti o váš token zřizování.

## <a name="step-3-add-grammarly-from-the-azure-ad-application-gallery"></a>Krok 3. Jak gramaticky přidat z Galerie aplikací Azure AD

Přidejte gramaticky z Galerie aplikací Azure AD, abyste mohli začít spravovat zřizování pro gramatiku. Pokud jste dříve nastavili pro jednotné přihlašování gramatickou hodnotu, můžete použít stejnou aplikaci. Při počátečním testování integrace doporučujeme vytvořit samostatnou aplikaci. Další informace o tom, jak přidat aplikaci z Galerie, najdete v [tomto rychlém](../manage-apps/add-application-portal.md)startu.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování

Službu zřizování Azure AD můžete použít k určení oboru, který se zřídí v závislosti na přiřazení aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oboru, jak je popsáno v tématu [zřizování aplikací s filtry oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Když přiřadíte uživatele a skupiny do gramaticky, musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat tak další role.

* Začněte v malém. Před zavedením všech uživatelů proveďte test pomocí malé sady uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete tuto možnost řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastaven na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-grammarly"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro gramaticky

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="configure-automatic-user-provisioning-for-grammarly-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro gramaticky ve službě Azure AD

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**  >  **všechny aplikace**.

    ![Snímek obrazovky, který zobrazuje podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikací vyberte možnost **gramaticky**.

    ![Snímek obrazovky, který zobrazuje gramatický odkaz v seznamu aplikací.](common/all-applications.png)

1. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky zobrazující kartu zřizování](common/provisioning.png)

1. Nastavte **režim zřizování** na **automaticky**.

    ![Snímek obrazovky, který ukazuje režim zřizování nastavený na automatické.](common/provisioning-automatic.png)

1. V části **přihlašovací údaje správce** zadejte do pole zadejte **adresu URL tenanta** `https://sso.grammarly.com/scim/v2` a do pole **tajný token** zadejte token, který se poskytuje gramaticky (viz krok 2 výše). Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k gramaticky. Pokud se připojení nepovede, zajistěte, aby měl váš gramatický účet oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky zobrazující pole Adresa URL tenanta a tajný klíč tokenu](common/provisioning-testconnection-tenanturltoken.png)

1. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování. Zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![Snímek obrazovky zobrazující okno s oznámením e-mailem](common/provisioning-notification-email.png)

1. Vyberte **Uložit**.

1. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele pro gramaticky**.

1. Zkontrolujte atributy uživatele synchronizované z Azure AD do gramaticky v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají pro porovnávání uživatelských účtů v rámci gramatické operace aktualizace. Pokud změníte [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), bude nutné zajistit, aby GRAMATICKÉ rozhraní API podporovalo filtrování uživatelů na základě tohoto atributu. Vyberte **Uložit** a potvrďte všechny změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |userName|Řetězec|&check;|
   |externalId|Řetězec|
   |active|Logická hodnota|
   |displayName|Řetězec|
   |emails[type eq "work"].value|Řetězec|


1. Pokud chcete nakonfigurovat filtry oborů, přečtěte si pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pokud chcete povolit službu zřizování Azure AD pro gramaticky, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Snímek obrazovky, na kterém se zobrazuje stav zřizování zapnuto.](common/provisioning-toggle-on.png)

1. Definujte uživatele nebo skupiny, které chcete v případě potřeby zřídit, v části **Nastavení** vyberte požadované hodnoty v poli **Rozsah** .

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

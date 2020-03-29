---
title: 'Kurz: Konfigurace programu Wrike pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty společnosti Wrike.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 5dd4f5ac6152c22b5e2a84ecc0774672bcd5590b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064185"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Kurz: Konfigurace wrike pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které provádíte ve Wrike a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a deprovision uživatelů nebo skupin wrike.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací masu jako služby (SaaS) pomocí služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce náhledu najdete v [tématu Doplňkové podmínky použití pro náhledy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Wrike nájemce](https://www.wrike.com/price/)
* Uživatelský účet ve Wrike s oprávněními správce

## <a name="assign-users-to-wrike"></a>Přiřazení uživatelů k Wrike
Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů jsou synchronizováni pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů se rozhodněte, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Wrike. Pak přiřadit tyto uživatele nebo skupiny Wrike podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Důležité tipy pro přiřazení uživatelů do Wrike

* Doporučujeme přiřadit jediného uživatele Azure AD wrike otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé nebo skupiny mohou být přiřazeny později.

* Když přiřadíte uživatele k Wrike, musíte vybrat všechny platné role specifické pro aplikaci (pokud jsou k dispozici) v dialogovém okně přiřazení. Uživatelé s rolí Výchozí přístup jsou z zřizování vyloučeni.

## <a name="set-up-wrike-for-provisioning"></a>Nastavit Wrike pro zřizování

Před konfigurací Wrike pro automatické zřizování uživatelů pomocí Azure AD, je třeba povolit system for-domain Identity Management (SCIM) zřizování na Wrike.

1. Přihlaste se ke [své administrátorské konzoli Wrike](https://www.Wrike.com/login/). Přejděte na id klienta. Vyberte **aplikace & integrace**.

    ![Aplikace & integrace](media/Wrike-provisioning-tutorial/admin.png)

2.  Přejděte na **Azure AD** a vyberte ho.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Vyberte SCIM. Zkopírujte **základní adresu URL**.

    ![Základní adresa URL](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Vyberte **ROZHRANÍ API** > **Azure SCIM**.

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Otevře se vyskakovací okno. Zadejte stejné heslo, které jste vytvořili dříve pro vytvoření účtu.

    ![Token pro vytvoření wrike](media/Wrike-provisioning-tutorial/password.png)

6.  Zkopírujte **tajný token**a vložte jej do služby Azure AD. Chcete-li dokončit nastavení zřizování na Wrike, vyberte **možnost Uložit.**

    ![Trvalý přístupový token](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Přidat Wrike z galerie

Před konfigurací Wrike pro automatické zřizování uživatelů pomocí Azure AD, přidejte Wrike z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

Pokud chcete přidat Wrike z galerie aplikací Azure AD, postupujte takto.

1. Na [webu Azure Portal](https://portal.azure.com)v levém navigačním podokně vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Wrike**, vyberte v panelu výsledků **možnost Wrike** a pak vyberte **Přidat,** chcete-li přidat aplikaci.

    ![Wrike v seznamu výsledků](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Konfigurace automatického zřizování uživatelů na Wrike 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin ve Wrike na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Chcete-li povolit jednotné přihlašování pro Wrike založené na SAML, postupujte podle pokynů v [kurzu jednotného přihlášení Wrike](wrike-tutorial.md). Jednotné přihlašování lze konfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce se vzájemně doplňují.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Wrike ve službě Azure AD

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace** > **Všechny aplikace**.

    ![Všechny aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Možnost Wrike**.

    ![Odkaz Wrike v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Režim zřizování nastavený na automatické](common/provisioning-automatic.png)

5. V části Přihlašovací údaje správce zadejte **hodnoty základní adresy URL** a **tokenu trvalého přístupu** načtené dříve v **adrese URL klienta** a **tajném tokenu**. Vyberte **Testovat připojení** a ujistěte se, že Azure AD můžete připojit k Wrike. Pokud se připojení nezdaří, ujistěte se, že váš účet Wrike má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

7. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování. Zaškrtněte **políčko Odeslat e-mailové oznámení, když dojde k chybě.**

    ![E-mail s oznámením](common/provisioning-notification-email.png)

8. Vyberte **Uložit**.

9. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s wrike**.

    ![Mapování uživatelů Wrike](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Wrike v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Wrike pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte **uložit.**

    ![Wrike uživatelské atributy](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Chcete-li konfigurovat filtry oborů, postupujte podle pokynů v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Chcete-li povolit službu zřizování Azure AD pro Wrike, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit Wrike výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

14. Až budete připraveni k zřízení, vyberte **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace. Další informace o tom, jak dlouho trvá uživatelům nebo skupinám k zajištění, naleznete v tématu [Jak dlouho bude trvat zřizování uživatelů?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Část Aktuální **stav** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Wrike. Další informace naleznete [v tématu Kontrola stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Chcete-li číst protokoly zřizování Azure AD, najdete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

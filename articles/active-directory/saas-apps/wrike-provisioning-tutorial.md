---
title: 'Kurz: Konfigurace Wrike pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro Automatické zřizování a rušení uživatelských účtů pro Wrike.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 53b1db1a8c4da59055c0af5f448fa0c8a6933daf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95988089"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Kurz: Konfigurace Wrike pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které provedete v Wrike a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a rušení zřizování uživatelů nebo skupin do Wrike.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro aplikace typu software jako služba (SaaS) s Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant Wrike](https://www.wrike.com/price/)
* Uživatelský účet v Wrike s oprávněními správce

## <a name="assign-users-to-wrike"></a>Přiřazení uživatelů k Wrike
Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé nebo skupiny, které jsou přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, rozhodněte se, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Wrike. Pak těmto uživatelům nebo skupinám přiřaďte Wrike podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Důležité tipy pro přiřazení uživatelů k Wrike

* Doporučujeme, abyste jednomu uživateli Azure AD přiřadili Wrike k otestování automatické konfigurace zřizování uživatelů. Další uživatele nebo skupiny se dají přiřadit později.

* Když přiřadíte uživatele k Wrike, musíte vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná) v dialogovém okně přiřazení. Uživatelé s výchozí rolí přístupu se z zřizování vylučují.

## <a name="set-up-wrike-for-provisioning"></a>Nastavení Wrike pro zřizování

Než nakonfigurujete Wrike pro Automatické zřizování uživatelů pomocí Azure AD, musíte povolit systém pro zřizování SCIM (Domain Identity Management) na Wrike.

1. Přihlaste se ke [konzole pro správu Wrike](https://www.Wrike.com/login/). Přejít na ID tenanta. Vyberte **aplikace & integrace**.

    ![Aplikace & integrace](media/Wrike-provisioning-tutorial/admin.png)

2.  Přejít na **Azure AD** a vyberte ji.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Vyberte SCIM. Zkopírujte **základní adresu URL**.

    ![Základní adresa URL](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Vyberte **API**  >  **Azure SCIM**.

    ![SCIM Azure](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Otevře se místní okno. Pokud chcete vytvořit účet, zadejte stejné heslo, které jste vytvořili dříve.

    ![Wrike vytvořit token](media/Wrike-provisioning-tutorial/password.png)

6.  Zkopírujte **tajný token** a vložte ho do Azure AD. Výběrem **Uložit** dokončete nastavení zřizování na Wrike.

    ![Trvalý přístupový token](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Přidání Wrike z Galerie

Před konfigurací Wrike pro Automatické zřizování uživatelů pomocí Azure AD přidejte Wrike z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

Pokud chcete přidat Wrike z Galerie aplikací Azure AD, postupujte podle těchto kroků.

1. V [Azure Portal](https://portal.azure.com)v levém navigačním podokně vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Wrike**, na panelu výsledků vyberte **Wrike** a pak vyberte **Přidat** , aby se aplikace přidala.

    ![Wrike v seznamu výsledků](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Konfigurace automatického zřizování uživatelů na Wrike 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Wrike na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Pokud chcete povolit jednotné přihlašování založené na SAML pro Wrike, postupujte podle pokynů v [kurzu Wrike jednotného přihlašování](wrike-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Wrike ve službě Azure AD

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**  >  **všechny aplikace**.

    ![Všechny aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Wrike**.

    ![Odkaz Wrike v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Režim zřizování nastavený na automatické](common/provisioning-automatic.png)

5. V části přihlašovací údaje správce zadejte **základní adresu URL** a hodnoty **trvalého přístupového tokenu** , které byly dříve načteny v **adrese URL tenanta** a **tajném tokenu** v uvedeném pořadí. Vyberte **Test připojení** , aby se služba Azure AD mohla připojit k Wrike. Pokud se připojení nepovede, ujistěte se, že má váš účet Wrike oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

7. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování. Zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mail s oznámením](common/provisioning-notification-email.png)

8. Vyberte **Uložit**.

9. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Wrike**.

    ![Mapování uživatelů Wrike](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Zkontrolujte atributy uživatele synchronizované z Azure AD do Wrike v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Wrike pro operace aktualizace. Vyberte **Uložit** a potvrďte všechny změny.

    ![Atributy uživatele Wrike](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Pokud chcete nakonfigurovat filtry oborů, postupujte podle pokynů v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Pokud chcete povolit službu Azure AD Provisioning pro Wrike, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit pro Wrike, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

14. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než následné synchronizace. Další informace o tom, jak dlouho trvá uživatelům nebo skupinám při zřizování, najdete v tématu [Jak dlouho bude trvat zřizování uživatelů?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pomocí oddílu **aktuální stav** můžete monitorovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Wrike. Další informace najdete v tématu o [kontrole stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Pokud si chcete přečíst protokoly zřizování Azure AD, přečtěte si téma [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
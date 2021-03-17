---
title: 'Kurz: Konfigurace Foodee pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro Automatické zřizování a rušení uživatelských účtů pro Foodee.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 8b4bfa7e9bf457d79c6c4a0b5255bce4fe36dff4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358673"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Kurz: Konfigurace Foodee pro Automatické zřizování uživatelů

V tomto článku se dozvíte, jak nakonfigurovat Azure Active Directory (Azure AD) ve službě Foodee a Azure AD tak, aby automaticky zřídily nebo zrušily zřizování uživatelů nebo skupin Foodee.

> [!NOTE]
> Tento článek popisuje konektor, který je postaven na službě Azure AD zřizování uživatelů. Informace o tom, co tato služba funguje a jak funguje, a získat odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů při SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je momentálně ve verzi Preview. Další informace o funkci používání podmínek Azure pro funkce ve verzi Preview najdete v [části dodatečné použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že jste splnili následující požadavky:

* Tenant Azure AD
* [Tenant Foodee](https://www.food.ee/about/)
* Uživatelský účet v Foodee s oprávněními správce

## <a name="assign-users-to-foodee"></a>Přiřazení uživatelů k Foodee 

Azure AD používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé nebo skupiny, které jsou přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, měli byste se rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Foodee. Po provedení tohoto rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Foodee podle pokynů v tématu [přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-foodee"></a>Důležité tipy pro přiřazení uživatelů k Foodee 

Při přiřazování uživatelů mějte na paměti následující tipy:

* Doporučujeme, abyste přidělili pouze jednoho uživatele Azure AD k Foodee, abyste mohli otestovat konfiguraci automatického zřizování uživatelů. Později můžete přiřadit další uživatele nebo skupiny.

* Když přiřazujete uživatele k Foodee, vyberte libovolnou platnou roli specifickou pro aplikaci, pokud je k dispozici v podokně **přiřazení** . Uživatelé, kteří mají *výchozí roli přístupu* , jsou ze zřizování vyloučeni.

## <a name="set-up-foodee-for-provisioning"></a>Nastavení Foodee pro zřizování

Než nakonfigurujete Foodee pro Automatické zřizování uživatelů pomocí Azure AD, musíte povolit systém pro zřizování SCIM (Domain Identity Management) v Foodee.

1. Přihlaste se k [Foodee](https://www.food.ee/login/)a pak vyberte ID tenanta.

    :::image type="content" source="media/Foodee-provisioning-tutorial/tenant.png" alt-text="Snímek obrazovky hlavní nabídky portálu Foodee Enterprise V nabídce se zobrazí zástupný symbol ID tenanta." border="false":::

1. V části **Podnikový portál** vyberte **jednotné přihlašování**.

    ![Nabídka Foodee Enterprise Portal v levém podokně](media/Foodee-provisioning-tutorial/scim.png)

1. Zkopírujte hodnotu do pole **token rozhraní API** pro pozdější použití. Zadáte ho do pole **tajný token** na kartě **zřizování** aplikace Foodee ve Azure Portal.

    :::image type="content" source="media/Foodee-provisioning-tutorial/token.png" alt-text="Snímek obrazovky stránky na portálu Foodee Enterprise Hodnota tokenu P I je zvýrazněna." border="false":::

## <a name="add-foodee-from-the-gallery"></a>Přidání Foodee z Galerie

Pokud chcete nakonfigurovat Foodee pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat Foodee z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

Pokud chcete přidat Foodee z Galerie aplikací Azure AD, udělejte toto:

1. V [Azure Portal](https://portal.azure.com)v levém podokně vyberte možnost **Azure Active Directory**.

    ![Příkaz Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace**  >  **všechny aplikace**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, vyberte v horní části podokna možnost **Nová aplikace** .

    ![Tlačítko Nová aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **Foodee** , v podokně výsledků vyberte **Foodee** a pak vyberte **Přidat** , aby se aplikace přidala.

    ![Foodee v seznamu výsledků](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Konfigurace automatického zřizování uživatelů na Foodee 

V této části nakonfigurujete službu zřizování Azure AD tak, aby vytvořila, aktualizovala a zakázala uživatele nebo skupiny v Foodee na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování založené na SAML pro Foodee podle pokynů v [kurzu Foodee jednotného přihlašování](Foodee-tutorial.md). Můžete nakonfigurovat jednotné přihlašování nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

Nakonfigurujte Automatické zřizování uživatelů pro Foodee ve službě Azure AD pomocí následujícího postupu:

1. V [Azure Portal](https://portal.azure.com)vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu **aplikace** vyberte **Foodee**.

    ![Odkaz Foodee v seznamu aplikací](common/all-applications.png)

1. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

1. V rozevíracím seznamu **režim zřizování** vyberte **automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

1. V části **přihlašovací údaje správce** postupujte takto:

   a. Do pole **Adresa URL tenanta** zadejte hodnotu **https: \/ /concierge.Food.ee/SCIM/v2** , kterou jste získali dříve.

   b. Do pole **token tajného klíče** zadejte hodnotu **tokenu rozhraní API** , kterou jste získali dříve.
   
   c. Pokud chcete zajistit, aby se služba Azure AD mohla připojit k Foodee, vyberte **Test připojení**. Pokud se připojení nepovede, ujistěte se, že má váš účet Foodee oprávnění správce, a zkuste to znovu.

    ![Odkaz testovat připojení](common/provisioning-testconnection-tenanturltoken.png)

1. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a pak zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![Textové pole pro oznamovací E-mail](common/provisioning-notification-email.png)

1. Vyberte **Uložit**.

1. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Foodee**.

    :::image type="content" source="media/Foodee-provisioning-tutorial/usermapping.png" alt-text="Snímek obrazovky oddílu mapování V části název je zvýrazněná položka synchronizovat Azure Active Directory uživatelů do Foodee." border="false":::

1. V části **mapování atributů** zkontrolujte atributy uživatelů synchronizované z Azure AD do Foodee. Atributy, které jsou vybrány jako **odpovídající** vlastnosti, se používají k porovnání *uživatelských účtů* v Foodee pro operace aktualizace. 

    :::image type="content" source="media/Foodee-provisioning-tutorial/userattribute.png" alt-text="Snímek obrazovky se stránkou mapování atributů. V tabulce jsou uvedeny atributy Azure Active Directory a Foodee a Priorita porovnání." border="false":::

1. Změny potvrďte tak, že vyberete **Uložit**.
1. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do Foodee**.

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupmapping.png" alt-text="Snímek obrazovky oddílu mapování V části název se zvýrazní synchronizace skupin Azure Active Directory Foodee." border="false":::

1. V části **mapování atributů** zkontrolujte atributy uživatelů synchronizované z Azure AD do Foodee. Atributy, které jsou vybrány jako **odpovídající** vlastnosti, se používají ke spárování *skupinových účtů* v Foodee pro operace aktualizace.

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupattribute.png" alt-text="Snímek obrazovky se stránkou mapování atributů. Tabulka obsahuje seznam atributů Azure Active Directory, atributů Foodee a priority pro porovnání." border="false":::

1. Změny potvrďte tak, že vyberete **Uložit**.
1. Nakonfigurujte filtry oborů. Informace o tom, jak postupovat, najdete v [kurzu o filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pokud chcete povolit službu Azure AD Provisioning pro Foodee, v části **Nastavení** změňte **stav zřizování** na **zapnuto**.

    ![Přepínač stavu zřizování](common/provisioning-toggle-on.png)

1. V části **Nastavení** v rozevíracím seznamu **obor** definujte uživatele nebo skupiny, které chcete zřídit pro Foodee.

    ![Rozevírací seznam rozsah zřizování](common/provisioning-scope.png)

1. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Tlačítko pro uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Předchozí operace spustí počáteční synchronizaci uživatelů nebo skupin, které jste definovali v rozevíracím seznamu **Rozsah** . Počáteční synchronizace trvá déle než následné synchronizace. Další informace najdete v tématu [Jak dlouho bude trvat zřizování uživatelů?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pomocí oddílu **aktuální stav** můžete monitorovat průběh a postupovat podle odkazů na sestavu aktivity zřizování. Tato sestava popisuje všechny akce, které provádí služba zřizování Azure AD v Foodee. Další informace najdete v tématu o [kontrole stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Pokud si chcete přečíst protokoly zřizování Azure AD, přečtěte si téma [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

---
title: 'Kurz: Konfigurace automatického zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se, jak nakonfigurovat Azure Active Directory pro Automatické zřizování a rušení uživatelských účtů, které se budou využívat.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 13ae960f5d259314f00f8f09b2999a36c0919bc5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94353709"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Kurz: Konfigurace funkce pro Automatické zřizování uživatelů

V tomto článku se dozvíte, jak nakonfigurovat Azure Active Directory (Azure AD) pro Automatické zřizování a rušení zřizování uživatelů nebo skupin.

> [!NOTE]
> Tento článek popisuje konektor, který je založený na službě zřizování uživatelů Azure AD. Důležité informace o této službě a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů při SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je momentálně ve verzi Preview. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Předpoklady

Scénář, který je popsaný v tomto článku, předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Klient přestroje](https://harness.io/pricing/)
* Uživatelský účet ve více kabelech s oprávněními *správce*

## <a name="assign-users-to-harness"></a>Přiřazení uživatelů ke kabelům

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé nebo skupiny, které jsou přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, rozhodněte se, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup ke svým kabelům. Tyto uživatele nebo skupiny pak můžete přiřadit podle pokynů v tématu [přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-harness"></a>Důležité tipy pro přiřazování uživatelů ke kabelům

* Pro otestování automatické konfigurace zřizování uživatelů doporučujeme přiřadit jediného uživatele služby Azure AD. Další uživatele nebo skupiny se dají přiřadit později.

* Když přiřadíte uživatele k používání, musíte v dialogovém okně **přiřazení** vybrat libovolnou platnou roli specifickou pro aplikaci (je-li k dispozici). Uživatelé s *výchozí rolí přístupu* se z zřizování vylučují.

## <a name="set-up-harness-for-provisioning"></a>Nastavení rozpracovaného svazku pro zřizování

1. Přihlaste se ke [konzole správce](https://app.harness.io/#/login)aplikace a pak přejděte na správu **nepřetržitého**  >  **přístupu** do zabezpečení.

    ![Konzola správce ve více kabelech](media/harness-provisioning-tutorial/admin.png)

1. Vyberte možnost **klíče rozhraní API**.

    ![Odkaz na klíče rozhraní API pro převyužívání](media/harness-provisioning-tutorial/apikeys.png)

1. Vyberte **Přidat klíč rozhraní API**. 

    ![Přidat odkaz na klíč rozhraní API](media/harness-provisioning-tutorial/addkey.png)

1. V podokně **Přidat klíč rozhraní API** udělejte toto:

    ![Podokno přidat klíč rozhraní API](media/harness-provisioning-tutorial/title.png)
   
   a. Do pole **název** zadejte název klíče.  
   b. V rozevíracím seznamu **oprávnění zděděných z** vyberte možnost. 
   
1. Vyberte **Odeslat**.

1. Zkopírujte **klíč** pro pozdější použití v tomto kurzu.

    ![Vytvoření tokenu pro vytvoření svazku](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Přidání svazku z Galerie

Než začnete konfigurovat funkci pro Automatické zřizování uživatelů pomocí Azure AD, musíte do svého seznamu spravovaných aplikací pro SaaS přidat z Galerie aplikací službu Azure AD kabel.

1. V [Azure Portal](https://portal.azure.com)v levém podokně vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace**  >  **všechny aplikace**.

    ![Odkaz všechny aplikace](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

1. Do vyhledávacího **pole zadejte příkaz** Get, v seznamu výsledků vyberte možnost předaný **svazek** a potom přidejte aplikaci kliknutím na tlačítko **Přidat** .

    ![Využití svazku v seznamu výsledků](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Konfigurace automatického zřizování uživatelů ke využívání 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin ve svazku na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout, že povolíte jednotné přihlašování založené na SAML na základě pokynů v [kurzu jednotného přihlašování](./harness-tutorial.md)pomocí tohoto stroje. Můžete nakonfigurovat jednotné přihlašování nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

> [!NOTE]
> Další informace o koncovém bodu SCIM najdete v článku [klíče rozhraní API](https://docs.harness.io/article/smloyragsm-api-keys) pro povýšení.

Pokud chcete nakonfigurovat automatické zřizování uživatelů pro funkci ve službě Azure AD, udělejte toto:

1. V [Azure Portal](https://portal.azure.com)vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikace vyberte možnost **převyužívat**.

    ![Odkaz na svazek v seznamu aplikací](common/all-applications.png)

1. Vyberte **zřizování**.

    ![Tlačítko zřizování](common/provisioning.png)

1. V rozevíracím seznamu **režim zřizování** vyberte **automaticky**.

    ![Rozevírací seznam "režim zřizování"](common/provisioning-automatic.png)

1. V části **přihlašovací údaje správce** postupujte takto:

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. Do pole **Adresa URL tenanta** zadejte **`https://app.harness.io/gateway/api/scim/account/<your_harness_account_ID>`** . Pokud jste se přihlásili ke službě, můžete získat ID svého účtu z adresy URL v prohlížeči.
   b. Do pole **token tajného klíče** zadejte hodnotu ověřovacího tokenu SCIM, kterou jste uložili v kroku 6 části nastavení "nastavení svazku pro zřizování".  
   c. Vyberte **Test připojení** , aby se služba Azure AD mohla připojit k tomuto svazku. Pokud se připojení nepovede, ujistěte se, že váš účet správce má oprávnění *správce* , a zkuste to znovu.

1. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a pak zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![Pole "oznamovací E-mail"](common/provisioning-notification-email.png)

1. Vyberte **Uložit**.

1. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele**.

    ![Odkaz "synchronizace Azure Active Directory uživatelů na předaný svazek"](media/harness-provisioning-tutorial/usermappings.png)

1. V části **mapování atributů** zkontrolujte atributy uživatelů, které jsou synchronizované z Azure AD, a využijte je. Atributy vybrané jako *párování* se používají ke spárování uživatelských účtů v nástroji pro operace aktualizace. Vyberte **Uložit** a potvrďte všechny změny.

    ![Podokno mapování atributů uživatele](media/harness-provisioning-tutorial/userattributes.png)

1. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny a využijte**.

    ![Odkaz "synchronizace skupin Azure Active Directory ke svazku"](media/harness-provisioning-tutorial/groupmappings.png)

1. V části **mapování atributů** zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD, a využijte je. Atributy vybrané jako *odpovídající* vlastnosti se používají ke spárování skupin ve svazku pro operace aktualizace. Vyberte **Uložit** a potvrďte všechny změny.

    ![Podokno mapování atributů pro skupinu kabelů](media/harness-provisioning-tutorial/groupattributes.png)

1. Pokud chcete nakonfigurovat filtry oborů, přečtěte si téma [zřizování aplikace na základě atributů s filtry oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. V části **Nastavení** povolte službu zřizování Azure AD pro práci s přepínačem **stav zřizování** přepnout na **zapnuto**.

    ![Přepínač stavu zřizování přepnout na zapnuto](common/provisioning-toggle-on.png)

1. V části **Nastavení** v rozevíracím seznamu **obor** vyberte, jakým způsobem chcete synchronizovat uživatele nebo skupiny, které chcete využívat.

    ![Rozsah zřizování](common/provisioning-scope.png)

1. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Tlačítko pro uložení zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci uživatelů nebo skupin, které zřizování provádíte. Počáteční synchronizace trvá déle než později. Synchronizace probíhá přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Chcete-li sledovat průběh, přejít do části **Podrobnosti o synchronizaci** . Můžete také sledovat odkazy na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na platformě.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [Sestava automatického zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
---
title: 'Kurz: Konfigurace foodee pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty společnosti Foodee.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 2195056ec66550063aba5ce5e2b977b51a6dc5e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057802"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Kurz: Konfigurace foodee pro automatické zřizování uživatelů

Tento článek ukazuje, jak nakonfigurovat Azure Active Directory (Azure AD) v Foodee a Azure AD automaticky zřídit nebo deprovision uživatelů nebo skupin foodee.

> [!NOTE]
> Tento článek popisuje konektor, který je postavený na nad službou Azure AD User Provisioning. Informace o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Preview. Další informace o funkci používání podmínek Azure pro funkce náhledu najdete v [doplňkových podmínkách použití pro microsoft azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že jste splnili následující požadavky:

* Klient Azure AD
* [Nájemce Foodee](https://www.food.ee/about/)
* Uživatelský účet v Foodee s oprávněními správce

## <a name="assign-users-to-foodee"></a>Přiřazení uživatelů k Foodee 

Azure AD používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů jsou synchronizováni pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Foodee. Po provedení tohoto určení můžete přiřadit tyto uživatele nebo skupiny společnosti Foodee podle pokynů v části [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-foodee"></a>Důležité tipy pro přiřazení uživatelů k Foodee 

Při přiřazování uživatelů mějte na paměti následující tipy:

* Doporučujeme přiřadit jenom jednoho uživatele Azure AD foodee k testování konfigurace automatického zřizování uživatelů. Později můžete přiřadit další uživatele nebo skupiny.

* Když přiřazujete uživatele k foodee, vyberte v podokně **Přiřazení** libovolnou platnou roli specifickou pro aplikaci, pokud je k dispozici. Uživatelé, kteří mají *roli výchozí přístup* jsou vyloučeni z zřizování.

## <a name="set-up-foodee-for-provisioning"></a>Nastavit Foodee pro zřizování

Před konfigurací Foodee pro automatické zřizování uživatelů pomocí Azure AD, je třeba povolit system for cross-domain Identity Management (SCIM) zřizování v Foodee.

1. Přihlaste se k [Foodee](https://www.food.ee/login/)a vyberte ID klienta.

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. V části **Portál enterprise**vyberte Možnost **Jednotné přihlášení**.

    ![Nabídka levého podokna portálu Foodee Enterprise Portal](media/Foodee-provisioning-tutorial/scim.png)

1. Zkopírujte hodnotu v poli **tokenrozhraní API** pro pozdější použití. Zadejte ji do pole **Tajný token** na kartě **Zřizování** vaší aplikace Foodee na webu Azure Portal.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>Přidat Foodee z galerie

Chcete-li nakonfigurovat Foodee pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat Foodee z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

Pokud chcete přidat Foodee z galerie aplikací Azure AD, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com)v levém podokně vyberte **Azure Active Directory**.

    ![Příkaz Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace** > **Všechny aplikace**.

    ![Podokno Podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, vyberte v horní části podokna možnost **Nová aplikace.**

    ![Tlačítko Nová aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **Foodee**, vyberte **Foodee** v podokně výsledků a pak vyberte **Přidat,** chcete-li přidat aplikaci.

    ![Foodee v seznamu výsledků](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Konfigurace automatického zřizování uživatelů pro Foodee 

V této části nakonfigurujete službu zřizování Azure AD tak, aby vytvářela, aktualizovala a zakazovala uživatele nebo skupiny v Foodee na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit saml-založené jednotné přihlášení pro Foodee podle pokynů v [Foodee jednotné přihlášení tutorial](Foodee-tutorial.md). Můžete nakonfigurovat jednotné přihlašování nezávisle na automatickézřivací službě uživatelů, i když se tyto dvě funkce vzájemně doplňují.

Nakonfigurujte automatické zřizování uživatelů pro Foodee ve službě Azure AD následujícím způsobem:

1. Na [portálu Azure](https://portal.azure.com)vyberte **Podnikové aplikace** > **Všechny aplikace**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu **Aplikace** vyberte **položku Foodee**.

    ![Odkaz Foodee v seznamu Aplikace](common/all-applications.png)

1. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

1. V rozevíracím seznamu **Režim zřizování** vyberte **Možnost Automaticky**.

    ![Karta Zřizování](common/provisioning-automatic.png)

1. V části **Pověření správce**postupujte takto:

   a. Do pole **Adresa URL klienta** zadejte **hodnotu https:\//concierge.food.ee/scim/v2,** kterou jste načetli dříve.

   b. Do pole **Tajný token** zadejte hodnotu **tokenu rozhraní API,** kterou jste načetli dříve.
   
   c. Chcete-li zajistit, aby se Azure AD mohl připojit k Foodee, vyberte **testovat připojení**. Pokud se připojení nezdaří, ujistěte se, že váš účet Foodee má oprávnění správce, a akci opakujte.

    ![Odkaz Testovací připojení](common/provisioning-testconnection-tenanturltoken.png)

1. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě.**

    ![Textové pole E-mail s oznámením](common/provisioning-notification-email.png)

1. Vyberte **Uložit**.

1. V části **Mapování**vyberte **Synchronizovat uživatele služby Azure Active Directory s foodee**.

    ![Mapování uživatelů foodee](media/Foodee-provisioning-tutorial/usermapping.png)

1. V části **Mapování atributů**zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Foodee. Atributy, které jsou vybrány jako **odpovídající** vlastnosti se používají k porovnání *uživatelských účtů* v Foodee pro operace aktualizace. 

    ![Mapování uživatelů foodee](media/Foodee-provisioning-tutorial/userattribute.png)

1. Chcete-li změny potvrdit, vyberte **uložit**.
1. V části **Mapování**vyberte **Synchronizovat skupiny Služby Active Directory Azure s foodee**.

    ![Mapování uživatelů foodee](media/Foodee-provisioning-tutorial/groupmapping.png)

1. V části **Mapování atributů**zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Foodee. Atributy, které jsou vybrány jako **odpovídající** vlastnosti se používají k porovnání *účtů skupiny* v Foodee pro operace aktualizace.

    ![Mapování uživatelů foodee](media/Foodee-provisioning-tutorial/groupattribute.png)

1. Chcete-li změny potvrdit, vyberte **uložit**.
1. Nakonfigurujte filtry oborů. Postup naleznete v pokynech v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Chcete-li povolit službu zřizování Azure AD pro Foodee, v části **Nastavení** změňte **stav zřizování** na **Zapnuto**.

    ![Přepínač stavu zřizování](common/provisioning-toggle-on.png)

1. V části **Nastavení**definujte v rozevíracím seznamu **Obor** uživatele nebo skupiny, které chcete zřídit společnosti Foodee.

    ![Rozevírací seznam Oboru zřizování](common/provisioning-scope.png)

1. Až budete připraveni k zřízení, vyberte **Uložit**.

    ![Tlačítko Uložit konfiguraci zřizování](common/provisioning-configuration-save.png)

Předchozí operace spustí počáteční synchronizaci uživatelů nebo skupin, které jste definovali v rozevíracím seznamu **Obor.** Počáteční synchronizace trvá déle než následné synchronizace. Další informace naleznete v tématu [Jak dlouho bude trvat zřizování uživatelů?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Část Aktuální **stav** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit y zřizování. Sestava popisuje všechny akce, které jsou prováděny službou zřizování Azure AD na Foodee. Další informace naleznete [v tématu Kontrola stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Chcete-li číst protokoly zřizování Azure AD, najdete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

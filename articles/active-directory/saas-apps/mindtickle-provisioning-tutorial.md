---
title: 'Kurz: Konfigurace mindtickle pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřaštila a zřaštila uživatelské účty do MindTickle.
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
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: 6ae7b6ac9c6feb539e4da3627ebbabd3c5b628fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061214"
---
# <a name="tutorial-configure-mindtickle-for-automatic-user-provisioning"></a>Kurz: Konfigurace MindTickle pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v MindTickle a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-zřizování uživatelů a/nebo skupin mindtickle.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Tenant MindTickle](https://www.mindtickle.com/)
* Uživatelský účet v MindTickle s oprávněními správce.

## <a name="assigning-users-to-mindtickle"></a>Přiřazení uživatelů k MindTickle

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k MindTickle. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny MindTickle podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mindtickle"></a>Důležité tipy pro přiřazení uživatelů k MindTickle

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a MindTickle otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k MindTickle, musíte vybrat všechny platné role specifické pro aplikaci (pokud jsou k dispozici) v dialogovém okně přiřazení. Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="setup-mindtickle-for-provisioning"></a>Nastavení MindTickle pro zřizování

Před konfigurací MindTickle pro automatické zřizování uživatelů s Azure AD, budete muset povolit zřizování SCIM na MindTickle.


1.  Oslovte [tým podpory MindTickle](mailto:help@mindtickle.com) získat token JWT potřebné ke konfiguraci zřizování SCIM.


## <a name="add-mindtickle-from-the-gallery"></a>Přidat MindTickle z galerie

Chcete-li nakonfigurovat MindTickle pro automatické zřizování uživatelů pomocí Azure AD, je potřeba přidat MindTickle z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat MindTickle z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **MindTickle**, vyberte **MindTickle** v panelu výsledků a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![MindTickle v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mindtickle"></a>Konfigurace automatického zřizování uživatelů na MindTickle 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v MindTickle na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit saml-založené jednotné přihlašování pro MindTickle , podle pokynů uvedených v [MindTickle jednotné přihlášení tutorial](mindtickle-tutorial.md). Jednotné přihlašování lze konfigurovat nezávisle na automatickézřivací službě uživatelů, i když tyto dvě funkce se vzájemně doplňují

### <a name="to-configure-automatic-user-provisioning-for-mindtickle-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro MindTickle ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Položku MindTickle**.

    ![Odkaz MindTickle v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://admin.mindtickle.com/scim` zadejte adresu **URL klienta**. Zadejte hodnotu **tokenu JWT** načtenou dříve v textovém poli V tajném tokenu, zadejte hodnotu **tokenu JWT,** která byla zadána týmem podpory MindTickle. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k mým zásadám. Pokud se připojení nezdaří, ujistěte se, že váš účet MindTickle má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **synchronizovat uživatele služby Azure Active Directory s položkou MindTickle**.

    ![MindTickle mapování uživatelů](media/mindtickle-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na MindTickle v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v MindTickle pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![MindTickle mapování uživatelů](media/mindtickle-provisioning-tutorial/userattribute.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro MindTickle, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit MindTickle výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace. Další informace o tom, jak dlouho bude trvat uživatelům nebo skupinám, naleznete v tématu [Jak dlouho bude trvat zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users). 

Část Aktuální **stav** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na MindTickle. Další informace naleznete [v tématu Kontrola stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Chcete-li číst protokoly zřizování Azure AD, najdete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

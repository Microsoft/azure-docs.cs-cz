---
title: 'Kurz: Konfigurace visitly pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty na Visitly.
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
ms.openlocfilehash: 73cc1a58689db7902843f222aa4874a5e188be44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063147"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Kurz: Konfigurace Visitly pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které provádíte v Visitly a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a deprovision uživatelů nebo skupin na Visitly.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací masu jako služby (SaaS) pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce náhledu najdete v [tématu Doplňkové podmínky použití pro náhledy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Navštívený nájemce](https://www.visitly.io/pricing/)
* Uživatelský účet v Visitly s oprávněními správce

## <a name="assign-users-to-visitly"></a>Přiřazení uživatelů k Návštěvě 

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů jsou synchronizováni pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů se rozhodněte, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Visitly. Pak přiřadit tyto uživatele nebo skupiny Visitly podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Důležité tipy pro přiřazení uživatelů k Visitly 

* Doporučujeme přiřadit jednoho uživatele Azure AD k Visitly k testování konfigurace automatického zřizování uživatelů. Další uživatelé nebo skupiny mohou být přiřazeny později.

* Při přiřazení uživatele k Návštěvě je nutné v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí Výchozí přístup jsou z zřizování vyloučeni.

## <a name="set-up-visitly-for-provisioning"></a>Nastavit Visitly pro zřizování

Než nakonfigurujete Visitly pro automatické zřizování uživatelů pomocí Azure AD, musíte povolit system for cross-domain Identity Management (SCIM) zřizování na Visitly.

1. Přihlaste se do [Visitly](https://app.visitly.io/login). Vyberte**synchronizaci** **hostitelů** > integrace .

    ![Synchronizace hostitele](media/Visitly-provisioning-tutorial/login.png)

2. Vyberte část **Azure AD.**

    ![Oddíl Azure AD](media/Visitly-provisioning-tutorial/integration.png)

3. Zkopírujte **klíč rozhraní API**. Tyto hodnoty se zadávají do pole **Tajný token** na kartě **Zřizování** vaší aplikace Visitly na webu Azure Portal.

    ![Klíč rozhraní API](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Přidat Visitly z galerie

Chcete-li nakonfigurovat Visitly pro automatické zřizování uživatelů pomocí Azure AD, přidejte Visitly z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

Pokud chcete visitly přidat z galerie aplikací Azure AD, postupujte takto.

1. Na [webu Azure Portal](https://portal.azure.com)v levém navigačním podokně vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Visitly**, v panelu výsledků vyberte **Visitly** a pak vyberte **Přidat,** chcete-li přidat aplikaci.

    ![Návštěva v seznamu výsledků](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Konfigurace automatického zřizování uživatelů na Visitly 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Visitly na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Chcete-li povolit jednotné přihlašování na saml pro Visitly, postupujte podle pokynů v [visitly jednotné přihlášení kurzu](Visitly-tutorial.md). Jednotné přihlašování lze konfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce se vzájemně doplňují.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Visitly ve službě Azure AD

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace** > **Všechny aplikace**.

    ![Všechny aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Visitly**.

    ![Odkaz Navštívený v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Režim zřizování nastavený na automatické](common/provisioning-automatic.png)

5. V části Přihlašovací údaje správce `https://api.visitly.io/v1/usersync/SCIM` zadejte hodnoty klíče a **klíče rozhraní API** načtené dříve v adrese URL **klienta** a **tajný token**. Vyberte **Testovat připojení** a ujistěte se, že Azure AD můžete připojit k Visitly. Pokud se připojení nezdaří, ujistěte se, že váš účet Visitly má oprávnění správce, a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování. Zaškrtněte **políčko Odeslat e-mailové oznámení, když dojde k chybě.**

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **Mapování** vyberte **synchronizovat uživatele služby Azure Active Directory s návštěvou**.

    ![Mapování uživatelů navštívené](media/visitly-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Visitly v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v Visitly pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte **uložit.**

    ![Atributy uživatele visitly](media/visitly-provisioning-tutorial/userattribute.png)

10. Chcete-li konfigurovat filtry oborů, postupujte podle pokynů v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Chcete-li povolit službu zřizování Azure AD pro Visitly, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit visitly výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

13. Až budete připraveni k zřízení, vyberte **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace. Další informace o tom, jak dlouho trvá uživatelům nebo skupinám k zajištění, naleznete v tématu [Jak dlouho bude trvat zřizování uživatelů?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Část Aktuální **stav** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Visitly. Další informace naleznete [v tématu Kontrola stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Chcete-li číst protokoly zřizování Azure AD, najdete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

Visitly nepodporuje tvrdé odstranění. Vše je pouze obnovitelné.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

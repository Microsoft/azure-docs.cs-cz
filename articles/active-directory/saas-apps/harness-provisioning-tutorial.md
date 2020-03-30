---
title: 'Kurz: Konfigurace funkce Harness pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty pro využití.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 518d86fff04a23f1c1e63c44c53485b99f30637d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057826"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Kurz: Konfigurace funkce Harness pro automatické zřizování uživatelů

V tomto článku se dozvíte, jak nakonfigurovat Azure Active Directory (Azure AD) automaticky zřídit a zrušením zřizování uživatelů nebo skupin pro využití.

> [!NOTE]
> Tento článek popisuje konektor, který je postavený na nad službou zřizování uživatelů Azure AD. Důležité informace o této službě a odpovědi na často kladené otázky naleznete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Preview. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto článku předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Tenant a Harness](https://harness.io/pricing/)
* Uživatelský účet v systému Harness s *oprávněními správce*

## <a name="assign-users-to-harness"></a>Přiřazení uživatelů k postroji

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů jsou synchronizováni pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů se rozhodněte, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k využití. Tyto uživatele nebo skupiny pak můžete přiřadit k aplikaci Harness podle pokynů v části [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-harness"></a>Důležité tipy pro přiřazení uživatelů k systému Harness

* Doporučujeme přiřadit jednoho uživatele Azure AD k využití k testování konfigurace automatického zřizování uživatelů. Další uživatelé nebo skupiny mohou být přiřazeny později.

* Když přiřadíte uživatele k aplikaci Harness, musíte v dialogovém okně **Přiřazení** vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí *Výchozí přístup* jsou z zřizování vyloučeni.

## <a name="set-up-harness-for-provisioning"></a>Nastavení postroje pro zřizování

1. Přihlaste se ke [konzoli Harness Admin Console](https://app.harness.io/#/login)a přejděte na **službu Continuous Security** > **Access Management**.

    ![Konzola pro správu funkce Harness](media/harness-provisioning-tutorial/admin.png)

1. Vyberte **klávesy rozhraní API**.

    ![Odkaz Klíče rozhraní API pro využití](media/harness-provisioning-tutorial/apikeys.png)

1. Vyberte **Přidat klíč rozhraní API**. 

    ![Odkaz Přidat klíč rozhraní API](media/harness-provisioning-tutorial/addkey.png)

1. V podokně **Přidat klíč rozhraní API** postupujte takto:

    ![Podokno Přidat klíč rozhraní Api](media/harness-provisioning-tutorial/title.png)
   
   a. V poli **Název** zadejte název klíče.  
   b. V **rozevíracím seznamu Oprávnění zděděná z** vyberte možnost. 
   
1. Vyberte **Odeslat**.

1. Zkopírujte **klíč** pro pozdější použití v tomto kurzu.

    ![Vytvořit token pro vytvoření postroje](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Přidat postroj z galerie

Než nakonfigurujete službu Harness pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat využití z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

1. Na [webu Azure Portal](https://portal.azure.com)v levém podokně vyberte **Azure Active Directory**.

    ![Tlačítko "Služba Azure Active Directory"](common/select-azuread.png)

1. Vyberte **podnikové aplikace** > **Všechny aplikace**.

    ![Odkaz "Všechny aplikace"](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko "Nová aplikace"](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **postroj**, v seznamu výsledků vyberte **Postroj** a pak vyberte tlačítko **Přidat** a přidejte aplikaci.

    ![Postroj v seznamu výsledků](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Konfigurace automatického zřizování uživatelů pro využití 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v systému Harness na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování pro službu Harness založenou na saml podle pokynů v [kurzu jednotného přihlašování k postroji](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial). Můžete nakonfigurovat jednotné přihlašování nezávisle na automatickézřivací službě uživatelů, i když se tyto dvě funkce vzájemně doplňují.

> [!NOTE]
> Další informace o koncovém bodu Harness SCIM najdete v článku [Klíče rozhraní API](https://docs.harness.io/article/smloyragsm-api-keys) pro využití.

Chcete-li nakonfigurovat automatické zřizování uživatelů pro službu Harness ve službě Azure AD, postupujte takto:

1. Na [portálu Azure](https://portal.azure.com)vyberte **Podnikové aplikace** > **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **Postroj**.

    ![Odkaz Postroj v seznamu aplikací](common/all-applications.png)

1. Vyberte **Zřizování**.

    ![Tlačítko Zřizování](common/provisioning.png)

1. V rozevíracím seznamu **Režim zřizování** vyberte **Možnost Automaticky**.

    ![Rozevírací seznam "Režim zřizování"](common/provisioning-automatic.png)

1. V části **Pověření správce**postupujte takto:

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. Do pole Adresa URL **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`** **klienta** zadejte .  
   b. Do pole **Tajný token** zadejte hodnotu tokenu ověřování SCIM, kterou jste uložili v kroku 6 části Nastavit postroj pro zřizování.  
   c. Vyberte **Testovat připojení,** abyste zajistili, že se Azure AD může připojit k využití. Pokud se připojení nezdaří, ujistěte se, že váš účet Harness má oprávnění *správce,* a akci opakujte.

1. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě.**

    ![Pole "E-mail s oznámením"](common/provisioning-notification-email.png)

1. Vyberte **Uložit**.

1. V části **Mapování**vyberte **možnost Synchronizovat uživatele služby Azure Active Directory k využití**.

    ![Využití odkazu Synchronizovat uživatele služby Azure Active Directory k využití](media/harness-provisioning-tutorial/usermappings.png)

1. V části **Mapování atributů**zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do využití. Atributy vybrané jako *Párování* se používají k porovnání uživatelských účtů v aplikaci Harness pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte **uložit.**

    ![Podokno "Mapování atributů" uživatele](media/harness-provisioning-tutorial/userattributes.png)

1. V části **Mapování**vyberte **synchronizovat skupiny služby Azure Active Directory, které chcete využít**.

    ![Využití odkazu Synchronizovat skupiny služby Azure Active Directory k využití](media/harness-provisioning-tutorial/groupmappings.png)

1. V části **Mapování atributů**zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do využití. Atributy vybrané jako *odpovídající* vlastnosti se používají tak, aby odpovídaly skupinám v aplikaci Harness pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte **uložit.**

    ![Podokno Skupina postrojů "Mapování atributů"](media/harness-provisioning-tutorial/groupattributes.png)

1. Pokud chcete nakonfigurovat filtry oborů, přečtěte si informace [o zřizování aplikací založených na atributech pomocí filtrů oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Chcete-li povolit zřizovací službu Azure AD pro využití, přepněte přepínač **Stav zřizování** **na Zapnuto**. **Settings**

    ![Přepínač stavu zřizování přepnutý na "Zapnuto"](common/provisioning-toggle-on.png)

1. V části **Nastavení**vyberte v rozevíracím seznamu **Obor** způsob synchronizace uživatelů nebo skupin, které zřizujete, do funkce Harness.

    ![Obor zřizování](common/provisioning-scope.png)

1. Až budete připraveni k zřízení, vyberte **Uložit**.

    ![Tlačítko Uložit zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci uživatelů nebo skupin, které zřizujete. Počáteční synchronizace trvá déle než pozdější. Synchronizace dochází přibližně každých 40 minut, tak dlouho, dokud je spuštěna služba zřizování Azure AD. Chcete-li sledovat průběh, přejděte do části **Podrobnosti synchronizace.** Můžete také sledovat odkazy na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v systému Harness.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete v [tématu Sestava automatického zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

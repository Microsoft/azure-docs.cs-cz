---
title: 'Kurz: Zřizování uživatelů pro GitHub – Azure AD'
description: Zjistěte, jak nakonfigurovat Službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty na GitHub.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82f7252f2d9cdd2c54fae593d8463bfe84bd6ce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057647"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Kurz: Konfigurace GitHubu pro automatické zřizování uživatelů

Cílem tohoto kurzu je ukázat kroky, které je potřeba provést v GitHuba a Azure AD automaticky zřídit a de-zřídit uživatelské účty z Azure AD na GitHub.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

* Klient adresáře Azure Active Directory
* Organizace GitHub vytvořená v [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), která vyžaduje [fakturační plán GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)
* Uživatelský účet na GitHubu s oprávněními správce pro organizaci
* Ujistěte se, že byl vaší organizaci poskytnut přístup oAuth, jak je popsáno [zde.](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)

> [!NOTE]
> Integrace zřizování Azure AD závisí na [rozhraní API GitHub SCIM](https://developer.github.com/v3/scim/), které je dostupné zákazníkům [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) v [fakturačním plánu GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations).

## <a name="assigning-users-to-github"></a>Přiřazení uživatelů ke GitHubu

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů jsou synchronizováni pouze uživatelé a skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD. 

Před konfigurací a povolením zřizovací služby se musíte rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci GitHub. Jakmile se rozhodnete, můžete tyto uživatele přiřadit k aplikaci GitHub podle pokynů zde:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Důležité tipy pro přiřazování uživatelů na GitHub

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k GitHub u testování konfigurace zřizování. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele ke GitHubu musíte v dialogovém okně úlohy vybrat roli **uživatele** nebo jinou platnou roli specifickou pro aplikaci (pokud je k dispozici). **Výchozí přístup** role nefunguje pro zřizování a tito uživatelé jsou přeskočeny.

## <a name="configuring-user-provisioning-to-github"></a>Konfigurace zřizování uživatelů na GitHubu

Tato část vás provede připojením azure ad k rozhraní API pro zřizování uživatelských účtů GitHubu a konfigurací zřizovací služby pro vytváření, aktualizaci a zakázání přiřazených uživatelských účtů na GitHubu na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování na základě SAML pro GitHub podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce kompliment navzájem.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů na GitHubu ve službě Azure AD

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Podnikové aplikace > všechny aplikace.**

2. Pokud jste již nakonfigurovali GitHub pro jednotné přihlašování, vyhledejte svou instanci GitHubu pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **GitHub** v galerii aplikací. Z výsledků hledání vyberte GitHub a přidejte ho do seznamu aplikací.

3. Vyberte svou instanci GitHubu a pak vyberte kartu **Zřizování.**

4. Nastavte **režim zřizování** na **automatické**.

    ![GitHub Zřizování](./media/github-provisioning-tutorial/GitHub1.png)

5. V části **Pověření správce** klikněte na **autorizovat**. Tato operace otevře dialogové okno autorizace GitHub v novém okně prohlížeče. Všimněte si, že je třeba zajistit, že jste schváleni k autorizaci přístupu. Postupujte podle pokynů popsaných [zde](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization).

6. V novém okně se přihlaste ke GitHubu pomocí účtu správce. V dialogovém okně výsledné autorizace vyberte tým GitHubu, pro který chcete povolit zřizování, a pak vyberte **Autorizovat**. Po dokončení se vraťte na portál Azure a dokončete konfiguraci zřizování.

    ![Dialogové okno Autorizace](./media/github-provisioning-tutorial/GitHub2.png)

7. Na webu Azure Portal zadejte **adresu URL klienta** a klikněte na **Testovat připojení,** abyste zajistili, že se Azure AD může připojit k vaší aplikaci GitHub. Pokud se připojení nezdaří, ujistěte se, že váš účet GitHub má oprávnění správce a **klient url** je `https://api.github.com/scim/v2/organizations/<Organization_name>`zadán správně, pak zkuste "Autorizovat" krok znovu (můžete vytvořit adresu URL **klienta** podle pravidla: , můžete najít své organizace pod účtem GitHub: Organizace **nastavení** > **).**

    ![Dialogové okno Autorizace](./media/github-provisioning-tutorial/GitHub3.png)

8. Do pole **E-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko Odeslat e-mailové oznámení, když dojde k chybě.

9. Klikněte na **Uložit**.

10. V části Mapování vyberte **Synchronizovat uživatele Služby Azure active directory s GitHubem**.

11. V části **Mapování atributů** zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na GitHub. Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v GitHubu pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko Uložit.

12. Pokud chcete povolit zřizovací službu Azure AD pro GitHub, změňte **stav zřizování** **na Zapnuto** v části **Nastavení**

13. Klikněte na **Uložit**.

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin přiřazených githubu v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěna. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na protokoly aktivit zřizování, které popisují všechny akce prováděné službou zřizování.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

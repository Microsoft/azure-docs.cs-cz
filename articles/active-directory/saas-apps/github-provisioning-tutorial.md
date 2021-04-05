---
title: 'Kurz: zřizování uživatelů pro GitHub – Azure AD'
description: Naučte se konfigurovat Azure Active Directory pro Automatické zřizování a rušení uživatelských účtů na GitHubu.
services: active-directory
author: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: Zhchia
ms.openlocfilehash: f1600dfc5705ca97f16e8966a796b54fc556d216
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359258"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Kurz: Konfigurace GitHubu pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které musíte provést na GitHubu a Azure AD, a automaticky zřídit a zrušit zřízení uživatelských účtů ze služby Azure AD do GitHubu.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující:

* Tenant Azure Active Directory
* Organizace GitHubu vytvořená v [GitHub Enterprise cloudu](https://help.github.com/articles/github-s-products/#github-enterprise), která vyžaduje [fakturační plán pro GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)
* Uživatelský účet v GitHubu s oprávněními správce k organizaci
* [SAML nakonfigurovaný pro organizaci GitHub Enterprise Cloud](./github-tutorial.md)
* Zajistěte, aby byl pro vaši organizaci poskytnutý přístup OAuth, jak je popsáno [zde](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization) .
* Zřizování SCIM pro jedinou organizaci se podporuje jenom v případě, že je jednotné přihlašování povolené na úrovni organizace.

> [!NOTE]
> Integrace zřizování Azure AD spoléhá na [rozhraní API GITHUB SCIM](https://developer.github.com/v3/scim/), které je dostupné pro zákazníky [GitHubu Enterprise cloudu](https://help.github.com/articles/github-s-products/#github-enterprise) v [plánu fakturace na webu GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations).

## <a name="assigning-users-to-github"></a>Přiřazení uživatelů k GitHubu

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů se synchronizují jenom uživatelé a skupiny přiřazené k aplikaci v Azure AD. 

Než nakonfigurujete a povolíte službu zřizování, musíte se rozhodnout, co uživatelé a skupiny v Azure AD reprezentují uživatelé, kteří potřebují přístup k vaší aplikaci GitHubu. Jakmile se rozhodnete, můžete tyto uživatele přiřadit do vaší aplikace GitHub podle pokynů uvedených tady:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Důležité tipy pro přiřazování uživatelů do GitHubu

* Doporučuje se, aby se k GitHubu pro otestování konfigurace zřizování přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele do GitHubu, musíte vybrat buď roli **uživatele** , nebo jinou platnou roli specifickou pro aplikaci (Pokud je dostupná) v dialogovém okně přiřazení. **Výchozí přístupová** role nefunguje pro zřizování a tito uživatelé se přeskočí.

## <a name="configuring-user-provisioning-to-github"></a>Konfigurace zřizování uživatelů na GitHubu

Tato část vás provede připojením k rozhraní API pro zřizování uživatelských účtů na GitHubu a konfigurací zřizovací služby k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v GitHubu na základě přiřazení uživatelů a skupin ve službě Azure AD.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů na GitHubu ve službě Azure AD

1. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Enterprise Apps > všechny aplikace**  .

2. Pokud jste již nakonfigurovali GitHub pro jednotné přihlašování, vyhledejte vaši instanci GitHubu pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **GitHub** v galerii aplikací. Z výsledků hledání vyberte GitHub a přidejte ho do seznamu aplikací.

3. Vyberte svou instanci GitHubu a pak vyberte kartu **zřizování** .

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Zřizování GitHubu](./media/github-provisioning-tutorial/GitHub1.png)

5. V části **Přihlašovací údaje správce** klikněte na **Autorizovat**. Tato operace otevře dialogové okno pro autorizaci GitHubu v novém okně prohlížeče. Všimněte si, že musíte zajistit, abyste měli jistotu, že budete schvalovat přístup. Postupujte podle pokynů popsaných [tady](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization).

6. V novém okně se přihlaste k GitHubu pomocí účtu správce. V dialogovém okně výsledné autorizace vyberte tým GitHubu, pro který chcete povolit zřizování, a pak vyberte **autorizovat**. Po dokončení se vraťte na Azure Portal a dokončete konfiguraci zřizování.

    ![Snímek obrazovky se zobrazí přihlašovací stránka GitHubu.](./media/github-provisioning-tutorial/GitHub2.png)

7. V Azure Portal zadejte **adresu URL klienta** a klikněte na **Test připojení** . tím zajistíte, aby se služba Azure AD mohla připojit k vaší aplikaci GitHub. Pokud se připojení nepovede, ujistěte se, že váš účet GitHub má oprávnění správce a **Adresa URL tenanta** je zavedená správně, a pak zkuste znovu provést krok autorizovat ( **můžete být v** `https://api.github.com/scim/v2/organizations/<Organization_name>` rámci svého účtu GitHubu, který můžete najít   >  ).

    ![Snímek obrazovky se zobrazí stránka organizace v GitHubu.](./media/github-provisioning-tutorial/GitHub3.png)

8. Zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování v poli **e-mail s oznámením** , a zaškrtněte políčko Odeslat e-mailové oznámení, když dojde k chybě.

9. Klikněte na **Uložit**.

10. V části mapování vyberte **synchronizovat Azure Active Directory uživatelů do GitHubu**.

11. V části **mapování atributů** zkontrolujte atributy uživatelů synchronizované z Azure AD do GitHubu. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v GitHubu pro operace aktualizace. Výběrem tlačítka Uložit potvrďte provedené změny.

12. Pokud chcete povolit službu Azure AD Provisioning pro GitHub, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

13. Klikněte na **Uložit**.

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin přiřazených k GitHubu v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit, které popisují všechny akce prováděné službou zřizování.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
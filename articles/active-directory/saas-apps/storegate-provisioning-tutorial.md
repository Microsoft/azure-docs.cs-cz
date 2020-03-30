---
title: 'Kurz: Konfigurace storegate pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a odpočitatla uživatelské účty storegate.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 628fa804-c0e6-4db1-ab6b-46ee9aab4d41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: 72903a36f88f9092ce1d203b557003083407320b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064253"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Kurz: Konfigurace storegate pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Storegate a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-provision uživatelů nebo skupin storegate.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Tenant Storegate](https://www.storegate.com)
* Uživatelský účet na Storegate s oprávněními správce.

## <a name="assign-users-to-storegate"></a>Přiřazení uživatelů k bráně Storegate

Azure Active Directory používá koncept s názvem přiřazení k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Storegate. Jakmile se rozhodnete, můžete těmto uživatelům a/nebo skupinám přiřadit storegate podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Důležité tipy pro přiřazování uživatelů do Storegate

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k Storegate k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k Storegate musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="set-up-storegate-for-provisioning"></a>Nastavení Storegate pro zřizování

Před konfigurací Storegate pro automatické zřizování uživatelů s Azure AD, budete muset načíst některé zřizování informace z Storegate.

1. Přihlaste se do [konzoly Storegate Admin Console](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) a přejděte k nastavení kliknutím na ikonu uživatele v pravém horním rohu a vyberte **Nastavení účtu**.

    ![Storegate Přidat SCIM](media/storegate-provisioning-tutorial/admin.png)

2. V rámci nastavení přejděte na **nastavení team >** a ověřte, zda je přepínač zapnutý v části Jednotné **přihlašování.**

    ![Nastavení brány Úložiště](media/storegate-provisioning-tutorial/team.png)

    ![Přepínací tlačítko Storegate](media/storegate-provisioning-tutorial/sso.png)

3. Zkopírujte **adresu URL klienta** a **token**. Tyto hodnoty se zanesou do polí **URL klienta** a **tajný token** v kartě Zřizování vaší aplikace Storegate na webu Azure Portal. 

    ![Vytvořit token Storegate](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Přidání Storegate z galerie

Chcete-li nakonfigurovat Storegate pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat Storegate z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Storegate**, vpanelu výsledků vyberte **Storegate.** 

    ![Storegate v seznamu výsledků](common/search-new-app.png)

5. Vyberte tlačítko **Registrace pro Storegate,** které vás přesměruje na přihlašovací stránku Storegate. 

    ![Storegate OIDC Přidat](media/storegate-provisioning-tutorial/signup.png)

6.  Přihlaste se do [konzoly Storegate Admin Console](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) a přejděte k nastavení kliknutím na ikonu uživatele v pravém horním rohu a vyberte **Nastavení účtu**.

    ![Přihlášení storegate](media/storegate-provisioning-tutorial/admin.png)

7. V rámci nastavení přejděte na **nastavení > týmu** a klikněte na přepínač v části Jednotné přihlášení, spustí se tok souhlasu. Klikněte na **Aktivovat**.

    ![Tým Storegate](media/storegate-provisioning-tutorial/team.png)

    ![Skladové brány sso](media/storegate-provisioning-tutorial/sso.png)

    ![Aktivovat Storegate](media/storegate-provisioning-tutorial/activate.png)

8. Vzhledem k tomu, že Storegate je aplikace OpenIDConnect, zvolte přihlášení do Storegate pomocí pracovního účtu Microsoft.

    ![Přihlášení Storegate OIDC](media/storegate-provisioning-tutorial/login.png)

9. Po úspěšném ověření přijměte výzvu k souhlasu pro stránku souhlasu. Aplikace pak bude automaticky přidána do vašeho tenanta a budete přesměrováni na svůj účet Storegate.

    ![Souhlas storegate OIDc](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Konfigurace automatického zřizování uživatelů do Storegate 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Storegate na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!NOTE]
> Další informace o koncovém bodu SCIM společnosti Storegate naleznete na [tomto](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/).

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Storegate ve službě Azure AD

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Storegate**.

    ![Odkaz Storegate v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://dialpad.com/scim` zadejte adresu **URL klienta**. Zadejte hodnotu, kterou jste načetli a uložili dříve z Storegate v **tajném tokenu**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k Storegate. Pokud se připojení nezdaří, ujistěte se, že váš účet Storegate má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s storegate**.

    ![Mapování uživatelů Storegate](media/storegate-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Storegate v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Storegate pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy Storegate](media/storegate-provisioning-tutorial/userattributes.png)

10. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Chcete-li povolit službu zřizování Azure AD pro Storegate, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit Storegate výběrem požadovaných hodnot v **scope** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

13. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Storegate.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

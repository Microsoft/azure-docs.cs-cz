---
title: 'Kurz: Konfigurace matice priorit pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty do matice priorit.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a4598a99-3c98-4c14-86c2-95cc562e2439
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: 80ffaba6713027d216958e0be2cd4ae35a8d2d70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063435"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Kurz: Konfigurace matice priorit pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Priority Matrix a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-provision uživatelů a/nebo skupin priority Matrix.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Tenant priority Matrix](https://appfluence.com/pricing/)
* Uživatelský účet v matici priorit s oprávněními správce.

## <a name="assign-users-to-priority-matrix"></a>Přiřazení uživatelů k matici priority

Azure Active Directory používá koncept s názvem přiřazení k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k matici priorit. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny priority Matrix podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Důležité tipy pro přiřazení uživatelů k matici priority

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a priority Matrix k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k matici priorit je nutné v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="set-up-priority-matrix-for-provisioning"></a>Nastavení matice priority pro zřizování

Před konfigurací Priority Matrix pro automatické zřizování uživatelů s Azure AD, budete muset načíst některé informace zřizování z Priority Matrix.

1. Přihlaste se do [konzole pro správu priority Matrix](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning).

3. Klikněte na **přihlašovací token Oauth** pro Prioritní matici.

    ![Priorita Matice Přidat SCIM](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Klikněte na tlačítko **ZÍSKAT NOVÝ TOKEN.** Zkopírujte **řetězec tokenu**. Tato hodnota se zadá do pole **Tajný token** na kartě Zřizování aplikace Priority Matrix na webu Azure Portal. 

    ![Vytvořit token prioritní matice](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Přidat matici priority z galerie

Chcete-li nakonfigurovat matici priorit pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat Matice priority z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Matice priorit**, vpanelu výsledků vyberte **Matice priorit.** 

    ![Matice priority v seznamu výsledků](common/search-new-app.png)

5. Vyberte tlačítko **Registrace pro prioritní matici,** které vás přesměruje na přihlašovací stránku Priority Matrix. 

    ![Přidání oidc matice priority](media/priority-matrix-provisioning-tutorial/signup.png)

6. Vzhledem k tomu, že Prioritní matice je aplikace OpenIDConnect, zvolte přihlášení k Prioritní matici pomocí pracovního účtu Microsoft.

    ![Přihlášení OIDC priority Matrix](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Po úspěšném ověření přijměte výzvu k souhlasu pro stránku souhlasu. Aplikace pak bude automaticky přidána do vašeho tenanta a budete přesměrováni na váš účet Priority Matrix.

    ![Prioritní matice Souhlas OIDc](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Konfigurace automatického zřizování uživatelů na matici priorit 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v matici priorit na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!NOTE]
> Další informace o koncovém bodu SCIM priority Matrix naleznete v části [Zřizování uživatelů a Matice priorit](https://appfluence.com/help/article/user-provisioning/).

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro matici priorit ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Priorita matice**.

    ![Odkaz Matice priority v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://sync.appfluence.com/scim/v2/` zadejte adresu **URL klienta**. Zadejte hodnotu, kterou jste načetli a uložili dříve z matice priority v **tajném tokenu**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k matici priorit. Pokud se připojení nezdaří, ujistěte se, že váš účet Priority Matrix má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s maticí priorit**.

    ![Mapování uživatelů matice priorit](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na prioritu matice v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v matici priorit pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy matice priorit](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Chcete-li povolit službu zřizování Azure AD pro matici priorit, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit matici priority, výběrem požadovaných hodnot v **scope** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

13. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Matici priorit.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)



---
title: 'Kurz: Konfigurace fuze pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřizovala a zřizovala uživatelské účty společnosti Fuze.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 34718201-4f0e-4260-9af0-b3b70a1e8265
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: a58402297380116f83214e52ae7f2796412755b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057856"
---
# <a name="tutorial-configure-fuze-for-automatic-user-provisioning"></a>Kurz: Konfigurace fuze pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny ve Fuze a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-zřizování uživatelů a/nebo skupin fuze.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Tenanta Azure AD.
* [Nájemce Fuze](https://www.fuze.com/).
* Uživatelský účet ve fuze s oprávněními správce.

## <a name="assigning-users-to-fuze"></a>Přiřazení uživatelů k Fuze

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Fuze. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny fuze podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-fuze"></a>Důležité tipy pro přiřazení uživatelů do společnosti Fuze

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen fuze k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele ke společnosti Fuze musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="setup-fuze-for-provisioning"></a>Nastavení fuze pro zřizování

Před konfigurací Fuze pro automatické zřizování uživatelů pomocí Azure AD budete muset povolit zřizování SCIM na Fuze. 

1. Nejprve se obraťte na svého zástupce společnosti Fuze a požádejte o následující požadované informace:

    * Seznam položek soupoložek produktů Fuze, které jsou v současné době používány ve vaší společnosti.
    * Seznam kódů lokací pro lokality vaší společnosti.
    * Seznam kódů oddělení pro vaši společnost.

2. Tyto sloky a kódy naleznete ve smlouvě fuze a v konfiguračních dokumentech nebo se obraťte na svého zástupce společnosti Fuze.

3. Jakmile jsou požadavky přijaty, váš zástupce Fuze vám poskytne ověřovací token Fuze, který je nutný k povolení integrace. Tato hodnota se zadá do pole Tajný token na kartě Zřizování aplikace Fuze na webu Azure Portal.

## <a name="add-fuze-from-the-gallery"></a>Přidat Fuze z galerie

Před konfigurací Fuze pro automatické zřizování uživatelů pomocí Azure AD je potřeba přidat Fuze z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Fuze z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Fuze**, vyberte **Fuze** v panelu výsledků a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![Fuze v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-fuze"></a>Konfigurace automatického zřizování uživatelů pro Fuze 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin ve Fuze na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování společnosti FUZe založené na saml , podle pokynů uvedených v [kurzu fuze jednotného přihlašování](fuze-tutorial.md). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Fuze ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Fuze**.

    ![Odkaz Fuze v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://api.fuze.com/scim/v2` zadejte adresu **URL klienta**. Zadejte hodnotu **ověřovacího tokenu SCIM** načtenou dříve od zástupce společnosti Fuze v **tajném tokenu**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit ke Fuze. Pokud se připojení nezdaří, ujistěte se, že váš účet Fuze má oprávnění správce a zkuste to znovu.

    ![Token adresy URL klienta](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s fuze**.

    ![Mapování uživatelů fuze](media/fuze-provisioning-tutorial/image01.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Fuze v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům ve fuze pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Mapování uživatelů fuze](media/fuze-provisioning-tutorial/image00.png)

10. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Chcete-li povolit službu zřizování Azure AD pro Fuze, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit fuze, výběrem požadovaných hodnot v **scope** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD ve Fuze.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Fuze podporuje vlastní atributy SCIM s názvem **Nároky**. Tyto atributy lze pouze vytvořit a neaktualizovat. 

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování](../app-provisioning/check-status-user-account-provisioning.md).
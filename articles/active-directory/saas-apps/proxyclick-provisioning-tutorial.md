---
title: 'Kurz: Konfigurace kliknutí na proxy uživatele pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty na proxyclick.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 95cb0371c4b2181d8f09991fe6e652c0e939f3e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063352"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Kurz: Konfigurace funkce Proxyclick pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Proxyclick a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-zřizování uživatelů nebo skupin proxyclick.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Tenant proxyclick](https://www.proxyclick.com/pricing)
* Uživatelský účet v proxyklepne s oprávněními správce.

## <a name="add-proxyclick-from-the-gallery"></a>Přidat Proxyclick z galerie

Před konfigurací Proxyclick pro automatické zřizování uživatelů s Azure AD, je potřeba přidat Proxyclick z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat proxyklepnu z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Proxyclick**, vyberte **Proxyclick** v panelu výsledků a pak klepněte na tlačítko **Přidat** pro přidání aplikace.

    ![Proxyklikněte do seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Přiřazení uživatelů k proxyclick

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k proxyclick. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny Proxyclick podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Důležité tipy pro přiřazení uživatelů k Proxyclick

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen proxyclick otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k příkazu Proxyclick je nutné v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Konfigurace automatického zřizování uživatelů na proxyclick 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v proxyclick na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také zvolit, aby SAML-založené jednotné přihlášení pro Proxyclick, podle pokynů uvedených v [Proxyclick jednotného přihlášení-na tutorial](proxyclick-tutorial.md). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro proxykliknuve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Příkaz Proxyclick**.

    ![Odkaz Proxyclick v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. Chcete-li načíst **adresu URL klienta** a **tajný token** vašeho účtu Proxyclick, postupujte podle návodu, jak je popsáno v kroku 6.

6. Přihlaste se ke konzoli [Proxyclick Admin Console](https://app.proxyclick.com/login//?destination=%2Fdefault). Přejděte na **integrace nastavení** > **Integrations** > **Procházet marketplace**.

    ![Nastavení proxyclicku](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Integrace proxyclick](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Vyberte **Azure AD**. Klepněte na tlačítko **Nainstalovat .**

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Proxyclick Nainstalovat](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Vyberte **Zřizování uživatelů** a klepněte na **tlačítko Spustit integraci**. 

    ![Zřizování uživatelů služby Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    V části**Integrace** **nastavení** > by se nyní mělo zobrazit příslušné nastavení konfigurace . Vyberte **nastavení** v části **Azure AD (Zřizování uživatelů)**.

    ![Proxyclick vytvořit](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Adresu URL **klienta** a **tajný token** najdete zde.

    ![Proxyclick vytvořit token](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Po vyplnění polí uvedených v kroku 5, klikněte na **test připojení** k zajištění Azure AD můžete připojit k Proxyclick. Pokud se připojení nezdaří, ujistěte se, že váš účet Proxyclick má oprávnění správce a zkuste to znovu.

    ![Podpisový](common/provisioning-testconnection-tenanturltoken.png)

8. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **Mapování** vyberte **synchronizovat uživatele služby Azure Active Directory s klepnutím na proxyklepnout**.

    ![Mapování uživatelů pomocí příkazu Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD proxyklepněte v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Proxyclick pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy uživatele proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Chcete-li povolit službu zřizování Azure AD pro proxyclick, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

15. Definujte uživatele nebo skupiny, které chcete zřídit proxyklepnu, výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

16. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na proxyclick.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektorů

* Proxyclick **vyžaduje, aby e-maily** a **userName** měly stejnou zdrojovou hodnotu. Všechny aktualizace obou atributů změní ostatní hodnoty.
* Proxyclick nepodporuje zřizování pro skupiny.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)


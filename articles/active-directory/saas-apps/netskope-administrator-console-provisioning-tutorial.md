---
title: 'Kurz: Konfigurace konzoly správce Netskope pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a odpočovává uživatelské účty do konzoly správců Netskope.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e99f0e0f-28d0-43c6-a52b-df873fb9d551
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: eaee8e3305572d696e52c3879be2e2b9924bc93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061220"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Kurz: Konfigurace konzoly správce Netskope pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Netskope Administrator Console a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-provision uživatelů a/nebo skupin netskope administrator console.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Klient konzoly správce Netskope](https://www.netskope.com/)
* Uživatelský účet v konzole správce Netskope s oprávněními správce.

## <a name="assigning-users-to-netskope-administrator-console"></a>Přiřazení uživatelů ke konzole správce Netskope

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup ke konzoli správce Netskope. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny Netskope Administrator Console podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Důležité tipy pro přiřazení uživatelů ke konzoli správce Netskope

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k Netskope konzoly správce k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele ke konzoli Netskope Administrator Console je nutné v dialogovém okně úlohy vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>Nastavení konzoly správce Netskope pro zřizování

1. Přihlaste se ke [konzoli správce služby Netskope Administrator Console](https://netskope.goskope.com/). Přejděte do **nastavení > .**

    ![Konzola pro správu správce Netskope](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Přejděte na **Nástroje**. V nabídce **Nástroje** přejděte do **adresářových nástrojů > integrace SCIM**.

    ![Nástroje pro konzolu správce Netskope](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope Správce Konzola Přidat SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Posuňte se dolů a klikněte na tlačítko **Přidat token.** V dialogovém okně **Přidat název klienta OAuth** zadejte **název klienta** a klikněte na tlačítko **Uložit.**

    ![Konzola správce Netskope přidat token](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Název clientu konzoly správce Netskope](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Zkopírujte **adresu URL serveru SCIM** a **token**. Tyto hodnoty se zanesou do polí URL klienta a tajný token v záložce Zřizování aplikace Netskope Administrator Console na webu Azure Portal.

    ![Vytvořit token konzoly správce Netskope](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Přidání konzoly správce Netskope z galerie

Před konfigurací konzoly správce Netskope pro automatické zřizování uživatelů pomocí azure ad, je třeba přidat Netskope správce konzoly z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat konzolu správce Netskope z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte v panelu výsledků položku **Netskope Administrator Console**, vyberte **příkaz Netskope Administrator Console** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![Konzola správce Netskope v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Konfigurace automatického zřizování uživatelů do konzoly správce Netskope 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v konzole správce Netskope na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování založené na saml pro konzoli Netskope Administrator Console podle pokynů uvedených v [kurzu jednotného přihlášení konzoly Správce Netskope](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial). Jednotné přihlašování lze konfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce se vzájemně doplňují.

> [!NOTE]
> Další informace o koncovém bodu SCIM konzoly Správce Netskope naleznete na [tomto](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon).

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro konzolu správce Netskope ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **položku Netskope Administrator Console**.

    ![Odkaz Konzola správce Netskope v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** zadejte hodnotu adresy URL **serveru SCIM** načtenou dříve v **adrese URL klienta**. Zadejte hodnotu **TOKEN** načtenou dříve v **tajném tokenu**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit ke konzoli správce Netskope. Pokud se připojení nezdaří, ujistěte se, že váš účet správce netskope má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s konzolí správce Netskope**.

    ![Mapování uživatelů konzoly správce Netskope](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do služby Netskope Administrator Console v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v konzole správce Netskope pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy konzoly správce Netskope](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. V části **Mapování** vyberte **Synchronizovat skupiny Služby Azure Active Directory do konzoly správce Netskope**.

    ![Mapování konzoly správce Netskope](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do služby Netskope Administrator Console v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v konzole správce Netskope pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy skupiny konzoly správce Netskope](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro konzolu správce Netskope, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit konzoli správce Netskope, výběrem požadovaných hodnot v **části Obor.** **Settings**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Služby Azure AD na konzoli správce Netskope.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)


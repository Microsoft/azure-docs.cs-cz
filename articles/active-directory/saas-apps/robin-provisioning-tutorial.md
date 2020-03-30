---
title: 'Kurz: Konfigurace robinu pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřizovala uživatelské účty robinu Poweredovi.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 178ca5b3-4155-43ab-84f5-650d25c5a74a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2019
ms.author: Zhchia
ms.openlocfilehash: fabd8a1953bedf6c3db6da443903a6dbd965b01e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060928"
---
# <a name="tutorial-configure-robin-for-automatic-user-provisioning"></a>Kurz: Konfigurace robinu pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Robin a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-provision uživatelů nebo skupin robin.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Robin nájemce](https://robinpowered.com/pricing/)
* Uživatelský účet v Robinu s oprávněními správce.

## <a name="assigning-users-to-robin"></a>Přiřazení uživatelů k robinu

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Robinovi. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny Robin podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-robin"></a>Důležité tipy pro přiřazení uživatelů k Robinu

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a Robin otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele robinu je nutné v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="set-up-robin-for-provisioning"></a>Nastavit Robin pro zřizování

1. Přihlaste se ke [konzoli Robin Admin Console](https://dashboard.robinpowered.com/login). Přejděte na **Spravovat > integrace > scim > spravovat**.

    ![robin poháněl Admin Console](media/robin-provisioning-tutorial/robin-admin.png)

2.  Vygenerujte nový token organizace. Pokud tento token ztratíte, můžete vždy vytvořit nový, aniž by to ovlivnilo stávající uživatele.

    ![robin powered Přidat SCIM](media/robin-provisioning-tutorial/robin-token.png)

3.  Zkopírujte **ověřovací token SCIM**. Tato hodnota se zadá do pole Tajný token na kartě Zřizování vaší aplikace Robin na webu Azure Portal.



## <a name="add-robin-from-the-gallery"></a>Přidání Robina z galerie

Před konfigurací Robin pro automatické zřizování uživatelů pomocí Azure AD, je potřeba přidat Robin z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Pokud chcete robin přidat z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Robin**, vyberte **Robin** v panelu výsledků a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![Robin v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-robin"></a>Konfigurace automatického zřizování uživatelů pro Robin 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v robinu na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování pro Robin založené na SAML podle pokynů uvedených v [kurzu robin jednotného přihlášení](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial). Jednotné přihlašování lze konfigurovat nezávisle na automatickézřivací službě uživatelů, i když tyto dvě funkce se vzájemně doplňují

### <a name="to-configure-automatic-user-provisioning-for-robin-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Robin ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Robin**.

    ![Robin poháněl odkaz v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://api.robinpowered.com/v1.0/scim-2` zadejte adresu **URL klienta**. Zadejte hodnotu **ověřovacího tokenu SCIM** načtenou dříve v **tokenu tajného klíče**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k Robinovi. Pokud se připojení nezdaří, ujistěte se, že váš účet Robin má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s robinem**.

    ![robin poháněl mapování uživatelů](media/robin-provisioning-tutorial/robin-user-mapping.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Robin v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v Robin pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![robin poháněl uživatelské atributy](media/robin-provisioning-tutorial/robin-user-attribute-mapping.png)

10. V části **Mapování** vyberte **Synchronizovat skupiny služby Azure Active Directory s robinem**.

    ![robin poháněl mapování skupin](media/robin-provisioning-tutorial/robin-group-mapping.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na Robin v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v Robin pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![robin poháněl atributy skupiny](media/robin-provisioning-tutorial/robin-group-attribute-mapping.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro Robin, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete robinovi zřídit, výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Robin.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).



## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)


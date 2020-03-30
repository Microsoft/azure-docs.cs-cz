---
title: 'Kurz: Konfigurace webu TheOrgWiki pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a odpočitatla uživatelské účty na TheOrgWiki.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eb2096e-f7b6-4a56-a814-0f3d0e788063
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2b0b11ae89e1f0d150b84f49b04a8badcb44e82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063134"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Kurz: Konfigurace TheOrgWiki pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v TheOrgWiki a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-provision uživatelů nebo skupin theOrgWiki.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Tenanta Azure AD.
* [Tenant OrgWiki](https://www.theorgwiki.com/welcome/).
* Uživatelský účet v TheOrgWiki s oprávněními správce.

## <a name="assign-users-to-theorgwiki"></a>Přiřazení uživatelů k TheOrgWiki

Azure Active Directory používá koncept s názvem přiřazení k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k TheOrgWiki. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny TheOrgWiki podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Důležité tipy pro přiřazení uživatelů k TheOrgWiki

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a TheOrgWiki otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k TheOrgWiki musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="set-up-theorgwiki-for-provisioning"></a>Nastavení TheOrgWiki pro zřizování

Před konfigurací TheOrgWiki pro automatické zřizování uživatelů pomocí Azure AD, budete muset povolit zřizování SCIM na TheOrgWiki.

1. Přihlaste se ke své [Konzole pro správu TheOrgWiki](https://www.theorgwiki.com/login/). Klikněte na **Admin Console**.

    ![Theorgwiki Přidat SCIM](media/theorgwiki-provisioning-tutorial/login.png)

2. V Konzole pro správu klikněte na **kartu Nastavení**. 

    ![Theorgwiki Přidat SCIM](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Přejděte na **účty služeb**.

    ![Theorgwiki Přidat SCIM](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Klikněte na **+Účet služby**. V části **Typ účtu služby**vyberte **možnost Založená na tokenu**. Klikněte na **Uložit**.

    ![Theorgwiki Přidat SCIM](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Zkopírujte **aktivní tokeny**. Tato hodnota se zadá do pole Tajný token na kartě Zřizování aplikace TheOrgWiki na webu Azure Portal.
     
    ![Theorgwiki Přidat SCIM](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Přidání TheOrgWiki z galerie

Chcete-li nakonfigurovat TheOrgWiki pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat TheOrgWiki z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **TheOrgWiki**, vyberte **theorgwiki** v panelu výsledků. 

    ![TheOrgWiki v seznamu výsledků](common/search-new-app.png)

5. Vyberte tlačítko **Registrace pro TheOrgWiki,** které vás přesměruje na přihlašovací stránku TheOrgWiki. 

    ![Theorgwiki Přidat SCIM](media/theorgwiki-provisioning-tutorial/image00.png)

6.  V pravém horním rohu vyberte **Přihlásit se**.

    ![Theorgwiki Přidat SCIM](media/theorgwiki-provisioning-tutorial/image02.png)

7. Vzhledem k tomu, že TheOrgWiki je aplikace OpenIDConnect, zvolte přihlášení k OrgWiki pomocí pracovního účtu Microsoft.

    ![Theorgwiki Přidat SCIM](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Po úspěšném ověření bude aplikace automaticky přidána do vašeho tenanta a budete přesměrováni na váš účet TheOrgWiki.

    ![OrgWiki Přidat SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Konfigurace automatického zřizování uživatelů na TheOrgWiki 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v TheOrgWiki na základě přiřazení uživatelů nebo skupin ve službě Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro TheOrgWiki ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **TheOrgWiki**.

    ![Odkaz OrgWiki v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` zadejte adresu **URL klienta**. 

    Příklad: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> **Hodnotu subdomény** lze nastavit pouze během počátečního procesu registrace pro TheOrgWiki.
 
6. Zadejte hodnotu tokenu do pole **Tajný token,** kterou jste načetli dříve z TheOrgWiki. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k TheOrgWiki. Pokud se připojení nezdaří, ujistěte se, že váš účet TheOrgWiki má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

7. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

8. Klikněte na **Uložit**.

9. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s theorgwiki**.

    ![Mapování uživatelů TheOrgWiki](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na TheOrgWiki v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v TheOrgWiki pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy TheOrgWiki](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Chcete-li povolit službu zřizování Azure AD pro TheOrgWiki, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit orgwiki výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

14. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace. Další informace o tom, jak dlouho bude trvat uživatelům nebo skupinám, naleznete v tématu [Jak dlouho bude trvat zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Část Aktuální **stav** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na TheOrgWiki. Další informace naleznete [v tématu Kontrola stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Chcete-li číst protokoly zřizování Azure AD, najdete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování](../app-provisioning/check-status-user-account-provisioning.md).
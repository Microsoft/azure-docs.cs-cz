---
title: 'Kurz: Konfigurace ERP oracle fusion pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty do erp Oracle Fusion.
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
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 08a82be5a11ba7b81cd1939fc5b386c161c43480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061172"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Kurz: Konfigurace ERP oracle fusion pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Oracle Fusion ERP a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-provision uživatelů a/nebo skupin oracle fusion ERP.

> [!NOTE]
>  Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce Preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Tenant ERP oracle fusion](https://www.oracle.com/applications/erp/).
* Uživatelský účet v ERP oracle fusion s oprávněními správce.

## <a name="assign-users-to-oracle-fusion-erp"></a>Přiřazení uživatelů k ERP oracle fusion 
Azure Active Directory používá koncept s názvem přiřazení k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k ERP Oracle Fusion. Jakmile se rozhodnete, můžete tyto uživatele a/nebo skupiny přiřadit k ERP oracle fusion podle pokynů zde:
 
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Důležité tipy pro přiřazení uživatelů k ERP oracle fusion 

 * Doporučuje se, aby jeden uživatel Azure AD je přiřazen k Oracle Fusion ERP k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k erp oracle fusion je nutné v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí Výchozí přístup jsou z zřizování vyloučeni.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Nastavení ERP oracle fusion pro zřizování

Před konfigurací Oracle Fusion ERP pro automatické zřizování uživatelů pomocí Azure AD, budete muset povolit zřizování SCIM na Oracle Fusion ERP.

1. Přihlaste se ke [konzoli Oracle Fusion ERP Admin Console](https://cloud.oracle.com/sign-in)

2. Klikněte na navigátor v levém horním rohu. V části **Nástroje**vyberte **možnost Konzola zabezpečení**.

    ![Oracle Fusion ERP Přidat SCIM](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. Přejděte na **uživatelé**.
    
    ![Oracle Fusion ERP Přidat SCIM](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. Uložte uživatelské jméno a heslo pro uživatelský účet správce, který použijete pro přihlášení do konzole pro správu ERP oracle fusion. Tyto hodnoty je třeba zadat do polí **Uživatelské jméno** správce a **heslo** na kartě Zřizování vaší aplikace ERP Oracle Fusion na webu Azure Portal.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Přidání ERP oracle fusion z galerie

Chcete-li nakonfigurovat Oracle Fusion ERP pro automatické zřizování uživatelů pomocí Azure AD, je třeba přidat Oracle Fusion ERP z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat ERP Oracle Fusion z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Oracle Fusion ERP**, vyberte v panelu výsledků **erp oracle fusion.**

    ![Erp oracle fusion v seznamu výsledků](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Konfigurace automatického zřizování uživatelů pro oracle fusion erp 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v ERP Oracle Fusion na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na saml pro Oracle Fusion ERP podle pokynů uvedených v [kurzu Oracle Fusion ERP jednotnépřihlášení](oracle-fusion-erp-tutorial.md). Jednotné přihlašování lze konfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce se vzájemně doplňují.

> [!NOTE]
> Další informace o koncovém bodě SCIM společnosti Oracle Fusion ERP naleznete v rozhraní [REST API pro běžné funkce v řešení Oracle Applications Cloud](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html).

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Fuze ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Oracle Fusion ERP**.

    ![Odkaz ERP oracle fusion v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` zadejte adresu **URL klienta**. Do polí **Uživatelské jméno** správce a **heslo** načtené dříve zadejte uživatelské jméno správce a heslo. Klikněte na **Test připojení** mezi Azure AD a Oracle Fusion ERP. 

    ![Oracle Fusion ERP Přidat SCIM](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části Mapování vyberte **synchronizovat uživatele služby Azure Active Directory s ERP oracle fusion**. **Mappings**

    ![Oracle Fusion ERP Přidat SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Oracle Fusion ERP v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v erp oracle fusion pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Oracle Fusion ERP Přidat SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. V části **Mapování** vyberte **Synchronizovat skupiny Azure Active Directory s ERP oracle fusion**.

    ![Mapování skupinerp erp oracle fusion](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do Oracle Fusion ERP v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v erp oracle fusion pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy skupiny ERP oracle fusion](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit zřizovací službu Azure AD pro Oracle Fusion ERP, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit erp oracle fusion výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

    Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v ERP Oracle Fusion.

    Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Oracle Fusion ERP podporuje pouze základní ověřování pro jejich koncový bod SCIM.
* Oracle Fusion ERP nepodporuje zřizování skupiny.
* Role v ERP Oracle Fusion jsou mapovány na skupiny ve službě Azure AD. Chcete-li přiřadit role uživatelům v Erp Oracle Fusion z Azure AD, budete muset přiřadit uživatele k požadované skupiny Azure AD, které jsou pojmenovány po rolích v Oracle Fusion ERP.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

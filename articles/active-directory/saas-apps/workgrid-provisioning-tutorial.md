---
title: 'Kurz: Konfigurace pracovní mřížky pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty do Workgridu.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: 94d70447117c73a309959ddf66972c921aa5e687
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062802"
---
# <a name="tutorial-configure-workgrid--for-automatic-user-provisioning"></a>Kurz: Konfigurace pracovní mřížky pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Workgrid a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-provision uživatelů nebo skupin workgrid.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Tenanta Azure AD.
* [Tenant Workgrid](https://www.workgrid.com/)
* Uživatelský účet v Workgrid u oprávnění správce.

## <a name="assigning-users-to-workgrid"></a>Přiřazení uživatelů k workgridu 

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Workgrid. Jakmile se rozhodnete, můžete těmto uživatelům a/nebo skupinám přiřadit workgrid podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workgrid"></a>Důležité tipy pro přiřazení uživatelů do workgridu 

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k Workgrid otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k workgridu musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="set-up-workgrid-for-provisioning"></a>Nastavení workgridu pro zřizování

Před konfigurací Workgrid pro automatické zřizování uživatelů s Azure AD, budete muset povolit zřizování SCIM na Workgrid.

1. Přihlaste se do Workgrid. Přejděte na **uživatelé > zřizování uživatelů**.

    ![Workgrid](media/Workgrid-provisioning-tutorial/user.png)

2. V části **Rozhraní API pro správu účtů**klepněte na tlačítko Vytvořit **pověření**.

    ![Workgrid](media/Workgrid-provisioning-tutorial/scim.png)

3. Zkopírujte hodnoty **koncového bodu SCIM** a **přístupového tokenu.** Ty se zanesou do pole **Adresa URL klienta** a **tajný token** na kartě Zřizování vaší aplikace Workgrid na webu Azure Portal.

    ![Workgrid](media/Workgrid-provisioning-tutorial/token.png)


## <a name="add-workgrid--from-the-gallery"></a>Přidání workgridu z galerie

Chcete-li nakonfigurovat Workgrid pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat Workgrid z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Workgrid z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Pracovní mřížku**, vpanelu s výsledky vyberte **Pracovní mřížku** a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![Pracovní mřížka v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workgrid"></a>Konfigurace automatického zřizování uživatelů do workgridu  

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Workgrid u uživatelů a/nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování pro Workgrid založené na SAML podle pokynů uvedených v [kurzu jednotného přihlašování workgridu](Workgrid-tutorial.md). Jednotné přihlašování lze konfigurovat nezávisle na automatickézřivací službě uživatelů, i když tyto dvě funkce se vzájemně doplňují

### <a name="to-configure-automatic-user-provisioning-for-workgrid--in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Workgrid ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Workgrid**.

    ![Odkaz Workgrid v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části Přihlašovací údaje správce zadejte hodnoty **koncového bodu SCIM** a **tokenu přístupu** načtené dříve v **adrese URL klienta** a **tajný token.** Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k Workgrid. Pokud se připojení nezdaří, ujistěte se, že váš účet Workgrid má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s workgrid**.

    ![Mapování uživatelů pracovní sítě](media/Workgrid-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Workgrid v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v workgrid pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy workgridu](media/Workgrid-provisioning-tutorial/userattribute.png)

10. V části **Mapování** vyberte **Synchronizovat skupiny Služby Active Directory Azure s pracovní misíří sítí.**

    ![Mapování uživatelů pracovní sítě](media/Workgrid-provisioning-tutorial/groupmapping.png)

12. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do Workgrid v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v workgrid pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Mapování uživatelů pracovní sítě](media/Workgrid-provisioning-tutorial/groupattribute.png)

13. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Chcete-li povolit službu zřizování Azure AD pro Workgrid, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

15. Definujte uživatele nebo skupiny, které chcete zřídit workgrid u výběru požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

16. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace. Další informace o tom, jak dlouho bude trvat uživatelům nebo skupinám, naleznete v tématu [Jak dlouho bude trvat zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Část Aktuální **stav** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Workgridu. Další informace naleznete [v tématu Kontrola stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Chcete-li číst protokoly zřizování Azure AD, najdete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

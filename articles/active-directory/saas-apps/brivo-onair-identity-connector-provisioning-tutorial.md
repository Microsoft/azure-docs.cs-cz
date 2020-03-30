---
title: 'Kurz: Konfigurace konektoru identity Brivo Onair pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a odpočišťovala uživatelské účty do konektoru Identity Brivo Onair.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 542ce04c-ef7d-4154-9b0e-7f68e1154f6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: f3c1e7337c0ce07b7fbebb9f954deeb75f0b9584
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246649"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Kurz: Konfigurace konektoru identity Brivo Onair pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Brivo Onair Identity Connector a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-provision uživatelů a/nebo skupin brivo onair identity konektoru.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Tenant konektoru Identity Brivo Onair](https://www.brivo.com/lp/quote)
* Uživatelský účet v konektoru identity Brivo Onair s oprávněními senior správce.

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Přiřazení uživatelů ke konektoru Identity Brivo Onair

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup ke konektoru Identity Brivo Onair. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny Brivo Onair Identity Connector podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Důležité tipy pro přiřazení uživatelů ke konektoru Brivo Onair Identity Connector

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k Brivo Onair Identity Connector k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele ke konektoru identity Brivo Onair je nutné v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>Nastavení konektoru identity Brivo Onair pro zřizování

1.    Přihlaste se ke konzoli [pro správu konektoru Brivo Onair Identity Connector](https://acs.brivo.com/login/). Přejděte do **nastavení účtu > účtu**.

    ![Konzola pro správu konektoru Identity Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2.  Klikněte na kartu **Azure AD.** Na stránce podrobností **azure ad** znovu zadejte heslo svého účtu senior správce. Klikněte na **Odeslat**.

    ![Brivo Onair Identity Connector azure](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3.    Klikněte na tlačítko **Kopírovat token** a uložte **tajný token**. Tato hodnota se zadá do pole Tajný token na kartě Zřizování aplikace Brivo Onair Identity Connector na webu Azure Portal.

    ![Token konektoru identity Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Přidání konektoru Brivo Onair Identity Connector z galerie

Před konfigurací Brivo Onair Identity Connector pro automatické zřizování uživatelů pomocí Azure AD, je třeba přidat Brivo Onair Identity Connector z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat konektor Identity Společnosti Brivo Onair z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Brivo Onair Identity Connector**, vyberte v panelu výsledků **konektor identity Brivo Onair** a potom klepnutím na tlačítko **Přidat** aplikaci přidejte.

    ![Brivo Onair Identity Connector v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Konfigurace automatického zřizování uživatelů na konektor identity Brivo Onair 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v konektoru identity Brivo Onair na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro konektor identity Brivo Onair ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Brivo Onair Identity Connector**.

    ![Odkaz Brivo Onair Identity Connector v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://scim.brivo.com/ActiveDirectory/v2/` zadejte adresu **URL klienta**. Zadejte hodnotu **ověřovacího tokenu SCIM** načtenou dříve v **tokenu tajného klíče**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit ke konektoru Identity Brivo Onair. Pokud se připojení nezdaří, ujistěte se, že váš účet Brivo Onair Identity Connector má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **synchronizovat uživatele služby Azure Active Directory s konektorem Identity Brivo Onair**.

    ![Mapování uživatelů konektoru identity Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Brivo Onair Identity Connector v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v konektoru identity Brivo Onair pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy uživatele konektoru identity Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. V části **Mapování** vyberte **synchronizovat skupiny Služby Active Directory Azure s konektorem identity Brivo Onair**.

    ![Mapování skupinkonektorů brivo identity](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na Brivo Onair Identity Connector v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v Konektor identity Brivo Onair pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy skupiny konektorů identity Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro Konektor identity Brivo Onair, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit konektoru identity Brivo Onair, výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na konektoru identity Brivo Onair.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)


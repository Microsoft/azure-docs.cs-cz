---
title: 'Kurz: Konfigurace 15Five pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřizovala uživatelské účty na 15Five.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a276c004-9f71-4efc-8cca-1f615760249f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: f1f66a7b69048180bc41c8f2fa432598f00f7f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059218"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Kurz: Konfigurace 15Five pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v 15Five a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-zřizování uživatelů a/nebo skupin na 15Five.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Tenanta Azure AD.
* [15Pět nájemce](https://www.15five.com/pricing/).
* Uživatelský účet v 15Five s oprávněními správce.

## <a name="assigning-users-to-15five"></a>Přiřazení uživatelů k 15Five

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k 15Five. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny 15Five podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-15five"></a>Důležité tipy pro přiřazení uživatelů k 15Five

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a 15Five k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele do 15Five, musíte vybrat všechny platné role specifické pro aplikaci (pokud jsou k dispozici) v dialogovém okně přiřazení. Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="setup-15five-for-provisioning"></a>Nastavení 15Five pro zřizování

Před konfigurací 15Five pro automatické zřizování uživatelů s Azure AD, budete muset povolit zřizování SCIM na 15Five.

1. Přihlaste se ke své [155 Admin Console](https://my.15five.com/). Přejděte na **funkce > integrace**.

    ![15Pět konzolí pro správu](media/15five-provisioning-tutorial/integration.png)

2.  Klikněte na **SCIM 2.0**.

    ![15Pět konzolí pro správu](media/15five-provisioning-tutorial/image00.png)

3.  Přejděte na **integrační > generování tokenu OAuth**.

    ![15Pět Přidat SCIM](media/15five-provisioning-tutorial/image02.png)

4.  Zkopírujte hodnoty **základní adresy URL SCIM 2.0** a **přístupového tokenu**. Tato hodnota se zadá do pole **Adresa URL klienta** a **tajný token** na kartě Zřizování vaší aplikace 15Five na webu Azure Portal.
    
    ![15Pět Přidat SCIM](media/15five-provisioning-tutorial/image03.png)

## <a name="add-15five-from-the-gallery"></a>Přidat 15Five z galerie

Chcete-li nakonfigurovat 15Five pro automatické zřizování uživatelů pomocí Azure AD, je třeba přidat 15Five z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat 15Five z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **15Five**, vyberte **15Five** v panelu výsledků a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

    ![15Pět v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-15five"></a>Konfigurace automatického zřizování uživatelů na 15Five 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v 15Five na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování založené na SAML pro 15Five , podle pokynů uvedených v [kurzu 15Five jednotnépřihlášení](15five-tutorial.md). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro 15Five ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **15Five**.

    ![Odkaz 15Five v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5.  V části Přihlašovací údaje správce zadejte **základní adresu URL SCIM 2.0 a hodnoty přístupového tokenu** načtené dříve v adrese URL **klienta** a **tajný token.** Klikněte na **test připojení** a ujistěte se, že Azure AD můžete připojit k 15Five. Pokud se připojení nezdaří, ujistěte se, že váš účet 15Five má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory na 15Five**.

    ![15Pět uživatelských mapování](media/15five-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na 15Five v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v 15Five pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![15Pět uživatelských atributů](media/15five-provisioning-tutorial/userattribute.png)

10. V části **Mapování** vyberte **Synchronizovat skupiny služby Azure Active Directory na 15Five**.

    ![15Pět skupinových mapování](media/15five-provisioning-tutorial/groupmapping.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na 15Five v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupiny v 15Five pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![15Pět atributů skupiny](media/15five-provisioning-tutorial/groupattribute.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro 15Five, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit 15Five výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

    Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na 15Five.

    Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v [tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Omezení konektoru

* 15Five nepodporuje tvrdé odstranění pro uživatele.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování](../app-provisioning/check-status-user-account-provisioning.md).

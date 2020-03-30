---
title: 'Kurz: Konfigurace myPolicies pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty do mých zásad.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f000896d-a78c-4d20-a79c-74c1f9b4961a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 353da826b6e339d40a5d85bbf63caac5bf7094f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061349"
---
# <a name="tutorial-configure-mypolicies-for-automatic-user-provisioning"></a>Kurz: Konfigurace myPolicies pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v myPolicies a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-provision uživatelů a/nebo skupin myPolicies.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Tenanta Azure AD.
* [Klient myPolicies](https://mypolicies.com/index.html#section10).
* Uživatelský účet v myZásady s oprávněními správce.

## <a name="assigning-users-to-mypolicies"></a>Přiřazení uživatelů k mým zásadám

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k myPolicies. Jakmile se rozhodnete, můžete těmto uživatelům a/nebo skupinám přiřadit myPolicies podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mypolicies"></a>Důležité tipy pro přiřazení uživatelů k mým zásadám

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a myPolicies otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k myPolicies, musíte vybrat všechny platné role specifické pro aplikaci (pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="setup-mypolicies-for-provisioning"></a>Nastavení myPolicies pro zřizování

Před konfigurací myPolicies pro automatické zřizování uživatelů s Azure AD, budete muset povolit zřizování SCIM na myPolicies.

1. Obraťte se na svého **support@mypolicies.com** zástupce myPolicies na získání tajný token potřebný ke konfiguraci zřizování SCIM.

2.  Uložte hodnotu tokenu poskytovanou zástupcem myPolicies. Tato hodnota se zadá do pole **Tajný token** na kartě Zřizování aplikace myPolicies na webu Azure Portal.

## <a name="add-mypolicies-from-the-gallery"></a>Přidání myPolicies z galerie

Chcete-li nakonfigurovat myPolicies pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat myPolicies z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat myPolicies z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **myPolicies**, vyberte **myPolicies** v panelu výsledků a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![myPolicies v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mypolicies"></a>Konfigurace automatického zřizování uživatelů na myPolicies 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v myPolicies na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování na základě SAML pro myPolicies , podle pokynů uvedených v [myPolicies jednotné přihlášení kurzu](mypolicies-tutorial.md). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-provisioning-for-mypolicies-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro myPolicies ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **myPolicies**.

    ![Odkaz myPolicies v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Přihlašovací údaje** `https://<myPoliciesCustomDomain>.mypolicies.com/scim` správce zadejte `<myPoliciesCustomDomain>` do adresy URL **klienta,** kde je vaše vlastní doména myPolicies. Svou doménu zákazníka myPolicies můžete načíst z adresy URL.
Příklad: `<demo0-qa>`.mypolicies.com.

6. Do **tajného tokenu**zadejte hodnotu tokenu, která byla načtena dříve. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k mým zásadám. Pokud se připojení nezdaří, ujistěte se, že váš účet myPolicies má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

7. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

8. Klikněte na **Uložit**.

9. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s myPolicies**.

    ![myPolicies Mapování uživatelů](media/mypolicies-provisioning-tutorial/usermapping.png)

10. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na myPolicies v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v myPolicies pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![myPolicies Mapování uživatelů](media/mypolicies-provisioning-tutorial/userattribute.png)

11. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Chcete-li povolit službu zřizování Azure AD pro myPolicies, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit myPolicies výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

14. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na myPolicies.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* myPolicies vždy vyžaduje **userName**, **e-mail** a **externalId**.
* myPolicies nepodporuje pevné odstranění uživatelských atributů.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

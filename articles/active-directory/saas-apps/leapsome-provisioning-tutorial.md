---
title: 'Kurz: Konfigurace leapsome pro automatické zřizování uživatelů pomocí Služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřizovala uživatelské účty leapsome.
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
ms.date: 06/28/2019
ms.author: jeedes
ms.openlocfilehash: e5837887325c06f9140a3f40eb183139782e2a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057460"
---
# <a name="tutorial-configure-leapsome-for-automatic-user-provisioning"></a>Kurz: Konfigurace Leapsome pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Leapsome a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-provision uživatelů nebo skupin leapsome.

> [!NOTE]
>  Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Tenanta Azure AD.
* [Skokavý](https://www.Leapsome.com/en/pricing) nájemník.
* Uživatelský účet v Leapsome s oprávněními správce.

## <a name="assigning-users-to-leapsome"></a>Přiřazení uživatelů k Leapsome

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Leapsome. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny Leapsome podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-leapsome"></a>Důležité tipy pro přiřazení uživatelů k Leapsome

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a Leapsome otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele leapsome, musíte vybrat všechny platné role specifické pro aplikaci (pokud jsou k dispozici) v dialogovém okně přiřazení. Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.


## <a name="setup-leapsome-for-provisioning"></a>Nastavení Leapsome pro zřizování

1. Přihlaste se ke své [Administrátorské konzoli Leapsome](https://www.Leapsome.com/app/#/login). Přejděte do **nastavení nastavení > nastavení správce**.

    ![Leapsome Admin Console](media/Leapsome-provisioning-tutorial/leapsome-admin-console.png)

2.  Přejděte do **integrace > zřizování uživatelů SCIM**.

    ![Leapsome Přidat SCIM](media/Leapsome-provisioning-tutorial/leapsome-add-scim.png)

3.  Zkopírujte **ověřovací token SCIM**. Tato hodnota se zadá do pole Tajný token na kartě Zřizování vaší aplikace Leapsome na webu Azure Portal.

    ![Leapsome vytvořit token](media/Leapsome-provisioning-tutorial/leapsome-create-token.png)

## <a name="add-leapsome-from-the-gallery"></a>Přidat Leapsome z galerie

Před konfigurací Leapsome pro automatické zřizování uživatelů s Azure AD, je potřeba přidat Leapsome z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Leapsome z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Leapsome**, vyberte **Leapsome** v panelu výsledků a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

    ![Leapsome v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-leapsome"></a>Konfigurace automatického zřizování uživatelů na Leapsome 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Leapsome na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování na saml pro Leapsome , podle pokynů uvedených v [leapsome jednotném přihlašování .](Leapsome-tutorial.md) Jednotné přihlašování lze konfigurovat nezávisle na automatickézřivací službě uživatelů, i když tyto dvě funkce se vzájemně doplňují

### <a name="to-configure-automatic-user-provisioning-for-leapsome-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Leapsome ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Leapsome**.

    ![Odkaz Leapsome v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://www.leapsome.com/api/scim` zadejte adresu **URL klienta**. Zadejte hodnotu **ověřovacího tokenu SCIM** načtenou dříve v **tokenu tajného klíče**. Klikněte na **Test připojení** a ujistěte se, že Azure AD můžete připojit k Leapsome. Pokud se připojení nezdaří, ujistěte se, že váš účet Leapsome má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části Mapování vyberte **Synchronizovat uživatele služby Azure Active Directory s leapsome**. **Mappings**

    ![Přeskočí mapování uživatelů](media/Leapsome-provisioning-tutorial/Leapsome-user-mappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Leapsome v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v Leapsome pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Přeskočí uživatelské atributy](media/Leapsome-provisioning-tutorial/Leapsome-user-attributes.png)

10. V části **Mapování** vyberte **Synchronizovat skupiny Služby Azure Active Directory na Leapsome**.

    ![Mapování skupiny Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-group-mappings.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na Leapsome v části **Mapování atributů.** Atributy vybrané jako **Odpovídající** vlastnosti se používají k porovnání skupin v Leapsome pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy skupiny Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-group-attributes.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro Leapsome, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit Leapsome výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Leapsome.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektorů

* Leapsome vyžaduje **userName** být jedinečný.
* Leapsome umožňuje pouze ukládání pracovních e-mailových adres.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

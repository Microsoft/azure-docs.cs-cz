---
title: 'Kurz: Konfigurace iProvy pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty pro iProva.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 4edba747-242d-4795-9539-649f33af4c13
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: bb730bad2837616aee0ebfa2da04015542782d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057496"
---
# <a name="tutorial-configure-iprova-for-automatic-user-provisioning"></a>Kurz: Konfigurace iProvy pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v iProva a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-provision uživatelů a/nebo skupin pro iProva.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Klient iProva](https://www.iProva.com/)
* Uživatelský účet v iProva s oprávněními správce.

## <a name="assigning-users-to-iprova"></a>Přiřazení uživatelů k iPrově

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k iProva. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny iProva podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-iprova"></a>Důležité tipy pro přiřazení uživatelů k iProva

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a iProva otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k iPrově musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="set-up-iprova-for-provisioning"></a>Nastavení iProvy pro zřizování

1. Přihlaste se ke [konzoli iProva Admin Console](https://www.iProva.com/). Přejděte **na Přejít na > správu aplikací**.

    ![Konzola pro správu iProva](media/iprova-provisioning-tutorial/admin.png)

2.  Klikněte na **Externí správa uživatelů**.

    ![iProva Přidat SCIM](media/iprova-provisioning-tutorial/external.png)

3. Chcete-li přidat nového poskytovatele, klikněte na ikonu **plus.** V novém dialogovém okně **Přidat zprostředkovatele** zadejte **název**. Můžete přidat **omezení přístupu založené na protokolu IP**. Klikněte na tlačítko **OK.**

    ![iProva přidat nové](media/iprova-provisioning-tutorial/add.png)

    ![IProva přidat zprostředkovatele](media/iprova-provisioning-tutorial/addprovider.png)

4.  Klikněte na trvalé tlačítko **tokenu.** Zkopírujte **trvalý token** a uložte jej, protože to bude jediný čas, kdy jej můžete zobrazit. Tato hodnota se zadá do pole Tajný token na kartě Zřizování vaší aplikace iProva na webu Azure Portal.

    ![vytvořit token iProva](media/iprova-provisioning-tutorial/token.png)

## <a name="add-iprova-from-the-gallery"></a>Přidání iProvy z galerie

Před konfigurací iProva pro automatické zřizování uživatelů pomocí Azure AD, je potřeba přidat iProva z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat iProva z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **iProva**, vyberte **iProva** v panelu výsledků a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![iProva v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-iprova"></a>Konfigurace automatického zřizování uživatelů pro iProva 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v iPrově na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit saml-založené jednotné přihlašování pro iProva podle pokynů uvedených v [iProva jednotné přihlášení tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/iProva-tutorial). Jednotné přihlašování lze konfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce se vzájemně doplňují.

### <a name="to-configure-automatic-user-provisioning-for-iprova-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro iProva ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **iProva**.

    ![Odkaz iProva v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://identitymanagement.services.iProva.nl/scim` zadejte adresu **URL klienta**. Zadejte hodnotu **trvalého tokenu** načtenou dříve v **tajném tokenu**. Klikněte na **Testovat připojení** a ujistěte se, že Azure AD můžete připojit k iProva. Pokud se připojení nezdaří, ujistěte se, že váš účet iProva má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s iProvou**.

    ![Mapování uživatelů iProva](media/iprova-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na iProva v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v iProva pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy iProva](media/iprova-provisioning-tutorial/userattributes.png)

10. V části **Mapování** vyberte **Synchronizovat skupiny Služby Active Directory Azure s iProvou**.

    ![Mapování skupin y iProva](media/iprova-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na iProva v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v iProva pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy skupiny iProva](media/iprova-provisioning-tutorial/groupattributes.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro iProva, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit iProva výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na iPrově.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)


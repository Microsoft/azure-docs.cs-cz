---
title: 'Kurz: Konfigurace souboru SmartFile pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty do souboru SmartFile.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eeff992-a84f-4f88-a360-9accbd077538
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: b113cc27195b2ce954d677ab0f1ec83e394946be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060214"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>Kurz: Konfigurace souboru SmartFile pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v SmartFile a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-provision uživatelů nebo skupin na SmartFile.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Tenanta Azure AD.
* [Klient smartfile](https://www.SmartFile.com/pricing/).
* Uživatelský účet v souboru SmartFile s oprávněními správce.

## <a name="assigning-users-to-smartfile"></a>Přiřazení uživatelů k souboru SmartFile

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k smartfile. Jakmile se rozhodnete, můžete těmto uživatelům a/nebo skupinám přiřadit program SmartFile podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>Důležité tipy pro přiřazení uživatelů k souboru SmartFile

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k SmartFile k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k souboru SmartFile je nutné v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="setup-smartfile-for-provisioning"></a>Instalace inteligentního souboru pro zřizování

Před konfigurací SmartFile pro automatické zřizování uživatelů pomocí Azure AD, budete muset povolit zřizování SCIM na SmartFile a shromažďovat další potřebné podrobnosti.

1. Přihlaste se do konzole SmartFile Admin Console. Přejděte do pravého horního rohu konzole SmartFile Admin Console. Vyberte **kód Product Key**.

    ![Konzola pro správu souboru SmartFile](media/smartfile-provisioning-tutorial/login.png)

2. Chcete-li vygenerovat nosný token, zkopírujte **kód Product Key** a heslo **produktu**. Vložte je do poznámkového bloku s dvojtečkou mezi nimi.
    
     ![Přidání scim smartfile](media/smartfile-provisioning-tutorial/auth.png)

    ![Přidání scim smartfile](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>Přidání smartfile z galerie

Chcete-li nakonfigurovat SmartFile pro automatické zřizování uživatelů pomocí Azure AD, je třeba přidat SmartFile z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat SmartFile z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SmartFile**, vpanelu výsledky vyberte **SmartFile** a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![SmartFile v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>Konfigurace automatického zřizování uživatelů do souboru SmartFile 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v smartfile na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování na saml pro SmartFile podle pokynů uvedených v [kurzu smartfile jednotné přihlášení](SmartFile-tutorial.md). Jednotné přihlašování lze konfigurovat nezávisle na automatickézřivací službě uživatelů, i když tyto dvě funkce se vzájemně doplňují

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro SmartFile ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **SmartFile**.

    ![Odkaz SmartFile v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5.  V části **Pověření správce** `https://<SmartFile sitename>.smartfile.com/ftp/scim` zadejte adresu **URL klienta**. Příklad by vypadal `https://demo1test.smartfile.com/ftp/scim`jako . Zadejte hodnotu **tokenu nosiče** (ProductKey:ProductPassword), kterou jste načetli dříve v **tajném tokenu**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k SmartFile. Pokud se připojení nezdaří, ujistěte se, že váš účet SmartFile má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s programem SmartFile**.

    ![Mapování uživatelů smartfile](media/smartfile-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na SmartFile v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v souboru SmartFile pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy smartfile](media/smartfile-provisioning-tutorial/userattribute.png)

10. V části **Mapování** vyberte **Synchronizovat skupiny Služby Azure Active Directory s programem SmartFile**.

    ![Mapování skupin smartfile](media/smartfile-provisioning-tutorial/groupmapping.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na SmartFile v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v souboru SmartFile pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy skupiny smartfile](media/smartfile-provisioning-tutorial/groupattribute.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro SmartFile, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro smartfile výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

    Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na SmartFile.

    Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v [tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Omezení konektoru

* Soubor SmartFile podporuje pouze pevné odstranění. 

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

 [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

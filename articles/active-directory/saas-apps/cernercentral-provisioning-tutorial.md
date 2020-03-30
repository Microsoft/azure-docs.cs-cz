---
title: 'Kurz: Zřizování uživatelů pro Cerner Central – Azure AD'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala uživatele do seznamu v aplikaci Cerner Central.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ed04d8fdcc2d79c66e2ebc53c737c78664e4621
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058312"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Kurz: Konfigurace aplikace Cerner Central pro automatické zřizování uživatelů

Cílem tohoto kurzu je ukázat kroky, které je potřeba provést v Cerner Central a Azure AD automaticky zřídit a de-zřizování uživatelských účtů z Azure AD do seznamu uživatelů v Cerner Central.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

* Tenanta Azure Active Directory.
* Nájemce Cerner Central

> [!NOTE]
> Azure Active Directory se integruje s Cerner Central pomocí protokolu [SCIM.](http://www.simplecloud.info/)

## <a name="assigning-users-to-cerner-central"></a>Přiřazení uživatelů k cerner central

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů jsou synchronizováni pouze uživatelé a skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD. 

Před konfigurací a povolením zřizovací služby byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k Cerner Central. Jakmile se rozhodnete, můžete tyto uživatele přiřadit společnosti Cerner Central podle pokynů zde:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Důležité tipy pro přiřazení uživatelů do cerner central

* Doporučuje se, aby jeden uživatel Azure AD přiřazen a Cerner Central otestovat konfiguraci zřizování. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Po dokončení počátečního testování pro jednoho uživatele společnost Cerner Central doporučuje přiřadit celý seznam uživatelů určených pro přístup k jakémukoli řešení Cerner (nejen Cerner Central), které má být zřízeno do seznamu uživatelů společnosti Cerner.  Ostatní řešení Cerner využívají tento seznam uživatelů v seznamu uživatelů.

* Při přiřazování uživatele k cernercentralu musíte v dialogovém okně přiřazení vybrat roli **Uživatele.** Uživatelé s rolí "Výchozí přístup" jsou z zřizování vyloučeni.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Konfigurace zřizování uživatelů do aplikace Cerner Central

Tato část vás provede připojením azure ad k seznamu uživatelů cerner central pomocí rozhraní API pro zřizování uživatelského účtu SCIM společnosti Cerner a konfigurací zřizovací služby pro vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v aplikaci Cerner Central na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování na saml pro Cerner Central podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce se vzájemně doplňují. Další informace naleznete v [kurzu jednotného přihlašování Cerner Central](cernercentral-tutorial.md).

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů pro Cerner Central ve službě Azure AD:

Chcete-li zřídit uživatelské účty pro Cerner Central, budete muset požádat o systémový účet Cerner Central od společnosti Cerner a vygenerovat token nosiče OAuth, který azure ad můžete použít pro připojení k koncovému bodu SCIM Cerner. Doporučuje se také provést integraci v prostředí izolovaného prostoru Cerner před nasazením do produkčního prostředí.

1. Prvním krokem je zajistit, aby uživatelé, kteří spravují integraci Cerner a Azure AD, měli účet CernerCare, který je nutný pro přístup k dokumentaci potřebné k dokončení pokynů. V případě potřeby použijte níže uvedené adresy URL k vytvoření účtů CernerCare v každém příslušném prostředí.

   * Pískoviště:https://sandboxcernercare.com/accounts/create

   * Výroby:https://cernercare.com/accounts/create  

2. Dále musí být vytvořen systémový účet pro Azure AD. Pomocí níže uvedených pokynů vyžádejte si systémový účet pro izolované služby a produkční prostředí.

   * Pokyny:https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Pískoviště:https://sandboxcernercentral.com/system-accounts/

   * Výroby:https://cernercentral.com/system-accounts/

3. Dále vygenerujte token nosiče OAuth pro každý z vašich systémových účtů. Chcete-li to provést, postupujte podle následujících pokynů.

   * Pokyny:https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Pískoviště:https://sandboxcernercentral.com/system-accounts/

   * Výroby:https://cernercentral.com/system-accounts/

4. Nakonec musíte získat ID sféry soupisek uživatelů pro sandbox i produkční prostředí v Cerneru k dokončení konfigurace. Informace o tom, jak získat https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIMtento, naleznete v této adrese: . 

5. Teď můžete nakonfigurovat Azure AD zřídit uživatelské účty cerner. Přihlaste se k [portálu Azure](https://portal.azure.com)a přejděte do části **Azure Active Directory > Enterprise Apps > všechny aplikace.**

6. Pokud jste již nakonfigurovali cerner central pro jednotné přihlašování, vyhledejte svou instanci Cerner Central pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **cerner central** v galerii aplikací. Ve výsledcích hledání vyberte Cerner Central a přidejte ji do seznamu aplikací.

7. Vyberte svou instanci Cerner Central a pak vyberte kartu **Zřizování.**

8. Nastavte **režim zřizování** na **automatické**.

   ![Centrální zřizování Cerneru](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Vyplňte následující pole v části **Přihlašovací údaje správce**:

   * Do pole **Adresa URL klienta** zadejte adresu URL v níže uvedeném formátu, která nahradí "User-Roster-Realm-ID" id sféry, které jste získali v kroku #4.

    > Pískoviště:https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Výroby:https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * Do pole **Tajný token** zadejte nosný token OAuth, který jste vygenerovali v kroku #3 a klepněte na tlačítko **Testovat připojení**.

   * Oznámení o úspěchu byste měli vidět na pravé horní straně portálu.

1. Do pole **E-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko níže.

1. Klikněte na **Uložit**.

1. V části **Mapování atributů** zkontrolujte atributy uživatele a skupiny, které mají být synchronizovány z Azure AD na Cerner Central. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům a skupinám v cerner central pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko Uložit.

1. Chcete-li povolit službu zřizování Azure AD pro Cerner Central, změňte **stav zřizování** **na Zapnuto** v části **Nastavení**

1. Klikněte na **Uložit**.

Tím se spustí počáteční synchronizace všech uživatelů nebo skupin přiřazených společnosti Cerner Central v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na protokoly zřizování aktivit, které popisují všechny akce prováděné službou zřizování v aplikaci Cerner Central.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Cerner Central: Publikování dat identit pomocí Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Kurz: Konfigurace aplikace Cerner Central pro jednotné přihlašování pomocí služby Azure Active Directory](cernercentral-tutorial.md)
* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

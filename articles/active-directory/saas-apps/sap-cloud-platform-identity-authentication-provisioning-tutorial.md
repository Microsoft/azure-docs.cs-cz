---
title: 'Kurz: Konfigurace ověřování identity cloudové platformy SAP pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a odpočává uživatelské účty pro ověřování identity cloudové platformy SAP.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f49b5738-c769-403b-8f29-84ddeea7fbf1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: c30a7b1e6440cf69f7a4858273b365d885e5ec7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060415"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Kurz: Konfigurace ověřování identity cloudové platformy SAP pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v SAP Cloud Platform Identity Authentication a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-provision uživatelů a/nebo skupin sap cloudové platformy identity ověřování.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Tenant ověřování identity cloudové platformy SAP](https://cloudplatform.sap.com/pricing.html)
* Uživatelský účet v azure cloud platformy identity authentication s oprávněními správce.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Přiřazení uživatelů k ověřování identity cloudové platformy SAP

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k ověřování identity cloudové platformy SAP. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny sap cloudové platformy identity ověřování podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Důležité tipy pro přiřazení uživatelů k ověřování identity cloudové platformy SAP

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k SAP Cloud Platform Identity Authentication k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k ověřování identity cloudové platformy SAP je nutné v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>Nastavení ověřování identity cloudové platformy SAP pro zřizování

1. Přihlaste se ke [konzoli pro správu ověřování identity cloudové platformy SAP](https://sapmsftintegration.accounts.ondemand.com/admin). Přejděte na **uživatelé & oprávnění > správci**.

    ![Konzola pro správu ověřování identity cloudové platformy SAP](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Vytvořte uživatele správce a vyberte ho.  

3.  V části Konfigurovat autorizace zapněte přepínací tlačítko proti **možnosti Spravovat uživatele** a **Spravovat skupiny**.

    ![Ověřování identity cloudové platformy SAP Přidání SCIM](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. Obdržíte e-mail pro aktivaci svého účtu a nastavit heslo pro **SLUŽBU ověřování identity cloudové platformy SAP**.

4.  Zkopírujte **ID uživatele** a **heslo**. Tyto hodnoty se zadají do polí Uživatelské jméno správce a Heslo správce v kartě Zřizování aplikace SAP Cloud Platform Identity Authentication na webu Azure Portal.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Přidání ověřování identity cloudové platformy SAP z galerie

Před konfigurací SAP Cloud Platform Identity Authentication pro automatické zřizování uživatelů pomocí Azure AD, je třeba přidat SAP Cloud Platform Identity Authentication z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat ověřování identity cloudové platformy SAP z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ověřování identity cloudové platformy SAP**, v panelu výsledků vyberte **SAP Cloud Platform Identity Authentication** a pak kliknutím na tlačítko **Přidat** aplikaci přidejte.

    ![Ověřování identity cloudové platformy SAP v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Konfigurace automatického zřizování uživatelů pro ověřování identity cloudové platformy SAP 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v sap cloudové platformě Identity Authentication na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování založené na SAML pro ověřování identity cloudové platformy SAP podle pokynů uvedených v [kurzu jednotného přihlášení ověřování identity cloudové platformy SAP](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial). Jednotné přihlašování lze konfigurovat nezávisle na automatickézřivací službě uživatelů, i když tyto dvě funkce se vzájemně doplňují

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro ověřování identity cloudové platformy SAP ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **SAP Cloud Platform Identity Authentication**.

    ![Odkaz ověřování identity cloudové platformy SAP v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://<tenantID>.accounts.ondemand.com/service/scim ` zadejte adresu **URL klienta**. Zadejte hodnoty **ID uživatele** a **hesla** načtené dříve v **uživatelském jménu správce** a **hesle správce.** Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k ověřování identity cloudové platformy SAP. Pokud se připojení nezdaří, ujistěte se, že váš účet SAP Cloud Platform Identity Authentication má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s ověřováním identity cloudové platformy SAP**.

    ![Mapování uživatelů ověřování identity cloudové platformy SAP](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na SAP Cloud Platform Identity Authentication v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v SAP Cloud Platform Identity Authentication pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy uživatele ověřování identity cloudové platformy SAP](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Chcete-li povolit službu zřizování Azure AD pro ověřování identity cloudové platformy SAP, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit ověřování identity cloudové platformy SAP, výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

13. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v ověřování identity cloudové platformy SAP.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Koncový bod SCIM ověřování identity cloudové platformy SAP vyžaduje, aby určité atributy měly určitý formát. Můžete vědět více o těchto atributů a jejich konkrétní formát [zde](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)


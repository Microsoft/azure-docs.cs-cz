---
title: 'Kurz: Konfigurace ověřování identity v cloudové platformě SAP pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřizování uživatelských účtů pro ověřování identity cloudové platformy SAP.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: b3fa2996edf5882cc02eeee92bcc3114bcd33348
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273411"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Kurz: Konfigurace ověřování identity cloudové platformy SAP pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést v rámci ověřování identity cloudové platformy SAP a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD tak, aby automaticky zřídily a zrušily zřizování uživatelů a/nebo skupin pro ověřování identity cloudových platforem SAP.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant ověřování identity cloudové platformy SAP](https://cloudplatform.sap.com/pricing.html)
* Uživatelský účet v rámci ověřování identity cloudové platformy SAP s oprávněními správce.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Přiřazení uživatelů k ověřování identity cloudové platformy SAP

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k ověřování identity cloudové platformy SAP. Jakmile se rozhodnete, můžete těmto uživatelům nebo skupinám přiřadit ověřování identity cloudové platformy pomocí následujících pokynů:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Důležité tipy pro přiřazení uživatelů k ověřování identity cloudové platformy SAP

* Doporučujeme, aby se k otestování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD k ověřování identity cloudové platformy SAP. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k ověřování identity cloudové platformy SAP musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>Nastavení ověřování identity pro cloudovou platformu SAP pro zřizování

1. Přihlaste se ke [konzole pro správu ověřování identity cloudové platformy SAP](https://sapmsftintegration.accounts.ondemand.com/admin). Přejděte na **uživatele & autorizací > správce**.

    ![Konzola pro správu ověřování identity cloudové platformy SAP](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Kliknutím na tlačítko **+ Přidat** na levém panelu přidejte nového správce do seznamu. Vyberte **Přidat systém** a zadejte název systému.   

> [!NOTE]
> Uživatel pole v ověřování identity cloudové platformy SAP musí být typu **System**. Vytvoření normálního uživatele správce může vést k *neautorizovaným* chybám při zřizování.   

3.  V části konfigurovat autorizace přepněte na přepínací tlačítko u **možnosti spravovat uživatele** a **Spravovat skupiny**.

    ![Ověřování identity cloudové platformy SAP přidat SCIM](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. Po aktivaci účtu obdržíte e-mail a nastavíte heslo pro **službu ověřování identity cloudové platformy SAP**.

4.  Zkopírujte **ID uživatele** a **heslo**. Tyto hodnoty se zadají do polí uživatelské jméno správce a heslo správce v uvedeném pořadí na kartě zřizování aplikace ověřování identity cloudové platformy SAP v Azure Portal.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Přidání ověřování identity cloudové platformy SAP z Galerie

Před konfigurací ověřování identity cloudové platformy SAP pro Automatické zřizování uživatelů se službou Azure AD je nutné přidat ověřování identity cloudové platformy SAP z Galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat ověřování identity cloudové platformy SAP z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAP Cloud Platform ověřování identity**, na panelu výsledků vyberte **ověřování identity cloudové platformy SAP** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Ověřování identity cloudové platformy SAP v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Konfigurace automatického zřizování uživatelů pro ověřování identity cloudové platformy SAP 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v tématu ověřování identity cloudových platforem SAP na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro ověřování identity cloudové platformy SAP podle pokynů uvedených v [kurzu pro jednotné přihlašování pomocí služby SAP Cloud Platform ověřování identity](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce vzájemně přidávají.

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro ověřování identity cloudové platformy SAP v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **SAP Cloud Platform ověřování identity ověřování**.

    ![Odkaz na ověřování identity cloudové platformy SAP v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://<tenantID>.accounts.ondemand.com/service/scim ` **adresu URL tenanta**. Zadejte hodnoty **ID uživatele** a **hesla** načtené dříve v **uživatelském jménu správce** a v části **heslo správce** . Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k ověřování identity cloudové platformy SAP. Pokud se připojení nepovede, ujistěte se, že váš účet pro ověřování identity cloudové platformy SAP má oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatelů pro ověřování identity cloudové platformy SAP**.

    ![Mapování uživatelů pro ověřování identity v cloudové platformě SAP](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Zkontrolujte atributy uživatele, které se synchronizují z Azure AD do ověřování identity cloudové platformy SAP v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v ověřování identity cloudové platformy SAP pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele ověřování identity pro cloudovou platformu SAP](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu zřizování Azure AD pro ověřování identity cloudové platformy SAP, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

12. Určete uživatele nebo skupiny, které chcete zřídit pro ověřování identity cloudové platformy SAP, a to výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD při ověřování identity cloudové platformy SAP.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Koncový bod SCIM ověřování identity cloudové platformy SAP vyžaduje, aby určité atributy byly specifického formátu. Další informace o těchto atributech a jejich konkrétním formátu můžete získat [tady](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).


---
title: 'Kurz: Konfigurace Správce hesel & digitálního trezoru pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se, jak nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů k udržování správce hesel & digitálního trezoru.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 2670dc0cb56805a2afa966bee1d2aa52b6c8e46a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358971"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Kurz: Konfigurace Správce hesel & digitálního trezoru pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v nástroji & Digital trezor a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD tak, aby automaticky zřídily a zrušily zřizování uživatelů a skupin správců hesel & digitálního trezoru.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Správce &ového hesla – tenant služby digitálního trezoru](https://keepersecurity.com/pricing.html?t=e)
* Uživatelský účet v nástroji Správce hesel služby & Digital trezor s oprávněními správce.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Přidání správce hesel & digitálního trezoru z Galerie

Než začnete konfigurovat Správce hesel a & digitálního trezoru pro Automatické zřizování uživatelů pomocí Azure AD, musíte do svého seznamu spravovaných aplikací v SaaS přidat & digitálního trezoru z Galerie aplikací Azure AD.

**Pokud chcete přidat správce hesel & digitálního trezoru z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Keeper Password manager & Digital trezor**, v panelu výsledků vyberte možnost **správce hesel & digitální trezor** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Správce hesel & digitálního trezoru v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Přiřazení uživatelů k programu Správce hesel & digitálního trezoru

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k & digitálního trezoru správce hesel. Po rozhodnutí můžete těmto uživatelům nebo skupinám přiřadit tyto uživatele a skupiny, aby správce hesel & digitální trezor, a to podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Důležité tipy pro přiřazení uživatelů k programu Správce hesel & digitálního trezoru

* Doporučuje se, aby se k otestování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD k & digitálního trezoru správce hesel. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele k programu Správce hesel & digitálního trezoru, musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Konfigurace automatického zřizování uživatelů do programu Správce hesel & digitálního trezoru 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů a/nebo skupin v nástroji Správce hesel & digitálních trezorů na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro & digitálního trezoru pomocí protokolu SAML, a to podle pokynů uvedených v [kurzu správce hesel keep & v rámci jednotného přihlašování k digitálnímu trezoru](keeperpasswordmanager-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro správce uživatelských hesel & digitálního trezoru ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **správce hesel úložiště & digitální trezor**.

    ![Odkaz na digitální trezor & správce hesel v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta** a **tajný Token** vašeho správce hesel správce &, jak je popsáno v kroku 6.

6. Přihlaste se ke [konzole správce udržování](https://keepersecurity.com/console/#login). Klikněte na **správce** a vyberte existující uzel nebo vytvořte nový. Přejděte na kartu **zřizování** a vyberte **Přidat metodu**.

    ![Konzola správce Keep](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Vyberte **SCIM (systém pro správu identit mezi doménami**).

    ![Přidání SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Klikněte na **vytvořit zřizovací token**.

    ![Koncový bod pro zachování a vytvoření](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Zkopírujte hodnoty pro **URL** a **tokeny** a vložte je do **adresy URL tenanta** a **tajného tokenu** ve službě Azure AD. Kliknutím na **Uložit** dokončete nastavení zřizování na zachovat.

    ![Vytvořit token pro uchování](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Po vyplnění polí zobrazených v kroku 5 klikněte na **Test připojení** . tím zajistíte, aby se služba Azure AD mohla připojit k programu správce hesel & digitálnímu trezoru. Pokud se připojení nepovede, zajistěte, aby měl účet správce hesel & účtu digitálního trezoru oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatelů a zachovejte správce hesel & digitálního trezoru**.

    ![Mapování uživatelů pro udržování](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Zkontrolujte atributy uživatelů, které jsou synchronizované z Azure AD, a zachovejte správce hesel & digitálního trezoru v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v nástroji Správce hesel služby & Digital trezor pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy zachovat uživatele](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do programu Správce hesel & digitálního trezoru**.

    ![Mapování skupin pro udržování](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD, a udržujte si správce hesel & digitálního trezoru v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v nástroji Správce hesel keeper & digitálního trezoru pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy skupiny Keep](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pokud chcete povolit službu Azure AD Provisioning pro správce udržování hesel & digitálních trezorů, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

16. Určete uživatele nebo skupiny, které chcete zřídit pro správce hesel & digitálního trezoru, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **rozsahu** .

    ![Rozsah zřizování](common/provisioning-scope.png)

17. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou Azure AD Provisioning pomocí správce hesel & digitálního trezoru.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Správce hesel služby & Digital trezor vyžaduje, aby **e-maily** a **uživatelské jméno** měly stejnou zdrojovou hodnotu, protože jakékoli aktualizace obou atributů změní jinou hodnotu.
* Správce hesel služby & Digital trezor nepodporuje odstranění uživatelů, pouze zakázat. Zakázaní uživatelé se v uživatelském rozhraní konzoly správce uchovává jako uzamčené.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).


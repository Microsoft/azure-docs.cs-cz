---
title: 'Kurz: Konfigurace Oracle Fusion ERP pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro Automatické zřizování a rušení uživatelských účtů pro Oracle Fusion ERP.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: da6e1a8ba31f8f4991bde4803191598a015a68b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358435"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Kurz: Konfigurace Oracle Fusion ERP pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést v databázi Oracle Fusion ERP a Azure Active Directory (Azure AD), a nakonfigurovat tak, aby služba Azure AD automaticky zřídila a zrušila zřizování uživatelů a skupin pro Oracle Fusion ERP.

> [!NOTE]
>  Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je momentálně ve verzi Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure Preview.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant Oracle Fusion ERP](https://www.oracle.com/applications/erp/).
* Uživatelský účet v aplikaci Oracle Fusion ERP s oprávněními správce.

## <a name="assign-users-to-oracle-fusion-erp"></a>Přiřazení uživatelů k databázi Oracle Fusion ERP 
Azure Active Directory používá koncept nazvaný přiřazení k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k databázi Oracle Fusion ERP. Po rozhodnutí můžete přiřadit tyto uživatele a skupiny do Oracle Fusion ERP podle pokynů uvedených tady:
 
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Důležité tipy pro přiřazení uživatelů k databázi Oracle Fusion ERP 

 * Doporučuje se, aby se jeden uživatel Azure AD přiřadil k platformě Oracle Fusion ERP, aby otestoval automatickou konfiguraci zřizování uživatelů. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele do Oracle Fusion ERP musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s výchozí rolí přístupu se z zřizování vylučují.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Nastavení pro zřizování pro Oracle Fusion ERP

Před konfigurací Oracle Fusion ERP pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit zřizování SCIM pro Oracle Fusion ERP.

1. Přihlaste se ke [konzole pro správu nástroje Oracle Fusion ERP](https://cloud.oracle.com/sign-in)

2. Klikněte na navigátor v levém horním rohu. V části **nástroje** vyberte **Konzola zabezpečení**.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/login.png" alt-text="Snímek stránky navigátoru v konzole pro správu Oracle Fusion E R P Nástroje a konzola zabezpečení jsou zvýrazněné." border="false":::

3. Přejděte na **uživatele**.
    
    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user.png" alt-text="Snímek obrazovky s panelem v konzole pro správu Oracle Fusion E R P Položka uživatele je zvýrazněna." border="false":::

4. Uložte uživatelské jméno a heslo pro uživatelský účet správce, který použijete k přihlášení do konzoly pro správu nástroje Oracle Fusion ERP. Tyto hodnoty je třeba zadat do polí **uživatelské jméno** a **heslo** správce na kartě zřizování aplikace Oracle Fusion ERP v Azure Portal.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Přidání databáze Oracle Fusion ERP z Galerie

Pokud chcete nakonfigurovat Azure Fusion ERP pro Automatické zřizování uživatelů pomocí Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Azure Fusion ERP z Galerie aplikací Azure AD.

**Pokud chcete přidat Azure Fusion ERP z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Oracle Fusion ERP** a na panelu výsledků vyberte **Oracle Fusion ERP** .

    ![Oracle Fusion ERP v seznamu výsledků](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Konfigurace automatického zřizování uživatelů pro Oracle Fusion ERP 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů a/nebo skupin v Oracle Fusion ERP na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Oracle Fusion ERP podle pokynů uvedených v [kurzu Oracle Fusion ERP jednotného přihlašování](oracle-fusion-erp-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

> [!NOTE]
> Další informace o koncovém bodu služby Oracle Fusion pro SCIM najdete [v tématu REST API pro běžné funkce v cloudu pro aplikace Oracle](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html).

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Fuze ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Oracle Fusion ERP**.

    ![Odkaz Oracle Fusion ERP v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` **adresu URL tenanta**. Zadejte uživatelské jméno a heslo správce dříve načtené do polí **uživatelské jméno** a **heslo** správce. Klikněte na **Testovat připojení** mezi Azure AD a Oracle Fusion ERP. 

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/admin.png" alt-text="Snímek obrazovky oddílu přihlašovací údaje správce K dispozici je tlačítko Test Connection a pole pro klienta U R L, uživatelské jméno správce a heslo správce." border="false":::

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele do Oracle Fusion ERP**.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png" alt-text="Snímek obrazovky oddílu mapování V části název je synchronizace Azure Active Directory uživatelů do Oracle Fusion E R P viditelná." border="false":::

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Oracle Fusion ERP v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Oracle Fusion ERP pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png" alt-text="Snímek obrazovky se stránkou mapování atributů. Tabulka obsahuje seznam atributů Azure Active Directory a Oracle Fusion E R P a Priorita porovnání." border="false":::

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do Oracle Fusion ERP**.

    ![Mapování skupin ERP pro Oracle Fusion](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do Oracle Fusion ERP v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v databázi Oracle Fusion ERP pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy skupiny ERP pro Oracle Fusion](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro Oracle Fusion ERP, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro Oracle Fusion ERP, a to tak, že v části **Nastavení** vyberete požadované hodnoty v poli **Rozsah** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

    Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Část s **podrobnostmi o synchronizaci** můžete použít ke sledování průběhu a následného odkazu na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Oracle Fusion ERP.

    Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Řešení Oracle Fusion ERP podporuje pro svůj koncový bod SCIM jenom základní ověřování.
* Oracle Fusion ERP nepodporuje zřizování skupin.
* Role v Oracle Fusion ERP se mapují na skupiny v Azure AD. Pokud chcete přiřadit role uživatelům ve službě Oracle Fusion ERP ze služby Azure AD, budete muset přiřadit uživatele k požadovaným skupinám Azure AD, které se pojmenují po rolích v Oracle Fusion ERP.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

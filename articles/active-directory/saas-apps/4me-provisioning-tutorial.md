---
title: 'Kurz: Konfigurace 4me pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro 4me.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: c0c428997cfba8871a29d9bfe0df0a6920a1d22f
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357585"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Kurz: Konfigurace 4me pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v 4me a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro 4me.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant 4me](https://www.4me.com/trial/)
* Uživatelský účet v 4me s oprávněními správce.

## <a name="add-4me-from-the-gallery"></a>Přidání 4me z Galerie

Před konfigurací 4me pro Automatické zřizování uživatelů se službou Azure AD je nutné přidat 4me z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat 4me z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **4me** , na panelu výsledků vyberte **4me** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![4me v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Přiřazování uživatelů k 4me

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k 4me. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k 4me podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Důležité tipy pro přiřazení uživatelů k 4me

* Doporučuje se, aby se k 4me k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k 4me musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Konfigurace automatického zřizování uživatelů na 4me 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v 4me na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro 4me podle pokynů uvedených v [kurzu 4me jednotného přihlašování](4me-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro 4me ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **4me**.

    ![Odkaz 4me v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. Pokud chcete načíst **adresu URL tenanta** a **tajný token** účtu 4me, postupujte podle návodu popsaného v kroku 6.

6. Přihlaste se ke konzole pro správu 4me. Přejděte na **Nastavení**.

    ![Nastavení 4me](media/4me-provisioning-tutorial/4me01.png)

    Do vyhledávacího panelu zadejte **aplikace** .

    ![aplikace 4me](media/4me-provisioning-tutorial/4me02.png)

    Otevřete rozevírací seznam **SCIM** pro načtení tajného tokenu a koncového bodu SCIM.

    ![4me SCIM](media/4me-provisioning-tutorial/4me03.png)

7. Po vyplnění polí zobrazených v kroku 5 klikněte na **Test připojení** , aby se služba Azure AD mohla připojit k 4me. Pokud se připojení nepovede, ujistěte se, že má váš účet 4me oprávnění správce, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé 4me**.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-user-mapping.png" alt-text="Snímek obrazovky se stránkou mapování V části název je zvýrazněná položka synchronizovat Azure Active Directory uživatelů do FourMe." border="false":::
    
11. Zkontrolujte atributy uživatele synchronizované z Azure AD do 4me v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v 4me pro operace aktualizace. Ujistěte se prosím, že [4me podporuje filtrování](https://developer.4me.com/v1/scim/users/) u odpovídajícího atributu, který jste zvolili. Kliknutím na tlačítko **Uložit** potvrďte změny.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-user-attributes.png" alt-text="Snímek obrazovky se stránkou mapování atributů. Tabulka obsahuje seznam atributů Azure Active Directory, odpovídající atributy FourMe a stav pro porovnání." border="false":::
    
12. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do 4me**.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-group-mapping.png" alt-text="Snímek obrazovky se stránkou mapování V části název se zvýrazní synchronizace skupin Azure Active Directory FourMe." border="false":::
    
13. Zkontrolujte atributy skupiny synchronizované z Azure AD do 4me v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v 4me pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Mapování skupin 4me](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pokud chcete povolit službu Azure AD Provisioning pro 4me, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

16. Definujte uživatele nebo skupiny, které chcete zřídit pro 4me, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

17. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v 4me.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* 4me má různé adresy URL koncových bodů SCIM pro testovací a produkční prostředí. Předchozí končí na **. QA** , zatímco druhý končí na **. com.**
* 4me vygenerované tajné tokeny mají datum vypršení platnosti v měsíci od generace.
* 4me nepodporuje operace **odstranění** .

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

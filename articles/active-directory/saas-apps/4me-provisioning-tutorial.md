---
title: 'Kurz: Konfigurace 4me pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do 4me.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: zchia
ms.openlocfilehash: 0cfd760eb9d631bf6ab098afef0ef9b66c92cfa6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67168152"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Kurz: Konfigurace 4me pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v 4me a Azure Active Directory (Azure AD) ke konfiguraci Azure AD, aby automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se 4me.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných Microsoft Azure podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

* Klient služby Azure AD
* [4me tenanta](https://www.4me.com/trial/)
* Uživatelský účet v 4me s oprávněními správce.

## <a name="add-4me-from-the-gallery"></a>Přidání 4me z Galerie

Před konfigurací 4me pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat 4me z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat 4me z Galerie aplikací Azure AD, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **4me**vyberte **4me** panel výsledků a pak klikněte na **přidat** tlačítko pro přidání aplikace.

    ![4me v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Přiřazování uživatelů k 4me

Azure Active Directory používá koncept volá *přiřazení* určit, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze na uživatele a/nebo skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k 4me. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny 4me podle zde uvedených pokynů:

* [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Důležité tipy pro přiřazování uživatelů k 4me

* Dále je doporučeno jednoho uživatele Azure AD, je přiřazená 4me otestovat automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazení uživatele k 4me, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Konfigurace automatické zřizování uživatelů pro 4me 

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v 4me podle přiřazení uživatele a/nebo skupiny ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro 4me, podle pokynů [4me jednotné přihlašování – kurz](4me-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro 4me ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **4me**.

    ![Odkaz 4me v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování](common/provisioning-automatic.png)

5. Načíst **adresy URL Tenanta** a **tajný klíč tokenu** 4me účtu, postupujte podle návodu, jak je popsáno v kroku 6.

6. Přihlaste se ke konzole správce 4me. Přejděte do **nastavení**.

    ![Nastavení 4me](media/4me-provisioning-tutorial/4me01.png)

    Zadejte **aplikace** na panelu hledání.

    ![4me aplikace](media/4me-provisioning-tutorial/4me02.png)

    Otevřít **SCIM** rozevírací seznam pro načtení tokenu tajný klíč a koncový bod SCIM.

    ![4me SCIM](media/4me-provisioning-tutorial/4me03.png)

7. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k 4me. Pokud se nepovede, ujistěte se, že váš účet 4me má oprávnění správce a zkuste to znovu.

    ![Podpisový](common/provisioning-testconnection-tenanturltoken.png)

8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![E-mailové oznámení](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům 4me**.

    ![Mapování uživatelů 4me](media/4me-provisioning-tutorial/4me-user-mapping.png)
    
11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do 4me v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v 4me pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Mapování uživatelů 4me](media/4me-provisioning-tutorial/4me-user-attributes.png)
    
12. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory k 4me**.

    ![Mapování uživatelů 4me](media/4me-provisioning-tutorial/4me-group-mapping.png)
    
13. Zkontrolujte skupiny atributů, které se synchronizují ze služby Azure AD do 4me v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají k vyhodnocení skupiny v 4me pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Mapování skupin 4me](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Služba pro 4me zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Stav zřizování zapnutém](common/provisioning-toggle-on.png)

16. Definovat uživatele a/nebo skupiny, které chcete k poskytování 4me výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Zřizování oboru](common/provisioning-scope.png)

17. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Ukládá se konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba na 4me zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* 4me má jiný koncový bod adresy URL SCIM pro testovací a produkční prostředí. Nejprve končí **.qa** při druhém končí **.com**
* 4me vygeneruje tajný klíč tokeny mají datum vypršení platnosti v měsíci z: generace.
* nepodporuje 4me **odstranit** operace

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)
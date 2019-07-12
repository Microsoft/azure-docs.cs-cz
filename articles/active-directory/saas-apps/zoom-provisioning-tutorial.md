---
title: 'Kurz: Konfigurace přiblížení pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat služby Azure Active Directory tak, aby automaticky zřizovat a rušit přístup uživatelských účtů do přiblížení.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 0a21a35e6d4a938d41e8bc11cebc1be5738d893e
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671007"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Kurz: Konfigurace přiblížení pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v přiblížení a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit přístup uživatelů nebo skupinám přiblížit.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných Microsoft Azure podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

* Klient služby Azure AD
* [Přiblížení tenanta](https://zoom.us/pricing)
* Uživatelský účet v přiblížení s oprávněními správce

## <a name="add-zoom-from-the-gallery"></a>Přidání přiblížení z Galerie

Před konfigurací přiblížení pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat přiblížení na váš seznam spravovaných aplikací SaaS z Galerie aplikací Azure AD.

**Přiblížení přidat z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V  **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **přiblížení**vyberte **přiblížení** panel výsledků a pak klikněte na **přidat** tlačítko pro přidání aplikace.

    ![Lupa v seznamu výsledků](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>Přiřazení uživatelů ke zvětšení

Azure Active Directory používá koncept volá *přiřazení* určit, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze na uživatele a/nebo skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebovat přístup ke zvětšení. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny přiblížit podle zde uvedených pokynů:

* [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>Důležité tipy pro přiřazování uživatelů k přiblížení

* Dále je doporučeno jednoho uživatele Azure AD je přiřazena přiblížit k otestování automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazování uživatele přiblížit, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configure-automatic-user-provisioning-to-zoom"></a>Konfigurovat automatické zřizování uživatelů pro přiblížení 

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele nebo skupiny v přiblížení podle přiřazení uživatele a/nebo skupiny ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro přiblížení, postupujte podle pokynů uvedených v [přiblížení jednotné přihlašování – kurz](zoom-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Konfigurovat automatické zřizování uživatelů pro přiblížení ve službě Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **přiblížení**.

    ![Přiblížení odkaz v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování](common/provisioning-automatic.png)

5. V části **přihlašovacích údajů správce** části, zadejte `https://api.zoom.us/scim` v **adresy URL Tenanta**. Načíst **tajný klíč tokenu** vašeho účtu přiblížení, postupujte podle návodu, jak je popsáno v kroku 6.

6. Přihlaste se k vaší [přiblížení konzoly pro správu](https://zoom.us/signin). Přejděte do **Upřesnit > Lupa pro vývojáře** v levém navigačním podokně.

    ![Integrace přiblížení](media/zoom-provisioning-tutorial/zoom01.png)

    Přejděte do **spravovat** v pravém horním rohu stránky. 

    ![Instalace přiblížení](media/zoom-provisioning-tutorial/zoom02.png)

    Přejděte do vaší vytvořené aplikace Azure AD. 
    
    ![Přiblížení aplikace](media/zoom-provisioning-tutorial/zoom03.png)

    Vyberte **aplikace pověření** v levém navigačním podokně.

    ![Přiblížení aplikace](media/zoom-provisioning-tutorial/zoom04.png)

    Načtení tokenu JWT hodnoty níže a do vstupní **tajný klíč tokenu** pole ve službě Azure AD. Pokud budete potřebovat nový token vypršení platnosti, budete ji muset znovu nakonfigurovat vypršení platnosti čas, který se automaticky vygenerovat nový token. 

    ![Instalace přiblížení](media/zoom-provisioning-tutorial/zoom05.png)

7. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k přiblížení. Pokud se nepovede, ujistěte se, že váš účet přiblížení má oprávnění správce a zkuste to znovu.

    ![Podpisový](common/provisioning-testconnection-tenanturltoken.png)

8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![E-mailové oznámení](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizace Azure Active Directory Users přiblížit**.

    ![Mapování uživatelů přiblížení](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do přiblížit **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v přiblížení pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.
    
     ![Mapování uživatelů přiblížení](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Služba pro přiblížení zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.
    
    ![Stav zřizování zapnutém](common/provisioning-toggle-on.png)

14. Definovat uživatele a/nebo skupiny, které chcete ke zřízení přiblížit výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Zřizování oboru](common/provisioning-scope.png)

15. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Ukládá se konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba na přiblížení zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Přiblížení nepodporuje zřizování pro skupiny.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

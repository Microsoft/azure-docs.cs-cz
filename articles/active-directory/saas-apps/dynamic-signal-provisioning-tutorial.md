---
title: 'Kurz: Nakonfigurovat dynamické signál pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do dynamické signálu.
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: f9bfa0eaea67919bad775af5cf9071cd3d6973ca
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672655"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>Kurz: Nakonfigurovat dynamické signál pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v dynamické signál a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se dynamické signálu.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných Microsoft Azure podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

* Klient služby Azure AD
* [Dynamické signál tenanta](https://dynamicsignal.com/)
* Účet uživatele s oprávněními správce v dynamické signálu.

## <a name="add-dynamic-signal-from-the-gallery"></a>Přidat dynamické signál z Galerie

Před konfigurací dynamické signál pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat dynamické signál z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat dynamické signál z Galerie aplikací Azure AD, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **dynamické signál**vyberte **dynamické signál** panel výsledků a pak klikněte na **přidat** tlačítko pro přidání aplikace.

    ![Dynamické signálu v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Přiřazování uživatelů k dynamické signálu

Azure Active Directory používá koncept volá *přiřazení* určit, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze na uživatele a/nebo skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k dynamické signálu. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny dynamické signál podle zde uvedených pokynů:

* [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Důležité tipy pro přiřazování uživatelů k dynamické signálu

* Dále je doporučeno jednoho uživatele Azure AD je přiřazen k dynamické signál k otestování automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazení uživatele k dynamické signálu, je nutné vybrat žádné platné roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>Konfigurace automatické zřizování uživatelů pro dynamické signálu 

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v dynamické signál podle uživatele a/nebo přiřazení skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro dynamické signál, postupujte podle pokynů uvedených v [dynamické signál jednotné přihlašování – kurz](dynamicsignal-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro dynamické signálu ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **dynamické signál**.

    ![Dynamické signál odkaz v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování](common/provisioning-automatic.png)

5. V části **přihlašovacích údajů správce** části, zadejte **adresy URL Tenanta** a **tajný klíč tokenu** účtu dynamické signál, jak je popsáno v kroku 6.

6. Přejděte v konzole pro správu dynamického signál **správce > Upřesnit > rozhraní API**.

    ![Dynamické signál zřizování](./media/dynamic-signal-provisioning-tutorial/secret-token-1.png)

    Kopírovat **adresy URL rozhraní API SCIM** k **Tenant URL**. Klikněte na **vygenerovat nový Token** ke generování **nosného tokenu** a zkopírujte hodnotu do **tajný klíč tokenu**.

    ![Dynamické signál zřizování](./media/dynamic-signal-provisioning-tutorial/secret-token-2.png)

7. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k dynamické signálu. Pokud se nepovede, ujistěte se, že váš účet dynamické signál má oprávnění správce a zkuste to znovu.

    ![Adresa URL tenanta + Token](common/provisioning-testconnection-tenanturltoken.png)

8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![E-mailové oznámení](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizace Azure Active Directory Users dynamické signál**.

    ![Mapování uživatelů na dynamické signálu](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD pro dynamické signálu v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v dynamické signál pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Dynamické signál atributy uživatele](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Povolit Azure AD pro dynamické signál služby zřizování, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Stav zřizování zapnutém](common/provisioning-toggle-on.png)

14. Definovat uživatele a/nebo skupiny, které chcete ke zřízení dynamické signál výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Zřizování oboru](common/provisioning-scope.png)

15. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Ukládá se konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba na signál dynamické zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Dynamické signál nepodporuje trvalé uživatele odstraní z Azure AD. Operace k odstranění uživatele trvale v dynamické signál, má být provedena prostřednictvím konzoly pro správu dynamického signál uživatelského rozhraní. 
* Dynamické signálu v současné době nepodporuje skupiny.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)


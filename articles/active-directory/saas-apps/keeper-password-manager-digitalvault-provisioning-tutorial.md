---
title: 'Kurz: Nakonfigurovat správce hesel držitel & digitální trezoru pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do správce hesel držitel & digitální trezoru.
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
ms.openlocfilehash: 74bfe37323a17bde19e4a9bf4ec28c9c3910b37f
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67666251"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Kurz: Nakonfigurovat správce hesel držitel & digitální trezoru pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v správce hesel držitel & digitální trezor a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se držitel heslo Správce & digitální trezoru.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných Microsoft Azure podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

* Klient služby Azure AD
* [Správce hesel držitel & digitální trezor tenanta](https://keepersecurity.com/pricing.html?t=e)
* Účet uživatele s oprávněními správce v správce hesel držitel & digitální trezoru.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Přidat správce hesel držitel & digitální trezoru z Galerie

Před konfigurací Správce hesel držitel & digitální trezoru pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat správce hesel držitel & digitální trezoru z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat správce hesel držitel & digitální trezoru z Galerie aplikací Azure AD, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **správce hesel držitel & digitální trezor**vyberte **správce hesel držitel & digitální trezor** panel výsledků a pak klikněte na **přidat**tlačítko pro přidání aplikace.

    ![Správce hesel držitel & digitální trezoru v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Přiřazování uživatelů k správce hesel držitel & digitální trezoru

Azure Active Directory používá koncept volá *přiřazení* určit, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze na uživatele a/nebo skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k správce hesel držitel & digitální trezoru. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny správce hesel držitel & digitální trezor podle zde uvedených pokynů:

* [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Důležité tipy pro přiřazování uživatelů k správce hesel držitel & digitální trezoru

* Dále je doporučeno jednoho uživatele Azure AD je přiřazen do správce hesel držitel & digitální trezor k otestování automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazení uživatele k správce hesel držitel & digitální trezoru, je nutné vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Konfigurace automatické zřizování uživatelů pro správce hesel držitel & digitální trezoru 

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v držitel heslo správce & digitální trezor založený na uživatele a/nebo přiřazení skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro správce hesel držitel & digitální trezoru, postupujte podle pokynů uvedených v [správce hesel držitel & digitální trezor jednotné přihlašování – kurz](keeperpasswordmanager-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro správce hesel držitel & digitální trezoru ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **správce hesel držitel & digitální trezor**.

    ![Správce hesel držitel & digitální trezor odkaz v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování](common/provisioning-automatic.png)

5. V části **přihlašovacích údajů správce** části, zadejte **adresy URL Tenanta** a **tajný klíč tokenu** váš držitel heslo správce a účet digitální trezor, jak je popsáno v kroku 6.

6. Přihlaste se k vaší [konzoly pro správu držitel](https://keepersecurity.com/console/#login). Klikněte na **správce** a vyberte existující uzel nebo vytvořte novou. Přejděte **zřizování** kartě a vyberte **přidat metodu**.

    ![Držitel konzoly pro správu](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Vyberte **SCIM (systém pro správu identit mezi doménami**.

    ![Držitel přidat SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Klikněte na tlačítko **vytvořit zřizování Token**.

    ![Držitel vytvoření koncového bodu](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Zkopírujte hodnoty **URL** a **Token** a vložte je do **adresy URL Tenanta** a **tajný klíč tokenu** ve službě Azure AD. Klikněte na tlačítko **Uložit** dokončení zřizování nastavení na držitelem.

    ![Držitel vytvořit Token](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD připojovat k správce hesel držitel & digitální trezoru. Pokud se nepovede, ujistěte se, že váš držitel heslo správce & digitální trezor účet má oprávnění správce a zkuste to znovu.

    ![Adresa URL tenanta + Token](common/provisioning-testconnection-tenanturltoken.png)

8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![E-mailové oznámení](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizace Azure Active Directory Users správce hesel držitel & digitální trezor**.

    ![Držitel mapování uživatelů](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do správce hesel držitel & digitální trezoru **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v správce hesel držitel & digitální trezoru pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Držitel atributy uživatele](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory pro správce hesel držitel & digitální trezor**.

    ![Držitel skupiny mapování](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Zkontrolujte skupiny atributů, které se synchronizují ze služby Azure AD do správce hesel držitel & digitální trezoru **mapování atributů** části. Atributy vybrané jako **odpovídající** vlastnosti se používají k vyhodnocení skupiny správce hesel držitel & digitální trezoru pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Držitel skupiny atributů](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Služba správce hesel držitel & digitální trezor zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Stav zřizování zapnutém](common/provisioning-toggle-on.png)

16. Definovat uživatele a/nebo skupiny, které chcete ke zřízení správce hesel držitel & digitální trezor výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Zřizování oboru](common/provisioning-scope.png)

17. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Ukládá se konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce prováděné zřizování na držitel heslo správce služby Azure AD & Digitální trezoru.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Správce hesel držitel & digitální trezor vyžaduje **e-mailů** a **uživatelské jméno** používat stejnou zdrojovou hodnotu, všechny aktualizace buď atributy upraví jinou hodnotu.
* Správce hesel držitel & digitální trezoru není podporují odstraní uživatele, pouze zakázat. Zakázaní uživatelé se zobrazí podle uzamčené v uživatelském rozhraní konzoly Správce držitelem.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)


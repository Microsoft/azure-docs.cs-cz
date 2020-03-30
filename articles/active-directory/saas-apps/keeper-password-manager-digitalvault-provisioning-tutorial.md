---
title: 'Kurz: Konfigurace správce hesel keeperu & digitálním trezoru pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřizuje a zřizuje uživatelské účty správci hesel správce & digitálním trezorem.
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
ms.openlocfilehash: 236527a9889879f872ef8c3867a7ec3c1b1ba0a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057506"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Kurz: Konfigurace správce hesel keeperu & digitálním trezoru pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny ve Správci hesel keeperu & Digital Vault a Azure Active Directory (Azure AD) ke konfiguraci Azure AD tak, aby automaticky zřizovala a odjinala uživatele nebo skupiny správce hesel keeperu & Digital Vault.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Správce hesel správce & klienta digitálního trezoru](https://keepersecurity.com/pricing.html?t=e)
* Uživatelský účet ve Správci hesel správce & digital vault s oprávněními správce.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Přidat Správce hesel správce & digitálního trezoru z galerie

Před konfigurací Správce hesel keeperu & Digital Vault pro automatické zřizování uživatelů pomocí Azure AD je potřeba přidat správce hesel keeperu & Digital Vault z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat správce hesel keeperu & Digital Vault z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **& Správce hesel správce Digital Vault**, vpanelu s výsledky vyberte správce **hesel správce & digitálního trezoru** a klepnutím na tlačítko **Přidat** aplikaci přidejte.

    ![Správce hesel správce & digitálním trezoru v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Přiřazení uživatelů k správci hesel správce & digitálního trezoru

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup ke správci hesel keeperu & digital vault. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny Keeper Password Manager & Digital Vault podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Důležité tipy pro přiřazení uživatelů k Správci hesel keeperu & Digital Vault

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen správce hesel Keeper & Digital Vault k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele správci hesel správce & digitálním trezoru je nutné v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Konfigurace automatického zřizování uživatelů do správce hesel správce & digitálním trezoru 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin ve Správci hesel správce & Digital Vault na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování založené na saml pro Správce hesel keeper & Digital Vault podle pokynů uvedených v [keeper password manager & Digital Vault jednotnépřihlášení tutorial](keeperpasswordmanager-tutorial.md). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro správce hesel správce & digital vaultu ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **správce hes &el správce Digital Vault**.

    ![Odkaz Správce hesel správce & v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Přihlašovací údaje správce** zadejte adresu URL **klienta** a **tajný token** správce hesel správce & účtu digitálního trezoru, jak je popsáno v kroku 6.

6. Přihlaste se do [konzole Keeper Admin Console](https://keepersecurity.com/console/#login). Klikněte na **Admin** a vyberte existující uzel nebo vytvořte nový. Přejděte na kartu **Zřizování** a vyberte **Přidat metodu**.

    ![Konzola pro správu keeperu](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Vyberte **možnost SCIM (Systém pro správu identit mezi doménami**.

    ![Brankář Přidat SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Klepněte na **tlačítko Vytvořit token zřizování**.

    ![Vytvořit koncový bod keeperu](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Zkopírujte hodnoty pro **adresu URL** a **token** a vložte je do adresy **URL klienta** a **tajného tokenu** ve službě Azure AD. Kliknutím na **Uložit** dokončete nastavení zřizování na Keeperu.

    ![Brankář vytvořit token](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Po vyplnění polí uvedených v kroku 5 klikněte na **test připojení** a ujistěte se, že Azure AD můžete připojit k Správce hesel Keeper & Digital Vault. Pokud se připojení nezdaří, ujistěte se, že váš účet Správce hesel správce & účet Digital Vault má oprávnění správce, a akci opakujte.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

8. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části Mapování vyberte **synchronizovat uživatele služby Azure Active Directory se správcem hesel držitele & digitální míchací**. **Mappings**

    ![Mapování uživatelů keeperu](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do správce hesel správce & Digital Vault v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů ve Správci hesel správce správce pro správce & digitální min. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy uživatele keeperu](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. V části **Mapování** vyberte **synchronizovat skupiny Služby Azure Active Directory se správcem hesel správce & digitální míchací**.

    ![Mapování skupiny keeperů](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do Správce hesel Keeper & Digital Vault v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám ve Správci hesel správce & digitálním trezoru pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy skupiny keeperů](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Chcete-li povolit službu zřizování Azure AD pro správce hesel keeperu & Digital Vault, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

16. Definujte uživatele nebo skupiny, které chcete zřídit správci hesel & Digitální trezor výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

17. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD ve Správci hesel správce & Digital Vault.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektorů

* Správce hesel správce & Digital Vault vyžaduje, aby **e-maily** a **uživatelské jméno** měly stejnou zdrojovou hodnotu, protože všechny aktualizace obou atributů změní druhou hodnotu.
* Správce hesel správce & Digital Vault nepodporuje uživatele odstraní, pouze zakázat. Zakázaní uživatelé se zobrazí jako zamknuté v uživatelském rozhraní Keeper Admin Console.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)


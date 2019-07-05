---
title: 'Kurz: Konfigurace Smartsheet pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Smartsheetu.
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
ms.date: 06/07/2019
ms.author: zchia
ms.openlocfilehash: e9d0d996b961c31732642c9476f529bfb7561fbe
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453395"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Kurz: Konfigurace Smartsheet pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést ve Smartsheetu a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny k Smartsheetu.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných Microsoft Azure podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

* Klient služby Azure AD
* [Smartsheet tenanta](https://www.smartsheet.com/pricing)
* Uživatelský účet na plánu Smartsheet Enterprise nebo Enterprise Premier s oprávněními správce systému.

## <a name="assign-users-to-smartsheet"></a>Přiřazení uživatelů k Smartsheetu

Azure Active Directory používá koncept volá *přiřazení* určit, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze na uživatele a/nebo skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k Smartsheetu. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny k Smartsheetu podle zde uvedených pokynů:

* [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Důležité tipy pro přiřazování uživatelů k Smartsheetu

* Dále je doporučeno jednoho uživatele Azure AD je přiřazená k Smartsheetu testování automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazení uživatele k Smartsheetu, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

* Aby parity v přiřazení role uživatele mezi Smartsheetu a Azure AD, doporučujeme využívat stejné přiřazení rolí v seznamu uživatelů služby úplné Smartsheet. Chcete-li načíst tento seznam uživatelů ze Smartsheetu, přejděte na **účet správce > Správa uživatelů > Další akce > stáhnout seznam uživatelů (csv)** .

* Smartsheet pro přístup k některým funkcím v aplikaci, musí uživatel mít několik rolí. Další informace o typech uživatelů a oprávnění ve Smartsheetu, přejděte na [typy uživatelů a oprávnění](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Pokud má uživatel přiřazené ve Smartsheetu, více rolí můžete **musí** Ujistěte se, že tato přiřazení rolí se replikují ve službě Azure AD, aby se zabránilo situaci, kde uživatelé ztratit přístup k objektům Smartsheet trvale. Každou jedinečnou roli ve Smartsheetu **musí** přiřadit do jiné skupiny ve službě Azure AD. Uživatel **musí** pak přidají do každé skupiny odpovídající požadovaných rolí. 

## <a name="set-up-smartsheet-for-provisioning"></a>Nastavení Smartsheet pro zřizování

Před konfigurací Smartsheet pro automatické zřizování uživatelů pomocí Azure AD, je potřeba povolit zřizování SCIM ve Smartsheetu.

1. Přihlaste se jako **SysAdmin** v **[Smartsheet portál](https://app.smartsheet.com/b/home)** a přejděte do **správce účtu**.

    ![Smartsheet Account Admin](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Přejděte na **kontrolních mechanismů pro zabezpečení > uživatel automatické zřizování > Upravit**.

    ![Kontrolní mechanismy zabezpečení Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Přidat a ověřit e-mailových domén pro uživatele, které chcete zřídit ze služby Azure AD k Smartsheetu. Zvolte **nejsou povolené** zajistit, že všechny zřizování akce pocházejí pouze z Azure AD a také zajistit, že je synchronizovaný s Azure AD přiřazení uživatele seznam Smartsheet.

    ![Zřizování uživatelů Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Po dokončení ověření, budete muset aktivovat domény. 

    ![Smartsheet Activate Domain](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Generovat **tajný klíč tokenu** vyžadované ke konfiguraci automatické zřizování uživatelů pomocí Azure AD tak, že přejdete do **aplikací a integrace**.

    ![Smartsheet Install](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Zvolte **přístup přes rozhraní API**. Klikněte na tlačítko **vygenerovat nový přístupový token**.

    ![Smartsheet Install](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Definujte název tokenu pro přístup k rozhraní API. Klikněte na **OK**.

    ![Smartsheet Install](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Zkopírujte Token pro přístup k rozhraní API a uložte ho jako to bude pouze čas, můžete ji zobrazit. Je vyžadováno, **tajný klíč tokenu** pole ve službě Azure AD.

    ![Smartsheet token](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Přidání Smartsheet z Galerie

Ke konfiguraci Smartsheet pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Smartsheet z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

1. V  **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Smartsheet**vyberte **Smartsheet** na panelu výsledků. 

    ![Smartsheet v seznamu výsledků](common/search-new-app.png)

5. Vyberte **zaregistrovat Smartsheet** tlačítko, které vás přesměrují na přihlašovací stránku vaší Smartsheet. 

    ![Přidat Smartsheet OIDC](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Smartsheet je OpenIDConnect aplikaci, zvolte možnost přihlásit se k Smartsheetu pomocí svého pracovního účtu Microsoft.

    ![Smartsheet OIDC přihlášení](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Po úspěšném ověření přijetí výzvy souhlasu pro stránka pro odsouhlasení podmínek. Aplikace se pak automaticky přidají do vašeho tenanta a budete přesměrováni do vašeho účtu Smartsheet.

    ![Smartsheet OIDc souhlas](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Konfigurovat automatické zřizování uživatelů k Smartsheetu 

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny ve Smartsheetu podle přiřazení uživatele a/nebo skupiny ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro Smartsheet ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Smartsheet**.

    ![Odkaz Smartsheetu v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování](common/provisioning-automatic.png)

5. V části **přihlašovacích údajů správce** části zadejte `https://scim.smartsheet.com/v2/` v **adresy URL Tenanta**. Vstupní hodnota, která načte a předtím uložili ze Smartsheetu v **tajný klíč tokenu**. Klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k Smartsheetu. Pokud se nepovede, ujistěte se, že váš účet Smartsheet má oprávnění správce systému a zkuste to znovu.

    ![Podpisový](common/provisioning-testconnection-tenanturltoken.png)

6. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![E-mailové oznámení](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelů k Smartsheetu**.

    ![Mapování uživatelů Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD k Smartsheetu v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty ve Smartsheetu pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Atributy uživatele Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Služba pro Smartsheet zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Stav zřizování zapnutém](common/provisioning-toggle-on.png)

12. Definovat uživatele a/nebo skupiny, které chcete ke zřízení k Smartsheetu výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Zřizování oboru](common/provisioning-scope.png)

13. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Ukládá se konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba ve Smartsheetu zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Smartsheet nepodporuje obnovitelné odstranění. Když uživatele **aktivní** atribut je nastaven na hodnotu False, Smartsheet trvale odstraní uživatele.

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

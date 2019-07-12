---
title: 'Kurz: Nakonfigurovat federované adresář pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů federovaných adresáře.
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
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 19f5690a6852161abce2565a8c4a52ce86ff5187
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840586"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Kurz: Nakonfigurovat federované adresář pro automatické zřizování uživatelů

Cíl v tomto kurzu je předvést postup provést v adresáři federovaných a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny do federované adresáře.

> [!NOTE]
>  Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných Microsoft Azure podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

* Tenanta Azure AD.
* [A jsou federované Directory](https://www.federated.directory/pricing).
* Účet uživatele s oprávněními správce v adresáři federované.

## <a name="assign-users-to-federated-directory"></a>Přiřazení uživatelů k federované adresáře
Azure Active Directory používá koncept volá přiřazení k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze na uživatele a/nebo skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k Directory federovaný. Po se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny k federované adresář podle zde uvedených pokynů:

 * [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Důležité tipy pro přiřazování uživatelů k federované adresáře
 * Dále je doporučeno jednoho uživatele Azure AD Directory federovaný přiřazen k otestování automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazení uživatele k federované adresáře, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s rolí výchozího přístupu k nezahrnou se zřizování.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Nastavení federované Directory pro zřizování

Před konfigurací federované adresář pro automatické zřizování uživatelů pomocí Azure AD, je potřeba povolit zřizování SCIM na Directory federovaný.

1. Přihlaste se k vaší [federované konzoly pro správu adresáře](https://federated.directory/of)

    ![Federované kurzu adresáře](media/federated-directory-provisioning-tutorial/companyname.png)

2. Přejděte do **adresáře > adresáře uživatelů** a výběr vašeho tenanta. 

    ![federované adresáře](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Generovat trvalé nosný token, přejděte na **klíče adresář > vytvořte nový klíč.** 

    ![federované adresáře](media/federated-directory-provisioning-tutorial/federated01.png)

4. Vytvoření adresáře klíče. 

    ![federované adresáře](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Kopírovat **přístupový Token** hodnotu. Tuto hodnotu zadá do **tajný klíč tokenu** pole na kartě zřizování aplikace federované Directory na webu Azure Portal. 

    ![federované adresáře](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Přidejte adresář federované z Galerie

Jak nakonfigurovat federované adresář pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat adresář federované z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Přidat adresář federované z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V  **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **federované Directory**vyberte **federované Directory** na panelu výsledků.

    ![Federované adresáře v seznamu výsledků](common/search-new-app.png)

5. Přejděte **adresy URL** zvýraznit níže v samostatných prohlížeče. 

    ![federované adresáře](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Klikněte na tlačítko **přihlášení**.

    ![federované adresáře](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Federované adresář je OpenIDConnect aplikaci, zvolte k přihlášení do adresáře federované pomocí svého pracovního účtu Microsoft.
    
    ![federované adresáře](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Po úspěšném ověření přijetí výzvy souhlasu pro stránka pro odsouhlasení podmínek. Aplikace se pak automaticky přidají do vašeho tenanta a budete přesměrováni k vašemu účtu Directory federovaný.

    ![federované adresáře přidat SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Konfigurace automatické zřizování uživatelů do federované adresáře 

Tato část vás provede kroky konfigurace služba zřizování Azure AD k vytvoření, aktualizace a zakázat uživatele a/nebo skupiny v adresáři federované podle uživatele a/nebo přiřazení skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro federované adresář ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **federované Directory**.

    ![Federované Directory odkaz v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování](common/provisioning-automatic.png)

5. V části **přihlašovacích údajů správce** části zadejte `https://api.federated.directory/v2/` v adrese URL Tenanta. Zadejte hodnotu, která načte a předtím uložili z federovaných adresáře v **tajný klíč tokenu**. Klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k federované adresáře. Pokud se nepovede, ujistěte se, že váš účet federované Directory má oprávnění správce a zkuste to znovu.

    ![Adresa URL tenanta + Token](common/provisioning-testconnection-tenanturltoken.png)

8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![E-mailové oznámení](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizace Azure Active Directory Users na federované adresář**.

    ![Federované kurzu adresáře](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD Federovaná adresáře **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty federované adresář pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Federované kurzu adresáře](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Povolit zřizování pro federované adresář služby Azure AD, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Stav zřizování zapnutém](common/provisioning-toggle-on.png)

14. Definovat uživatele a/nebo skupiny, které chcete ke zřízení do adresáře federované výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Zřizování oboru](common/provisioning-scope.png)

15. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Ukládá se konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce prováděné zřizování služby na federované adresář Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)
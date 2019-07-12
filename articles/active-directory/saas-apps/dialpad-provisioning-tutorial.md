---
title: 'Kurz: Konfigurace Dialpad pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Dialpad.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 32e634bc089417aaa8080b30a5f77f663a3d8b33
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611782"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Kurz: Konfigurace Dialpad pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v Dialpad a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se Dialpad.

> [!NOTE]
>  Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).

> Tento konektor je aktuálně ve verzi Preview. Další informace o obecných Microsoft Azure podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

* Tenanta Azure AD.
* [Dialpad tenant](https://www.dialpad.com/pricing/).
* Účet uživatele s oprávněními správce v Dialpad.

## <a name="assign-users-to-dialpad"></a>Přiřazení uživatelů k Dialpad
Azure Active Directory používá koncept volá přiřazení k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze na uživatele a/nebo skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k Dialpad. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny Dialpad podle zde uvedených pokynů:
 
* [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Důležité tipy pro přiřazování uživatelů k Dialpad

 * Dále je doporučeno jednoho uživatele Azure AD, je přiřazená Dialpad otestovat automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazení uživatele k Dialpad, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s rolí výchozího přístupu k nezahrnou se zřizování.

## <a name="setup-dialpad-for-provisioning"></a>Instalační program Dialpad pro zřizování

Před konfigurací Dialpad pro automatické zřizování uživatelů pomocí Azure AD, je potřeba načíst některé informace o zajištění z Dialpad.

1. Přihlaste se k vaší [konzoly pro správu Dialpad](https://dialpadbeta.com/login) a vyberte **nastavení správy**. Ujistěte se, že **moji firmu** vybraný z rozevíracího seznamu. Přejděte do **ověřování > klíče rozhraní API**.

    ![Přidat Dialpad SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Generovat nový klíč kliknutím **přidejte klíč** a konfigurace vlastností tajný token.

    ![Přidat Dialpad SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Přidat Dialpad SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Klikněte na tlačítko **kliknutím ji zobrazíte hodnotu** tlačítko naposledy vytvořený klíč rozhraní API a zkopírujte hodnotu zobrazenou. Tuto hodnotu zadá do **tajný klíč tokenu** pole na kartě zřizování Dialpad aplikace na webu Azure Portal. 

    ![Dialpad vytvořit Token](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Přidání Dialpad z Galerie

S konfigurací Dialpad pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Dialpad z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Dialpad z Galerie aplikací Azure AD, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Dialpad**vyberte **Dialpad** na panelu výsledků.
    ![Dialpad v seznamu výsledků](common/search-new-app.png)

5. Přejděte **adresy URL** zvýraznit níže v samostatných prohlížeče. 

    ![Přidat Dialpad SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. V pravém horním rohu vyberte **přihlásit > online použijte Dialpad**.

    ![Přidat Dialpad SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Dialpad je OpenIDConnect aplikaci, zvolte možnost se přihlásit a Dialpad pomocí svého pracovního účtu Microsoft.

    ![Přidat Dialpad SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. Po úspěšném ověření přijetí výzvy souhlasu pro stránka pro odsouhlasení podmínek. Aplikace se pak automaticky přidají do vašeho tenanta a budete přesměrováni do vašeho účtu Dialpad.

    ![Přidat Dialpad SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Konfigurovat automatické zřizování uživatelů pro Dialpad

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v Dialpad podle přiřazení uživatele a/nebo skupiny ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro Dialpad ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Dialpad**.

    ![Odkaz Dialpad v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování](common/provisioning-automatic.png)

5. V části **přihlašovacích údajů správce** části zadejte `https://dialpad.com/scim` v **adresy URL Tenanta**. Vstupní hodnota, která načte a předtím uložili z Dialpad v **tajný klíč tokenu**. Klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k Dialpad. Pokud se nepovede, ujistěte se, že váš účet Dialpad má oprávnění správce a zkuste to znovu.

    ![Adresa URL tenanta + Token](common/provisioning-testconnection-tenanturltoken.png)

6. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![E-mailové oznámení](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům Dialpad**.

    ![Mapování uživatelů Dialpad](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Dialpad v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Dialpad pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Atributy uživatele Dialpad](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Služba pro Dialpad zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Stav zřizování zapnutém](common/provisioning-toggle-on.png)

12. Definovat uživatele a/nebo skupiny, které chcete k poskytování Dialpad výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Zřizování oboru](common/provisioning-scope.png)

13. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Ukládá se konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba na Dialpad zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Omezení konektoru
* Dialpad nepodporuje přejmenuje skupinu ještě dnes. To znamená, že jakékoli změny **displayName** skupiny v Azure AD se aktualizuje a projeví v Dialpad.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

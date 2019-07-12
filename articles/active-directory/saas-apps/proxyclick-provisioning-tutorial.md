---
title: 'Kurz: Konfigurace Proxyclick pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Proxyclick.
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
ms.openlocfilehash: c1656e6cc0c690e5a2bccfd2efab02aa843875b8
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672887"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Kurz: Konfigurace Proxyclick pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v Proxyclick a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se Proxyclick.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných Microsoft Azure podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

* Klient služby Azure AD
* [Proxyclick tenanta](https://www.proxyclick.com/pricing)
* Účet uživatele s oprávněními správce v Proxyclick.

## <a name="add-proxyclick-from-the-gallery"></a>Přidání Proxyclick z Galerie

Před konfigurací Proxyclick pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Proxyclick z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Proxyclick z Galerie aplikací Azure AD, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Proxyclick**vyberte **Proxyclick** panel výsledků a pak klikněte na **přidat** tlačítko pro přidání aplikace.

    ![Proxyclick v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Přiřazování uživatelů k Proxyclick

Azure Active Directory používá koncept volá *přiřazení* určit, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze na uživatele a/nebo skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k Proxyclick. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny Proxyclick podle zde uvedených pokynů:

* [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Důležité tipy pro přiřazování uživatelů k Proxyclick

* Dále je doporučeno jednoho uživatele Azure AD, je přiřazená Proxyclick otestovat automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazení uživatele k Proxyclick, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Konfigurace automatické zřizování uživatelů pro Proxyclick 

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v Proxyclick podle přiřazení uživatele a/nebo skupiny ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro Proxyclick, postupujte podle pokynů uvedených v [Proxyclick jednotné přihlašování – kurz](proxyclick-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro Proxyclick ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Proxyclick**.

    ![Odkaz Proxyclick v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování](common/provisioning-automatic.png)

5. Načíst **adresy URL Tenanta** a **tajný klíč tokenu** Proxyclick účtu, postupujte podle návodu, jak je popsáno v kroku 6.

6. Přihlaste se k vaší [konzoly pro správu Proxyclick](https://app.proxyclick.com/login//?destination=%2Fdefault). Přejděte do **nastavení** > **integrace** > **procházet Marketplace**.

    ![Nastavení Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Integrace Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Vyberte **Azure AD**. Klikněte na tlačítko **nainstalovat**.

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Instalace Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Vyberte **zřizování uživatelů** a klikněte na tlačítko **Start integrace**. 

    ![Zřizování uživatelů Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    Příslušná nastavení konfigurace uživatelského rozhraní by měl nyní zobrazí v části **nastavení** > **integrace**. Vyberte **nastavení** pod **Azure AD (zřizování uživatelů)** .

    ![Vytvoření Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Můžete najít **adresy URL Tenanta** a **tajný klíč tokenu** tady.

    ![Proxyclick vytvořit Token](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k Proxyclick. Pokud se nepovede, ujistěte se, že váš účet Proxyclick má oprávnění správce a zkuste to znovu.

    ![Podpisový](common/provisioning-testconnection-tenanturltoken.png)

8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![E-mailové oznámení](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům Proxyclick**.

    ![Mapování uživatelů Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Proxyclick v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Proxyclick pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Atributy uživatele Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Služba pro Proxyclick zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Stav zřizování zapnutém](common/provisioning-toggle-on.png)

15. Definovat uživatele a/nebo skupiny, které chcete k poskytování Proxyclick výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Zřizování oboru](common/provisioning-scope.png)

16. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Ukládá se konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba na Proxyclick zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Vyžaduje Proxyclick **e-mailů** a **uživatelské jméno** mít stejnou hodnotu zdroje. Všechny aktualizace buď atributy upraví jinou hodnotu.
* Proxyclick nepodporuje zřizování pro skupiny.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)


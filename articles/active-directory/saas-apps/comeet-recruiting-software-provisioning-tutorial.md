---
title: 'Kurz: Konfigurace Comeet náboru softwaru pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Comeet přijetí softwaru.
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
ms.date: 05/07/2019
ms.author: zchia
ms.openlocfilehash: e8414b9737e0ee7f847827a432dd9887931a2532
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65470387"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Kurz: Konfigurace Comeet náboru softwaru pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v Comeet náboru Software a služby Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se Comeet přijetí softwaru.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných Microsoft Azure podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

* Klient služby Azure AD
* [Software náboru Comeet tenanta](https://www.comeet.co/)
* Účet uživatele s oprávněními správce v Comeet přijetí softwaru.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Přidat Software náboru Comeet z Galerie

Před konfigurací Comeet náboru softwaru pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Comeet náboru Software z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Přidat Software náboru Comeet z Galerie aplikací Azure AD, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Comeet náboru softwaru**vyberte **Comeet náboru softwaru** panel výsledků a pak klikněte na **přidat** tlačítko pro přidání aplikace.

    ![Comeet náboru Software v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Přiřazování uživatelů k Comeet náboru softwaru

Azure Active Directory používá koncept volá *přiřazení* určit, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze na uživatele a/nebo skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k Comeet přijetí softwaru. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny Comeet přijetí softwaru podle zde uvedených pokynů:

* [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Důležité tipy pro přiřazování uživatelů k Comeet náboru softwaru

* Dále je doporučeno jednoho uživatele Azure AD je přiřazená Comeet náboru Software k otestování automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazení uživatele k přijetí softwaru Comeet, je nutné vybrat v dialogovém okně přiřazení žádné platné roli specifické pro aplikaci (Pokud je k dispozici). Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Konfigurace automatické zřizování uživatelů pro přijetí softwaru Comeet 

Tato část příručky vás provede kroky pro konfiguraci služba zřizování Azure AD k vytvoření, aktualizace a zakázat uživatele a/nebo skupiny v softwaru náboru Comeet podle uživatele a/nebo přiřazení skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro přijetí softwaru Comeet, postupujte podle pokynů uvedených v [Comeet náboru softwaru jednotné přihlašování – kurz](comeetrecruitingsoftware-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro přijetí softwaru Comeet ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Comeet náboru softwaru**.

    ![Odkaz Comeet náboru Software v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování](common/provisioning-automatic.png)

5. V části **přihlašovacích údajů správce** části, zadejte **adresy URL Tenanta** a **tajný klíč tokenu** Comeet náboru softwaru účtu, jak je popsáno v kroku 6.

6. V [konzoly pro správu softwaru náboru Comeet](https://app.comeet.co/), přejděte na **Comeet > Nastavení > ověřování > Microsoft Azure**a zkopírujte **tajný klíč tokenu pro vaši společnost**hodnota, která se **tajný klíč tokenu** pole ve službě Azure AD.

    ![Comeet náboru zřizování softwaru](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k Comeet přijetí softwaru. Pokud se nepovede, ujistěte se, že váš účet Comeet náboru softwaru má oprávnění správce a zkuste to znovu.

    ![Podpisový](common/provisioning-testconnection-token.png)

8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![E-mailové oznámení](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům Comeet**.

    ![Comeet náboru mapování uživatelů na Software](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Comeet náboru Software **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Comeet náboru softwaru pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Comeet náboru softwaru skupiny atributů](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Služba pro přijetí softwaru Comeet zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Stav zřizování zapnutém](common/provisioning-toggle-on.png)

14. Definovat uživatele a/nebo skupiny, které chcete ke zřízení pro přijetí softwaru Comeet výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Zřizování oboru](common/provisioning-scope.png)

15. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Ukládá se konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba Comeet náboru softwaru zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Software náboru comeet aktuálně nepodporuje skupiny.

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)


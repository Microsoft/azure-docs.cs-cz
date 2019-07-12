---
title: 'Kurz: Konfigurace Peakon automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Peakon.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 2547f34432ca1b4b52f34c343bb4aad2f2407f53
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673142"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Kurz: Konfigurace Peakon pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v Peakon a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se Peakon.

> [!NOTE]
>  Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Preview. Další informace o obecných Microsoft Azure podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky

* Tenanta Azure AD.
* [Peakon tenant](https://peakon.com/us/pricing/).
* Účet uživatele s oprávněními správce v Peakon.

## <a name="assigning-users-to-peakon"></a>Přiřazování uživatelů k Peakon

Azure Active Directory používá koncept volá *přiřazení* určit, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze na uživatele a/nebo skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k Peakon. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny Peakon podle zde uvedených pokynů:

* [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Důležité tipy pro přiřazování uživatelů k Peakon 

* Dále je doporučeno jednoho uživatele Azure AD, je přiřazená Peakon otestovat automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazení uživatele k Peakon, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="set-up-peakon-for-provisioning"></a>Nastavení Peakon pro zřizování

1.  Přihlaste se k vaší [konzoly pro správu Peakon](https://app.Peakon.com/login). Klikněte na **konfigurace**. 

    ![Peakon konzoly pro správu](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Vyberte **integrace**.
    
    ![Peakon-employee-provision](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Povolit **zaměstnance zřizování**.

    ![Peakon-employee-provision](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Zkopírujte hodnoty **SCIM 2.0 URL** a **tokenu nosiče OAuth**. Tyto hodnoty pak zadáte v **adresy URL Tenanta** a **tajný klíč tokenu** pole na kartě zřizování Peakon aplikace na webu Azure Portal.

    ![Peakon Create Token](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Přidání Peakon z Galerie

S konfigurací Peakon pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Peakon z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

1. V  **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Peakon**vyberte **Peakon** panel výsledků a pak klikněte na **přidat** tlačítko pro přidání aplikace.

    ![Peakon v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Konfigurace automatické zřizování uživatelů pro Peakon 

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v Peakon podle přiřazení uživatele a/nebo skupiny ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro Peakon, postupujte podle pokynů uvedených v [Peakon Single sign-on kurzu](peakon-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro Peakon ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Peakon**.

    ![Odkaz Peakon v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování](common/provisioning-automatic.png)

5. V části **přihlašovacích údajů správce** části, zadejte **SCIM 2.0 URL** a **tokenu nosiče OAuth** hodnoty získané dříve v **adresy URL Tenanta** a **tajný klíč tokenu** v uvedeném pořadí. Klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k Peakon. Pokud se nepovede, ujistěte se, že váš účet Peakon má oprávnění správce a zkuste to znovu.

    ![Adresa URL tenanta + Token](common/provisioning-testconnection-tenanturltoken.png)

7. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![E-mailové oznámení](common/provisioning-notification-email.png)

8. Klikněte na **Uložit**.

9. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům Peakon**.

    ![Mapování uživatelů Peakon](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Peakon v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Peakon pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Atributy uživatele Peakon](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).
    
    ![Zřizování oboru](common/provisioning-scope.png)

15. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Ukládá se konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba na Peakon zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Všechny vlastní uživatelské atributy v Peakon muset rozšířit z Peakon vaší vlastní SCIM uživatele rozšíření `urn:ietf:params:scim:schemas:extension:peakon:2.0:User`.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)
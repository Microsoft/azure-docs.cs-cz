---
title: 'Kurz: Konfigurace automatického zřizování uživatelů Peakon pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Peakon.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: c04bbd5459690262b484582e807569b965a0439b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349884"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Kurz: Konfigurace Peakon pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Peakon a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro Peakon.

> [!NOTE]
>  Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je momentálně ve verzi Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.
## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant Peakon](https://peakon.com/us/pricing/)
* Uživatelský účet v Peakon s oprávněními správce.

## <a name="assigning-users-to-peakon"></a>Přiřazování uživatelů k Peakon

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Peakon. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Peakon podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Důležité tipy pro přiřazení uživatelů k Peakon 

* Doporučuje se, aby se k Peakon k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Peakon musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-peakon-for-provisioning"></a>Nastavení Peakon pro zřizování

1.  Přihlaste se ke [konzole pro správu Peakon](https://app.Peakon.com/login). Klikněte na **Konfigurace**. 

    ![Konzola pro správu Peakon](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Vyberte **integrace**.
    
    ![Snímek obrazovky možností konfigurace s možností integrace s názvem.](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Povolte **zřizování zaměstnanců**.

    ![Snímek obrazovky s oddílem zřizování zaměstnanců pomocí možnosti Povolit s názvem.](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Zkopírujte hodnoty pro **adresu URL SCIM 2,0** a **token nosiče OAuth**. Tyto hodnoty se zadají do pole **Adresa URL klienta** a **tajný token** na kartě zřizování vaší aplikace Peakon ve Azure Portal.

    ![Peakon vytvořit token](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Přidání Peakon z Galerie

Pokud chcete nakonfigurovat Peakon pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat Peakon z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Peakon**, na panelu výsledků vyberte **Peakon** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Peakon v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Konfigurace automatického zřizování uživatelů na Peakon 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Peakon na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Peakon podle pokynů uvedených v [kurzu Peakon jednotného přihlašování](peakon-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Peakon ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Peakon**.

    ![Odkaz Peakon v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL SCIM 2,0** a hodnoty **nosných tokenů OAuth** načtené dříve v **adrese URL tenanta** a **tajného tokenu** . Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Peakon. Pokud se připojení nepovede, ujistěte se, že má váš účet Peakon oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

7. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

8. Klikněte na **Uložit**.

9. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Peakon**.

    ![Mapování uživatelů Peakon](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Zkontrolujte atributy uživatele synchronizované z Azure AD do Peakon v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Peakon pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele Peakon](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v       [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
    
    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Peakon.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Všechny vlastní atributy uživatelů v Peakon musí být rozšířeny z vlastního uživatelského rozšíření SCIM z Peakon `urn:ietf:params:scim:schemas:extension:peakon:2.0:User` .

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
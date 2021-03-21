---
title: 'Kurz: Konfigurace TheOrgWiki pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro TheOrgWiki.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 8238b9902aafcabc079c551a0eabc7170042209a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357619"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Kurz: Konfigurace TheOrgWiki pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v TheOrgWiki a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro TheOrgWiki.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant OrgWiki](https://www.theorgwiki.com/welcome/)
* Uživatelský účet v TheOrgWiki s oprávněními správce.

## <a name="assign-users-to-theorgwiki"></a>Přiřazení uživatelů k TheOrgWiki

Azure Active Directory používá koncept nazvaný přiřazení k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k TheOrgWiki. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k TheOrgWiki podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Důležité tipy pro přiřazení uživatelů k TheOrgWiki

* Doporučuje se, aby se k TheOrgWiki k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k TheOrgWiki musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-theorgwiki-for-provisioning"></a>Nastavení TheOrgWiki pro zřizování

Před konfigurací TheOrgWiki pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na TheOrgWiki.

1. Přihlaste se ke [konzole pro správu TheOrgWiki](https://www.theorgwiki.com/login/). Klikněte na **Konzola pro správu**.

    ![Snímek org wiki s uživatelem a konzolou pro správu s názvem.](media/theorgwiki-provisioning-tutorial/login.png)

2. V konzole pro správu klikněte na **kartu nastavení**. 

    ![Snímek obrazovky s konzolou pro správu organizace v organizaci, která má kartu nastavení s názvem.](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Přejděte na **účty služeb**.

    ![Snímek obrazovky se stránkou s účty služby v konzole pro správu organizace na wikiwebu](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Klikněte na **+ účet služby**. V části **typ účtu služby** vyberte **založené na tokenech**. Klikněte na **Uložit**.

    ![Snímek obrazovky dialogového okna nový účet služby s typem účtu služby, založeným na tokenech a možnostmi Uložit.](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Zkopírujte **aktivní tokeny**. Tato hodnota se zadá do pole token tajného kódu na kartě zřizování vaší aplikace TheOrgWiki ve Azure Portal.
     
    ![Snímek obrazovky s dialogovým oknem spravovat tokeny pro zřizování C I M.](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Přidání TheOrgWiki z Galerie

Pokud chcete nakonfigurovat TheOrgWiki pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat TheOrgWiki z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **TheOrgWiki**, na panelu výsledků vyberte **TheOrgWiki** . 

    ![TheOrgWiki v seznamu výsledků](common/search-new-app.png)

5. Vyberte tlačítko **zaregistrovat se k TheOrgWiki** , které vás přesměruje na přihlašovací stránku TheOrgWiki. 

    ![Snímek přihlašovací stránky org wiki s adresou URL, která se vyvolala](media/theorgwiki-provisioning-tutorial/image00.png)

6.  V pravém horním rohu vyberte **přihlášení**.

    ![Snímek obrazovky v pravém horním rohu přihlašovací stránky s názvem možnost přihlásit se.](media/theorgwiki-provisioning-tutorial/image02.png)

7. TheOrgWiki je aplikace OpenIDConnect, která se rozhodne přihlásit k OrgWiki pomocí pracovního účtu Microsoft.

    ![Snímek obrazovky se stránkou pro přihlášení k organizaci wiki s možností Přihlásit se účtem Microsoft s názvem.](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Po úspěšném ověření se aplikace automaticky přidá do vašeho tenanta a budete přesměrováni na svůj účet TheOrgWiki.

    ![OrgWiki přidat SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Konfigurace automatického zřizování uživatelů na TheOrgWiki 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TheOrgWiki na základě přiřazení uživatelů nebo skupin ve službě Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro TheOrgWiki ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **TheOrgWiki**.

    ![Odkaz OrgWiki v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` **adresu URL tenanta**. 

    Příklad: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> **Hodnotu subdomény** lze nastavit pouze během počátečního procesu registrace pro TheOrgWiki.
 
6. Zadejte hodnotu tokenu v poli **tajný token** , který jste dříve získali z TheOrgWiki. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k TheOrgWiki. Pokud se připojení nepovede, ujistěte se, že má váš účet TheOrgWiki oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

7. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

8. Klikněte na **Uložit**.

9. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé TheOrgWiki**.

    ![Mapování uživatelů TheOrgWiki](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Zkontrolujte atributy uživatele synchronizované z Azure AD do TheOrgWiki v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v TheOrgWiki pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele TheOrgWiki](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Pokud chcete povolit službu Azure AD Provisioning pro TheOrgWiki, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit pro OrgWiki, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

14. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než následné synchronizace. Další informace o tom, jak dlouho bude trvat pro uživatele a/nebo skupiny, najdete v článku [Jak dlouho bude trvat, než budou uživatelé zřizovat](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pomocí oddílu **aktuální stav** můžete monitorovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v TheOrgWiki. Další informace najdete v tématu o [kontrole stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Pokud si chcete přečíst protokoly zřizování Azure AD, přečtěte si téma [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* Přečtěte si, [Jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování](../app-provisioning/check-status-user-account-provisioning.md).
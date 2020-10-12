---
title: 'Kurz: Konfigurace Workteam pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Workteam.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: 3c2fba1cba2c06e07b262e37eac651afb5c102cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317497"
---
# <a name="tutorial-configure-workteam--for-automatic-user-provisioning"></a>Kurz: Konfigurace Workteam pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Workteam a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro Workteam.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant Workteam](https://workte.am/pricing.html)
* Uživatelský účet v Workteam s oprávněními správce.

## <a name="assigning-users-to-workteam"></a>Přiřazování uživatelů k Workteam 

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Workteam. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Workteam podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workteam"></a>Důležité tipy pro přiřazení uživatelů k Workteam 

* Doporučuje se, aby se k Workteam k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Workteam musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-workteam--for-provisioning"></a>Nastavení Workteam pro zřizování

Před konfigurací Workteam pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na Workteam.

1. Přihlaste se k [Workteam](https://app.workte.am/account/signin). Klikněte na nastavení **organizace**  >  **Nastavení**.

    ![Snímek obrazovky Workteam U I s možnostmi nastavení organizace a nastavení s názvem.](media/workteam-provisioning-tutorial/settings.png)

2. Přejděte na konec a povolte možnosti zřizování Workteam.

    ![Snímek obrazovky dolní části oddílu nastavení s ikonou zřizování uživatelů S C I M, která se vyvolala](media/workteam-provisioning-tutorial/icon.png)

3. Zkopírujte **základní adresu URL** a **nosný token**. Tyto hodnoty se zadají do pole **Adresa URL klienta**a **tajný token** na kartě zřizování vaší aplikace Workteam ve Azure Portal.

    ![Snímek obrazovky S dialogovým oknem nastavení S C I M se ZÁKLADNÍmi textovými poli TOKENu pro R L a NOSIČem](media/workteam-provisioning-tutorial/scim.png)


## <a name="add-workteam--from-the-gallery"></a>Přidání Workteam z Galerie

Pokud chcete nakonfigurovat Workteam pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat Workteam z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Workteam z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Workteam**, na panelu výsledků vyberte **Workteam** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Workteam v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workteam"></a>Konfigurace automatického zřizování uživatelů na Workteam  

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Workteam na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Workteam podle pokynů uvedených v [kurzu Workteam jednotného přihlašování](workteam-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce vzájemně přidávají.

### <a name="to-configure-automatic-user-provisioning-for-workteam--in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Workteam ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Workteam**.

    ![Odkaz Workteam v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části přihlašovací údaje správce zadejte **základní adresu URL** a hodnoty **nosných tokenů** NAČTENÉ dříve v **adrese URL tenanta** a **tajného tokenu** v uvedeném pořadí. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Workteam. Pokud se připojení nepovede, ujistěte se, že má váš účet Workteam oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Workteam**.

    ![Mapování uživatelů Workteam](media/workteam-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Workteam v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Workteam pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele Workteam](media/workteam-provisioning-tutorial/userattribute.png)

11. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Pokud chcete povolit službu Azure AD Provisioning pro Workteam, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit pro Workteam, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

14. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než následné synchronizace. Další informace o tom, jak dlouho bude trvat pro uživatele a/nebo skupiny, najdete v článku [Jak dlouho bude trvat, než budou uživatelé zřizovat](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pomocí oddílu **aktuální stav** můžete monitorovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Workteam. Další informace najdete v tématu o [kontrole stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Pokud si chcete přečíst protokoly zřizování Azure AD, přečtěte si téma [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

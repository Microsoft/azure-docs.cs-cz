---
title: 'Kurz: Konfigurace Foodee pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Foodee.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: e444d6254b575ead4b5d436a4b92f960c61b1d1c
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802872"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Kurz: Konfigurace Foodee pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Foodee a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro Foodee.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant Foodee](https://Foodee.com/pricing/)
* Uživatelský účet v Foodee s oprávněními správce.

## <a name="assigning-users-to-foodee"></a>Přiřazování uživatelů k Foodee 

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Foodee. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Foodee podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-foodee"></a>Důležité tipy pro přiřazení uživatelů k Foodee 

* Doporučuje se, aby se k Foodee k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Foodee musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-foodee-for-provisioning"></a>Nastavení Foodee pro zřizování

Před konfigurací Foodee pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na Foodee.

1. Přihlaste se k [Foodee](https://www.food.ee/login/). Klikněte na **ID tenanta** .

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

2. V části Podnikový portál > vyberte **jednotné přihlašování**.

    ![Foodee](media/Foodee-provisioning-tutorial/scim.png)

3. Zkopírujte **token rozhraní API**. Tyto hodnoty se zadají do pole **token tajného klíče** na kartě zřizování aplikace Foodee ve Azure Portal.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)


## <a name="add-foodee-from-the-gallery"></a>Přidání Foodee z Galerie

Pokud chcete nakonfigurovat Foodee pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat Foodee z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Foodee z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Foodee**, na panelu výsledků vyberte **Foodee** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Foodee v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-foodee"></a>Konfigurace automatického zřizování uživatelů na Foodee  

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Foodee na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Foodee podle pokynů uvedených v [kurzu Foodee jednotného přihlašování](Foodee-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

### <a name="to-configure-automatic-user-provisioning-for-foodee-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Foodee ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Foodee**.

    ![Odkaz Foodee v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části přihlašovací ` https://concierge.food.ee/scim/v2` údaje správce zadejte hodnoty a **tokeny rozhraní API** načtené dříve v **adrese URL tenanta** a **tajného tokenu** v uvedeném pořadí. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Foodee. Pokud se připojení nepovede, ujistěte se, že má váš účet Foodee oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Foodee**.

    ![Mapování uživatelů Foodee](media/Foodee-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Foodee v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Foodee pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele Foodee](media/Foodee-provisioning-tutorial/userattribute.png)

10. V části **mapování** vyberte * * synchronizovat Azure Active Directory skupin do **Foodee**

    ![Atributy uživatele Foodee](media/Foodee-provisioning-tutorial/groupmapping.png)

11. Zkontrolujte atributy uživatele synchronizované z Azure AD do Foodee v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupinových účtů v Foodee pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele Foodee](media/Foodee-provisioning-tutorial/groupattribute.png)

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro Foodee, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro Foodee, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než následné synchronizace. Další informace o tom, jak dlouho bude trvat pro uživatele a/nebo skupiny, najdete v článku [Jak dlouho bude trvat, než budou uživatelé zřizovat](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pomocí oddílu **aktuální stav** můžete monitorovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Foodee. Další informace najdete v tématu o [kontrole stavu zřizování uživatelů](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Pokud si chcete přečíst protokoly zřizování Azure AD, přečtěte si téma [vytváření sestav o automatickém zřizování uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).



## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)

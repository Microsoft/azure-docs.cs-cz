---
title: 'Kurz: Konfigurace Wrike pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Wrike.
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
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: f94c12ed355b25e6a10c8d71e176d9bf3bf78758
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70847098"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Kurz: Konfigurace Wrike pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Wrike a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro Wrike.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant Wrike](https://www.wrike.com/price/)
* Uživatelský účet v Wrike s oprávněními správce.

## <a name="assign-users-to-wrike"></a>Přiřazení uživatelů k Wrike
Azure Active Directory používá koncept nazvaný přiřazení k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Wrike. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Wrike podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Důležité tipy pro přiřazení uživatelů k Wrike

* Doporučuje se, aby se k Wrike k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Wrike musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-wrike-for-provisioning"></a>Nastavení Wrike pro zřizování

Před konfigurací Wrike pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na Wrike.

1. Přihlaste se ke [konzole pro správu Wrike](https://www.Wrike.com/login/). Přejděte k ID tenanta. Vyberte **aplikace & integrace**.

    ![Konzola pro správu Wrike](media/Wrike-provisioning-tutorial/admin.png)

2.  Přejděte do **Azure AD**. Vyberte ji.

    ![Konzola pro správu Wrike](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Vyberte SCIM. Zkopírujte **základní adresu URL**.

    ![Konzola pro správu Wrike](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Klikněte na **rozhraní API** > vyberte **Azure SCIM**.

    ![Wrike přidat SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Otevře se automaticky otevírané okno. Zadejte stejné heslo, které jste vytvořili dříve pro vytvoření účtu. 

    ![Wrike vytvořit token](media/Wrike-provisioning-tutorial/password.png)

6.  Zkopírujte **tajný token** a vložte ho do Azure AD. Kliknutím na **Uložit** dokončete nastavení zřizování na Wrike.

    ![Wrike vytvořit token](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Přidání Wrike z Galerie

Před konfigurací Wrike pro Automatické zřizování uživatelů se službou Azure AD je nutné přidat Wrike z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Wrike z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Wrike**, na panelu výsledků vyberte **Wrike** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Wrike v seznamu výsledků](common/search-new-app.png)


## <a name="configuring-automatic-user-provisioning-to-wrike"></a>Konfigurace automatického zřizování uživatelů na Wrike 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Wrike na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Wrike podle pokynů uvedených v [kurzu Wrike jednotného přihlašování](wrike-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

### <a name="to-configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Wrike ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Wrike**.

    ![Odkaz Wrike v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části přihlašovací údaje správce zadejte **základní adresu URL** a hodnoty **trvalého přístupového tokenu** , které byly dříve načteny v **adrese URL tenanta** a v uvedeném **tajném tokenu** . Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Wrike. Pokud se připojení nepovede, ujistěte se, že má váš účet Wrike oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

7. V poli **e-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

8. Klikněte na **Uložit**.

9. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Wrike**.

    ![Mapování uživatelů Wrike](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Zkontrolujte atributy uživatele synchronizované z Azure AD do Wrike v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Wrike pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele Wrike](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Pokud chcete povolit službu Azure AD Provisioning pro Wrike, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit pro Wrike, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

14. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než následné synchronizace. Další informace o tom, jak dlouho bude trvat pro uživatele a/nebo skupiny, najdete v článku [Jak dlouho bude trvat, než budou uživatelé zřizovat](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pomocí oddílu **aktuální stav** můžete monitorovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Wrike. Další informace najdete v tématu o [kontrole stavu zřizování uživatelů](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Pokud si chcete přečíst protokoly zřizování Azure AD, přečtěte si téma [vytváření sestav o automatickém zřizování uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)

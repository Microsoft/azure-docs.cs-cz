---
title: 'Kurz: Konfigurace 15Five pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro 15Five.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a276c004-9f71-4efc-8cca-1f615760249f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: c49d6fff50e74e67a4fef4ba9915efb72698a5b4
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641796"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Kurz: Konfigurace 15Five pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v 15Five a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro 15Five.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant 15Five](https://www.15five.com/pricing/)
* Uživatelský účet v 15Five s oprávněními správce.

## <a name="assigning-users-to-15five"></a>Přiřazování uživatelů k 15Five

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k 15Five. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k 15Five podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-15five"></a>Důležité tipy pro přiřazení uživatelů k 15Five

* Doporučuje se, aby se k 15Five k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k 15Five musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-15five-for-provisioning"></a>Nastavení 15Five pro zřizování

Před konfigurací 15Five pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na 15Five.

1. Přihlaste se ke [konzole pro správu 15Five](https://my.15five.com/). Přejděte na **funkce > integrace**.

    ![Konzola pro správu 15Five](media/15five-provisioning-tutorial/integration.png)

2.  Klikněte na **SCIM 2,0**.

    ![Konzola pro správu 15Five](media/15five-provisioning-tutorial/image00.png)

3.  Přejděte na **SCIM integration > vygenerujte token OAuth**.

    ![15Five přidat SCIM](media/15five-provisioning-tutorial/image02.png)

4.  Zkopírujte hodnoty **základní adresy URL** a **přístupového tokenu**pro SCIM 2,0. Tato hodnota se zadá do pole **Adresa URL tenanta** a **tajný token** na kartě zřizování vaší aplikace 15Five ve Azure Portal.
    
    ![15Five přidat SCIM](media/15five-provisioning-tutorial/image03.png)

## <a name="add-15five-from-the-gallery"></a>Přidání 15Five z Galerie

Pokud chcete nakonfigurovat 15Five pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat 15Five z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat 15Five z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **15Five**, na panelu výsledků vyberte **15Five** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![15Five v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-15five"></a>Konfigurace automatického zřizování uživatelů na 15Five 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v 15Five na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro 15Five podle pokynů uvedených v [kurzu 15Five jednotného přihlašování](15five-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro 15Five ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **15Five**.

    ![Odkaz 15Five v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5.  V části přihlašovací údaje správce zadejte **základní adresu URL SCIM 2,0 a** hodnoty přístupového tokenu načtené dříve v **adrese URL tenanta** a **tajného tokenu** . Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k 15Five. Pokud se připojení nepovede, ujistěte se, že má váš účet 15Five oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé 15Five**.

    ![Mapování uživatelů 15Five](media/15five-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do 15Five v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v 15Five pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele 15Five](media/15five-provisioning-tutorial/userattribute.png)

10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do 15Five**.

    ![Mapování skupin 15Five](media/15five-provisioning-tutorial/groupmapping.png)

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do 15Five v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v 15Five pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![15Five – atributy skupiny](media/15five-provisioning-tutorial/groupattribute.png)

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro 15Five, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro 15Five, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

    Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v 15Five.

    Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md) .
    
## <a name="connector-limitations"></a>Omezení konektoru

* 15Five nepodporuje pevné odstranění pro uživatele.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* Přečtěte si, [Jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování](../manage-apps/check-status-user-account-provisioning.md).

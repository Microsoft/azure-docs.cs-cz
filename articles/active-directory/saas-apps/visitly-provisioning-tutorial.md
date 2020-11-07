---
title: 'Kurz: konfigurace na návštěvu pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro Automatické zřizování a rušení uživatelských účtů k návštěvě.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: ff3f3ab65df2d801b7c962de7cce645e9fc00b30
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358605"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Kurz: konfigurace na návštěvu pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které provedete v rámci návštěvy a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a rušení zřizování uživatelů nebo skupin.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro aplikace typu software jako služba (SaaS) s Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Navštěvující tenant](https://www.visitly.io/pricing/)
* Uživatelský účet v rámci návštěvy s oprávněními správce

## <a name="assign-users-to-visitly"></a>Přiřazení uživatelů k návštěvě 

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé nebo skupiny, které jsou přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, rozhodněte se, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k návštěvě. Pak těmto uživatelům nebo skupinám přiřaďte, abyste mohli navštěvovat, a to podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Důležité tipy pro přiřazování uživatelů k návštěvě 

* K otestování automatické konfigurace zřizování uživatelů Doporučujeme, abyste jednomu uživateli Azure AD přiřadili k návštěvě. Další uživatele nebo skupiny se dají přiřadit později.

* Když přiřadíte uživatele k návštěvě, je nutné vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná) v dialogovém okně přiřazení. Uživatelé s výchozí rolí přístupu se z zřizování vylučují.

## <a name="set-up-visitly-for-provisioning"></a>Nastavte návštěvu pro zřizování.

Než navštívíte návštěvu pro Automatické zřizování uživatelů se službou Azure AD, musíte povolit systém pro SCIM (Provisioning pro správu identit mezi doménami).

1. Přihlaste se k [návštěvě](https://app.visitly.io/login). Vyberte možnost **integrace**  >  **hostitele synchronizace**.

    ![Synchronizace hostitele](media/Visitly-provisioning-tutorial/login.png)

2. Vyberte část **Azure AD** .

    ![Část Azure AD](media/Visitly-provisioning-tutorial/integration.png)

3. Zkopírujte **klíč rozhraní API**. Tyto hodnoty se zadávají do pole **tajný token** na kartě **zřizování** vaší navštívené aplikace v Azure Portal.

    ![Klíč rozhraní API](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Přidat návštěvu z Galerie

Pokud chcete konfigurovat návštěvu pro Automatické zřizování uživatelů pomocí Azure AD, přidejte do svého seznamu spravovaných aplikací pro SaaS návštěvu z Galerie aplikací Azure AD.

Pokud chcete přidat návštěvu z Galerie aplikací Azure AD, postupujte podle těchto kroků.

1. V [Azure Portal](https://portal.azure.com)v levém navigačním podokně vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte příkaz **navštívit** , v panelu výsledků vyberte možnost **navštívit** a přidejte aplikaci kliknutím na tlačítko **Přidat** .

    ![Přejít do seznamu výsledků](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Konfigurace automatického zřizování uživatelů pro návštěvu 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v rámci návštěvy na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Pokud chcete povolit jednotné přihlašování založené na SAML pro návštěvu, postupujte podle pokynů v [kurzu jednotného přihlašování s jednotným přihlašováním](Visitly-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro návštěvu ve službě Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**  >  **všechny aplikace**.

    ![Všechny aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **navštívit**.

    ![Odkaz na návštěvu v seznamu aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Režim zřizování nastavený na automatické](common/provisioning-automatic.png)

5. V části přihlašovací údaje správce zadejte `https://api.visitly.io/v1/usersync/SCIM` hodnoty **klíčů rozhraní API** , které jste dříve načetli v **adrese URL tenanta** a **tajného tokenu** v uvedeném pořadí. Vyberte **Test připojení** , aby se mohla služba Azure AD připojit k návštěvě. Pokud se připojení nepovede, ujistěte se, že má váš návštěvu oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování. Zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatelů, abyste mohli navštěvovat**.

    ![Navštívit mapování uživatelů](media/visitly-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD, abyste mohli přejít do části **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v možnosti návštěvy pro operace aktualizace. Vyberte **Uložit** a potvrďte všechny změny.

    ![Navštívit atributy uživatele](media/visitly-provisioning-tutorial/userattribute.png)

10. Pokud chcete nakonfigurovat filtry oborů, postupujte podle pokynů v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro návštěvu, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit k návštěvě, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než následné synchronizace. Další informace o tom, jak dlouho trvá uživatelům nebo skupinám při zřizování, najdete v tématu [Jak dlouho bude trvat zřizování uživatelů?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pomocí oddílu **aktuální stav** můžete monitorovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD při návštěvě. Další informace najdete v tématu o [kontrole stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Pokud si chcete přečíst protokoly zřizování Azure AD, přečtěte si téma [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

Při návštěvě není podporována Pevná odstranění. Všechno je jenom obnovitelné odstranění.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

---
title: 'Kurz: Konfigurace MerchLogix pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro MerchLogix.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.openlocfilehash: 9be2205ad0664d58c7a2ef0c07481b1c7aa02402
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273343"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Kurz: Konfigurace MerchLogix pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v MerchLogix a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro MerchLogix.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* Tenant MerchLogix
* Technický kontakt na MerchLogix, který může poskytnout adresu URL koncového bodu SCIM a tajný token potřebný k zřizování uživatelů

## <a name="adding-merchlogix-from-the-gallery"></a>Přidání MerchLogix z Galerie

Před konfigurací MerchLogix pro Automatické zřizování uživatelů se službou Azure AD je nutné přidat MerchLogix z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat MerchLogix z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** . 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte k **podnikovým aplikacím**  >  **všechny aplikace**.

    ![Oddíl Enterprise Applications][2]

3. Pokud chcete přidat MerchLogix, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace][3]

4. Do vyhledávacího pole zadejte **MerchLogix**.

5. Na panelu výsledků vyberte **MerchLogix**a potom kliknutím na tlačítko **Přidat** přidejte MerchLogix do seznamu aplikací SaaS.

    ![Snímek obrazovky přidat z části sloupce s textovým polem pro zadání názvu s názvem.][4]

## <a name="assigning-users-to-merchlogix"></a>Přiřazování uživatelů k MerchLogix

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci ve službě Azure AD. 

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k MerchLogix. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k MerchLogix podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Důležité tipy pro přiřazení uživatelů k MerchLogix

* Doporučuje se, aby k testování počáteční konfigurace automatického zřizování uživatelů byl přiřazený jeden uživatel Azure AD MerchLogix. Další uživatele a skupiny můžete přiřadit později, až budou testy úspěšné.

* Při přiřazování uživatele k MerchLogix musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Konfigurace automatického zřizování uživatelů na MerchLogix 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v MerchLogix na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro MerchLogix podle pokynů uvedených v [kurzu MerchLogix jednotného přihlašování](merchlogix-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro MerchLogix ve službě Azure AD:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace > všechny aplikace**.

2. V seznamu aplikací SaaS vyberte MerchLogix.

3. Vyberte kartu **Zřizování**.

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s oddílem MerchLogix-Prisioning s možností zřizování s názvem, režim zřizování nastavený na hodnotu automaticky a možnost Test připojení se vyvolala.](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. V části **přihlašovací údaje správce** :

    * Do pole **Adresa URL tenanta** zadejte adresu URL koncového bodu SCIM, kterou vám poskytl technický kontakt MerchLogix.

    * Do pole **token tajného klíče** zadejte tajný token poskytnutý technickým kontaktem MerchLogix.

6. Po vyplnění polí zobrazených v kroku 5 klikněte na **Test připojení** , aby se služba Azure AD mohla připojit k MerchLogix. Pokud se připojení nepovede, ujistěte se, že má váš účet MerchLogix oprávnění správce, a zkuste to znovu.

7. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

8. Klikněte na **Uložit**.

9. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé MerchLogix**.

10. Zkontrolujte atributy uživatele synchronizované z Azure AD do MerchLogix v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v MerchLogix pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

11. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do MerchLogix**.

12. Zkontrolujte atributy skupiny synchronizované z Azure AD do MerchLogix v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v MerchLogix pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

13. Pokud chcete povolit službu Azure AD Provisioning pro MerchLogix, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

14. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v MerchLogix.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png

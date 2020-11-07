---
title: 'Kurz: Konfigurace BlueJeans pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro BlueJeans.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 204cdc689d5a117df428bb314a81a35081f7b13c
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357636"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Kurz: Konfigurace BlueJeans pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v BlueJeans a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro BlueJeans.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující:

* Tenant Azure AD
* Tenant BlueJeans s plánem [Moje společnost](https://www.BlueJeans.com/pricing) nebo lepší povolený
* Uživatelský účet v BlueJeans s oprávněními správce

> [!NOTE]
> Integrace zřizování Azure AD spoléhá na [rozhraní BlueJeans API](https://BlueJeans.github.io/developer), které je dostupné pro BlueJeans týmy na standardním plánu nebo lepší.

## <a name="adding-bluejeans-from-the-gallery"></a>Přidání BlueJeans z Galerie

Před konfigurací BlueJeans pro Automatické zřizování uživatelů se službou Azure AD je nutné přidat BlueJeans z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat BlueJeans z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **BlueJeans** , na panelu výsledků vyberte **BlueJeans** a pak kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![BlueJeans v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Přiřazování uživatelů k BlueJeans

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k BlueJeans. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k BlueJeans podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Důležité tipy pro přiřazení uživatelů k BlueJeans

* Doporučuje se, aby se k BlueJeans k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k BlueJeans musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Konfigurace automatického zřizování uživatelů na BlueJeans

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v BlueJeans na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro BlueJeans podle pokynů uvedených v [kurzu BlueJeans jednotného přihlašování](bluejeans-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro BlueJeans ve službě Azure AD:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **podnikové aplikace** , vyberte **všechny aplikace** a pak vyberte **BlueJeans**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **BlueJeans**.

    ![Odkaz BlueJeans v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s postranním panelem BlueJeans Enterprise aplikace s zvýrazněnou možností zřizování a vyvolala se.](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek stránky zřizování s oddíly pro zřizování a přihlašovací údaje správce s názvem.](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. V části **přihlašovací údaje správce** zadejte **uživatelské jméno správce** a **heslo správce** účtu BlueJeans. Příklady těchto hodnot:

   * V poli **uživatelské jméno správce** naplňte uživatelské jméno účtu správce v tenantovi BlueJeans. Příklad: admin@contoso.com.

   * V poli **heslo správce** vyplňte heslo odpovídající uživatelskému jménu správce.

6. Po vyplnění polí zobrazených v kroku 5 klikněte na **Test připojení** , aby se služba Azure AD mohla připojit k BlueJeans. Pokud se připojení nepovede, ujistěte se, že má váš účet BlueJeans oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky s oddílem přihlašovací údaje správce s možností připojení testu s názvem.](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Snímek obrazovky s textovým e-mailem s oznámením](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Klikněte na **Uložit**.

9. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé BlueJeans**.

    ![Snímek obrazovky oddílu mapování s zvýrazněnou možností synchronizovat Azure Active Directory uživatele do BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Zkontrolujte atributy uživatele synchronizované z Azure AD do BlueJeans v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v BlueJeans pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Snímek obrazovky s oddílem mapování atributů se zobrazenými sedmi mapováními](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Pokud chcete povolit službu Azure AD Provisioning pro BlueJeans, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Snímek obrazovky s oddílem nastavení s možností stav zřizování nastavenou na zapnuto](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit pro BlueJeans, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Snímek obrazovky s nastavením oboru se zvýrazněnou možností synchronizovat pouze přiřazení uživatelé a skupiny.](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Snímek obrazovky s postranním panelem BlueJeans Enterprise aplikace pomocí možnosti uložit s názvem.](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v BlueJeans.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* BlueJeans nepovoluje uživatelská jména, která překračují 30 znaků.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png

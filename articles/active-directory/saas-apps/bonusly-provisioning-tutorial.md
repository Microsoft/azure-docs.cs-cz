---
title: 'Kurz: Konfigurace bonusu pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřizovala uživatelské účty na Bonusly.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 879b0ee9-042a-441b-90a7-8c364d62426a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7694e441a59680a9b9544d3479100c1f779964ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058837"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Kurz: Konfigurace bonusly pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Bonusly a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-zřizování uživatelů nebo skupin bonusly.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující:

* Klient Azure AD
* [Bonusový nájemce](https://bonus.ly/pricing)
* Uživatelský účet v bonusovém účtu s oprávněními správce

> [!NOTE]
> Integrace zřizování Azure AD závisí na [bonusly rest API](https://konghq.com/solutions/gateway/), který je k dispozici pro vývojáře Bonusly.

## <a name="adding-bonusly-from-the-gallery"></a>Přidání Bonusly z galerie

Před konfigurací Bonusly pro automatické zřizování uživatelů pomocí Azure AD, je třeba přidat Bonusly z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Bonusly z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Bonusly**, z panelu výsledků vyberte **Bonusly** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

    ![Bonusně v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Přiřazení uživatelů k Bonusly

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD. 

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Bonusly. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny bonusly podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Důležité tipy pro přiřazení uživatelů k Bonusly

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k Bonusly k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k bonusové, musíte vybrat všechny platné role specifické pro aplikaci (pokud jsou k dispozici) v dialogovém okně přiřazení. Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Konfigurace automatického zřizování uživatelů na Bonusly

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Bonusly na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Bonusly podle pokynů uvedených v [kurzu Bonusly single sign-on](bonus-tutorial.md). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Bonusly ve službě Azure AD:

1. Přihlaste se na [portál Azure](https://portal.azure.com) portal a vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Bonusly**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **bonusová možnost**.

    ![Bonusový odkaz v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **Přihlašovací údaje správce** zadejte tajný **token** svého bonusového účtu, jak je popsáno v kroku 6.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. **Tajný token** pro váš bonusový účet se nachází v admin > společnosti **> integrace**. V části **Pokud chcete kódovat,** klikněte na **API > vytvořit nový přístupový token rozhraní API** a vytvořte nový tajný token.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. Na následující obrazovce zadejte název přístupového tokenu do zadaného textového pole a stiskněte **klávesu Create Api Key**. Nový přístupový token se zobrazí na několik sekund v automaticky otevírané okno.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/Token02.png)

8. Po naplnění polí uvedených v kroku 5 klikněte na **test připojení** a ujistěte se, že Azure AD můžete připojit k Bonusly. Pokud se připojení nezdaří, ujistěte se, že váš účet Bonusly má oprávnění správce a zkuste to znovu.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Klikněte na **Uložit**.

11. V části **Mapování** vyberte **synchronizovat uživatele služby Azure Active Directory s bonusy**.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Bonusly v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v Bonusly pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Chcete-li povolit službu zřizování Azure AD pro Bonusly, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. Definujte uživatele nebo skupiny, které chcete bonusálně zřídit, výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Bonusly.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png

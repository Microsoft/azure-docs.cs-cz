---
title: 'Kurz: Konfigurace samanageu pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty do Sasprávy.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060444"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Kurz: Konfigurace samanageu pro automatické zřizování uživatelů

Tento kurz ukazuje kroky, které je třeba provést v Samanage a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a deprovision uživatelů a skupin samanage.

> [!NOTE]
> Tento kurz popisuje konektor, který je postavený na nad službou zřizování uživatelů Azure AD. Informace o tom, co tato služba dělá, jak funguje, a nejčastější dotazy naleznete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací masu jako služby (SaaS) pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že máte:

* Tenanta Azure AD.
* [Samanage nájemce](https://www.samanage.com/pricing/) s balíčkem Professional.
* Uživatelský účet v Samanage s oprávněními správce.

> [!NOTE]
> Integrace zřizování Azure AD závisí na [rozhraní Samanage Rest API](https://www.samanage.com/api/). Toto rozhraní API je k dispozici vývojářům Samanage pro účty s balíčkem Professional.

## <a name="add-samanage-from-the-azure-marketplace"></a>Přidání Samanageu z Azure Marketplace

Než nakonfigurujete Samanage pro automatické zřizování uživatelů pomocí Azure AD, přidejte Samanage z Azure Marketplace do seznamu spravovaných aplikací SaaS.

Chcete-li přidat Samanage z webu Marketplace, postupujte takto.

1. Na [webu Azure Portal](https://portal.azure.com)vyberte v navigačním podokně vlevo **službu Azure Active Directory**.

    ![Ikona služby Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte v horní části dialogového okna možnost **Nová aplikace.**

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Samanage** a z panelu výsledků vyberte **Samanage.** Chcete-li aplikaci přidat, vyberte **přidat**.

    ![Samanage v seznamu výsledků](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Přiřazení uživatelů ke samanageu

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů jsou synchronizováni pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů se rozhodněte, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup ke sasporu. Chcete-li těmto uživatelům nebo skupinám přiřadit samanage, postupujte podle pokynů v části [Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Důležité tipy pro přiřazení uživatelů k Samanage

*    Dnes samanage role jsou automaticky a dynamicky naplnění v unovém rozhraní portálu Azure. Před přiřazením role Samanage uživatelům, ujistěte se, že počáteční synchronizace je dokončena proti Samanage načíst nejnovější role ve vašem tenantovi Samanage.

*    Doporučujeme přiřadit jednoho uživatele Azure AD ssmanage otestovat počáteční konfiguraci automatického zřizování uživatelů. Další uživatele a skupiny můžete přiřadit později po úspěšném testování.

*    Když přiřadíte uživatele ke samanageu, vyberte v dialogovém okně přiřazení libovolnou platnou roli specifickou pro aplikaci, pokud je k dispozici. Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Konfigurace automatického zřizování uživatelů pro Samanage

Tato část vás provede kroky ke konfiguraci služby zřizování Azure AD. Použijte ji k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Samanage na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování saml pro Samanage. Postupujte podle pokynů v [samanage jednotného přihlášení tutorial](samanage-tutorial.md). Jednotné přihlašování lze konfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce se vzájemně doplňují.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Samanage ve službě Azure AD

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace** > **Všechny aplikace** > **Samanage**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Samanage**.

    ![Odkaz Samanage v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Samanage Zřizování](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Přihlašovací údaje správce** zadejte adresu URL **klienta** Samanage a **tajný token**. Klikněte na **Testovat připojení** a ujistěte se, že Azure AD můžete připojit k Samanage. Pokud se připojení nezdaří, ujistěte se, že váš účet Samanage má oprávnění správce a zkuste to znovu.

    ![Samanage testovací připojení](./media/samanage-provisioning-tutorial/provisioning.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, aby se zjištovací oznámení o chybách zřizování zjištovací oznámení. Zaškrtněte **políčko Odeslat e-mailové oznámení, když dojde k chybě.**

    ![Samanage oznámení e-mail](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Vyberte **Uložit**.

8. V části **Mapování** vyberte **synchronizovat uživatele služby Azure Active Directory se samanage .**

    ![Samanage synchronizace uživatelů](./media/samanage-provisioning-tutorial/UserMappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Samanage v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v Samanage pro operace aktualizace. Chcete-li uložit změny, vyberte **uložit**.

    ![Samanage odpovídající uživatelské atributy](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. Pokud chcete povolit mapování skupin, vyberte v části **Mapování** **možnost Synchronizovat skupiny služby Azure Active Directory se samanageem**.

    ![Synchronizace skupinsa Spravovat](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. Nastavit **možnost Zapnuto** na **Ano** pro synchronizaci skupin Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do Samanage v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v Samanage pro operace aktualizace. Chcete-li uložit změny, vyberte **uložit**.

    ![Samanage odpovídající skupiny atributy](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Chcete-li konfigurovat filtry oborů, postupujte podle pokynů v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro Samanage, v části **Nastavení** **změňte stav zřizování** **na Zapnuto**.

    ![Samanage Stav zřizování](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit samanage. V části **Nastavení** vyberte požadované hodnoty v **oboru**. Když vyberete možnost **Synchronizovat všechny uživatele a skupiny,** zvažte omezení, jak je popsáno v následující části "Omezení konektoru".

    ![Samanage obor](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. Až budete připraveni k zřízení, vyberte **Uložit**.

    ![Samanage Uložit](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než pozdější synchronizace. Dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. 

Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivity zřizování. Sestava popisuje všechny akce prováděné službou zřizování Azure AD na Samanage.

Informace o tom, jak číst protokoly zřizování Azure AD, naleznete v [tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

Pokud vyberete možnost **Synchronizovat všechny uživatele a skupiny** a nakonfigurujete hodnotu atributu **Role** Samanage, musí být hodnota pod **polem Výchozí hodnota, pokud je null (volitelně)** vyjádřena v následujícím formátu:

- {"displayName":"role"}, kde role je výchozí hodnota, kterou chcete.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png

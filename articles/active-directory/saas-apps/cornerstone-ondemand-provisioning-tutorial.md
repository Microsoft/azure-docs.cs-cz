---
title: 'Kurz: Konfigurace základního kamene ondemand pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty cornerstone ondemand.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6212e74ecbf8327d3939138de2e92868f29b0f1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058418"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Kurz: Konfigurace základního kamene OnDemand pro automatické zřizování uživatelů

Tento kurz ukazuje kroky, které je třeba provést v Cornerstone OnDemand a Azure Active Directory (Azure AD) pro konfiguraci Azure AD tak, aby automaticky zřizovala a odstavovala uživatele nebo skupiny cornerstone OnDemand.

> [!NOTE]
> Tento kurz popisuje konektor, který je postavený na nad službou zřizování uživatelů Azure AD. Informace o tom, co tato služba dělá, jak funguje, a nejčastější dotazy naleznete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací masu jako služby (SaaS) pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že máte:

* Tenanta Azure AD.
* Základní kámen NaDemand nájemce.
* Uživatelský účet v Cornerstone OnDemand s oprávněními správce.

> [!NOTE]
> Integrace zřizování Azure AD závisí na [webové službě Cornerstone OnDemand](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf). Tato služba je k dispozici pro týmy Cornerstone OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Přidání základního kamene na vyžádání z Azure Marketplace

Než nakonfigurujete Cornerstone OnDemand pro automatické zřizování uživatelů pomocí Azure AD, přidejte Cornerstone OnDemand z Marketplace do seznamu spravovaných aplikací SaaS.

Chcete-li přidat Cornerstone OnDemand z marketplace, postupujte takto.

1. Na [webu Azure Portal](https://portal.azure.com)vyberte v navigačním podokně vlevo **službu Azure Active Directory**.

    ![Ikona služby Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte v horní části dialogového okna možnost **Nová aplikace.**

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Cornerstone OnDemand** a z panelu výsledků vyberte **Cornerstone OnDemand.** Chcete-li aplikaci přidat, vyberte **přidat**.

    ![Cornerstone OnDemand v seznamu výsledků](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Přiřazení uživatelů k Cornerstone OnDemand

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů jsou synchronizováni pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů se rozhodněte, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Cornerstone OnDemand. Chcete-li tyto uživatele nebo skupiny přiřadit k cornerstoneondemand, postupujte podle pokynů v části [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Důležité tipy pro přiřazení uživatelů k Cornerstone OnDemand

* Doporučujeme přiřadit jednoho uživatele Azure AD na Cornerstone OnDemand k testování konfigurace automatického zřizování uživatelů. Později můžete přiřadit další uživatele nebo skupiny.

* Když přiřadíte uživatele k Cornerstone OnDemand, vyberte v dialogovém okně přiřazení libovolnou platnou roli specifickou pro aplikaci, pokud je k dispozici. Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Konfigurace automatického zřizování uživatelů na Cornerstone OnDemand

Tato část vás provede kroky ke konfiguraci služby zřizování Azure AD. Použijte ji k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Cornerstone OnDemand na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

Chcete-li nakonfigurovat automatické zřizování uživatelů pro Cornerstone OnDemand ve službě Azure AD, postupujte takto.

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace** > **Všechny aplikace** > **Cornerstone OnDemand**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Základní kámen OnDemand**.

    ![Základní kámen OnDemand odkaz v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Základní kámen OnDemand Zřizování](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Základní kámen OnDemand zřizování režimu](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **Přihlašovací údaje správce** zadejte uživatelské jméno správce, heslo správce a doménu účtu Cornerstone OnDemand:

    * V poli **Uživatelské jméno správce** vyplňte doménu nebo uživatelské jméno účtu správce v tenantovi Cornerstone OnDemand. Příkladem je contoso\admin.

    * Do pole **Heslo správce** vyplňte heslo, které odpovídá uživatelskému jménu správce.

    * V poli **Doména** vyplňte adresu URL webové služby klienta Cornerstone OnDemand. Služba je například umístěna na adrese `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, a `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`pro společnost Contoso je doména . Další informace o tom, jak načíst adresu URL webové služby, naleznete v [tomto pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Po vyplnění polí uvedených v kroku 5 vyberte **testovat připojení** a ujistěte se, že Azure AD můžete připojit k Cornerstone OnDemand. Pokud se připojení nezdaří, ujistěte se, že váš základní kámen OnDemand účet má oprávnění správce a zkuste to znovu.

    ![Základní kámen OnDemand Test Připojení](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, aby se zjištovací oznámení o chybách zřizování zjištovací oznámení. Zaškrtněte **políčko Odeslat e-mailové oznámení, když dojde k chybě.**

    ![Základní kámen ondemand oznámení e-mail](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Vyberte **Uložit**.

9. V části Mapování vyberte **synchronizovat uživatele služby Azure Active Directory s cornerstone OnDemand**. **Mappings**

    ![Základní kámen OnDemand synchronizace](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Cornerstone OnDemand v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v Cornerstone OnDemand pro operace aktualizace. Chcete-li uložit změny, vyberte **uložit**.

    ![Základní kámen OnDemand mapování atributů](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Chcete-li konfigurovat filtry oborů, postupujte podle pokynů v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Chcete-li povolit službu zřizování Azure AD pro Cornerstone OnDemand, v části **Nastavení** změňte **stav zřizování** na **Zapnuto**.

    ![Základní kámen OnDemand Zřizování Stav](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit Cornerstone OnDemand. V části **Nastavení** vyberte požadované hodnoty v **oboru**.

    ![Základní kámen na poptávku rozsah](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Až budete připraveni k zřízení, vyberte **Uložit**.

    ![Základní kámen OnDemand Uložit](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než pozdější synchronizace. Dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. 

Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivity zřizování. Sestava popisuje všechny akce prováděné službou zřizování Azure AD na Cornerstone OnDemand.

Informace o tom, jak číst protokoly zřizování Azure AD, naleznete v [tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

Základní kámen OnDemand **Position** atribut očekává hodnotu, která odpovídá rolím na portálu Cornerstone OnDemand. Seznam platných hodnot **pozice** získáte na webu **Úprava uživatelského záznamu > organizační struktura > pozice** na portálu Cornerstone OnDemand.

![Základní kámen OnDemand Zřizování Upravit uživatelský záznam](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Základní kámen OnDemand Zřizování pozice](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Základní kámen OnDemand Provisioning seznam pozic](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png

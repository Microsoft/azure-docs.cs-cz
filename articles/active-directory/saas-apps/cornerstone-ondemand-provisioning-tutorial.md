---
title: 'Kurz: Nakonfigurujte základní kámen OnDemand pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a zrušit jejich zřízení uživatelských účtů do základní kámen OnDemand.
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
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85ddcf3aff7d15c946230cedb0da190bca6aeab7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127493"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Kurz: Nakonfigurujte základní kámen OnDemand pro automatické zřizování uživatelů

V tomto kurzu si ukážeme postup provést v základním kamenem OnDemand a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a zrušit jejich zřízení uživatelů nebo skupin k základní kámen OnDemand.

> [!NOTE]
> Tento kurz popisuje konektor, který je postavený na služba zřizování uživatelů Azure AD. Informace o význam této služby, jak to funguje a nejčastější dotazy, naleznete v tématu [automatizace zřizování uživatelů a jeho rušení pro software-as-a-service (SaaS) aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte:

* Tenanta Azure AD.
* Základní kámen OnDemand tenanta.
* Účet uživatele s oprávněními správce v základním kamenem OnDemand.

> [!NOTE]
> Zřizování integrace Azure AD spoléhá na [základní kámen OnDemand webová služba](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf). Tato služba je k dispozici pro týmy základní kámen OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Přidat základní kámen OnDemand na webu Azure Marketplace

Než nakonfigurujete základní kámen OnDemand pro automatické zřizování uživatelů pomocí Azure AD, přidejte základní kámen OnDemand z Marketplace si na seznam spravovaných aplikací SaaS.

Přidat základní kámen OnDemand z webu Marketplace, postupujte podle těchto kroků.

1. V [webu Azure portal](https://portal.azure.com), v navigačním podokně na levé straně vyberte **Azure Active Directory**.

    ![Ikona služby Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **základní kámen OnDemand** a vyberte **základní kámen OnDemand** na panelu výsledků. Chcete-li přidat aplikaci, **přidat**.

    ![Základní kámen OnDemand v seznamu výsledků](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Přiřazení uživatelů k základní kámen OnDemand.

Azure Active Directory používá koncept volá *přiřazení* určit, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Než začnete konfigurovat a povolit automatické zřizování uživatelů, rozhodněte, které uživatele nebo skupiny ve službě Azure AD potřebují přístup k základním kamenem OnDemand. Základní kámen OnDemand přiřadit tyto uživatele nebo skupiny, postupujte podle pokynů v [přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Důležité tipy pro přiřazování uživatelů k základní kámen OnDemand.

* Doporučujeme vám, přiřadit jeden uživatele Azure AD pro základní kámen OnDemand pro testování automatické konfigurace zřizování uživatelů. Můžete přiřadit dalším uživatelům nebo skupinám později.

* Pokud přiřadíte OnDemand základní kámen uživatele, vyberte libovolný platný specifické pro aplikaci roli, pokud je k dispozici, v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Konfigurovat automatické zřizování uživatelů pro základní kámen OnDemand.

Tato část vás provede kroky konfigurace zřizování služby Azure AD. Umožňuje vytvářet, aktualizovat a zakázat uživatele nebo skupiny v základním kamenem OnDemand podle uživatele nebo skupiny přiřazení ve službě Azure AD.

Pokud chcete nakonfigurovat automatické zřizování uživatelů pro základní kámen OnDemand ve službě Azure AD, postupujte takto.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace** > **všechny aplikace** > **základní kámen OnDemand**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **základní kámen OnDemand**.

    ![Základní kámen OnDemand odkaz v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Základní kámen OnDemand zřizování](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Režim zřizování základní kámen OnDemand.](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **přihlašovacích údajů správce** části, zadejte uživatelské jméno správce, správce heslo a doménu účtu vaší základní kámen OnDemand:

    * V **uživatelské jméno správce** pole, zadejte domény nebo uživatelské jméno účtu správce ve svém tenantovi základní kámen OnDemand. Příkladem je contoso\admin.

    * V **heslo správce** pole, zadejte heslo, které odpovídá uživatelské jméno správce.

    * V **domény** pole, zadejte adresu URL webové služby základní kámen OnDemand tenanta. Například se služba nachází v `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, a pro doménu Contoso doména `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Další informace o tom, jak načíst adresu URL webové služby najdete v tématu [tento pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Po vyplnění polí uvedené v kroku 5, vyberte **Test připojení** Ujistěte se, že Azure AD lze připojí k základní kámen OnDemand. Pokud se nepovede, ujistěte se, že váš základní kámen OnDemand účet má oprávnění správce a zkuste to znovu.

    ![Základní kámen OnDemand Test připojení](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. V **e-mailové oznámení** pole, zadejte e-mailovou adresu osoby nebo skupiny, ke zřizování chyba oznámení vůbec dostat. Vyberte **odeslání e-mailové oznámení, když dojde k selhání** zaškrtávací políčko.

    ![Základní kámen OnDemand oznámení e-mailu](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Vyberte **Uložit**.

9. V části **mapování** vyberte **synchronizace Azure Active Directory Users na základní kámen OnDemand**.

    ![Základní kámen OnDemand synchronizace](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do základní kámen OnDemand v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v základním kamenem OnDemand pro operace update. Chcete-li uložit všechny změny, vyberte **Uložit**.

    ![Mapování atributů OnDemand základní kámen](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Konfigurace filtrů oborů, postupujte podle pokynů [oboru filtru kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Povolit zřizování pro OnDemand základní kámen služby v Azure AD **nastavení** oddíl, změna **stavu zřizování** k **na**.

    ![Stav zřizování základní kámen OnDemand.](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Definujte uživatele nebo skupiny, které chcete pro poskytnutí základní kámen OnDemand. V **nastavení** vyberte hodnoty, které chcete v **oboru**.

    ![Základní kámen OnDemand oboru](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Jakmile budete připraveni ke zřízení, vyberte **Uložit**.

    ![Základní kámen OnDemand uložit](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupinám definovaným v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než novější se synchronizuje. Odehrávalo přibližně každých 40 minut za předpokladu, spustí služba zřizování Azure AD. 

Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na sestavu aktivit zřizování. Sestava popisuje všechny akce prováděné zřizování na OnDemand základní kámen služby Azure AD.

Informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

Základní kámen OnDemand **pozice** atribut očekává hodnotu, která odpovídá rolí na portálu pro základní kámen OnDemand. K získání seznamu platných **pozice** hodnoty, přejděte na **upravit záznam uživatele > Struktura organizace > pozici** portálu základní kámen OnDemand.

![Základní kámen OnDemand zřizování upravit záznam uživatele](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Základní kámen OnDemand zřizování pozice](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Základní kámen OnDemand zřizování umístění seznamu](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png

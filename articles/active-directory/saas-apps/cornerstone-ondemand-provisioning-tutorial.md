---
title: 'Kurz: Nakonfigurujte základní kámen OnDemand pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do základní kámen OnDemand.
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
ms.date: 01/31/2018
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: a24945cae273e072d3be314a66248674e25fe287
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198860"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Kurz: Nakonfigurujte základní kámen OnDemand pro automatické zřizování uživatelů


Cílem tohoto kurzu je předvést postup provést v základním kamenem OnDemand a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se základním kamenem OnDemand.


> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

*   Klient služby Azure AD
*   Základní kámen OnDemand tenanta
*   Uživatelský účet v základním kamenem OnDemand s oprávněními správce


> [!NOTE]
> Zřizování integrace Azure AD spoléhá na [základní kámen OnDemand Webservice](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf), který je dostupný pro základní kámen OnDemand týmy.

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Přidání základní kámen OnDemand z Galerie
Před konfigurací základní kámen OnDemand pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat základní kámen OnDemand z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat základní kámen OnDemand z Galerie aplikací Azure AD, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace** > **všechny aplikace**.

    ![Podnikové aplikace oddílu][2]
    
3. Chcete-li přidat základní kámen OnDemand, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **základní kámen OnDemand**.

    ![Základní kámen OnDemand zřizování](./media/cornerstone-ondemand-provisioning-tutorial/AppSearch.png)

5. Na panelu výsledků vyberte **základní kámen OnDemand**a potom klikněte na tlačítko **přidat** tlačítko pro přidání do seznamu aplikací SaaS základní kámen OnDemand.

    ![Základní kámen OnDemand zřizování](./media/cornerstone-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![Základní kámen OnDemand zřizování](./media/cornerstone-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cornerstone-ondemand"></a>Přiřazování uživatelů k základní kámen OnDemand.

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele a/nebo skupiny, které se "přiřadily" aplikace ve službě Azure AD. 

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k základním kamenem OnDemand. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny základní kámen OnDemand podle zde uvedených pokynů:

*   [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Důležité tipy pro přiřazování uživatelů k základní kámen OnDemand.

*   Dále je doporučeno jednoho uživatele Azure AD je přiřazená základní kámen OnDemand pro testování automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k základní kámen OnDemand, je nutné vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-cornerstone-ondemand"></a>Konfigurace automatické zřizování uživatelů pro základní kámen OnDemand.

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v základním kamenem OnDemand podle uživatele a/nebo přiřazení skupin ve službě Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cornerstone-ondemand-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro základní kámen OnDemand ve službě Azure AD:


1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace > všechny aplikace**.

2. Vyberte základní kámen OnDemand ze seznamu aplikací SaaS.
 
    ![Základní kámen OnDemand zřizování](./media/cornerstone-ondemand-provisioning-tutorial/Successcenter2.png)

3. Vyberte **zřizování** kartu.

    ![Základní kámen OnDemand zřizování](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Základní kámen OnDemand zřizování](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **přihlašovacích údajů správce** části, zadejte **uživatelské jméno správce**, **heslo správce**, a **domény** z vaší základní kámen OnDemand účet.

    *   V **uživatelské jméno správce** pole, naplnění doména\uživatelské jméno účtu správce ve svém tenantovi základní kámen OnDemand. Příklad: contoso\admin.

    *   V **heslo správce** pole, vyplnit odpovídající uživatelské jméno správce heslo.

    *   V **domény** pole, vyplnit adresy URL webové služby základní kámen OnDemand tenanta. Příklad: Služba se nachází na `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`pro doménu Contoso je `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Další informace o tom, jak načíst adresu URL webové služby najdete v tématu [tady](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k základní kámen OnDemand. Pokud se nepovede, ujistěte se, že váš základní kámen OnDemand účet má oprávnění správce a zkuste to znovu.

    ![Základní kámen OnDemand zřizování](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![Základní kámen OnDemand zřizování](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Klikněte na **Uložit**.

9. V části **mapování** vyberte **synchronizace Azure Active Directory Users na základní kámen OnDemand**.

    ![Základní kámen OnDemand zřizování](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do základní kámen OnDemand v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v základním kamenem OnDemand pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Základní kámen OnDemand zřizování](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Povolit zřizování pro OnDemand základní kámen služby Azure AD, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Základní kámen OnDemand zřizování](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Definovat uživatele a/nebo skupiny, které chcete ke zřízení na základní kámen OnDemand výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Základní kámen OnDemand zřizování](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Základní kámen OnDemand zřizování](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)


Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce prováděné zřizování na OnDemand základní kámen služby Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).
## <a name="connector-limitations"></a>Omezení konektoru

* Základní kámen OnDemand **pozice** atribut očekává hodnotu, která odpovídá rolí na portálu pro základní kámen OnDemand. Seznam platných **pozice** hodnoty lze získat tak, že přejdete do **upravit záznam uživatele > Struktura organizace > pozici** portálu základní kámen OnDemand.
    ![Základní kámen OnDemand zřizování upravit uživatele](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png) ![základní kámen OnDemand zřizování pozice](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png) ![základní kámen OnDemand zřizování seznam pozic](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)
    
## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png

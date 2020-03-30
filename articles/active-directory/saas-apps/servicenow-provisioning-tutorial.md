---
title: 'Kurz: Konfigurace služby ServiceNow pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak automaticky zřídit a odpojit uživatelské účty z Azure AD do ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 9e93d4b3f1880f2ac56a32a7b85aa6801fb7c14e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205095"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Kurz: Konfigurace služby Nyní pro automatické zřizování uživatelů

Tento kurz popisuje kroky, které je potřeba provést v ServiceNow a Azure Active Directory (Azure AD) ke konfiguraci automatickézřivačné zřizování uživatelů. Při konfiguraci Azure AD automaticky zřídí a de-zřídí uživatele a skupiny [ServiceNow](https://www.servicenow.com/) pomocí služby Azure AD zřizování. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytvoření uživatelů v ServiceNow
> * Odebrání uživatelů v servicenow, když už nevyžadují přístup
> * Zachovat atributy uživatelů synchronizované mezi Azure AD a ServiceNow
> * Zřídit skupiny a členství ve skupinách v ServiceNow
> * [Jednotné přihlašování](servicenow-tutorial.md) k ServiceNow (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* [Klient Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikací, správce cloudových aplikací, vlastník aplikace nebo globální správce). 
* Instance [ServiceNow](https://www.servicenow.com/) calgary nebo vyšší
* Instance [ServiceNow Express](https://www.servicenow.com/) v Helsinkách nebo vyšší
* Uživatelský účet v ServiceNow s rolí správce

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si o [tom, jak funguje služba zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se [mají mapovat mezi Azure AD a ServiceNow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace služby ServiceNow pro podporu zřizování pomocí Azure AD

1. Identifikujte název instance ServiceNow. Název instance najdete v adrese URL, kterou používáte pro přístup k servicenow. V níže uvedeném příkladu je název instance dev35214.

![ServiceNow Instance](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. Získejte přihlašovací údaje pro správce v ServiceNow. Přejděte do profilu uživatele v ServiceNow a ověřte, zda má uživatel roli správce. 

![Role správce ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání služby ServiceNow z galerie aplikací Azure AD

Přidejte ServiceNow z galerie aplikací Azure AD a začněte spravovat zřizování servicenow. Pokud jste dříve nastavili ServiceNow pro sso, můžete použít stejnou aplikaci. Doporučujeme však vytvořit samostatnou aplikaci při testování integrace zpočátku. Další informace o přidání aplikace z galerie [naleznete zde](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování 

Služba zřizování Azure AD umožňuje obor, který bude zřízen na základě přiřazení k aplikaci a nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete obor, kdo bude zřízen do vaší aplikace na základě přiřazení, můžete použít následující [kroky](../manage-apps/assign-user-or-group-access-portal.md) k přiřazení uživatelů a skupin k aplikaci. Pokud se rozhodnete obor, který bude zřízen výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oborů, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin službě ServiceNow je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí výchozí přístup jsou vyloučeni z zřizování a budou označeny jako neefektivně oprávněné v protokolech zřizování. Pokud je k dispozici pouze role v aplikaci výchozí přístupová role, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Vyzkoušejte s malou sadou uživatelů a skupin před zavedením pro všechny. Pokud je obor zřizování nastaven na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin do aplikace. Pokud je obor nastaven na všechny uživatele a skupiny, můžete určit [filtr oborů založených na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Krok 5. Konfigurace automatického zřizování uživatelů na ServiceNow 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Aplikaci TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro ServiceNow ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **ServiceNow**.

    ![Odkaz ServiceNow v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Přihlašovací údaje správce** zadejte svá pověření správce ServiceNow a uživatelské jméno. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k ServiceNow. Pokud se připojení nezdaří, ujistěte se, že váš účet ServiceNow má oprávnění správce a zkuste to znovu.

    ![Zajišťování](./media/servicenow-provisioning-tutorial/provisioning.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě.**

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části Mapování vyberte **synchronizovat uživatele služby Azure Active Directory s servicenow**. **Mappings**

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do ServiceNow v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v ServiceNow pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, že rozhraní API ServiceNow podporuje filtrování uživatelů na základě tohoto atributu. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

10. V části **Mapování** vyberte **Synchronizovat skupiny Azure Active Directory do služby ServiceNow**.

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do ServiceNow v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v ServiceNow pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro ServiceNow, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit ServiceNow výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení.** Počáteční cyklus trvá déle než následující cykly, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po konfiguraci zřizování můžete ke sledování nasazení použít následující prostředky:

1. Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé byli zřízeni úspěšně nebo neúspěšně
2. Zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) zobrazíte stav cyklu zřizování a jak blízko je k dokončení
3. Pokud se zdá, že konfigurace zřizování je v nestavu, aplikace přejde do karantény. Další informace o stavech karantény naleznete [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Tipy pro řešení problémů
* **InvalidLookupReference:** Při zřizování určitých atributů, jako je například oddělení a umístění v ServiceNow, hodnoty musí již existovat v referenční tabulce v ServiceNow. Můžete mít například dvě umístění (Seattle, Los Angeles) a tři oddělení (Prodej, Finance, Marketing) v tabulce **název tabulky vložení** v ServiceNow. Pokud se pokusíte zřídit uživatele, kde jeho oddělení je "Prodej" a umístění je "Seattle" bude úspěšně zřízena. Pokud se pokusíte zřídit uživatele s oddělením "Prodej" a umístění "LA", uživatel nebude zřízen. Umístění LA musí být přidáno do referenční tabulky v ServiceNow nebo atribut uživatele ve službě Azure AD musí být aktualizován tak, aby odpovídal formátu v ServiceNow. 
* **EntryJoiningPropertyValueIsMissing:** Zkontrolujte [mapování atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) a určete odpovídající atribut. Tato hodnota musí být k dispozici u uživatele nebo skupiny, kterou se pokoušíte zřídit. 
* Zkontrolujte [rozhraní SERVICENow SOAP API,](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) abyste pochopili všechny požadavky nebo omezení (například formát pro určení kódu země pro uživatele)
* Některá nasazení ServiceNow vyžadují povolení rozsahů IP adres pro zřizovací službu Azure AD. Vyhrazené rozsahy IP adres pro zřizovací službu Azure AD najdete [zde pod](https://www.microsoft.com/download/details.aspx?id=56519) "AzureActiveDirectoryDomainServices".
* Zřizování požadavků jsou odesílány ve výchozím nastavení https://{your-instance-name}.service-now.com/{table-name} . Pokud požadujete vlastní adresu URL klienta, můžete zadat celou adresu URL v poli názvu instance.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

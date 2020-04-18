---
title: 'Kurz: Konfigurace cesty Juno pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak automaticky zřídit a odpojit uživatelské účty z Azure AD na Juno Journey.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 79813b19-c96e-4459-a4e5-636a6b6f5041
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2020
ms.author: Zhchia
ms.openlocfilehash: 08de07a52d1e43dea91e6684d33027d8bcad61fc
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641846"
---
# <a name="tutorial-configure-juno-journey-for-automatic-user-provisioning"></a>Kurz: Konfigurace juno journey pro automatické zřizování uživatelů

Tento kurz popisuje kroky, které je potřeba provést v Juno Journey a Azure Active Directory (Azure AD) ke konfiguraci automatickézřivačné nastavení uživatelů. Při konfiguraci Azure AD automaticky zřídí a de-zřídí uživatele a skupiny [Juno Journey](https://www.junojourney.com/) pomocí služby Azure AD zřizování. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Juno Journey
> * Odebrání uživatelů v Juno Journey, když už nevyžadují přístup
> * Zachovat atributy uživatelů synchronizované mezi Azure AD a Juno Journey
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial) k Juno Journey (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* [Klient Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikací, správce cloudových aplikací, vlastník aplikace nebo globální správce). 
*  [Nájemce Juno Journey](https://www.junojourney.com/getstartedwithjuno).
*  Uživatelský účet v Juno Journey s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si o [tom, jak funguje služba zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se [mají mapovat mezi Azure AD a Juno Journey](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-juno-journey-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace juno journey pro podporu zřizování pomocí Azure AD

1. Pro **tajný token** a adresu URL **klienta** kontaktujte tým podpory Juno Journey na adrese support@the-juno.com. Tato hodnota se zadá do polí **tajný token** a **adresa URL klienta** v kartě Zřizování aplikace Juno Journey na webu Azure Portal. 

## <a name="step-3-add-juno-journey-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Juno Journey z galerie aplikací Azure AD

Přidejte Juno Journey z galerie aplikací Azure AD a začněte spravovat zřizování juno journey. Pokud jste již dříve nastavili Juno Journey pro spřijízd, můžete použít stejnou aplikaci. Doporučujeme však vytvořit samostatnou aplikaci při testování integrace zpočátku. Další informace o přidání aplikace z galerie [naleznete zde](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování 

Služba zřizování Azure AD umožňuje obor, který bude zřízen na základě přiřazení k aplikaci a nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete obor, kdo bude zřízen do vaší aplikace na základě přiřazení, můžete použít následující [kroky](../manage-apps/assign-user-or-group-access-portal.md) k přiřazení uživatelů a skupin k aplikaci. Pokud se rozhodnete obor, který bude zřízen výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oborů, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k cestě Juno je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí výchozí přístup jsou vyloučeni z zřizování a budou označeny jako neefektivně oprávněné v protokolech zřizování. Pokud je k dispozici pouze role v aplikaci výchozí přístupová role, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Vyzkoušejte s malou sadou uživatelů a skupin před zavedením pro všechny. Pokud je obor zřizování nastaven na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin do aplikace. Pokud je obor nastaven na všechny uživatele a skupiny, můžete určit [filtr oborů založených na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-juno-journey"></a>Krok 5. Konfigurace automatického zřizování uživatelů na Juno Journey 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Aplikaci TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-juno-journey-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Juno Journey ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Juno Journey**.

    ![Odkaz Juno Journey v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** zadejte hodnotu adresy URL klienta načtenou dříve v adrese **URL klienta**. Zadejte hodnotu tajného tokenu načtenou dříve v **tajném tokenu**. Klikněte na **Testovat připojení** a ujistěte se, že azure ad můžete připojit k Juno Journey. Pokud se připojení nezdaří, ujistěte se, že váš účet Juno Journey má oprávnění správce a zkuste to znovu.

    ![Zajišťování](./media/juno-journey-provisioning-tutorial/provisioning.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě.**

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s juno journey**.

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Juno Journey v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Juno Journey pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, že rozhraní JUNO Journey API podporuje filtrování uživatelů na základě tohoto atributu. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

   |Proměnná|Typ|
   |---|---|
   |userName|Řetězec|
   |externalId|Řetězec|
   |displayName|Řetězec|
   |title|Řetězec|
   |Aktivní|Logická hodnota|
   |preferredLanguage|Řetězec|
   |e-maily[zadejte eq "práce"].hodnota|Řetězec|
   |adresy[zadejte eq "work"].země|Řetězec|
   |adresy[zadejte eq "work"].region|Řetězec|
   |adresy[zadejte eq "práce"].lokalita|Řetězec|
   |adresy[zadejte eq "práce"].PSČ|Řetězec|
   |adresy[zadejte eq "work"].formátované|Řetězec|
   |addresses[zadejte eq "work"].streetAddress|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |název.middleName|Řetězec|
   |název.formátován|Řetězec|
   |phoneNumbers[zadejte eq "fax"].value|Řetězec|
   |phoneNumbers[zadejte eq "mobile"].value|Řetězec|
   |phoneNumbers[zadejte eq "work"].value|Řetězec|
   |urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:oddělení|Řetězec|
   |urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:employeeNumber|Řetězec|
   |urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:costCenter|Řetězec|
   urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:dělení|Řetězec|
   urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:manažer|Řetězec|
   urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:organizace|Řetězec|


10. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Chcete-li povolit službu zřizování Azure AD pro Juno Journey, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit Juno Journey výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

13. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení.** Počáteční cyklus trvá déle než následující cykly, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po konfiguraci zřizování můžete ke sledování nasazení použít následující prostředky:

* Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé byli zřízeni úspěšně nebo neúspěšně
* Zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) zobrazíte stav cyklu zřizování a jak blízko je k dokončení
* Pokud se zdá, že konfigurace zřizování je v nestavu, aplikace přejde do karantény. Další informace o stavech karantény naleznete [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../manage-apps/check-status-user-account-provisioning.md)

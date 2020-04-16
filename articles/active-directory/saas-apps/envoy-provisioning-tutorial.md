---
title: 'Kurz: Konfigurace funkce Vyslance pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak automaticky zřídit a odpojit uživatelské účty z Azure AD na vyslance.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: 68e17ba1dd5981e565e56d6c8137f77d33ad755b
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393496"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>Kurz: Konfigurace vyslance pro automatické zřizování uživatelů

Tento kurz popisuje kroky, které je potřeba provést v jazyce Envoy a Azure Active Directory (Azure AD) ke konfiguraci automatickézřivačné nastavení uživatelů. Při konfiguraci Azure AD automaticky zřídí a de-zřazení uživatelů a skupin [vyslanci](https://envoy.com/pricing/) pomocí služby Azure AD zřizování. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytvoření uživatelů v vyslance
> * Odebrání uživatelů v vyslance, pokud již nevyžadují přístup
> * Zachovat atributy uživatele synchronizované mezi Azure AD a Envoy
> * Zřizování skupin a členství ve skupinách v vyslanci
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-tutorial) k vyslanci (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* [Klient Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikací, správce cloudových aplikací, vlastník aplikace nebo globální správce). 
* [Vyslance nájemce](https://envoy.com/pricing/).
* Uživatelský účet v vyslance s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si o [tom, jak funguje služba zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se [mají mapovat mezi Azure AD a Envoy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-envoy-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace vyslance pro podporu zřizování pomocí Azure AD

1. Přihlaste se ke [konzoli Pro správu vyslance](https://dashboard.envoy.com/login). Klikněte na **Integrace**.

    ![Integrace vyslanců](media/envoy-provisioning-tutorial/envoy01.png)

2. Kliknutím na **Nainstalovat** pro **integraci Microsoft Azure SCIM**.

    ![Instalace vyslance](media/envoy-provisioning-tutorial/envoy02.png)

3. Klikněte na **Uložit** pro **synchronizaci všech uživatelů**. 

    ![Uložit vyslance](media/envoy-provisioning-tutorial/envoy03.png)

4. Zkopírujte **TOKEN NOSIČE OAUTH**. Tato hodnota se zadá do pole **Tajný token** na kartě zřizování aplikace Envoy na portálu Azure.
    
    ![Vyslanec OAUTH](media/envoy-provisioning-tutorial/envoy04.png)

## <a name="step-3-add-envoy-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání vyslance z galerie aplikací Azure AD

Přidejte vyslance z galerie aplikací Azure AD a začněte spravovat zřizování pro vyslance. Pokud jste dříve nastavili vyslance pro sso, můžete použít stejnou aplikaci. Doporučujeme však vytvořit samostatnou aplikaci při testování integrace zpočátku. Další informace o přidání aplikace z galerie [naleznete zde](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování 

Služba zřizování Azure AD umožňuje obor, který bude zřízen na základě přiřazení k aplikaci a nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete obor, kdo bude zřízen do vaší aplikace na základě přiřazení, můžete použít následující [kroky](../manage-apps/assign-user-or-group-access-portal.md) k přiřazení uživatelů a skupin k aplikaci. Pokud se rozhodnete obor, který bude zřízen výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oborů, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin vyslanci je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí výchozí přístup jsou vyloučeni z zřizování a budou označeny jako neefektivně oprávněné v protokolech zřizování. Pokud je k dispozici pouze role v aplikaci výchozí přístupová role, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Vyzkoušejte s malou sadou uživatelů a skupin před zavedením pro všechny. Pokud je obor zřizování nastaven na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin do aplikace. Pokud je obor nastaven na všechny uživatele a skupiny, můžete určit [filtr oborů založených na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-envoy"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro vyslance 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Aplikaci TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro vyslance ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **vyslance**.

    ![Odkaz Vyslance v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://app.envoy.com/scim/v2` zadejte adresu **URL klienta**. Zadejte hodnotu **TOKEN NOSIČE OAUTH** načtenou dříve v **tajném tokenu**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k vyslance. Pokud se připojení nezdaří, ujistěte se, že váš účet Envoy má oprávnění správce a zkuste to znovu.

   ![Zajišťování](./media/envoy-tutorial/provisioning.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě.**

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **Mapování** vyberte **synchronizovat uživatele služby Azure Active Directory s vyslancem**.

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na vyslance v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v vyslance pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, že rozhraní API pro vyslance podporuje filtrování uživatelů na základě tohoto atributu. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |externalId|Řetězec|
   |displayName|Řetězec|
   |title|Řetězec|
   |e-maily[zadejte eq "práce"].hodnota|Řetězec|
   |preferredLanguage|Řetězec|
   |Oddělení|Řetězec|
   |adresy[zadejte eq "work"].země|Řetězec|
   |adresy[zadejte eq "práce"].lokalita|Řetězec|
   |adresy[zadejte eq "work"].region|Řetězec|
   |adresy[zadejte eq "práce"].PSČ|Řetězec|
   |adresy[zadejte eq "work"].formátované|Řetězec|
   |addresses[zadejte eq "work"].streetAddress|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |název.formátován|Řetězec|
   |phoneNumbers[zadejte eq "mobile"].value|Řetězec|
   |phoneNumbers[zadejte eq "work"].value|Řetězec|
   |locale|Řetězec|

10. V části **Mapování** vyberte **synchronizovat skupiny služby Azure Active Directory s vyslancem**.

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na vyslance v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v envoy pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |externalId|Řetězec|
      |členy|Referenční informace|

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro vyslance, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit vyslanci, výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

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
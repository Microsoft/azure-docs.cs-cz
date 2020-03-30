---
title: 'Kurz: Konfigurace purecloudu podle genesys pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak automaticky zřazovat a odpočitat uživatelské účty z Azure AD na PureCloud od Genesys.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f04b88b-117e-40da-a15c-e3732b240d5d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: 119690b9046821ab538d879e1209c6ef77277370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370682"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>Kurz: Konfigurace PureCloud podle Genesys pro automatické zřizování uživatelů

Tento kurz popisuje kroky, které je potřeba provést v PureCloud u Genesys a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Při konfiguraci Azure AD automaticky zřídí a de-zřídí uživatele a skupiny [PureCloud od Genesys](https://www.genesys.com) pomocí služby Azure AD zřizování. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v PureCloud u Genesys
> * Odstraňte uživatele v PureCloud od Genesys, když už nevyžadují přístup
> * Udržujte atributy uživatelů synchronizované mezi Azure AD a PureCloud podle Genesys
> * Zřizování skupin a členství ve skupinách v PureCloud u Genesys
> * [Jednotné přihlášení](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial) k PureCloud od Genesys (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* [Klient Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikací, správce cloudových aplikací, vlastník aplikace nebo globální správce). 
* [Organizace](https://help.mypurecloud.com/?p=81984)PureCloud .
* Uživatel s [oprávněními](https://help.mypurecloud.com/?p=24360) k vytvoření klienta Oauth.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si o [tom, jak funguje služba zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se [mají mapovat mezi Azure AD a PureCloud podle Genesys](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace PureCloud u Genesys pro podporu zřizování pomocí Azure AD

1. Vytvořte [klienta Oauth](https://help.mypurecloud.com/?p=188023) nakonfigurovaného ve vaší organizaci PureCloud.
2. Vygenerujte token [se svým klientem oauth](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html).
3. Pokud chcete automaticky zřídit členství ve skupině v rámci PureCloudu, musíte [vytvořit skupiny](https://help.mypurecloud.com/?p=52397) v PureCloudu se stejným názvem jako skupina ve službě Azure AD.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání PureCloud u Genesys z galerie aplikací Azure AD

Přidejte PureCloud od Genesys z galerie aplikací Azure AD a začněte spravovat zřizování na PureCloud od Genesys. Pokud jste již dříve nastavili PureCloud od Genesys pro ssebou, můžete použít stejnou aplikaci. Doporučujeme však vytvořit samostatnou aplikaci při testování integrace zpočátku. Další informace o přidání aplikace z galerie [naleznete zde](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování 

Služba zřizování Azure AD umožňuje obor, který bude zřízen na základě přiřazení k aplikaci a nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete obor, kdo bude zřízen do vaší aplikace na základě přiřazení, můžete použít následující [kroky](../manage-apps/assign-user-or-group-access-portal.md) k přiřazení uživatelů a skupin k aplikaci. Pokud se rozhodnete obor, který bude zřízen výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oborů, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k PureCloud společností Genesys musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí výchozí přístup jsou vyloučeni z zřizování a budou označeny jako neefektivně oprávněné v protokolech zřizování. Pokud je k dispozici pouze role v aplikaci výchozí přístupová role, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Vyzkoušejte s malou sadou uživatelů a skupin před zavedením pro všechny. Pokud je obor zřizování nastaven na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin do aplikace. Pokud je obor nastaven na všechny uživatele a skupiny, můžete určit [filtr oborů založených na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>Krok 5. Konfigurace automatického zřizování uživatelů na PureCloud od Genesys 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Aplikaci TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro PureCloud podle Genesys ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **PureCloud by Genesys**.

    ![Odkaz PureCloud by Genesys v seznamu Aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Přihlašovací údaje správce** zadejte adresu URL rozhraní API PureCloud podle Genesys a token Oauth do polí URL **klienta** a **tajný token.** Adresa URL rozhraní API bude `{{API Url}}/api/v2/scim/v2`strukturována jako pomocí adresy URL rozhraní API pro vaši oblast PureCloud z [Centra pro vývojáře PureCloud](https://developer.mypurecloud.com/api/rest/index.html). Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k PureCloudu od Genesys. Pokud se připojení nezdaří, ujistěte se, že váš účet PureCloud by Genesys má oprávnění správce a zkuste to znovu.

    ![Zajišťování](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě.**

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části Mapování vyberte **Synchronizovat uživatele Služby Azure Active Directory s purecloudem podle Genesys**. **Mappings**

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na PureCloud genesys v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v PureCloud genesys pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní PureCloud by Genesys API podporovalo filtrování uživatelů na základě tohoto atributu. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

     |Atribut|Typ|
     |---|---|
     |userName|Řetězec|
     |Aktivní|Logická hodnota|
     |displayName|Řetězec|
     |e-maily[zadejte eq "práce"].hodnota|Řetězec|
     |title|Řetězec|
     |phoneNumbers[zadejte eq "mobile"].value|Řetězec|
     |phoneNumbers[zadejte eq "work"].value|Řetězec|
     |urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:oddělení|Řetězec|
     |urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:manažer|Odkaz|

10. V části **Mapování** vyberte **Synchronizovat skupiny Služby Active Directory Azure s PureCloud podle Genesys**.

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na PureCloud genesys v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v PureCloud od Genesys pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.** PureCloud by Genesys nepodporuje vytváření nebo odstraňování skupin a podporuje pouze aktualizaci skupin.

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |externalId|Řetězec|
      |členy|Odkaz|

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro PureCloud od Genesys, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele a/nebo skupiny, které chcete purecloudu zřídit genesys, výběrem požadovaných hodnot v **scope** v části **Nastavení.**

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

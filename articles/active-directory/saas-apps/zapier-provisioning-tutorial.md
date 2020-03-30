---
title: 'Kurz: Konfigurace zapieru pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak automaticky zřídit a odpojit uživatelské účty z Azure AD do Zapieru.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 66e224f9-2311-4564-bb84-99fce59a398f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: Zhchia
ms.openlocfilehash: 4091e4fd544dbc6450bc14bd0e0731c4d3024592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76992154"
---
# <a name="tutorial-configure-zapier-for-automatic-user-provisioning"></a>Kurz: Konfigurace Zapieru pro automatické zřizování uživatelů

Tento kurz popisuje kroky, které je potřeba provést v Zapier a Azure Active Directory (Azure AD) ke konfiguraci automatické zřizování uživatelů. Při konfiguraci Azure AD automaticky zřídí a de-zřídí uživatele a skupiny [Zapier](https://zapier.com/pricing) pomocí služby Azure AD zřizování. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytvoření uživatelů v Zapieru
> * Odebrání uživatelů v Zapieru, pokud už nevyžadují přístup
> * Zachovat synchronizaci atributů uživatelů mezi Azure AD a Zapierem
> * Zřizování skupin a členství ve skupinách v Zapieru
> * Jednotné přihlášení k Zapier (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* [Klient Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikací, správce cloudových aplikací, vlastník aplikace nebo globální správce). 
* Uživatelský účet v Zapieru s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si o [tom, jak funguje služba zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se [mají mapovat mezi Azure AD a Zapier](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-zapier-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Zapieru pro podporu zřizování pomocí Azure AD

1. Přihlaste se do [zapier Admin Console](https://zapier.com/app/login/). Přejděte do **nastavení** pod ID klienta.

    ![Zapier Admin Console](media/zapier-provisioning-tutorial/admin.png)

2. V části **NASTAVENÍ SPOLEČNOSTI**vyberte **zřizování uživatele**.

    ![Zapier Přidat SCIM](media/zapier-provisioning-tutorial/user.png)

3. Zkopírujte **základní adresu URL SCIM** a token **nosiče SCIM**. Tyto hodnoty se zanesou do polí ADRESA URL klienta a tajný token v kartě Zřizování vaší aplikace Zapier na webu Azure Portal.

    ![Zapier vytvořit token](media/zapier-provisioning-tutorial/token.png)

## <a name="step-3-add-zapier-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Zapiera z galerie aplikací Azure AD

Přidejte Zapier z galerie aplikací Azure AD a začněte spravovat zřizování do Zapieru. Pokud jste již dříve nastavili Zapier pro spři, můžete použít stejnou aplikaci. Doporučujeme však vytvořit samostatnou aplikaci při testování integrace zpočátku. Další informace o přidání aplikace z galerie [naleznete zde](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování 

Služba zřizování Azure AD umožňuje obor, který bude zřízen na základě přiřazení k aplikaci a nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete obor, kdo bude zřízen do vaší aplikace na základě přiřazení, můžete použít následující [kroky](../manage-apps/assign-user-or-group-access-portal.md) k přiřazení uživatelů a skupin k aplikaci. Pokud se rozhodnete obor, který bude zřízen výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oborů, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k zapieru je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí výchozí přístup jsou vyloučeni z zřizování a budou označeny jako neefektivně oprávněné v protokolech zřizování. Pokud je k dispozici pouze role v aplikaci výchozí přístupová role, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Vyzkoušejte s malou sadou uživatelů a skupin před zavedením pro všechny. Pokud je obor zřizování nastaven na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin do aplikace. Pokud je obor nastaven na všechny uživatele a skupiny, můžete určit [filtr oborů založených na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-zapier"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro Zapiera 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Aplikaci TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zapier-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Zapier ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

   ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zapier**.

   ![Odkaz Zapier v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

   ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

   ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Přihlašovací údaje správce** zadejte adresu URL **klienta** Zapier a **tajný token**. Klikněte na **Testovat připojení** a ujistěte se, že se Azure AD může připojit k Zapierovi. Pokud se připojení nezdaří, ujistěte se, že váš účet Zapier má oprávnění správce a zkuste to znovu.

   ![Zajišťování](./media/zapier-provisioning-tutorial/provisioning.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě.**

   ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory se zapierem**.

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Zapier v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Zapier pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní API Zapier podporovalo filtrování uživatelů na základě tohoto atributu. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

   |Proměnná|Typ|
   |---|---|
   |userName|Řetězec|
   |Aktivní|Logická hodnota|
   |externalId|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |e-maily[zadejte eq "práce"].hodnota|Řetězec|

10. V části **Mapování** vyberte **Synchronizovat skupiny Služby Active Directory Azure se zapierem**.

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do Zapier v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v Zapier pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    |Proměnná|Typ|
    |---|---|
    |displayName|Řetězec|
    |členy|Odkaz|

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro Zapier, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete dát Zapierovi, výběrem požadovaných hodnot v **scope** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení.** Počáteční cyklus trvá déle než následující cykly, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po konfiguraci zřizování můžete ke sledování nasazení použít následující prostředky:

- Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé byli zřízeni úspěšně nebo neúspěšně
- Zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) zobrazíte stav cyklu zřizování a jak blízko je k dokončení
- Pokud se zdá, že konfigurace zřizování je v nestavu, aplikace přejde do karantény. Další informace o stavech karantény naleznete [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../manage-apps/check-status-user-account-provisioning.md)

---
title: 'Kurz: Konfigurace přiblížení pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak automaticky zřídit a odpojit uživatelské účty z Azure AD do lupy.
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
ms.openlocfilehash: 94c261da0c935cb7a41dde768069099b4e5ed251
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384071"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Kurz: Konfigurace lupy pro automatické zřizování uživatelů

Tento kurz popisuje kroky, které je potřeba provést v zoomu a Azure Active Directory (Azure AD) ke konfiguraci automatickézřižení služby uživatelů. Při konfiguraci Azure AD automaticky zřídí a de-zřídí uživatele a skupiny [pro zvětšení](https://zoom.us/pricing/) pomocí služby Azure AD zřizování. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytvoření uživatelů v lupě
> * Odebrání uživatelů v aplikaci Lupa, pokud již nevyžadují přístup
> * Zachování synchronizace atributů uživatelů mezi Azure AD a Zoomem
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-tutorial) k zoomu (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* [Klient Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikací, správce cloudových aplikací, vlastník aplikace nebo globální správce). 
* [Klient zoom .](https://zoom.us/pricing)
* Uživatelský účet v lupě s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si o [tom, jak funguje služba zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se [mají mapovat mezi Azure AD a Zoom](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace lupy pro podporu zřizování pomocí Azure AD

1. Přihlaste se ke službě [Zoom Admin Console](https://zoom.us/signin). V levém navigačním podokně přejděte na **rozšířené > lupy pro vývojáře.**

    ![Integrace přiblížení](media/zoom-provisioning-tutorial/zoom01.png)

2. Přejděte na **Spravovat** v pravém horním rohu stránky. 

    ![Instalace lupy](media/zoom-provisioning-tutorial/zoom02.png)

3. Přejděte do vytvořené aplikace Azure AD. 
    
    ![Aplikace Lupa](media/zoom-provisioning-tutorial/zoom03.png)

4. V levém navigačním podokně vyberte **Přihlašovací údaje aplikace.**

    ![Aplikace Lupa](media/zoom-provisioning-tutorial/zoom04.png)

5. Zkopírujte a uložte **token JWT**. Tato hodnota se zadá do pole **Tajný token** na kartě Zřizování aplikace Zoom na webu Azure Portal. Pokud potřebujete nový token bez vypršení platnosti, budete muset znovu nakonfigurovat čas vypršení platnosti, který bude automaticky generovat nový token. 

    ![Instalace lupy](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání lupy z galerie aplikací Azure AD

Přidejte lupu z galerie aplikací Azure AD a začněte spravovat zřizování do lupy. Pokud jste již dříve nastavili lupu pro sso, můžete použít stejnou aplikaci. Doporučujeme však vytvořit samostatnou aplikaci při testování integrace zpočátku. Další informace o přidání aplikace z galerie [naleznete zde](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování 

Služba zřizování Azure AD umožňuje obor, který bude zřízen na základě přiřazení k aplikaci a nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete obor, kdo bude zřízen do vaší aplikace na základě přiřazení, můžete použít následující [kroky](../manage-apps/assign-user-or-group-access-portal.md) k přiřazení uživatelů a skupin k aplikaci. Pokud se rozhodnete obor, který bude zřízen výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oborů, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k aplikaci Lupa je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí výchozí přístup jsou vyloučeni z zřizování a budou označeny jako neefektivně oprávněné v protokolech zřizování. Pokud je k dispozici pouze role v aplikaci výchozí přístupová role, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Vyzkoušejte s malou sadou uživatelů a skupin před zavedením pro všechny. Pokud je obor zřizování nastaven na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin do aplikace. Pokud je obor nastaven na všechny uživatele a skupiny, můžete určit [filtr oborů založených na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro zvětšení 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Aplikaci TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro přiblížení ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Lupa**.

    ![Odkaz Lupa v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Přihlašovací údaje** `https://api.zoom.us/scim` správce zadejte **adresu URL klienta**. Zadejte hodnotu **tokenu JWT** načtenou dříve v **tajném tokenu**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit ke lupě. Pokud se připojení nezdaří, ujistěte se, že váš účet Zoom má oprávnění správce a zkuste to znovu.

    ![Zřizování přiblížení](./media/zoom-provisioning-tutorial/provisioning.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě.**

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **Mapování** vyberte **synchronizovat uživatele služby Azure Active Directory k přiblížení**.

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do zvětšení v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v lupě pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní API zoomu podporovalo filtrování uživatelů na základě tohoto atributu. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |Aktivní|Logická hodnota|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |e-maily[zadejte eq "práce"]|Řetězec|
   |urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:oddělení|Řetězec|

10. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Chcete-li povolit službu zřizování Azure AD pro lupu, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit lupě, výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

13. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení.** Počáteční cyklus trvá déle než následující cykly, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po konfiguraci zřizování můžete ke sledování nasazení použít následující prostředky:

1. Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé byli zřízeni úspěšně nebo neúspěšně
2. Zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) zobrazíte stav cyklu zřizování a jak blízko je k dokončení
3. Pokud se zdá, že konfigurace zřizování je v nestavu, aplikace přejde do karantény. Další informace o stavech karantény naleznete [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Omezení konektoru
* Zoom umožňuje pouze maximálně 9.999 základních uživatelů dnes.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../manage-apps/check-status-user-account-provisioning.md)

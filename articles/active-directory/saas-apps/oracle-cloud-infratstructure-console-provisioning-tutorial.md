---
title: 'Kurz: Konfigurace konzoly Oracle Cloud Infrastructure Console pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak automaticky zřídit a odpojit uživatelské účty z Azure AD do konzoly Oracle Cloud Infrastructure Console.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e22c8746-7638-4a0f-ae08-7db0c477cf52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 5aa33529a1957b6e7728b3a87bacf6bb91d987ae
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378948"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Kurz: Konfigurace konzoly Oracle Cloud Infrastructure Console pro automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v konzoli Oracle Cloud Infrastructure Console a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Při konfiguraci Azure AD automaticky zřídí a de-zřaže uživatelů a skupin [na Oracle Cloud Infrastructure Console](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) pomocí služby Azure AD zřizování. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v konzole Oracle Cloud Infrastructure Console
> * Odebrání uživatelů v konzole Oracle Cloud Infrastructure Console, pokud již nevyžadují přístup
> * Zachování synchronizace atributů uživatelů mezi azure a službou Azure AD a konzolou Oracle Cloud Infrastructure Console
> * Zřizování skupin a členství ve skupinách v konzole Oracle Cloud Infrastructure Console
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) ke konzoli Oracle Cloud Infrastructure Console (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* [Klient Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikací, správce cloudových aplikací, vlastník aplikace nebo globální správce). 
* Tenant oracle [cloudové](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)infrastruktury .
* Uživatelský účet v řešení Oracle Cloud Infrastructure Control s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si o [tom, jak funguje služba zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi službou Azure AD a konzolou Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace konzoly Oracle Cloud Infrastructure Console pro podporu zřizování pomocí Azure AD

1. Přihlaste se na portál pro správu konzoly Oracle Cloud Infrastructure Console. V levém horním rohu obrazovky přejděte na **Identity > Federation**.

    ![Správce oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Klikněte na adresu URL zobrazenou na stránce vedle konzoly Oracle Identity Cloud Service Console.

    ![Oracle URL](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Kliknutím na **Přidat zprostředkovatele identity** vytvořte nového zprostředkovatele identity. Uložte ID ID ID, které chcete použít jako součást adresy URL klienta. Kliknutím na ikonu plus vedle karty **Aplikace** vytvořte roli KlientAuth a udělte roli Správce identity IDCS.

    ![Ikona řešení Oracle Cloud](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Postupujte podle níže uvedených snímků obrazovky a nakonfigurujte aplikaci. Po dokončení konfigurace klikněte na **Uložit**.

    ![Konfigurace společnosti Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Zásady vystavování tokenů Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Na kartě Konfigurace aplikace rozbalte možnost **Obecné informace** a načtěte ID klienta a tajný klíč klienta.

    ![Generování tokenů Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Chcete-li vygenerovat tajný token Base64, zakódujte ID klienta a tajný klíč klienta ve formátu **ID klienta:Tajný klíč klienta**. Uložte tajný token. Tato hodnota se zadá do pole **Tajný token** na kartě zřizování aplikace Oracle Cloud Infrastructure Console na webu Azure Portal.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání konzoly Oracle Cloud Infrastructure Console z galerie aplikací Azure AD

Přidejte konzolu Oracle Cloud Infrastructure Console z galerie aplikací Azure AD a začněte spravovat zřizování do konzoly Oracle Cloud Infrastructure Console. Pokud jste již dříve nastavili konzolu Oracle Cloud Infrastructure Console pro správu a zabezpečení, můžete použít stejnou aplikaci. Doporučujeme však vytvořit samostatnou aplikaci při testování integrace zpočátku. Další informace o přidání aplikace z galerie [naleznete zde](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování 

Služba zřizování Azure AD umožňuje obor, který bude zřízen na základě přiřazení k aplikaci a nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete obor, kdo bude zřízen do vaší aplikace na základě přiřazení, můžete použít následující [kroky](../manage-apps/assign-user-or-group-access-portal.md) k přiřazení uživatelů a skupin k aplikaci. Pokud se rozhodnete obor, který bude zřízen výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oborů, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin ke konzoli Oracle Cloud Infrastructure Console je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí výchozí přístup jsou vyloučeni z zřizování a budou označeny jako neefektivně oprávněné v protokolech zřizování. Pokud je k dispozici pouze role v aplikaci výchozí přístupová role, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Vyzkoušejte s malou sadou uživatelů a skupin před zavedením pro všechny. Pokud je obor zřizování nastaven na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin do aplikace. Pokud je obor nastaven na všechny uživatele a skupiny, můžete určit [filtr oborů založených na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Krok 5. Konfigurace automatického zřizování uživatelů do konzoly Oracle Cloud Infrastructure Console 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Aplikaci TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro konzolu Oracle Cloud Infrastructure Console ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **oracle cloud infrastructure console**.

    ![Odkaz Konzola oracle cloud ové infrastruktury v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** zadejte adresu URL `https://<IdP ID>.identity.oraclecloud.com/admin/v1` **klienta** ve formátu . Například `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Zadejte hodnotu tajného tokenu načtenou dříve v **tajném tokenu**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit ke konzoli Oracle Cloud Infrastructure Console. Pokud se připojení nezdaří, ujistěte se, že váš účet konzoly Oracle Cloud Infrastructure Console má oprávnění správce, a zkuste to znovu.

    ![Zajišťování](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě.**

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory se konzolí Oracle Cloud Infrastructure Console**.

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do konzoly Oracle Cloud Infrastructure Console v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v konzole Oracle Cloud Infrastructure Console pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní API konzoly Oracle Cloud Infrastructure Console podporovalo filtrování uživatelů na základě tohoto atributu. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |userName|Řetězec|
      |Aktivní|Logická hodnota|
      |title|Řetězec|
      |e-maily[zadejte eq "práce"].hodnota|Řetězec|
      |preferredLanguage|Řetězec|
      |name.givenName|Řetězec|
      |name.familyName|Řetězec|
      |adresy[zadejte eq "work"].formátované|Řetězec|
      |adresy[zadejte eq "práce"].lokalita|Řetězec|
      |adresy[zadejte eq "work"].region|Řetězec|
      |adresy[zadejte eq "práce"].PSČ|Řetězec|
      |adresy[zadejte eq "work"].země|Řetězec|
      |addresses[zadejte eq "work"].streetAddress|Řetězec|
      |urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:employeeNumber|Řetězec|
      |urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:oddělení|Řetězec|
      |urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:costCenter|Řetězec|
      |urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:dělení|Řetězec|
      |urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:manažer|Referenční informace|
      |urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:organizace|Řetězec|
      |urn:ietf:params:scim:schémata:oracle:idcs:extension:user:bypassNotification|Logická hodnota|
      |urn:ietf:params:scim:schémata:oracle:idcs:extension:user:User:isFederatedUser|Logická hodnota|

10. V části **Mapování** vyberte **Synchronizovat skupiny Azure Active Directory se konzolí Oracle Cloud Infrastructure Console**.

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do konzoly Oracle Cloud Infrastructure Console v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v konzole Oracle Cloud Infrastructure Console pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |externalId|Řetězec|
      |členy|Referenční informace|

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit zřizovací službu Azure AD pro konzolu Oracle Cloud Infrastructure Console, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit konzoli Oracle Cloud Infrastructure **Settings** Console, výběrem požadovaných hodnot v **části Obor.**

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

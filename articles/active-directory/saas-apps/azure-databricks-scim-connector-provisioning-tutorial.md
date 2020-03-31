---
title: 'Kurz: Konfigurace konektoru SCIM Azure Databricks pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak automaticky zřídit a odpojit uživatelské účty z Azure AD do konektoru SCIM Azure Databricks.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b16eaf27-4bd1-4509-be2c-9a4f66b6badc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2020
ms.author: Zhchia
ms.openlocfilehash: fe1260982edc877c049716bd74f1bb3e90d33b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370532"
---
# <a name="tutorial-configure-azure-databricks-scim-connector-for-automatic-user-provisioning"></a>Kurz: Konfigurace konektoru SCIM Azure Databricks pro automatické zřizování uživatelů

Tento kurz popisuje kroky, které je potřeba provést v konektoru SCIM Azure Databricks a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Při konfiguraci Azure AD automaticky zřídí a de-zřídí uživatele a skupiny [Azure Databricks SCIM Konektor](https://databricks.com/) pomocí služby Azure AD zřizování. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v konektoru SCIM Azure Databricks
> * Odebrání uživatelů v konektoru SCIM Azure Databricks, když už nevyžadují přístup
> * Zachování synchronizace atributů uživatelů mezi azure a konektorem SCIM Databricks
> * Zřizování skupin a členství ve konektoru SCIM Azure Databricks

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* [Klient Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikací, správce cloudových aplikací, vlastník aplikace nebo globální správce). 
* Účet Azure Databricks s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si o [tom, jak funguje služba zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se [mají mapovat mezi Azure AD a Azure Databricks SCIM Connector](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-azure-databricks-scim-connector-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace konektoru SCIM Azure Databricks SCIM pro podporu zřizování pomocí Azure AD

1. Pokud chcete nastavit zřizování SCIM Azure Databricks, přidejte ho jako prostředek v tenantovi Služby Azure Active Directory a nakonfigurujte ho pomocí níže uvedených nastavení.

    ![Nastavení Azure Databricks](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/setup.png)

2. Chcete-li generovat osobní přístupový token v Azure Databricks odkazovat [na toto](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#token-management).

3. Zkopírujte **token**. Tato hodnota se zadá do pole Tajný token na kartě Zřizování aplikace Konektor SCIM Azure Databricks na webu Azure Portal.

## <a name="step-3-add-azure-databricks-scim-connector-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání konektoru SCIM Azure Databricks z galerie aplikací Azure AD

Přidejte konektor SCIM Azure Databricks z galerie aplikací Azure AD a začněte správu zřizování do konektoru SCIM Azure Databricks. Pokud jste již dříve nastavili konektor Azure Databricks SCIM pro spři ssebou, můžete použít stejnou aplikaci. Doporučujeme však vytvořit samostatnou aplikaci při testování integrace zpočátku. Další informace o přidání aplikace z galerie [naleznete zde](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování 

Služba zřizování Azure AD umožňuje obor, který bude zřízen na základě přiřazení k aplikaci a nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete obor, kdo bude zřízen do vaší aplikace na základě přiřazení, můžete použít následující [kroky](../manage-apps/assign-user-or-group-access-portal.md) k přiřazení uživatelů a skupin k aplikaci. Pokud se rozhodnete obor, který bude zřízen výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oborů, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin ke konektoru SCIM Azure Databricks je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí výchozí přístup jsou vyloučeni z zřizování a budou označeny jako neefektivně oprávněné v protokolech zřizování. Pokud je k dispozici pouze role v aplikaci výchozí přístupová role, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Vyzkoušejte s malou sadou uživatelů a skupin před zavedením pro všechny. Pokud je obor zřizování nastaven na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin do aplikace. Pokud je obor nastaven na všechny uživatele a skupiny, můžete určit [filtr oborů založených na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-azure-databricks-scim-connector"></a>Krok 5. Konfigurace automatického zřizování uživatelů do konektoru SCIM Azure Databricks 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Aplikaci TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!NOTE]
> Další informace o koncovém bodě SCIM azure databricks najdete [na tomto](https://docs.databricks.com/dev-tools/api/latest/scim.html
).

### <a name="to-configure-automatic-user-provisioning-for-azure-databricks-scim-connector-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro konektor SCIM Azure Databricks ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Konektor SCIM Azure Databricks .**

    ![Propojení konektoru SCIM Azure Databricks v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** zadejte hodnotu koncového bodu SCIM do adresy **URL klienta**. Adresa URL klienta by `https://<region>.azuredatabricks.net/api/2.0/preview/scim` měla být ve formátu, kde se **oblast** nachází ve vaší adrese URL domovské stránky Azure Databricks. Například koncový bod SCIM pro oblast `https://westus.azuredatabricks.net/api/2.0/preview/scim` **westus** bude . Zadejte hodnotu tokenu načtenou dříve v **tajném tokenu**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit ke konektoru SCIM Azure Databricks. Pokud se připojení nezdaří, ujistěte se, že váš účet Konektor SCIM Azure Databricks má oprávnění správce a zkuste to znovu.

    ![Zajišťování](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/provisioning.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě.**

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části Mapování vyberte **Synchronizovat uživatele Služby Azure Active Directory s konektorem SCIM Azure Databricks**. **Mappings**

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Konektor SCIM Azure Databricks v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v konektoru SCIM Azure Databricks PRO operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, že rozhraní API konektoru SCIM Azure Databricks podporuje filtrování uživatelů na základě tohoto atributu. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |displayName|Řetězec|
   |Aktivní|Logická hodnota|

10. V části **Mapování** vyberte **Synchronizovat skupiny Azure Active Directory s konektorem SCIM Azure Databricks**.

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na Konektor SCIM Azure Databricks v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání skupin v konektoru SCIM Azure Databricks pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

     |Atribut|Typ|
     |---|---|
     |displayName|Řetězec|
     |členy|Odkaz|

11. V části **Mapování** vyberte **Synchronizovat skupiny Azure Active Directory s konektorem SCIM Azure Databricks**.

12. Pokud chcete povolit zřizovací službu Azure AD pro konektor SCIM Azure Databricks, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit konektoru SCIM Azure Databricks, výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

14. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení.** Počáteční cyklus trvá déle než následující cykly, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po konfiguraci zřizování můžete ke sledování nasazení použít následující prostředky:

* Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé byli zřízeni úspěšně nebo neúspěšně
* Zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) zobrazíte stav cyklu zřizování a jak blízko je k dokončení
* Pokud se zdá, že konfigurace zřizování je v nestavu, aplikace přejde do karantény. Další informace o stavech karantény naleznete [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží
* Databricks vždy převádí své hodnoty uživatelského jména na malá písmena při ukládání do svého adresáře bez ohledu na velká písmena, která jim posíláme prostřednictvím SCIM.
* V současné době get požadavky proti SCIM ROZHRANÍ API Azure Databricks USER@contoso.com pro uživatele jsou malá a velká user@contoso.compísmena, takže pokud jsme dotaz na to přijde s 0 výsledky, jak jsou ukládat jako .

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../manage-apps/check-status-user-account-provisioning.md)

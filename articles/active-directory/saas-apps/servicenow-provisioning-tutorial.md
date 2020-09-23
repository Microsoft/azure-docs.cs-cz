---
title: 'Kurz: Konfigurace ServiceNow pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: b4ee17ba6587c38efb6da15117a5c55dc5abb873
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90979717"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Kurz: Konfigurace ServiceNow pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v ServiceNow i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, které se [ServiceNow](https://www.servicenow.com/) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v ServiceNow
> * Odebrat uživatele v ServiceNow, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a ServiceNow
> * Zřizování skupin a členství ve skupinách v ServiceNow
> * [Jednotné přihlašování](servicenow-tutorial.md) k ServiceNow (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet v Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (např. správce aplikací, správce cloudových aplikací, vlastník aplikací nebo globální správce) 
* [Instance ServiceNow](https://www.servicenow.com/) typu Calgary nebo vyšší
* [ServiceNow Express instance](https://www.servicenow.com/) Helsinky nebo vyšší
* Uživatelský účet v ServiceNow s rolí správce

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Zjistěte, kdo bude v [rozsahu zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a ServiceNow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace ServiceNow pro podporu zřizování pomocí Azure AD

1. Identifikujte název instance ServiceNow. Název instance najdete v adrese URL, kterou používáte pro přístup k ServiceNow. V následujícím příkladu je název instance dev35214.

   ![Instance ServiceNow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

2. Získejte přihlašovací údaje pro správce v ServiceNow. Přejděte do profilu uživatele v ServiceNow a ověřte, jestli má uživatel roli správce. 

   ![Role správce ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

3. Zkontrolujte, jestli jsou v ServiceNow **zakázaná** tato nastavení:

   1. Vybrat **System Security**  >  **nastavení vysokého zabezpečení**zabezpečení systému  >  **vyžaduje základní ověřování pro příchozí požadavky schématu**.
   2. Vybrat **Vlastnosti systému**  >  **webové služby**  >  **vyžadují základní autorizaci pro příchozí požadavky SOAP**.
     
   > [!IMPORTANT]
   > Pokud je toto nastavení *povolené*, modul zřizování nebude při komunikaci s ServiceNowem komunikovat.

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání ServiceNow z Galerie aplikací Azure AD

Přidejte ServiceNow z Galerie aplikací Azure AD a začněte spravovat zřizování pro ServiceNow. Pokud jste dříve nastavili ServiceNow pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k ServiceNow je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Krok 5. Konfigurace automatického zřizování uživatelů na ServiceNow 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro ServiceNow ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **ServiceNow**.

    ![Odkaz ServiceNow v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte svoje přihlašovací údaje správce ServiceNow a uživatelské jméno. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k ServiceNow. Pokud se připojení nepovede, ujistěte se, že má váš účet ServiceNow oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky ukazuje stránku zřizování služby, kde můžete zadat přihlašovací údaje správce.](./media/servicenow-provisioning-tutorial/provisioning.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé ServiceNow**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do ServiceNow v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v ServiceNow pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní ServiceNow API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do ServiceNow**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do ServiceNow v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v ServiceNow pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro ServiceNow, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro ServiceNow, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Tipy pro řešení problémů
* **InvalidLookupReference:** Při zřizování určitých atributů, jako je například oddělení a umístění v ServiceNow, musí hodnoty již existovat v referenční tabulce v ServiceNow. V tabulce **název tabulky** v ServiceNow můžete mít například dvě umístění (Praha, Los Angeles) a tři oddělení (prodej, finance, marketing). Pokud se pokusíte zřídit uživatele, kde má oddělení "prodej" a umístění je "Praha", bude zřízené úspěšně. Pokud se pokusíte zřídit uživatele s oddělením prodej a umístěním "LA", uživatel se nezřídí. Umístění LA musí být buď přidáno do referenční tabulky v ServiceNow, nebo musí být atribut uživatele v Azure AD aktualizovaný tak, aby odpovídal formátu v ServiceNow. 
* **EntryJoiningPropertyValueIsMissing:** Zkontrolujte [mapování atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) k identifikaci odpovídajícího atributu. Tato hodnota musí být k dispozici u uživatele nebo skupiny, které se pokoušíte zřídit. 
* Přečtěte si [rozhraní SERVICENOW SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) , abyste pochopili všechny požadavky nebo omezení (například formát pro určení kódu země pro uživatele).
* Požadavky zřizování se ve výchozím nastavení odesílají do https://{název-instance}. Service-Now. com/{Table-Name}. Pokud potřebujete vlastní adresu URL tenanta, můžete v poli název instance zadat celou adresu URL.
* **ServiceNowInstanceInvalid** 
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

   Tato chyba označuje problém komunikující s instancí ServiceNow. Dvakrát zkontrolujte, jestli jsou v ServiceNow *zakázaná* tato nastavení:
   
   1. Vybrat **System Security**  >  **nastavení vysokého zabezpečení**zabezpečení systému  >  **vyžaduje základní ověřování pro příchozí požadavky schématu**.
   2. Vybrat **Vlastnosti systému**  >  **webové služby**  >  **vyžadují základní autorizaci pro příchozí požadavky SOAP**.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

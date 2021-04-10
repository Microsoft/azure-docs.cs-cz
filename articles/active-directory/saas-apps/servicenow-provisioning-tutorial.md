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
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: b3b62e7c16106fd9d94d4a3438331dab4ce8b6e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539028"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Kurz: Konfigurace ServiceNow pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které provedete v ServiceNow i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Když je služba Azure AD nakonfigurovaná, automaticky zřídí a [odServiceNow](https://www.servicenow.com/) uživatele a skupiny pomocí služby zřizování Azure AD. 

Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v ServiceNow
> * Odebrat uživatele v ServiceNow, když už nepotřebují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a ServiceNow
> * Zřizování skupin a členství ve skupinách v ServiceNow
> * Povolení [jednotného přihlašování](servicenow-tutorial.md) k ServiceNow (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uživatelský účet ve službě Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce)
* [Instance ServiceNow](https://www.servicenow.com/) typu Calgary nebo vyšší
* [ServiceNow Express instance](https://www.servicenow.com/) Helsinky nebo vyšší
* Uživatelský účet v ServiceNow s rolí správce

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1: plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a ServiceNow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Krok 2: Konfigurace ServiceNow pro podporu zřizování pomocí Azure AD

1. Identifikujte název instance ServiceNow. Název instance najdete v adrese URL, kterou používáte pro přístup k ServiceNow. V následujícím příkladu je název instance **dev35214**.

   ![Snímek obrazovky zobrazující instanci ServiceNow](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. Získejte přihlašovací údaje pro správce v ServiceNow. V ServiceNow otevřete profil uživatele a ověřte, jestli má uživatel roli správce. 

   ![Snímek obrazovky, který zobrazuje roli správce ServiceNow.](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Krok 3: Přidání ServiceNow z Galerie aplikací Azure AD

Přidejte ServiceNow z Galerie aplikací Azure AD a začněte spravovat zřizování pro ServiceNow. Pokud jste dříve nastavili ServiceNow pro jednotné přihlašování (SSO), můžete použít stejnou aplikaci. Při testování integrace však doporučujeme vytvořit samostatnou aplikaci. [Přečtěte si další informace o přidání aplikace z Galerie](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: definování toho, kdo bude v oboru pro zřizování 

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení k aplikaci, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete použít [postup k přiřazení uživatelů a skupin k aplikaci](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete [použít filtr oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Pamatujte na tyto tipy:

* Když přiřazujete uživatele a skupiny do ServiceNow, musíte vybrat jinou roli než výchozí přístup. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat tak další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Když je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastaven na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Krok 5: Konfigurace automatického zřizování uživatelů na ServiceNow 

Tato část vás provede postupem konfigurace služby zřizování Azure AD pro vytváření, aktualizaci a zakázání uživatelů a skupin v TestApp. V Azure AD můžete založit konfiguraci pro přiřazení uživatelů a skupin.

Konfigurace automatického zřizování uživatelů pro ServiceNow ve službě Azure AD:

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**  >  **všechny aplikace**.

    ![Snímek obrazovky, který zobrazuje podokno podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte možnost **ServiceNow**.

    ![Snímek obrazovky, který zobrazuje seznam aplikací.](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností Automatická možnost byla vyvolána.](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte svoje přihlašovací údaje správce ServiceNow a uživatelské jméno. Vyberte **Test připojení** , aby se služba Azure AD mohla připojit k ServiceNow. Pokud se připojení nepovede, ujistěte se, že má váš účet ServiceNow oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky zobrazující stránku zřizování služby, kde můžete zadat přihlašovací údaje správce.](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování. Pak zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![Snímek obrazovky, který zobrazuje pole pro oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé ServiceNow**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do ServiceNow v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v ServiceNow pro operace aktualizace. 

   Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), musíte zajistit, aby rozhraní ServiceNow API podporovalo filtrování uživatelů na základě tohoto atributu. 
   
   Kliknutím na tlačítko **Uložit** potvrďte změny.

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do ServiceNow**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do ServiceNow v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v ServiceNow pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si pokyny v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro ServiceNow, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Snímek obrazovky, který zobrazuje stav zřizování zapnuto.](common/provisioning-toggle-on.png)

14. Definujte uživatele a skupiny, které chcete zřídit pro ServiceNow, výběrem požadovaných hodnot v části **Rozsah** **Nastavení** .

    ![Snímek obrazovky zobrazující možnosti pro obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Snímek obrazovky s tlačítkem pro uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly. K dalším cyklům dochází každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6: sledování nasazení
Po dokončení konfigurace zřizování použijte následující prostředky k monitorování nasazení:

- Pomocí [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně zřídili.
- Zkontrolujte indikátor [průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) a zobrazte stav cyklu zřizování a způsob, jakým se má dokončit.
- Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. [Přečtěte si další informace o stavech karantény](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží
* Při zřizování určitých atributů (například **oddělení** a **umístění**) v ServiceNow musí být hodnoty již existovat v referenční tabulce v ServiceNow. Pokud ne, zobrazí se chyba **InvalidLookupReference** . 

   Například můžete mít dvě umístění (Praha, Los Angeles) a tři oddělení (prodej, finance, marketing) v určité tabulce v ServiceNow. Pokud se pokusíte zřídit uživatele, jehož oddělení je "prodej" a jehož umístění je "Seattle", bude tento uživatel úspěšně zřízený. Pokud se pokusíte zřídit uživatele, jehož oddělení je "prodej" a jehož umístění je "LA", uživatel se nezřídí. Umístění "LA" musí být přidáno do referenční tabulky v ServiceNow nebo musí být atribut uživatele ve službě Azure AD aktualizován tak, aby odpovídal formátu v ServiceNow. 
* Pokud se zobrazí chyba **EntryJoiningPropertyValueIsMissing** , zkontrolujte [mapování atributů](../app-provisioning/customize-application-attributes.md) a Identifikujte atribut Matching. Tato hodnota musí být k dispozici u uživatele nebo skupiny, které se pokoušíte zřídit. 
* Pro pochopení všech požadavků a omezení (například formátu k určení kódu země pro uživatele) Zkontrolujte [rozhraní SERVICENOW SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html).
* Požadavky zřizování se ve výchozím nastavení odesílají do https://{název-instance}. Service-Now. com/{Table-Name}. Pokud potřebujete vlastní adresu URL tenanta, můžete jako název instance zadat celou adresu URL.
* Chyba **ServiceNowInstanceInvalid** indikuje problém komunikující s instancí ServiceNow. Zde je text chyby:
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

  Pokud máte problémy s testováním připojení, zkuste v ServiceNow vybrat **ne** pro následující nastavení:
   
  - **Zabezpečení systému**  >  Nastavení vysokého **zabezpečení**  >  **Vyžadovat základní ověřování pro příchozí požadavky na schéma**
  - **Vlastnosti systému**  >  **Webové služby**  >  **Vyžadovat základní autorizaci pro příchozí požadavky SOAP**

     ![Snímek obrazovky, který ukazuje možnost pro autorizaci požadavků SOAP.](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

  Pokud stále nemůžete problém vyřešit, obraťte se na podporu ServiceNow a požádejte je, aby zapnuli ladění SOAP, abyste mohli pomoct s řešením. 

* Služba zřizování Azure AD aktuálně funguje v rámci konkrétních [rozsahů IP adres](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges). V případě potřeby můžete omezit další rozsahy IP adres a přidat tyto konkrétní rozsahy IP adres do seznamu povolených aplikací. Tato technika umožní do vaší aplikace tok provozu ze služby zřizování Azure AD.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

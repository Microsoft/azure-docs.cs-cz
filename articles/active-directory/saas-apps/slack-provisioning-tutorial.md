---
title: 'Kurz: Zřizování uživatelů pro Slack – Azure AD'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřizovala uživatelské účty ve Slacku a rušila jejich zřízení.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: arvinh
ms.openlocfilehash: 92c2ae13b840d7a73d86365ce88584bcafc878e8
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181452"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Kurz: Konfigurace automatického zřizování uživatelů ve Slacku

Cílem tohoto kurzu je ukázat postup zajištění automatického zřizování a rušení zřízení uživatelských účtů ve Slacku z Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů ve Slacku
> * Odebírání uživatelů ve Slacku, když už nepotřebují přístup
> * Zajištění synchronizace atributů uživatelů mezi Azure AD a Slackem
> * Zřizování skupin a členství ve skupinách ve Slacku
> * [Jednotné přihlašování](./slack-tutorial.md) ke Slacku (doporučeno)


## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující:

* [Tenanta Azure AD](../develop/quickstart-create-new-tenant.md)
* Uživatelský účet v Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (např. správce aplikací, správce cloudových aplikací, vlastník aplikací nebo globální správce)
* Tenanta Slacku s [plánem Plus](https://aadsyncfabric.slack.com/pricing) nebo lepším
* Uživatelský účet ve Slacku s oprávněními správce týmu

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Zjistěte, jaká data by se [měla mapovat mezi Azure AD a Slackem](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>Krok 2. Přidání Slacku z galerie aplikací Azure AD

Přidejte Slack z galerie aplikací Azure AD, abyste mohli začít spravovat zřizování ve Slacku. Pokud jste již dříve ve Slacku nastavili jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md). 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>Krok 3. Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin ke Slacku musíte vybrat jinou roli než **Výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>Krok 4: Konfigurace automatického zřizování uživatelů ve Slacku 

Tato část vás provede připojením Azure AD k rozhraní API Slacku pro zřizování uživatelských účtů a konfigurací služby zřizování tak, aby ve Slacku vytvářela, aktualizovala a zakazovala přiřazené uživatelské účty na základě přiřazení uživatelů a skupin v Azure AD.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů ve Slacku v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Slack**.

    ![Odkaz na Slack v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **Přihlašovací údaje správce** klikněte na **Autorizovat**. Tím se v novém okně prohlížeče otevře dialogové okno pro autorizaci ve Slacku.

    ![Snímek obrazovky s tlačítkem pro autorizaci přihlašovacích údajů správce](media/slack-provisioning-tutorial/authorization.png)


6. V novém okně se přihlaste ke Slacku pomocí svého účtu správce týmu. Ve výsledném dialogovém okně pro autorizaci vyberte tým Slacku, pro který chcete povolit zřizování, a pak vyberte **Authorize** (Autorizovat). Po dokončení se vraťte na Azure Portal a dokončete konfiguraci zřizování.

    ![Dialogové okno pro autorizaci](./media/slack-provisioning-tutorial/slackauthorize.png)

7. Na webu Azure Portal klikněte na **Otestovat připojení** a ujistěte se, že se Azure AD může připojit k vaší aplikaci Slack. Pokud připojení selže, ujistěte se, že váš účet Slacku má oprávnění správce týmu, a zkuste znovu provést krok autorizace.

8. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

9. Vyberte **Uložit**.

10. V části Mapování vyberte **Synchronizovat uživatele Azure Active Directory do Slacku**.

11. V části **Mapování atributů** zkontrolujte atributy uživatelů, které se budou synchronizovat z Azure AD do Slacku. Mějte na paměti, že atributy vybrané jako **odpovídající** vlastnosti se budou používat k porovnávání uživatelských účtů ve Slacku pro účely operací aktualizace. Výběrem tlačítka Uložit potvrďte provedené změny.

   |Atribut|Typ|
   |---|---|
   |active|Logická hodnota|
   |externalId|Řetězec|
   |displayName|Řetězec|
   |name.familyName|Řetězec|
   |name.givenName|Řetězec|
   |title|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |userName|Řetězec|
   |nickName|Řetězec|
   |addresses[type eq "untyped"].streetAddress|Řetězec|
   |addresses[type eq "untyped"].locality|Řetězec|
   |addresses[type eq "untyped"].region|Řetězec|
   |addresses[type eq "untyped"].postalCode|Řetězec|
   |addresses[type eq "untyped"].country|Řetězec|
   |phoneNumbers[type eq "mobile"].value|Řetězec|
   |phoneNumbers[type eq "work"].value|Řetězec|
   |roles[primary eq "True"].value|Řetězec|
   |locale|Řetězec|
   |name.honorificPrefix|Řetězec|
   |photos[type eq "photo"].value|Řetězec|
   |profileUrl|Řetězec|
   |timezone|Řetězec|
   |userType|Řetězec|
   |urn:scim:schemas:extension:enterprise:1.0.department|Řetězec|
   |urn:scim:schemas:extension:enterprise:1.0.manager|Referenční informace|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|Řetězec|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|Řetězec|
   |urn:scim:schemas:extension:enterprise:1.0.organization|Řetězec|
   |urn:scim:schemas:extension:enterprise:1.0.division|Řetězec|

12. V části **Mapování** vyberte **Synchronizovat skupiny Azure Active Directory do Slacku**.

13. V části **Mapování atributů** zkontrolujte atributy skupin, které se budou synchronizovat z Azure AD do Slacku. Mějte na paměti, že atributy vybrané jako **odpovídající** vlastnosti se budou používat k porovnávání skupin ve Slacku pro účely operací aktualizace. Výběrem tlačítka Uložit potvrďte provedené změny.

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |členy|Referenční informace|

14. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pokud chcete pro Slack povolit službu zřizování Azure AD, v části **Nastavení** změňte **Stav zřizování** na **Zapnuto**.

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

16. Výběrem požadovaných hodnot v nabídce **Rozsah** v části **Nastavení** definujte uživatele nebo skupiny, které chcete ve Slacku zřídit.

    ![Rozsah zřizování](common/provisioning-scope.png)

17. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-5-monitor-your-deployment"></a>Krok 5. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Tipy pro řešení problémů

* Při konfiguraci atributu **displayName** Slacku mějte na paměti následující chování:

  * Hodnoty nejsou zcela jedinečné (např. 2 uživatelé můžou mít stejné zobrazované jméno).

  * Podporují se neanglické znaky, mezery a velká písmena. 
  
  * Mezi povolená interpunkční znaménka patří tečky, podtržítka, spojovníky, apostrofy, mezery (např. **( [ { } ] )** ) a oddělovače (např. **, / ;** ).
  
  * Vlastnost displayName nesmí obsahovat znak @. Pokud obsahuje znak @, může se v protokolech zřizování zobrazit přeskočená událost s popisem AttributeValidationFailed.

  * Aktualizuje se pouze v případě, že jsou v pracovišti nebo organizaci Slacku nakonfigurovaná tato dvě nastavení – **Synchronizace profilů je povolená** a **Uživatelé nemůžou změnit své zobrazované jméno**.

* Hodnota atributu **userName** Slacku musí být jedinečná a kratší než 21 znaků.

* Slack umožňuje porovnávání pouze s atributy **userName** a **email**.  
  
* Běžné kódy chyb jsou popsané v oficiální dokumentaci Slacku – https://api.slack.com/scim#errors

## <a name="change-log"></a>Protokol změn

* 16. 6. 2020 – Úprava atributu DisplayName tak, aby se aktualizoval pouze při vytvoření nového uživatele

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
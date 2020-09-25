---
title: 'Kurz: Konfigurace 15Five pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro 15Five.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 40fd048e1ace56af7a9198088d6129925033be93
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331212"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Kurz: Konfigurace 15Five pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v 15Five a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro [15Five](https://www.15five.com/pricing/). Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory.

> [!NOTE]
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v 15Five
> * Odebrat uživatele v 15Five, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a 15Five
> * Zřizování skupin a členství ve skupinách v 15Five
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/15five-tutorial) k 15Five (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant služby Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) .
* Uživatelský účet v Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (např. správce aplikací, správce cloudových aplikací, vlastník aplikací nebo globální správce)
* [Tenant 15Five](https://www.15five.com/pricing/)
* Uživatelský účet v 15Five s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Zjistěte, kdo bude v [rozsahu zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a 15Five](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-15five-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace 15Five pro podporu zřizování pomocí Azure AD

Před konfigurací 15Five pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na 15Five.

1. Přihlaste se ke [konzole pro správu 15Five](https://my.15five.com/). Přejděte na **funkce > integrace**.

    ![Konzola pro správu 15Five](media/15five-provisioning-tutorial/integration.png)

2.  Klikněte na **SCIM 2,0**.

    ![Konzola pro správu 15Five](media/15five-provisioning-tutorial/image00.png)

3.  Přejděte na **SCIM integration > vygenerujte token OAuth**.

    ![15Five přidat SCIM](media/15five-provisioning-tutorial/image02.png)

4.  Zkopírujte hodnoty **základní adresy URL** a **přístupového tokenu**pro SCIM 2,0. Tato hodnota se zadá do pole **Adresa URL tenanta** a **tajný token** na kartě zřizování vaší aplikace 15Five ve Azure Portal.
    
    ![15Five přidat SCIM](media/15five-provisioning-tutorial/image03.png)

## <a name="step-3-add-15five-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání 15Five z Galerie aplikací Azure AD

Přidejte 15Five z Galerie aplikací Azure AD a začněte spravovat zřizování pro 15Five. Pokud jste dříve nastavili 15Five pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k 15Five je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-5-configure-automatic-user-provisioning-to-15five"></a>Krok 5. Konfigurace automatického zřizování uživatelů na 15Five 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v 15Five na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro 15Five ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **15Five**.

    ![Odkaz 15Five v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5.  V části přihlašovací údaje správce zadejte **základní adresu URL SCIM 2,0 a hodnoty přístupového tokenu** načtené dříve v polích **Adresa URL tenanta** a **tajného tokenu** . Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k 15Five. Pokud se připojení nepovede, ujistěte se, že má váš účet 15Five oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé 15Five**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do 15Five v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v 15Five pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.


   |Atribut|Typ|
   |---|---|
   |active|Logická hodnota|
   |title|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |userName|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |externalId|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Referenční informace|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: employeeNumber|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: 15Five: 2.0: uživatel: umístění|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: 15Five: 2.0: uživatel: startDate|Řetězec|

10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do 15Five**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do 15Five v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v 15Five pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|
      |---|---|
      |externalId|Řetězec|
      |displayName|Řetězec|
      |členy|Referenční informace|

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro 15Five, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro 15Five, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

    Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  
    
## <a name="connector-limitations"></a>Omezení konektoru

* 15Five nepodporuje pro uživatele obnovitelné odstranění.

## <a name="change-log"></a>Protokol změn

* 06/16/2020 – Přidali jsme podporu pro atribut Enterprise Extension "Manager" a vlastní atributy "umístění" a "datum zahájení" pro uživatele.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* Přečtěte si, [Jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování](../app-provisioning/check-status-user-account-provisioning.md).

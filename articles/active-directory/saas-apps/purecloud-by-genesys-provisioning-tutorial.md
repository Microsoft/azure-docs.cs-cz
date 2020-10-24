---
title: 'Kurz: Konfigurace PureCloud podle Genesys pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po PureCloud pomocí Genesys.
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: 46fe93a6ba823a7932d25dcc3fc8cf64cffb389e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516530"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>Kurz: Konfigurace PureCloud podle Genesys pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v PureCloud pomocí Genesys a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, aby je [PureCloud podle Genesys](https://www.genesys.com) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v PureCloud pomocí Genesys
> * Odebrat uživatele v PureCloud pomocí Genesys, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a PureCloud pomocí Genesys
> * Zřizování skupin a členství ve skupinách v PureCloud pomocí Genesys
> * [Jednotné přihlašování](./purecloud-by-genesys-tutorial.md) do PureCloud podle Genesys (doporučeno)

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uživatelský účet v Azure AD s [oprávněním](../users-groups-roles/directory-assign-admin-roles.md) ke konfiguraci zřizování (např. správce aplikací, správce cloudových aplikací, vlastník aplikací nebo globální správce) 
* PureCloud [organizace](https://help.mypurecloud.com/?p=81984).
* Uživatel s [oprávněním](https://help.mypurecloud.com/?p=24360) k vytvoření klienta OAuth.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a PureCloud pomocí Genesys](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace PureCloud podle Genesys pro podporu zřizování s Azure AD

1. Vytvořte [klienta OAuth](https://help.mypurecloud.com/?p=188023) nakonfigurovaného ve vaší organizaci PureCloud.
2. Vygenerujte token [s vaším klientem OAuth](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html).
3. Pokud chcete automaticky zřídit členství ve skupině v rámci PureCloud, musíte v PureCloud [vytvořit skupiny](https://help.mypurecloud.com/?p=52397) s totožným názvem pro skupinu ve službě Azure AD.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání PureCloud pomocí Genesys z Galerie aplikací Azure AD

Přidáním PureCloud by Genesys z Galerie aplikací Azure AD spustíte správu zřizování pro PureCloud pomocí Genesys. Pokud jste dříve nastavili PureCloud pomocí Genesys pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin PureCloud pomocí Genesys je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro PureCloud pomocí Genesys 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro PureCloud pomocí Genesys ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **PureCloud podle Genesys**.

    ![Odkaz PureCloud podle Genesys v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte adresu URL PureCloud podle Genesys API a tokenu OAuth v poli **Adresa URL tenanta** a **tajného tokenu** (v uvedeném pořadí). Adresa URL rozhraní API bude strukturována jako `{{API Url}}/api/v2/scim/v2` , pomocí adresy URL rozhraní API vaší oblasti PureCloud z [centra pro vývojáře PureCloud](https://developer.mypurecloud.com/api/rest/index.html). Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k PureCloud pomocí Genesys. Pokud se připojení nepovede, zajistěte, aby měl účet PureCloud podle Genesys oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky se zobrazí v dialogovém okně přihlašovací údaje správce, kde můžete zadat svého tenanta U R L a tajného tokenu.](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé PureCloud podle Genesys**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do PureCloud podle Genesys v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v PureCloud pomocí Genesys pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), bude nutné zajistit, aby PureCloud rozhraní API pro Genesys podporuje filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

     |Atribut|Typ|
     |---|---|
     |userName|Řetězec|
     |active|Logická hodnota|
     |displayName|Řetězec|
     |emails[type eq "work"].value|Řetězec|
     |title|Řetězec|
     |phoneNumbers[type eq "mobile"].value|Řetězec|
     |phoneNumbers[type eq "work"].value|Řetězec|
     |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec|
     |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Referenční informace|
     |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: employeeNumber|Řetězec|
     

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do PureCloud podle Genesys**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do PureCloud podle Genesys v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v PureCloud podle Genesys pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny. PureCloud podle Genesys nepodporuje vytváření nebo odstraňování skupin a podporuje pouze aktualizaci skupin.

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |externalId|Řetězec|
      |členy|Referenční informace|

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro PureCloud pomocí Genesys, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro PureCloud pomocí Genesys, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

* S využitím [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
* Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
* Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="change-log"></a>Protokol změn

09/10 – přidala se podpora pro atribut Enterprise "employeeNumber".

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
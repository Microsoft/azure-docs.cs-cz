---
title: 'Kurz: Konfigurace zástupné pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po zástupné.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: 15d9504e2a886a66eeb429151305b885275b1f23
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91265487"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>Kurz: Konfigurace zástupné pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v zástupné i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, které se [zástupné](https://envoy.com/pricing/) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v zástupné
> * Odebrat uživatele v zástupné, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a zástupné
> * Zřizování skupin a členství ve skupinách v zástupné
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-tutorial) k zástupné (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet v Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (např. správce aplikací, správce cloudových aplikací, vlastník aplikací nebo globální správce) 
* [Tenant zástupné](https://envoy.com/pricing/)
* Uživatelský účet v zástupné s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Zjistěte, kdo bude v [rozsahu zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a zástupné](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-envoy-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace zástupné pro podporu zřizování pomocí Azure AD

1. Přihlaste se ke [konzole pro správu zástupné](https://dashboard.envoy.com/login). Klikněte na **integrace**.

    ![Integrace zástupné](media/envoy-provisioning-tutorial/envoy01.png)

2. Pro **integraci Microsoft Azure SCIM**klikněte na **nainstalovat** .

    ![Instalace zástupné](media/envoy-provisioning-tutorial/envoy02.png)

3. Klikněte na **Uložit** pro **synchronizaci všech uživatelů**. 

    ![Zástupné Uložit](media/envoy-provisioning-tutorial/envoy03.png)

4. Zkopírujte **token nosiče OAuth**. Tato hodnota se zadá do pole **token tajného** kódu na kartě zřizování vaší aplikace zástupné ve Azure Portal.
    
    ![Zástupné OAUTH](media/envoy-provisioning-tutorial/envoy04.png)

## <a name="step-3-add-envoy-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání zástupné z Galerie aplikací Azure AD

Přidejte zástupné z Galerie aplikací Azure AD a začněte spravovat zřizování pro zástupné. Pokud jste dříve nastavili zástupné pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k zástupné je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-envoy"></a>Krok 5. Konfigurace automatického zřizování uživatelů na zástupné 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro zástupné ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **zástupné**.

    ![Odkaz zástupné v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://app.envoy.com/scim/v2` **adresu URL tenanta**. V **tokenu tajného**kódu se zaznamenala hodnota **tokenu Bearer OAuth** získaná dřív. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k zástupné. Pokud se připojení nepovede, ujistěte se, že má váš účet zástupné oprávnění správce, a zkuste to znovu.

   ![zřizování](./media/envoy-tutorial/provisioning.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé zástupné**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do zástupné v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v zástupné pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní zástupné API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |externalId|Řetězec|
   |displayName|Řetězec|
   |title|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |preferredLanguage|Řetězec|
   |Oddělení|Řetězec|
   |adresy [typ EQ "Work"]. Country|Řetězec|
   |adresy [typ EQ "Work"].|Řetězec|
   |adresy [typ EQ "Work"]. region|Řetězec|
   |adresy [typ EQ "Work"]. postalCode|Řetězec|
   |adresy [Type EQ "Work"]. formátovaný|Řetězec|
   |adresy [Type EQ "]. streetAddress|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |název. formátovaný|Řetězec|
   |phoneNumbers[type eq "mobile"].value|Řetězec|
   |phoneNumbers[type eq "work"].value|Řetězec|
   |locale|Řetězec|

10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do zástupné**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do zástupné v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v zástupné pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |externalId|Řetězec|
      |členy|Referenční informace|

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro zástupné, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro zástupné, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

* S využitím [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
* Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user).
* Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../manage-apps/check-status-user-account-provisioning.md).
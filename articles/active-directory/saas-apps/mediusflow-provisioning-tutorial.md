---
title: 'Kurz: Konfigurace MediusFlow pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po MediusFlow.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: 71dc05e3949c2675be6255723530693304e1aa3e
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850077"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Kurz: Konfigurace MediusFlow pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v MediusFlow i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, které se [MediusFlow](https://www.mediusflow.com/) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v MediusFlow
> * Odebrat uživatele v MediusFlow, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a MediusFlow
> * Zřizování skupin a členství ve skupinách v MediusFlow
> * Jednotné přihlašování k MediusFlow (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* Aktivní předplatné MediusFlow se zajištěním kvality nebo produkčního tenanta.
* Uživatelský účet v MediusFlow s přístupovými právy správce, aby bylo možné provést konfiguraci v rámci MediusFlow.
* Společnosti přidané v tenantovi MediusFlow, kde by se měli uživatelé zřídit.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Zjistěte, kdo bude v [rozsahu zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a MediusFlow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace MediusFlow pro podporu zřizování pomocí Azure AD

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>Aktivace aplikace Microsoft 365 v rámci MediusFlow
Začněte tím, že v MediusFlow povolíte přístup ke službě Azure AD a funkci konfigurace Azure AD, a to provedením následujících kroků:

#### <a name="user-login"></a>Přihlášení uživatele
Pokud chcete povolit tok přihlášení Microsoft 365/Azure AD, přečtěte si [this] ( https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) článek.

#### <a name="user-transfer-configuration"></a>Konfigurace přenosu uživatelů
Pokud chcete povolit portál pro konfiguraci uživatelů pro zřizování ze služby Azure AD, přečtěte si [Tento](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) článek.

#### <a name="configure-user-provisioning"></a>Konfigurace zřizování uživatelů

1.  Přihlaste se ke [konzole správce MediusFlow](https://office365.cloudapp.mediusflow.com/) poskytnutím ID tenanta.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/1-auth.png" alt-text="Snímek obrazovky s konzolou pro správu MediusFlow V prvním kroku integrace se zvýrazní pole název tenanta MediusFlow a tlačítko ověřit." border="false":::

2. Ověřte připojení pomocí MediusFlow.

    ![Ověření](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Zadejte ID tenanta služby Azure AD.

    ![zadat ID tenanta](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   Další informace najdete v [nejčastějších dotazech](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id) k tomu, jak je najít.

4. Konfiguraci uložte.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/4-save-config.png" alt-text="Snímek obrazovky s konzolou pro správu MediusFlow V prvním kroku integrace se zvýrazní pole název tenanta MediusFlow a tlačítko ověřit." border="false":::

5. Vyberte zřizování uživatelů a klikněte na **OK**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png" alt-text="Snímek obrazovky s konzolou pro správu MediusFlow V prvním kroku integrace se zvýrazní pole název tenanta MediusFlow a tlačítko ověřit." border="false":::

6. Klikněte na **vygenerovat tajný klíč**. Tuto hodnotu zkopírujte a uložte. Tato hodnota se zadá do pole **token tajného** kódu na kartě **zřizování** vaší aplikace MediusFLow ve Azure Portal.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/6-create-secret-1.png" alt-text="Snímek obrazovky s konzolou pro správu MediusFlow V prvním kroku integrace se zvýrazní pole název tenanta MediusFlow a tlačítko ověřit." border="false":::

7. Klikněte na **OK**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/7-confirm-secret.png" alt-text="Snímek obrazovky s konzolou pro správu MediusFlow V prvním kroku integrace se zvýrazní pole název tenanta MediusFlow a tlačítko ověřit." border="false":::

8. Pokud chcete, aby uživatelé naimportovali s předem definovanou sadou rolí, společností a dalšími obecnými konfiguracemi v MediusFlow, budete ji muset nejdřív nakonfigurovat. Začněte přidáním konfigurace kliknutím na **Přidat novou konfiguraci**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png" alt-text="Snímek obrazovky s konzolou pro správu MediusFlow V prvním kroku integrace se zvýrazní pole název tenanta MediusFlow a tlačítko ověřit." border="false":::

9. Zadejte výchozí nastavení pro uživatele. V tomto zobrazení je možné nastavit výchozí atribut. Pokud jsou standardní nastavení v pořádku, stačí zadat jenom platný název společnosti. Vzhledem k tomu, že se tato nastavení konfigurace načítají z Mediusflow, musí být nejdřív nakonfigurovaná. Další informace najdete v části **požadavky** tohoto článku.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png" alt-text="Snímek obrazovky s konzolou pro správu MediusFlow V prvním kroku integrace se zvýrazní pole název tenanta MediusFlow a tlačítko ověřit." border="false":::

10. Kliknutím na **Uložit** uložte konfiguraci uživatele.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/10-done-1.png" alt-text="Snímek obrazovky s konzolou pro správu MediusFlow V prvním kroku integrace se zvýrazní pole název tenanta MediusFlow a tlačítko ověřit." border="false":::

11. Pokud chcete získat odkaz pro zřizování uživatelů, klikněte na **odkaz Kopírovat SCIM**. Tuto hodnotu zkopírujte a uložte. Tato hodnota se zadá do pole **Adresa URL tenanta** na kartě **zřizování** aplikace MediusFLow ve Azure Portal.
 
    :::image type="content" source="./media/mediusflow-provisioning-tutorial/11-get-scim-link.png" alt-text="Snímek obrazovky s konzolou pro správu MediusFlow V prvním kroku integrace se zvýrazní pole název tenanta MediusFlow a tlačítko ověřit." border="false":::

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání MediusFlow z Galerie aplikací Azure AD

Přidejte MediusFlow z Galerie aplikací Azure AD a začněte spravovat zřizování pro MediusFlow. Pokud jste již dříve MediusFlow nastavení pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k MediusFlow je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete ho řídit přiřazením jednoho nebo dvou uživatelů nebo skupin do aplikace. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>Krok 5. Konfigurace automatického zřizování uživatelů na MediusFlow 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro MediusFlow ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **MediusFlow**.

    ![Odkaz MediusFlow v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte hodnotu adresy URL tenanta načtenou dříve v **adrese URL tenanta**. Zadejte hodnotu tajného tokenu získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k MediusFlow. Pokud se připojení nepovede, ujistěte se, že má váš účet MediusFlow oprávnění správce, a zkuste to znovu.

      ![Snímek obrazovky se zobrazí v dialogovém okně přihlašovací údaje správce, kde můžete zadat svého tenanta U R L a tajného tokenu.](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé MediusFlow**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do MediusFlow v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v MediusFlow pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní MediusFlow API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |název. DisplayName|Řetězec|
   |active|Logická hodnota|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |název. formátovaný|Řetězec|
   |externalID|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Odkaz|


10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do MediusFlow**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do MediusFlow v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v MediusFlow pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |externalID|Řetězec|
      |členy|Referenční informace|

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro MediusFlow, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro MediusFlow, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../manage-apps/check-status-user-account-provisioning.md).

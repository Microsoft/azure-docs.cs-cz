---
title: 'Kurz: Konfigurace MediusFlow pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po MediusFlow.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: c6c65170-c5cb-44f6-81f8-25d972759e2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: 724068950153e6bc4687679892edf55ab89f3bf2
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927398"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Kurz: Konfigurace MediusFlow pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v MediusFlow i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, které se [MediusFlow](https://www.mediusflow.com/) pomocí služby zřizování Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md). 


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
1. Přečtěte si [, jak služba zřizování funguje](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
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

    ![Ověření](./media/mediusflow-provisioning-tutorial/1-auth.png)

2. Ověřte připojení pomocí MediusFlow.

    ![Ověření](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Zadejte ID tenanta služby Azure AD.

    ![zadat ID tenanta](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   Další informace najdete v [nejčastějších dotazech](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id) k tomu, jak je najít.

4. Konfiguraci uložte.

    ![Ověření](./media/mediusflow-provisioning-tutorial/4-save-config.png)

5. Vyberte zřizování uživatelů a klikněte na **OK**.

    ![Ověření](./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png)

6. Klikněte na **vygenerovat tajný klíč**. Tuto hodnotu zkopírujte a uložte. Tato hodnota se zadá do pole **token tajného** kódu na kartě **zřizování** vaší aplikace MediusFLow ve Azure Portal.

    ![Ověření](./media/mediusflow-provisioning-tutorial/6-create-secret-1.png)

7. Klikněte na **OK**.

    ![Ověření](./media/mediusflow-provisioning-tutorial/7-confirm-secret.png)

8. Pokud chcete, aby uživatelé naimportovali s předem definovanou sadou rolí, společností a dalšími obecnými konfiguracemi v MediusFlow, budete ji muset nejdřív nakonfigurovat. Začněte přidáním konfigurace kliknutím na **Přidat novou konfiguraci**.

    ![Uživatelé konfigurace](./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png)

9. Zadejte výchozí nastavení pro uživatele. V tomto zobrazení je možné nastavit výchozí atribut. Pokud jsou standardní nastavení v pořádku, stačí zadat jenom platný název společnosti. Vzhledem k tomu, že se tato nastavení konfigurace načítají z Mediusflow, musí být nejdřív nakonfigurovaná. Další informace najdete v části **požadavky** tohoto článku.

    ![Uživatelé konfigurace](./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png)

10. Kliknutím na **Uložit** uložte konfiguraci uživatele.

    ![Uživatelé konfigurace](./media/mediusflow-provisioning-tutorial/10-done-1.png)

11. Pokud chcete získat odkaz pro zřizování uživatelů, klikněte na **odkaz Kopírovat SCIM**. Tuto hodnotu zkopírujte a uložte. Tato hodnota se zadá do pole **Adresa URL tenanta** na kartě **zřizování** aplikace MediusFLow ve Azure Portal.
 
    ![Uživatelé konfigurace](./media/mediusflow-provisioning-tutorial/11-get-scim-link.png)

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání MediusFlow z Galerie aplikací Azure AD

Přidejte MediusFlow z Galerie aplikací Azure AD a začněte spravovat zřizování pro MediusFlow. Pokud jste již dříve MediusFlow nastavení pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z Galerie [najdete tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definujte, kdo bude v oboru pro zřizování. 

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení do aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů a skupin k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oboru, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k MediusFlow je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte malým. Než se pustíte do všech uživatelů, testujte je s malou sadou uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete ho řídit přiřazením jednoho nebo dvou uživatelů nebo skupin do aplikace. Pokud je obor nastavený na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>Krok 5. Konfigurace automatického zřizování uživatelů na MediusFlow 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro MediusFlow ve službě Azure AD:

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **MediusFlow**.

    ![Odkaz MediusFlow v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte hodnotu adresy URL tenanta načtenou dříve v **adrese URL tenanta**. Zadejte hodnotu tajného tokenu získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k MediusFlow. Pokud se připojení nepovede, ujistěte se, že má váš účet MediusFlow oprávnění správce, a zkuste to znovu.

      ![zřizování](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé MediusFlow**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do MediusFlow v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v MediusFlow pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní MediusFlow API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |e-maily [typ EQ "Work"]. Value|Řetězec|
   |název. DisplayName|Řetězec|
   |aktivně|Logická hodnota|
   |název. křestní jméno|Řetězec|
   |název. rodina|Řetězec|
   |název. formátovaný|Řetězec|
   |externalID|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Referenční informace|


10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do MediusFlow**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do MediusFlow v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v MediusFlow pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |externalID|Řetězec|
      |členy|Referenční informace|

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro MediusFlow, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro MediusFlow, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení** . Počáteční cyklus trvá déle než u dalších cyklů, ke kterým dojde přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Jakmile nakonfigurujete zřizování, použijte k monitorování nasazení tyto prostředky:

1. Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně nastavili.
2. Podívejte se na [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
3. Pokud se zdá, že konfigurace zřizování je ve stavu není v pořádku, bude aplikace přejít do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)

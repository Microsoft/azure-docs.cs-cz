---
title: 'Kurz: Konfigurace HootSuite pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po HootSuite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fbbee24e-272f-4fa6-819c-10c548bf0215
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2020
ms.author: Zhchia
ms.openlocfilehash: 1adbb0961ab610db936107f2fff210f4acecf2ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83883097"
---
# <a name="tutorial-configure-hootsuite-for-automatic-user-provisioning"></a>Kurz: Konfigurace HootSuite pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v HootSuite i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, které se [HootSuite](https://hootsuite.com/) pomocí služby zřizování Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v HootSuite
> * Odebrat uživatele v HootSuite, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a HootSuite
> * Zřizování skupin a členství ve skupinách v HootSuite
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial) k HootSuite (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* Uživatelský účet s [HootSuite](http://www.hootsuite.com/) , který má oprávnění **Spravovat člena** v organizaci.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si [, jak služba zřizování funguje](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a HootSuite](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-hootsuite-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace HootSuite pro podporu zřizování pomocí Azure AD

dev.support@hootsuite.comPokud chcete získat dlouhý tajný token, který budete potřebovat v pozdějších krocích, můžete se obrátit na. 

## <a name="step-3-add-hootsuite-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání HootSuite z Galerie aplikací Azure AD

Přidejte HootSuite z Galerie aplikací Azure AD a začněte spravovat zřizování pro HootSuite. Pokud jste dříve nastavili HootSuite pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z Galerie [najdete tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování. 

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení do aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů a skupin k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oboru, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k HootSuite je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte malým. Než se pustíte do všech uživatelů, testujte je s malou sadou uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastavený na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-hootsuite"></a>Krok 5. Konfigurace automatického zřizování uživatelů na HootSuite 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-hootsuite-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro HootSuite ve službě Azure AD:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](./media/hootsuite-provisioning-tutorial/enterprise-applications.png)

    ![Okno Všechny aplikace](./media/hootsuite-provisioning-tutorial/all-applications.png)

2. V seznamu aplikace vyberte **HootSuite**.

    ![Odkaz HootSuite v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** . klikněte na **Začínáme**.

    ![Karta zřizování](common/provisioning.png)

    ![Okno Začínáme](./media/hootsuite-provisioning-tutorial/get-started.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://platform.hootsuite.com/scim/v2` adresu URL tenanta. Zadejte hodnotu Long-Trvalá tajná hodnota tokenu, která byla dříve načtena v **kroku 2**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k HootSuite. Pokud se připojení nepovede, ujistěte se, že má váš účet HootSuite oprávnění správce, a zkuste to znovu.

    ![zřizování](./media/hootsuite-provisioning-tutorial/provisioning.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte možnost **zřídit Azure Active Directory uživatelé**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do HootSuite v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v HootSuite pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní HootSuite API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |e-maily [typ EQ "Work"]. Value|Řetězec|
   |aktivně|Logická hodnota|
   |displayName|Řetězec|
   |preferredLanguage|Řetězec|
   |údaj|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: HootSuite: 2.0: uživatel: organizationIds|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: HootSuite: 2.0: uživatel: teamIds|Řetězec|

10. Pokud chcete povolit službu Azure AD Provisioning pro HootSuite, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

11. Definujte uživatele nebo skupiny, které chcete zřídit pro HootSuite, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

12. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení** . Počáteční cyklus trvá déle než u dalších cyklů, ke kterým dojde přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Jakmile nakonfigurujete zřizování, použijte k monitorování nasazení tyto prostředky:

* Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně nastavili.
* Podívejte se na [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
* Pokud se zdá, že konfigurace zřizování je ve stavu není v pořádku, bude aplikace přejít do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  


## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)

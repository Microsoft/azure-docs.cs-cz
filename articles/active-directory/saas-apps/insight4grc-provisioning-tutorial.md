---
title: 'Kurz: Konfigurace Insight4GRC pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po Insight4GRC.
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: Zhchia
ms.openlocfilehash: 7d5904461d642f47e691d0a1aaa1f1faf439df1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96178116"
---
# <a name="tutorial-configure-insight4grc-for-automatic-user-provisioning"></a>Kurz: Konfigurace Insight4GRC pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v Insight4GRC i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, které se [Insight4GRC](https://www.rsmuk.com/) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Insight4GRC
> * Odebrat uživatele v Insight4GRC, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a Insight4GRC
> * Zřizování skupin a členství ve skupinách v Insight4GRC
> * [Jednotné přihlašování](./insight4grc-tutorial.md) k Insight4GRC (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uživatelský účet v Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (např. správce aplikací, správce cloudových aplikací, vlastník aplikací nebo globální správce) 
* Uživatelský účet v Insight4GRC s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a Insight4GRC](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-insight4grc-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Insight4GRC pro podporu zřizování pomocí Azure AD

Před konfigurací Insight4GRC pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na Insight4GRC.

1. Pokud chcete získat nosný token, musí koncový zákazník kontaktovat [tým podpory](mailto:support.ss@rsmuk.com).
2. Pokud chcete získat adresu URL koncového bodu SCIM, budete muset mít připravený název domény Insight4GRC, protože se bude používat k vytvoření adresy URL koncového bodu SCIM. Název domény Insight4GRC můžete načíst jako součást počátečního nákupu softwaru pomocí Insight4GRC.

## <a name="step-3-add-insight4grc-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Insight4GRC z Galerie aplikací Azure AD

Přidejte Insight4GRC z Galerie aplikací Azure AD a začněte spravovat zřizování pro Insight4GRC. Pokud jste dříve nastavili Insight4GRC pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin k Insight4GRC je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-insight4grc"></a>Krok 5. Konfigurace automatického zřizování uživatelů na Insight4GRC 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-insight4grc-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Insight4GRC ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Insight4GRC**.

    ![Odkaz Insight4GRC v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte adresu URL koncového bodu SCIM v **adrese URL klienta**. Adresa URL enpoint by měla být ve formátu, `https://<Insight4GRC Domain Name>.insight4grc.com/public/api/scim/v2 ` kde **Insight4GRC název domény** je hodnota získaná v předchozích krocích. Zadejte hodnotu tokenu nosiče, která byla dříve načtena v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Insight4GRC. Pokud se připojení nepovede, ujistěte se, že má váš účet Insight4GRC oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky se zobrazí v dialogovém okně přihlašovací údaje správce, kde můžete zadat svého tenanta U R L a tajného tokenu.](./media/insight4grc-provisioning-tutorial/provisioning.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Insight4GRC**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Insight4GRC v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Insight4GRC pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), budete muset zajistit, aby rozhraní Insight4GRC API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |externalId|Řetězec|
   |active|Logická hodnota|
   |title|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |phoneNumbers[type eq "work"].value|Řetězec|

10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do Insight4GRC**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do Insight4GRC v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v Insight4GRC pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |externalId|Řetězec|
      |členy|Referenční informace|

10. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro Insight4GRC, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro Insight4GRC, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

* Pomocí [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně zřídili.
* Zkontrolujte indikátor [průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) a zobrazte stav cyklu zřizování a způsob, jakým se má dokončit.
* Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* Přečtěte si, [Jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování](../app-provisioning/check-status-user-account-provisioning.md).
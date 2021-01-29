---
title: 'Kurz: Konfigurace BlueJeans pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro BlueJeans.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: d029f033a3c452587dbeeadf69c46cc99f604031
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053809"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Kurz: Konfigurace BlueJeans pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v BlueJeans i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele, aby [BlueJeans](https://www.bluejeans.com/pricing) pomocí služby Azure AD Provisioning. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v BlueJeans
> * Odebrat uživatele v BlueJeans, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a BlueJeans
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/bluejeans-tutorial) k BlueJeans (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenanta Azure AD](../develop/quickstart-create-new-tenant.md)
* Uživatelský účet ve službě Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* Tenant BlueJeans s plánem [společnosti](https://www.bluejeans.com/pricing) nebo je lépe povolený.
* Uživatelský účet v BlueJeans s oprávněními správce.
* Zřizování SCIM je povolené v BlueJeans Enterprise.

> [!NOTE]
> Integrace zřizování Azure AD spoléhá na [rozhraní BlueJeans API](https://BlueJeans.github.io/developer), které je dostupné pro BlueJeans týmy na standardním plánu nebo lepší.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a BlueJeans](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-bluejeans-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace BlueJeans pro podporu zřizování pomocí Azure AD

1. Přihlaste se ke konzole pro správu BlueJeans. Přejděte do nastavení skupiny > zabezpečení.
2. Vyberte **jednotné přihlašování** a **nakonfigurujte ho hned teď**.

    ![nyní](./media/bluejeans-provisioning-tutorial/configure.png)

3. V okně **zřizování & Integration** vyberte **vytvořit a spravovat uživatelské účty prostřednictvím IDP** a klikněte na **vygenerovat token**.

    ![vygenerování](./media/bluejeans-provisioning-tutorial/token.png)
    
4. Zkopírujte a uložte token. 
5. Adresa URL tenanta BlueJeans je `https://api.bluejeans.com/v2/scim` . **Adresa URL tenanta** a **tajný token** z předchozího kroku se zadají na kartě zřizování vaší aplikace BlueJeans ve Azure Portal.

## <a name="step-3-add-bluejeans-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání BlueJeans z Galerie aplikací Azure AD

Přidejte BlueJeans z Galerie aplikací Azure AD a začněte spravovat zřizování pro BlueJeans. Pokud jste dříve nastavili BlueJeans pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje obor, který se zřídí v závislosti na přiřazení aplikace a nebo na základě atributů uživatele. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Pokud se rozhodnete obor, který bude zřízený výhradně na atributech uživatele, můžete použít filtr oboru, jak je popsáno [zde](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů k BlueJeans je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do všech uživatelů, otestujte s malou sadou uživatelů. Pokud je obor pro zřizování nastavený na přiřazené uživatele, můžete to řídit přiřazením jednoho nebo dvou uživatelů k aplikaci. Pokud je obor nastavený na všichni uživatelé, můžete zadat [Filtr oboru založený na atributech](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-bluejeans"></a>Krok 5. Konfigurace automatického zřizování uživatelů na BlueJeans

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů v TestApp na základě přiřazení uživatelů v Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro BlueJeans ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **BlueJeans**.

    ![Odkaz BlueJeans v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Automatická karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte adresu URL vašeho tenanta BlueJeans a tajný token, který jste získali v kroku 2. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k BlueJeans. Pokud se připojení nepovede, ujistěte se, že má váš účet BlueJeans oprávnění správce, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)


6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby, která má obdržet oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k selhání**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé BlueJeans**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do BlueJeans v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v BlueJeans pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), budete muset zajistit, aby rozhraní BlueJeans API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

|Atribut|Typ|Podporováno pro filtrování|
|---|---|---|
|userName|Řetězec|&check;|
|active|Logická hodnota|
|title|Řetězec|
|emails[type eq "work"].value|Řetězec|
|name.givenName|Řetězec|
|name.familyName|Řetězec|
|phoneNumbers[type eq "work"].value|Řetězec|
|urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Řetězec|

10. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro BlueJeans, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele, které chcete zřídit pro BlueJeans, výběrem požadovaných hodnot v **oblasti** **Nastavení** v části.

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Omezení konektoru

* BlueJeans nepovoluje uživatelská jména, která překračují 30 znaků.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
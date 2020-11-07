---
title: 'Kurz: Konfigurace oddělení služeb SolarWinds (dříve Samanage) pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po SolarWinds oddělení služeb (dříve Samanage).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 5cdc36c20cbba148bb68bda700f5fdccbc593caf
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94352995"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>Kurz: Konfigurace služby SolarWinds Service Desk (dříve Samanage) pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v rámci služby SolarWinds Service Desk (dříve Samanage) a Azure Active Directory (Azure AD) pro konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny pro [SolarWinds oddělení služeb](https://www.samanage.com/pricing/) pomocí služby Azure AD Provisioning. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>Migrace na novou aplikaci SolarWinds Service Desk

Pokud máte existující integraci se službou SolarWinds, přečtěte si následující část o nadcházejících změnách. Pokud nastavujete službu SolarWinds Service Desk poprvé, můžete tuto část přeskočit a přejít na **podporované funkce**.

#### <a name="whats-changing"></a>Co se mění?

* Změny na straně Azure AD: autorizační metoda pro zřízení uživatelů v Samange má v **podstatě základní ověření**. Brzy uvidíte způsob, jakým se metoda autorizace změnila na **dlouhý tajný token**.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Co potřebuji k migraci stávající vlastní integrace do nové aplikace?

Pokud máte existující integraci služby SolarWinds Service Desk s platnými přihlašovacími údaji správce, **není nutná žádná akce**. Automaticky migrujeme zákazníky do nové aplikace. Tento proces se provádí kompletně na pozadí. Pokud vyprší platnost stávajících přihlašovacích údajů, nebo pokud potřebujete znovu autorizovat přístup k aplikaci, je potřeba vygenerovat dlouhodobě tajný token. Pokud chcete vygenerovat nový token, přečtěte si krok 2 tohoto článku.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Jak zjistím, jestli je moje aplikace migrována? 

Když se vaše aplikace migruje, v části **přihlašovací údaje správce** se pole **uživatelské jméno správce** a **heslo správce** nahradí jediným **tajným polem tokenu** .

## <a name="capabilities-supported"></a>Podporované funkce

> [!div class="checklist"]
> * Vytvoření uživatelů v oddělení služeb SolarWinds
> * Odebrat uživatele v oddělení služeb SolarWinds, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a oddělením služeb SolarWinds
> * Zřizování skupin a členství ve skupinách v SolarWinds oddělení služeb
> * [Jednotné přihlašování](./samanage-tutorial.md) do služby SolarWinds Service Desk (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uživatelský účet ve službě Azure AD s [oprávněním](../users-groups-roles/directory-assign-admin-roles.md) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* [Tenant služby SolarWinds Service](https://www.samanage.com/pricing/) se balíčkem Professional.
* Uživatelský účet ve službě SolarWinds Service s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a oddělením služeb SolarWinds](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace oddělení služeb SolarWinds pro podporu zřizování s Azure AD

Pokud chcete vygenerovat tajný token pro ověřování, podívejte se na [kurz ověřování pomocí tokenů pro integraci rozhraní API](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657).

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání oddělení služeb SolarWinds z Galerie aplikací Azure AD

Přidejte desku SolarWinds Service z Galerie aplikací Azure AD a začněte spravovat zřizování pro SolarWinds oddělení služeb. Pokud jste dříve nastavili službu SolarWinds pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin k SolarWinds oddělení služeb musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>Krok 5. Konfigurace automatického zřizování uživatelů na oddělení služeb SolarWinds 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro oddělení služeb SolarWinds v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **SolarWinds Service Desk**.

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky zobrazující kartu zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky, který ukazuje režim zřizování nastavený na automatické.](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://api.samanage.com` **adresu URL tenanta**.  Zadejte hodnotu tajného tokenu získanou dříve v **tajném tokenu**. Vyberte **Test připojení** , aby se služba Azure AD mohla připojit k SolarWinds oddělení služeb. Pokud se připojení nepovede, ujistěte se, že váš účet služby SolarWinds má oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky zobrazující vybrané tlačítko Test připojení](./media/samanage-provisioning-tutorial/provisioning.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé SolarWinds oddělení služeb**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do služby SolarWinds Service v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v oddělení služby SolarWinds pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), budete muset zajistit, aby rozhraní API oddělení služeb SolarWinds podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

      ![Mapování uživatelů Samange](./media/samanage-provisioning-tutorial/user-attributes.png)

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do služby SolarWinds Service Desk**.

11. Zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD do služby SolarWinds Service Desk v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v oddělení služby SolarWinds pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      ![Mapování skupin Samange](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning Service pro desku SolarWinds, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro SolarWinds oddělení služeb, výběrem požadovaných hodnot v **oblasti** **Nastavení** v části.

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="connector-limitations"></a>Omezení konektoru

Pokud vyberete možnost **synchronizovat všechny uživatele a skupiny** a nakonfigurujete hodnotu pro atribut **role** portálu SolarWinds Service, hodnota pod **výchozí hodnotou, pokud je pole null (volitelné)** , musí být vyjádřená v následujícím formátu:

- {"DisplayName": "role"}, kde role je výchozí požadovaná hodnota.

## <a name="change-log"></a>Protokol změn

* 09/14/2020 – změnili jsme název společnosti ve dvou kurzech SaaS od Samanage do služby SolarWinds Service Desk (dříve Samanage) na https://github.com/ravitmorales .
* 04/22/2020 – byla aktualizována metoda autorizace ze základního ověřování na dlouhý token tajného kódu.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
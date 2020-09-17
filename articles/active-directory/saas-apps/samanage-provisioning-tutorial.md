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
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 0941c95ee6215a710ebb1bbc2fba9fae09ccf16d
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707262"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>Kurz: Konfigurace služby SolarWinds Service Desk (dříve Samanage) pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v rámci služby SolarWinds Service Desk (dříve Samanage) a Azure Active Directory (Azure AD) pro konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny pro [SolarWinds oddělení služeb](https://www.samanage.com/pricing/) pomocí služby Azure AD Provisioning. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).

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
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial) do služby SolarWinds Service Desk (doporučeno)

## <a name="prerequisites"></a>Požadované součásti

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* [Tenant služby SolarWinds Service](https://www.samanage.com/pricing/) se balíčkem Professional.
* Uživatelský účet ve službě SolarWinds Service s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si [, jak služba zřizování funguje](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a oddělením služeb SolarWinds](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace oddělení služeb SolarWinds pro podporu zřizování s Azure AD

Pokud chcete vygenerovat tajný token pro ověřování, podívejte se na [kurz ověřování pomocí tokenů pro integraci rozhraní API](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657).

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání oddělení služeb SolarWinds z Galerie aplikací Azure AD

Přidejte desku SolarWinds Service z Galerie aplikací Azure AD a začněte spravovat zřizování pro SolarWinds oddělení služeb. Pokud jste dříve nastavili službu SolarWinds pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z Galerie [najdete tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definujte, kdo bude v oboru pro zřizování. 

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení do aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů a skupin k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oboru, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k SolarWinds oddělení služeb musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte malým. Než se pustíte do všech uživatelů, testujte je s malou sadou uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastavený na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>Krok 5. Konfigurace automatického zřizování uživatelů na oddělení služeb SolarWinds 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro oddělení služeb SolarWinds v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **SolarWinds Service Desk**.

3. Vyberte kartu **zřizování** .

    ![Snímek obrazovky zobrazující kartu zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Snímek obrazovky, který ukazuje režim zřizování nastavený na automatické.](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://api.samanage.com` **adresu URL tenanta**.  Zadejte hodnotu tajného tokenu získanou dříve v **tajném tokenu**. Vyberte **Test připojení** , aby se služba Azure AD mohla připojit k SolarWinds oddělení služeb. Pokud se připojení nepovede, ujistěte se, že váš účet služby SolarWinds má oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky zobrazující vybrané tlačítko Test připojení](./media/samanage-provisioning-tutorial/provisioning.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé SolarWinds oddělení služeb**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do služby SolarWinds Service v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v oddělení služby SolarWinds pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní API oddělení služeb SolarWinds podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

      ![Mapování uživatelů Samange](./media/samanage-provisioning-tutorial/user-attributes.png)

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do služby SolarWinds Service Desk**.

11. Zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD do služby SolarWinds Service Desk v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v oddělení služby SolarWinds pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      ![Mapování skupin Samange](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning Service pro desku SolarWinds, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro SolarWinds oddělení služeb, výběrem požadovaných hodnot v **oblasti** **Nastavení** v části.

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení** . Počáteční cyklus trvá déle než u dalších cyklů, ke kterým dojde přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Jakmile nakonfigurujete zřizování, použijte k monitorování nasazení tyto prostředky:

1. Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně nastavili.
2. Podívejte se na [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
3. Pokud se zdá, že konfigurace zřizování je ve stavu není v pořádku, bude aplikace přejít do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Omezení konektoru

Pokud vyberete možnost **synchronizovat všechny uživatele a skupiny** a nakonfigurujete hodnotu pro atribut **role** portálu SolarWinds Service, hodnota pod **výchozí hodnotou, pokud je pole null (volitelné)** , musí být vyjádřená v následujícím formátu:

- {"DisplayName": "role"}, kde role je výchozí požadovaná hodnota.

## <a name="change-log"></a>Protokol změn

* 09/14/2020 – změnili jsme název společnosti ve dvou kurzech SaaS od Samanage do služby SolarWinds Service Desk (dříve Samanage) na https://github.com/ravitmorales .
* 04/22/2020 – byla aktualizována metoda autorizace ze základního ověřování na dlouhý token tajného kódu.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)

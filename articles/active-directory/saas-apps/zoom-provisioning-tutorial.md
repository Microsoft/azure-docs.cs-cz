---
title: 'Kurz: Konfigurace přiblížení pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD pro přiblížení.
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
ms.openlocfilehash: 46312dc2037eb128908e743f29474b9b4e79a2a6
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519839"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Kurz: Konfigurace přiblížení pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v rámci lupy i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny pro [přiblížení](https://zoom.us/pricing/) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v přiblížení
> * Odebrat uživatele v přiblížení, pokud už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a přiblížením
> * [Jednotné přihlašování](./zoom-tutorial.md) pro přiblížení (doporučeno)

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenanta Azure AD](../develop/quickstart-create-new-tenant.md)
* Uživatelský účet v Azure AD s [oprávněním](../users-groups-roles/directory-assign-admin-roles.md) ke konfiguraci zřizování (např. správce aplikací, správce cloudových aplikací, vlastník aplikací nebo globální správce) 
* [Tenant přiblížení](https://zoom.us/pricing).
* Uživatelský účet v přiblížení s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a přiblížení](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace přiblížení na podporu zřizování pomocí Azure AD

1. Přihlaste se ke [konzole správce lupy](https://zoom.us/signin). Přejděte na **Web Advanced > App Marketplace** v levém navigačním podokně.

    ![Integrace lupy](media/zoom-provisioning-tutorial/zoom01.png)

2. V pravém horním rohu stránky přejděte ke **správě** . 

    ![Snímek obrazovky aplikace zoom Marketplace s možností spravovat s názvem.](media/zoom-provisioning-tutorial/zoom02.png)

3. Přejděte na vytvořenou aplikaci Azure AD. 
    
    ![Snímek obrazovky oddílu vytvořených aplikací s aplikací Azure A D, která se vyvolala](media/zoom-provisioning-tutorial/zoom03.png)

4. V levém navigačním podokně vyberte možnost **přihlašovací údaje aplikace** .

    ![Snímek obrazovky levého navigačního podokna s zvýrazněnou možností pověření aplikace](media/zoom-provisioning-tutorial/zoom04.png)

5. Zkopírujte a uložte **token JWT**. Tato hodnota se zadá do pole **token tajného klíče** na kartě zřizování vaší aplikace přiblížení v Azure Portal. Pokud potřebujete nový token bez vypršení platnosti, budete muset znovu nakonfigurovat čas vypršení platnosti, který bude automaticky generovat nový token. 

    ![Snímek obrazovky se stránkou s přihlašovacími údaji aplikace](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání lupy z Galerie aplikací Azure AD

Přidejte přiblížení z Galerie aplikací Azure AD a začněte spravovat zřizování pro přiblížení. Pokud jste již dříve nastavili přiblížení pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin k přiblížení musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro přiblížení 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro přiblížení v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Lupa**.

    ![Odkaz Lupa v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://api.zoom.us/scim` **adresu URL tenanta**. Zadejte hodnotu **tokenu JWT** získanou dříve v **tokenu tajného**kódu. Klikněte na **Test připojení** a ujistěte se, že se může služba Azure AD připojit k přiblížení. Pokud se připojení nepovede, zajistěte, aby měl váš účet přiblížení oprávnění správce, a zkuste to znovu.

    ![Zřizování lupy](./media/zoom-provisioning-tutorial/provisioning.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé pro přiblížení**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD, abyste mohli zvětšit oddíl **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v přiblížení pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), budete muset zajistit, aby rozhraní zoom API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |active|Logická hodnota|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |e-maily [typ EQ "Work"]|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec|

10. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro přiblížení, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro přiblížení, výběrem požadovaných hodnot v části **Rozsah** **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Omezení konektoru
* Možnost přiblížení umožňuje dnes maximálně 9 999 základních uživatelů.

## <a name="change-log"></a>Protokol změn
* 05/14/2020 – byla přidána podpora operací aktualizace pro e-maily [Type EQ "Work"] Attribute.
* 10/20/2020 – Přidali jsme podporu pro dvě nové role "licencované" a "on-Prem", která nahradí stávající role "pro" a "Corp". V budoucnu bude odebrána podpora pro role pro a Corp.

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
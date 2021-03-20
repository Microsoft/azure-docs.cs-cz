---
title: 'Kurz: Konfigurace Juno cesty pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po Juno cestu.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: Zhchia
ms.openlocfilehash: 0efb451997b0ed842e6757a7e6b30dd88b33f4aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96177881"
---
# <a name="tutorial-configure-juno-journey-for-automatic-user-provisioning"></a>Kurz: Konfigurace Juno cesty pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v rámci cesty Juno a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, aby [Juno cestu](https://www.junojourney.com/) pomocí služby Azure AD Provisioning. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Juno cestě
> * Odebrat uživatele v Juno cestě, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a Juno cestou
> * [Jednotné přihlašování](./juno-journey-tutorial.md) k Juno cestě (doporučeno)

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uživatelský účet v Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (např. správce aplikací, správce cloudových aplikací, vlastník aplikací nebo globální správce) 
*  [Tenant pro Juno cestu](https://www.junojourney.com/getstarted).
*  Uživatelský účet v Juno cestě s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a Juno cesta](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-juno-journey-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Juno cesty pro podporu zřizování pomocí Azure AD

1. V případě **tajného tokenu**  a **adresy URL klienta** kontaktujte tým podpory Juno pro jízdu na adrese support@the-juno.com . Tato hodnota se zadá do polí **tajný token**  a **Adresa URL tenanta** na kartě zřizování aplikace Juno pro cestu v Azure Portal. 

## <a name="step-3-add-juno-journey-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání cesty Juno z Galerie aplikací Azure AD

Přidejte cestu Juno z Galerie aplikací Azure AD a začněte spravovat zřizování pro Juno cestu. Pokud jste dříve nastavili cestu Juno pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin k Juno cestě musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-juno-journey"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro Juno cestu 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-juno-journey-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro cestu Juno ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **cestu Juno**.

    ![Odkaz na cestu Juno v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte hodnotu adresy URL tenanta načtenou dříve v **adrese URL tenanta**. Zadejte hodnotu tajného tokenu získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Juno cestě. Pokud se připojení nepovede, zajistěte, aby měl účet Juno cesty oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky se zobrazí v dialogovém okně přihlašovací údaje správce, kde můžete zadat svého tenanta U R L a tajného tokenu.](./media/juno-journey-provisioning-tutorial/provisioning.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele a Juno cestu**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD až do Juno cesty v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Juno cestě pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), bude nutné zajistit, aby rozhraní API pro Juno cestu podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Proměnná|Typ|
   |---|---|
   |userName|Řetězec|
   |externalId|Řetězec|
   |displayName|Řetězec|
   |title|Řetězec|
   |active|Logická hodnota|
   |preferredLanguage|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |adresy [typ EQ "Work"]. Country|Řetězec|
   |adresy [typ EQ "Work"]. region|Řetězec|
   |adresy [typ EQ "Work"].|Řetězec|
   |adresy [typ EQ "Work"]. postalCode|Řetězec|
   |adresy [Type EQ "Work"]. formátovaný|Řetězec|
   |adresy [Type EQ "]. streetAddress|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |název. middleName|Řetězec|
   |název. formátovaný|Řetězec|
   |phoneNumbers [typ EQ "fax"]. hodnota|Řetězec|
   |phoneNumbers[type eq "mobile"].value|Řetězec|
   |phoneNumbers[type eq "work"].value|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: employeeNumber|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: costCenter|Řetězec|
   urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: divize|Řetězec|
   urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Řetězec|
   urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Organization|Řetězec|


10. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro cestu Juno, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

12. Určete uživatele nebo skupiny, které chcete zřídit pro Juno cestu, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

* S využitím [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
* Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
* Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
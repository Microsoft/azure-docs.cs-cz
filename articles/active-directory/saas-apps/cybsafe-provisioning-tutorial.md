---
title: 'Kurz: Konfigurace CybSafe pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po CybSafe.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 7255fe44-1662-4ae4-9ff3-9492911b7ce0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2020
ms.author: Zhchia
ms.openlocfilehash: 0bb3b218b4d4992a01836cfb9becbea97236bd2e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95549916"
---
# <a name="tutorial-configure-cybsafe-for-automatic-user-provisioning"></a>Kurz: Konfigurace CybSafe pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v CybSafe i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, které se [CybSafe](https://app.cybsafe.com/login) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v CybSafe
> * Odebrat uživatele v CybSafe, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a CybSafe
> * Zřizování skupin a členství ve skupinách v CybSafe

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* Účet správce [CybSafe](https://app.cybsafe.com/login) s podnikovým předplatným.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Zjistěte, kdo bude v [rozsahu zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a CybSafe](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-cybsafe-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace CybSafe pro podporu zřizování pomocí Azure AD

1. **Adresa URL tenanta** pro CybSafe je: `https://app.cybsafe.com/scim/v2/` . Tato hodnota se zadá na kartě zřizování aplikace CybSafe v Azure Portal.
2. Přihlaste se k [CybSafe](https://app.cybsafe.com/login) pomocí svého účtu správce.
3. Přejděte do [Nastavení->Správa identit](https://app.cybsafe.com/settings/identity-management). 
4. V části zřizování uživatelů klikněte na **vygenerovat token** . Tato hodnota se zadá na kartě zřizování aplikace CybSafe v Azure Portal.

## <a name="step-3-add-cybsafe-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání CybSafe z Galerie aplikací Azure AD

Přidejte CybSafe z Galerie aplikací Azure AD a začněte spravovat zřizování pro CybSafe. Pokud jste již dříve CybSafe nastavení pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k CybSafe je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-cybsafe"></a>Krok 5. Konfigurace automatického zřizování uživatelů na CybSafe 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cybsafe-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro CybSafe ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **CybSafe**.

    ![Odkaz CybSafe v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Automatická karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte adresu URL vašeho tenanta CybSafe a tajný token. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k CybSafe. Pokud se připojení nepovede, ujistěte se, že má váš účet CybSafe oprávnění správce, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé CybSafe**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do CybSafe v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v CybSafe pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní CybSafe API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |userName|Řetězec|&check;|
   |active|Logická hodnota|
   |displayName|Řetězec|
   |title|Řetězec|
   |preferredLanguage|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |název. formátovaný|Řetězec|
   |name.honorificPrefix|Řetězec|
   |název. honorificSuffix|Řetězec|
   |nickName|Řetězec|
   |phoneNumbers[type eq "mobile"].value|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |locale|Řetězec|
   |timezone|Řetězec|
   |userType|Řetězec|

10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do CybSafe**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do CybSafe v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v CybSafe pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|Podporováno pro filtrování|
      |---|---|---|
      |displayName|Řetězec|&check;|
      |členy|Referenční informace|

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro CybSafe, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro CybSafe, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../manage-apps/check-status-user-account-provisioning.md).

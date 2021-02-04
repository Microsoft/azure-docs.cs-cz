---
title: 'Kurz: Konfigurace getabstract pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po getabstract.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 50be022f04a5c9fde9758d533adce657763db269
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566760"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>Kurz: Konfigurace getabstract pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v rámci metody getabstract a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny pro [getabstract](https://www.getabstract.com) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Umožňuje vytvořit uživatele v getabstract.
> * Odebere uživatele v getabstract, když nepotřebují přístup už.
> * Udržujte atributy uživatele synchronizované mezi Azure AD a getabstract.
> * Zřizování skupin a členství ve skupinách v getabstract.
> * [Jednotné přihlašování](getabstract-tutorial.md) pro getabstract (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* Klient getabstract (licence getabstract Corporate).
* Jednotné přihlašování je povolené v tenantovi Azure AD a v tenantovi getabstract.
* Schválení a SCIM povolení getabstract (odeslání e-mailu b2b.itsupport@getabstract.com ).

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Zjistěte, kdo bude v [rozsahu zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a getabstract](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace getabstract pro podporu zřizování pomocí Azure AD
1. Přihlásit se k getabstract
2. Klikněte na ikonu nastavení umístěnou v pravém horním rohu a potom klikněte na možnost **Moje centrální Správa** .
 
    ![getabstract my Central admin](media/getabstract-provisioning-tutorial/my-account.png)

3. Vyhledejte možnost **správce SCIM** a klikněte na ni.
 
    ![Správce getabstract SCIM](media/getabstract-provisioning-tutorial/scim-admin.png) 

4. Klikněte na tlačítko **Přejít** . 

    ![ID klienta getabstract SCIM](media/getabstract-provisioning-tutorial/scim-client-go.png)

5. Klikněte na **vygenerovat nový token** .

    ![getabstract SCIM token 1](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

6. Pokud jste si jisti, že jste si jisti, klikněte na tlačítko **generovat nový token** . V opačném případě klikněte na tlačítko **Storno** .

    ![SCIM token getabstract 2](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

7. Nakonec můžete buď kliknout na ikonu kopírování do schránky, nebo vybrat celý token a zkopírovat ho. Také si všimněte, že je tenant nebo základní adresa URL `https://www.getabstract.com/api/scim/v2` . Tyto hodnoty se zadají do pole **tajný token** * a **Adresa URL tenanta** * na kartě zřizování aplikace getabstract v Azure Portal.

    ![SCIM token getabstract 3](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)


## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>Krok 3. Přidat getabstract z Galerie aplikací Azure AD

Přidejte getabstract z Galerie aplikací Azure AD a začněte spravovat zřizování pro getabstract. Pokud jste dříve nastavili getabstract pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k getabstract je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro getabstract 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro funkci getabstract v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **getabstract**.

    ![Odkaz getabstract v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Automatická karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte adresu URL klienta getabstract a tajný token. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k getabstract. Pokud se připojení nepovede, zajistěte, aby měl účet getabstract oprávnění správce, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele do getabstract**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do getabstract v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v getabstract pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), je nutné zajistit, aby getabstract API podporoval filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |userName|Řetězec|&check;|
   |active|Logická hodnota|
   |emails[type eq "work"].value|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |externalId|Řetězec|
   |preferredLanguage|Řetězec|

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do getabstract**.

11. Zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD do getabstract v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v getabstract pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    |Atribut|Typ|Podporováno pro filtrování|
    |---|---|---|
    |displayName|Řetězec|&check;|
    |externalId|Řetězec|
    |členy|Referenční informace|
12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro getabstract, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. V části **Nastavení** definujte uživatele nebo skupiny, které chcete zřídit pro getabstract, a to tak, že vyberete požadované hodnoty v **rozsahu** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

* S využitím [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
* Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user).
* Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../manage-apps/check-status-user-account-provisioning.md).

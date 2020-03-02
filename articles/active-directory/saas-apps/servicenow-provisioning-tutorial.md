---
title: 'Kurz: Konfigurace ServiceNow pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 9e93d4b3f1880f2ac56a32a7b85aa6801fb7c14e
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205095"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Kurz: Konfigurace ServiceNow pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v ServiceNow i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, které se [ServiceNow](https://www.servicenow.com/) pomocí služby zřizování Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v ServiceNow
> * Odebrat uživatele v ServiceNow, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a ServiceNow
> * Zřizování skupin a členství ve skupinách v ServiceNow
> * [Jednotné přihlašování](servicenow-tutorial.md) k ServiceNow (doporučeno)

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* [Instance ServiceNow](https://www.servicenow.com/) typu Calgary nebo vyšší
* [ServiceNow Express instance](https://www.servicenow.com/) Helsinky nebo vyšší
* Uživatelský účet v ServiceNow s rolí správce

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si [, jak služba zřizování funguje](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a ServiceNow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace ServiceNow pro podporu zřizování pomocí Azure AD

1. Identifikujte název instance ServiceNow. Název instance najdete v adrese URL, kterou používáte pro přístup k ServiceNow. V následujícím příkladu je název instance dev35214.

![Instance ServiceNow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. Získejte přihlašovací údaje pro správce v ServiceNow. Přejděte do profilu uživatele v ServiceNow a ověřte, jestli má uživatel roli správce. 

![Role správce ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání ServiceNow z Galerie aplikací Azure AD

Přidejte ServiceNow z Galerie aplikací Azure AD a začněte spravovat zřizování pro ServiceNow. Pokud jste dříve nastavili ServiceNow pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z Galerie [najdete tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování. 

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení do aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů a skupin k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oboru, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k ServiceNow je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte malým. Než se pustíte do všech uživatelů, testujte je s malou sadou uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastavený na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Krok 5. Konfigurace automatického zřizování uživatelů na ServiceNow 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro ServiceNow ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **ServiceNow**.

    ![Odkaz ServiceNow v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte svoje přihlašovací údaje správce ServiceNow a uživatelské jméno. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k ServiceNow. Pokud se připojení nepovede, ujistěte se, že má váš účet ServiceNow oprávnění správce, a zkuste to znovu.

    ![zřizování](./media/servicenow-provisioning-tutorial/provisioning.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Save** (Uložit).

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé ServiceNow**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do ServiceNow v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v ServiceNow pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní ServiceNow API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do ServiceNow**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do ServiceNow v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v ServiceNow pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro ServiceNow, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro ServiceNow, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení** . Počáteční cyklus trvá déle než u dalších cyklů, ke kterým dojde přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Jakmile nakonfigurujete zřizování, použijte k monitorování nasazení tyto prostředky:

1. Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně nastavili.
2. Podívejte se na [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
3. Pokud se zdá, že konfigurace zřizování je ve stavu není v pořádku, bude aplikace přejít do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Tipy pro řešení problémů
* **InvalidLookupReference:** Při zřizování určitých atributů, jako je například oddělení a umístění v ServiceNow, musí hodnoty již existovat v referenční tabulce v ServiceNow. V tabulce **název tabulky** v ServiceNow můžete mít například dvě umístění (Praha, Los Angeles) a tři oddělení (prodej, finance, marketing). Pokud se pokusíte zřídit uživatele, kde má oddělení "prodej" a umístění je "Praha", bude zřízené úspěšně. Pokud se pokusíte zřídit uživatele s oddělením prodej a umístěním "LA", uživatel se nezřídí. Umístění LA musí být buď přidáno do referenční tabulky v ServiceNow, nebo musí být atribut uživatele v Azure AD aktualizovaný tak, aby odpovídal formátu v ServiceNow. 
* **EntryJoiningPropertyValueIsMissing:** Zkontrolujte [mapování atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) k identifikaci odpovídajícího atributu. Tato hodnota musí být k dispozici u uživatele nebo skupiny, které se pokoušíte zřídit. 
* Přečtěte si [rozhraní SERVICENOW SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) , abyste pochopili všechny požadavky nebo omezení (například formát pro určení kódu země pro uživatele).
* Některá nasazení ServiceNow vyžadují povolení rozsahů IP adres pro službu Azure AD Provisioning. Rezervované rozsahy IP adres pro službu Azure AD Provisioning [najdete v části](https://www.microsoft.com/download/details.aspx?id=56519) "AzureActiveDirectoryDomainServices".
* Požadavky zřizování se ve výchozím nastavení odesílají do https://{název-instance}. Service-Now. com/{Table-Name}. Pokud potřebujete vlastní adresu URL tenanta, můžete v poli název instance zadat celou adresu URL.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

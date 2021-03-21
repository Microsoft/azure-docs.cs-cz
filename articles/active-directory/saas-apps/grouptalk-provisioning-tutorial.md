---
title: 'Kurz: Konfigurace GroupTalk pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po GroupTalk.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: e537d393-2724-450f-9f5b-4611cdc9237c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 0af41127577c172cdab74ae908f0645733d49a42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735754"
---
# <a name="tutorial-configure-grouptalk-for-automatic-user-provisioning"></a>Kurz: Konfigurace GroupTalk pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v GroupTalk i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, které se [GroupTalk](https://www.grouptalk.com/) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v GroupTalk
> * Odebrat uživatele v GroupTalk, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a GroupTalk
> * Zřizování skupin a členství ve skupinách v GroupTalk

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uživatelský účet ve službě Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* Uživatelský účet v GroupTalk s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a GroupTalk](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-grouptalk-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace GroupTalk pro podporu zřizování pomocí Azure AD

1. Přihlaste se k podpoře GroupTalk support@grouptalk.com s **názvem tenanta** a **ID** , které chcete integrovat s Azure AD.
2. Až se vám oznámí, že je potřeba nastavit integraci služby Azure AD, přihlaste se ke Správci GroupTalk a přejděte k zobrazení vaší organizace. 
3. Měla by se zobrazit položka konfigurace integrace služby Azure AD. Kliknutím na něj ověříte **název** a **ID**  tenanta, abyste získali **token JWT (tajný token)**. 
4. Adresa URL tenanta GroupTalk je `https://api.grouptalk.com/api/scim/` . **Adresa URL tenanta** a **tajný token** načtený v předchozím kroku se zadají na kartě zřizování vaší aplikace GroupTalk ve Azure Portal. 

## <a name="step-3-add-grouptalk-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání GroupTalk z Galerie aplikací Azure AD

Přidejte **GroupTalk** z Galerie aplikací Azure AD a začněte spravovat zřizování pro GroupTalk.

1. Klikněte na tlačítko **zaregistrovat se k GroupTalk** , které vás přesměruje do aplikace pro správu GroupTalk.
2. Pokud jste už přihlášení k GroupTalk, odhlaste se, abyste se dostali na přihlašovací obrazovku. Vyberte kartu Azure AD a klikněte na tlačítko **Přihlásit** se.

    ![GroupTalk](media/grouptalk-provisioning-tutorial/login.png)

3. Přihlaste se pomocí účtu správce služby AD a přijměte přístupová práva aplikace GroupTalk. Po dokončení této akce se zobrazí chybová zpráva s informacemi o tom, že uživatel není přítomen. To se očekává, protože váš uživatel ještě nezřídil GroupTalk, ale teď jste do svého tenanta přidali GroupTalk.
4. Vraťte se na Azure Portal a ověřte, že je teď přidaný **GroupTalk** k podnikovým aplikacím.

Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin k GroupTalk je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-grouptalk"></a>Krok 5. Konfigurace automatického zřizování uživatelů na GroupTalk 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-grouptalk-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro GroupTalk ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **GroupTalk**.

    ![Odkaz GroupTalk v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Automatická karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte adresu URL vašeho tenanta GroupTalk a tajný token, který jste dříve získali z kroku 2. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k GroupTalk. Pokud se připojení nepovede, ujistěte se, že má váš účet GroupTalk oprávnění správce, a zkuste to znovu. Můžete vždycky získat nový tajný token, jak je popsáno v kroku 2.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé GroupTalk**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do GroupTalk v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v GroupTalk pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), budete muset zajistit, aby rozhraní GroupTalk API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |userName|Řetězec|&check;|
   |phoneNumbers[type eq "mobile"].value|Řetězec|&check;|
   |emails[type eq "work"].value|Řetězec|&check;|
   |active|Logická hodnota|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |externalId|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: costCenter|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Organization|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: grouptalk: 2.0: uživatel: Label1|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: grouptalk: 2.0: uživatel: Label2|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: grouptalk: 2.0: uživatel: Label3|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: grouptalk: 2.0: uživatel: Label4|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: grouptalk: 2.0: uživatel: Label5|Řetězec|


10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do GroupTalk**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do GroupTalk v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v GroupTalk pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|Podporováno pro filtrování|
      |---|---|---|
      |displayName|Řetězec|&check;|
      |členy|Referenční informace|
      |externalId|Řetězec|      
      |urn: IETF: parametry: SCIM: schémata: rozšíření: grouptalk: 2.0: název skupiny: Popis|Řetězec|

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro GroupTalk, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro GroupTalk, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).
4. Můžete kontaktovat podporu GroupTalk pro další protokoly zřizování nastavené jako vlastní sestavy v rámci správce GroupTalk. Ty můžou poskytnout další pokyny, proč se uživatelům a skupinám nepodaří správně zřídit.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
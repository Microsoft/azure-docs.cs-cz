---
title: 'Kurz: Konfigurace Bentley – Automatické zřizování uživatelů pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po Bentleyí automatického zřizování uživatelů.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 08778fff-f252-45c2-95d4-cc640c288af3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2021
ms.author: Zhchia
ms.openlocfilehash: 0d5aaee56606abcc886310049ad5a087f2aedb5e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590268"
---
# <a name="tutorial-configure-bentley---automatic-user-provisioning-for-automatic-user-provisioning"></a>Kurz: Konfigurace Bentley – Automatické zřizování uživatelů pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je potřeba provést v Bentley – Automatické zřizování uživatelů a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, aby [Bentley Automatické zřizování uživatelů](https://www.bentley.com) pomocí služby Azure AD Provisioning. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Bentley – Automatické zřizování uživatelů
> * Odebrat uživatele v Bentley – Automatické zřizování uživatelů, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a Bentley – Automatické zřizování uživatelů
> * Zřizování skupin a členství ve skupinách v Bentley – Automatické zřizování uživatelů

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* Federovaný účet s Bentley IMS.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Zjistěte, kdo bude v [rozsahu zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a Bentley – Automatické zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-bentley---automatic-user-provisioning-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Bentley – Automatické zřizování uživatelů pro podporu zřizování s Azure AD

Přihlaste se k Bentley týmu [podpory](https://communities.bentley.com/communities/other_communities/licensing_cloud_and_web_services/w/wiki/52836/microsoft-azure-ad-automatic-user-provisioning-configuration) zřizování uživatelů pro URL tenanta a tajného tokenu. Tyto hodnoty budou zadány na kartě zřizování aplikace Bentley v Azure Portal.

## <a name="step-3-add-bentley---automatic-user-provisioning-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Bentley – Automatické zřizování uživatelů z Galerie aplikací Azure AD

Přidání Bentley – Automatické zřizování uživatelů z Galerie aplikací Azure AD pro zahájení správy zřizování pro Bentley – Automatické zřizování uživatelů. Další informace o přidání aplikace z galerie najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k Bentley automatického zřizování uživatelů musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-bentley---automatic-user-provisioning"></a>Krok 5. Konfigurace automatického zřizování uživatelů na Bentley – Automatické zřizování uživatelů 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-bentley---automatic-user-provisioning-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Bentley – Automatické zřizování uživatelů v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Bentley – Automatické zřizování uživatelů**.

    ![Odkaz na Bentley – Automatické zřizování uživatelů v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Automatická karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte Bentley – Automatická adresa URL tenanta zřizování uživatelů a tajný token. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Bentley – Automatické zřizování uživatelů. Pokud se připojení nepovede, ujistěte se, že váš účet automatického zřizování uživatelů Bentley má oprávnění správce, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatelů a Bentley – Automatické zřizování uživatelů**.

9. Kontrola atributů uživatele synchronizovaných z Azure AD do Bentley – Automatické zřizování uživatelů v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Bentley – Automatické zřizování uživatelů pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), bude nutné zajistit, aby rozhraní API Bentley-Automatic usering API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |userName|Řetězec|&check;|
   |title|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |preferredLanguage|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |adresy [Type EQ "]. streetAddress|Řetězec|
   |adresy [typ EQ "Work"].|Řetězec|
   |adresy [typ EQ "Work"]. region|Řetězec|
   |adresy [typ EQ "Work"]. postalCode|Řetězec|
   |adresy [typ EQ "Work"]. Country|Řetězec|
   |phoneNumbers[type eq "work"].value|Řetězec|
   |externalId|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Bentley: 2.0: uživatel: isSoftDeleted|Řetězec|

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny a Bentley – Automatické zřizování uživatelů**.

11. Zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD, a Bentley – Automatické zřizování uživatelů v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v Bentley – Automatické zřizování uživatelů pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|Podporováno pro filtrování|
      |---|---|---|
      |displayName|Řetězec|&check;|
      |externalId|Řetězec|
      |členy|Referenční informace|
      |urn: IETF: parametry: SCIM: schémata: rozšíření: Bentley: 2.0: název skupiny: Popis|Řetězec|

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu zřizování Azure AD pro Bentley – Automatické zřizování uživatelů, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro Bentley – Automatické zřizování uživatelů výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Omezení konektoru
* Atribut Enterprise Extension "urn: IETF: param: SCIM: schemas: Extension: Enterprise: 2.0: User: Manager" není podporována a bude odebrán.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../manage-apps/check-status-user-account-provisioning.md).

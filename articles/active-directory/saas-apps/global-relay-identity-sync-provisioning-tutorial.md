---
title: 'Kurz: Konfigurace globální synchronizace přenosového identity pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po globální synchronizaci identity Relay.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 0c4a3bf0-d0a6-4eab-909b-6cf9f9234e4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2020
ms.author: Zhchia
ms.openlocfilehash: 400e82780abd08e0db4f49d72b352e290ea1f212
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900293"
---
# <a name="tutorial-configure-global-relay-identity-sync-for-automatic-user-provisioning"></a>Kurz: Konfigurace globální synchronizace identity Relay pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v globální synchronizaci identity a Azure Active Directory (Azure AD) pro konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a zruší pravidla pro uživatele a skupiny pro globální synchronizaci identity Relay pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytvoření uživatelů v globální synchronizaci identity Relay
> * Odebrat uživatele v globální synchronizaci identity Relay, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a globální synchronizací identity Relay
> * Zřizování skupin a členství ve skupinách v globální synchronizaci identity Relay

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce).

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Zjistěte, kdo bude v [rozsahu zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a synchronizovat globální přenos identity](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-global-relay-identity-sync-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace globální synchronizace přenosového identity pro zajištění podpory zřizování s Azure AD

Pokud chcete získat adresu URL tenanta, obraťte se na svého zástupce globální Relay pro synchronizaci identity. Tato hodnota se zadá do pole **Adresa URL tenanta** na kartě zřizování vaší aplikace globální synchronizace Relay Sync ve Azure Portal.

## <a name="step-3-add-global-relay-identity-sync-from-the-azure-ad-application-gallery"></a>Krok 3. Přidat globální synchronizaci identity Relay z Galerie aplikací Azure AD

Přidejte globální synchronizaci identity Relay z Galerie aplikací Azure AD a začněte spravovat zřizování pro globální synchronizaci identity Relay. Další informace o přidání aplikace z Galerie [najdete tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-global-relay-identity-sync"></a>Krok 5. Konfigurace automatického zřizování uživatelů na globální synchronizaci identity Relay 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v globální aplikaci pro synchronizaci identity Relay na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-global-relay-identity-sync-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro synchronizaci globální identity Relay v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace** .

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **globální synchronizace přenosového identity** .

    ![Odkaz globální přenos identity na synchronizaci v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování** .

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky** .

    ![Automatická karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL klienta** globální Relay pro synchronizaci identity. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k globální synchronizaci identity Relay. Pokud se připojení nepovede, zajistěte, aby váš globální účet pro synchronizaci identity Relay měl oprávnění správce, a pokud chcete tento problém vyřešit, kontaktujte svého globálního zástupce.

    ![Tlačítko autorizace](media/global-relay-identity-sync-provisioning-tutorial/authorization.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem** .

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit** .

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatele s globální synchronizací identity Relay** .

9. Zkontrolujte atributy uživatele synchronizované z Azure AD až po globální synchronizaci identity Relay v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v globální synchronizaci identity Relay pro operace aktualizace. Pokud se rozhodnete, že chcete změnit [odpovídající atribut cíle](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), je nutné zajistit, aby rozhraní API pro synchronizaci globálních identit identity Relay podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |active|Logická hodnota|
   |displayName|Řetězec|
   |title|Řetězec|
   |preferredLanguage|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |název. formátovaný|Řetězec|
   |adresy [Type EQ "Work"]. formátovaný|Řetězec|
   |adresy [Type EQ "]. streetAddress|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |adresy [typ EQ "Work"].|Řetězec|
   |adresy [typ EQ "Work"]. region|Řetězec|
   |adresy [typ EQ "Work"]. postalCode|Řetězec|
   |adresy [typ EQ "Work"]. Country|Řetězec|
   |adresy [Type EQ "ostatní"]. formátovaný|Řetězec|
   |phoneNumbers[type eq "work"].value|Řetězec|
   |phoneNumbers[type eq "mobile"].value|Řetězec|
   |phoneNumbers [typ EQ "fax"]. hodnota|Řetězec|
   |externalId|Řetězec|
   |name.honorificPrefix|Řetězec|
   |název. honorificSuffix|Řetězec|
   |nickName|Řetězec|
   |userType|Řetězec|
   |locale|Řetězec|
   |timezone|Řetězec|
   |e-maily [typ EQ "domů"]. hodnota|Řetězec|
   |e-maily [typ EQ "other"]. hodnota|Řetězec|
   |phoneNumbers [typ EQ "domů"]. hodnota|Řetězec|
   |phoneNumbers [typ EQ "other"]. hodnota|Řetězec|
   |phoneNumbers [typ EQ "pager"]. Value|Řetězec|
   |adresy [Type EQ "domů"]. streetAddress|Řetězec|
   |adresy [Type EQ "Home"].|Řetězec|
   |adresy [Type EQ "domů"]. region|Řetězec|
   |adresy [Type EQ "domů"]. postalCode|Řetězec|
   |adresy [typ EQ "domů"]. Country|Řetězec|
   |adresy [Type EQ "Home"]. formátovaný|Řetězec|
   |adresy [Type EQ "other"]. streetAddress|Řetězec|
   |adresy [Type EQ "other"].|Řetězec|
   |adresy [Type EQ "other"]. region|Řetězec|
   |adresy [Type EQ "other"]. postalCode|Řetězec|
   |adresy [Type EQ "other"]. Country|Řetězec|
   |role [Primary EQ "true"]. Display|Řetězec|
   |role [Primary EQ "true"]. Type|Řetězec|
   |roles[primary eq "True"].value|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: employeeNumber|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: costCenter|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Organization|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: divize|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Referenční informace|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: GlobalRelay: 2.0: uživatel: proxyAddresses|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: GlobalRelay: 2.0: uživatel: extensionAttribute1|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: GlobalRelay: 2.0: uživatel: extensionAttribute2|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: GlobalRelay: 2.0: uživatel: extensionAttribute3|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: GlobalRelay: 2.0: uživatel: extensionAttribute4|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: GlobalRelay: 2.0: uživatel: extensionAttribute5|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: GlobalRelay: 2.0: uživatel: extensionAttribute6|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: GlobalRelay: 2.0: uživatel: extensionAttribute7|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: GlobalRelay: 2.0: uživatel: extensionAttribute8|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: GlobalRelay: 2.0: uživatel: extensionAttribute9|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: GlobalRelay: 2.0: uživatel: extensionAttribute10|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: GlobalRelay: 2.0: uživatel: extensionAttribute11|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: GlobalRelay: 2.0: uživatel: extensionAttribute12|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: GlobalRelay: 2.0: uživatel: extensionAttribute13|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: GlobalRelay: 2.0: uživatel: extensionAttribute14|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: GlobalRelay: 2.0: uživatel: extensionAttribute15|Řetězec|



10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny s globální synchronizací identity Relay** .

11. Zkontrolujte atributy skupiny synchronizované z Azure AD až po globální synchronizaci identity Relay v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v globální synchronizaci identity Relay pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |členy|Referenční informace|

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu zřizování Azure AD pro globální synchronizaci identity Relay, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Určete uživatele nebo skupiny, které chcete zřídit pro globální synchronizaci identity Relay, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **rozsahu** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit** .

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení** . Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../manage-apps/check-status-user-account-provisioning.md).

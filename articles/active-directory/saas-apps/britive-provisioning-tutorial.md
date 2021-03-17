---
title: 'Kurz: Konfigurace Britive pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po Britive.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 622688b3-9d20-482e-aab9-ce2a1f01e747
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2021
ms.author: Zhchia
ms.openlocfilehash: 8bebcb49bc7bf31614a161c08d33d5910679b614
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225709"
---
# <a name="tutorial-configure-britive-for-automatic-user-provisioning"></a>Kurz: Konfigurace Britive pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v Britive i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, které se [Britive](https://www.britive.com/) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Britive
> * Odebrat uživatele v Britive, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a Britive
> * Zřizování skupin a členství ve skupinách v Britive
> * [Jednotné přihlašování](britive-tutorial.md) k Britive (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uživatelský účet ve službě Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* Tenant [Britive](https://www.britive.com/)
* Uživatelský účet v Britive s oprávněními správce.


## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
1. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Určete, jaká data se mají [mapovat mezi Azure AD a Britive](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-britive-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Britive pro podporu zřizování pomocí Azure AD

Aplikace bude nutné ručně nakonfigurovat pomocí níže uvedených kroků:
1. Přihlášení k aplikaci Britive s oprávněními správce
1. Klikněte na **správce – >Správa uživatelů – >zprostředkovatelé identity**
1. Klikněte na **Přidat zprostředkovatele identity**. Zadejte název a popis. Klikněte na tlačítko Přidat poskytovatele identity.

    ![Zprostředkovatel identity](media/britive-provisioning-tutorial/identity.png)

1. Zobrazí se stránka konfigurace podobná jedné.

    ![Konfigurační stránka](media/britive-provisioning-tutorial/configuration.png)

1. Klikněte na kartu **SCIM** . Změňte poskytovatele SCIM z obecného na Azure a uložte změny. Zkopírujte adresu URL SCIM a poznamenejte si ji. Tyto hodnoty se zadávají do polí **URL klienta** na kartě zřizování aplikace Britive ve Azure Portal.

    ![Stránka SCIM](media/britive-provisioning-tutorial/scim.png)

1. Klikněte na **vytvořit token**. Podle potřeby vyberte platnost tokenu a klikněte na tlačítko vytvořit token.

    ![Vytvořit token](media/britive-provisioning-tutorial/create-token.png)

1. Zkopírujte vygenerovaný token a poznamenejte si ho. Klikněte na tlačítko OK. Upozorňujeme, že uživatel nebude moct token znovu zobrazit. V případě potřeby klikněte na tlačítko Re-Create a vygenerujte nový token. Tyto hodnoty se zadávají do polí **tajný token** a adresa URL tenanta na kartě zřizování aplikace getabstract v Azure Portal.

    ![Kopírovat token](media/britive-provisioning-tutorial/copy-token.png) 


## <a name="step-3-add-britive-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Britive z Galerie aplikací Azure AD

Přidejte Britive z Galerie aplikací Azure AD a začněte spravovat zřizování pro Britive. Pokud jste dříve nastavili Britive pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin k Britive je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-britive"></a>Krok 5. Konfigurace automatického zřizování uživatelů na Britive 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Britive na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-britive-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Britive ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikace vyberte **Britive**.

    ![Odkaz Britive v seznamu aplikací](common/all-applications.png)

1. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

1. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Automatická karta zřizování](common/provisioning-automatic.png)

1. V části **přihlašovací údaje správce** zadejte adresu URL vašeho tenanta Britive a tajný token. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Britive. Pokud se připojení nepovede, ujistěte se, že má váš účet Britive oprávnění správce, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

1. Vyberte **Uložit**.

1. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Britive**.

1. Zkontrolujte atributy uživatele synchronizované z Azure AD do Britive v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Britive pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), budete muset zajistit, aby rozhraní Britive API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |userName|Řetězec|&check;
   |active|Logická hodnota|
   |displayName|Řetězec|
   |title|Řetězec|
   |externalId|Řetězec|
   |preferredLanguage|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |nickName|Řetězec|
   |userType|Řetězec|
   |locale|Řetězec|
   |timezone|Řetězec|
   |e-maily [typ EQ "domů"]. hodnota|Řetězec|
   |e-maily [typ EQ "other"]. hodnota|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |phoneNumbers [typ EQ "domů"]. hodnota|Řetězec|
   |phoneNumbers [typ EQ "other"]. hodnota|Řetězec|
   |phoneNumbers [typ EQ "pager"]. Value|Řetězec|
   |phoneNumbers[type eq "work"].value|Řetězec|
   |phoneNumbers[type eq "mobile"].value|Řetězec|
   |phoneNumbers [typ EQ "fax"]. hodnota|Řetězec|
   |adresy [Type EQ "Work"]. formátovaný|Řetězec|
   |adresy [Type EQ "]. streetAddress|Řetězec|
   |adresy [typ EQ "Work"].|Řetězec|
   |adresy [typ EQ "Work"]. region|Řetězec|
   |adresy [typ EQ "Work"]. postalCode|Řetězec|
   |adresy [typ EQ "Work"]. Country|Řetězec|
   |adresy [Type EQ "Home"]. formátovaný|Řetězec|
   |adresy [Type EQ "domů"]. streetAddress|Řetězec|
   |adresy [Type EQ "Home"].|Řetězec|
   |adresy [Type EQ "domů"]. region|Řetězec|
   |adresy [Type EQ "domů"]. postalCode|Řetězec|
   |adresy [typ EQ "domů"]. Country|Řetězec|
   |adresy [Type EQ "ostatní"]. formátovaný|Řetězec|
   |adresy [Type EQ "other"]. streetAddress|Řetězec|
   |adresy [Type EQ "other"].|Řetězec|
   |adresy [Type EQ "other"]. region|Řetězec|
   |adresy [Type EQ "other"]. postalCode|Řetězec|
   |adresy [Type EQ "other"]. Country|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: employeeNumber|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: costCenter|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Organization|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: divize|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Reference|


1. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do Britive**.

1. Zkontrolujte atributy skupiny synchronizované z Azure AD do Britive v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v Britive pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|Podporováno pro filtrování|
      |---|---|---|
      |displayName|Řetězec|&check;
      |externalId|Řetězec|
      |členy|Referenční informace|

1. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pokud chcete povolit službu Azure AD Provisioning pro Britive, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

1. Definujte uživatele nebo skupiny, které chcete zřídit pro Britive, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

1. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

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
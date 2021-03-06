---
title: 'Kurz: Konfigurace AlertMedia pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po AlertMedia.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: a5df0dd7-05a3-4744-9d51-ec33e89a934f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: Zhchia
ms.openlocfilehash: b15b20ee8e643dc260578d17913810aaf68cc929
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181087"
---
# <a name="tutorial-configure-alertmedia-for-automatic-user-provisioning"></a>Kurz: Konfigurace AlertMedia pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v AlertMedia i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, které se [AlertMedia](https://www.alertmedia.com/) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v AlertMedia
> * Odebrat uživatele v AlertMedia, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a AlertMedia
> * Zřizování skupin a členství ve skupinách v AlertMedia
> * [Jednotné přihlašování](./alertmedia-tutorial.md) k AlertMedia (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant služby Azure AD](../develop/quickstart-create-new-tenant.md).
* Uživatelský účet ve službě Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* [Tenant AlertMedia](https://dashboard.alertmedia.com/#/login)
* Uživatelský účet v AlertMedia s oprávněními správce ke konfiguraci integrace rozhraní API.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a AlertMedia](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-alertmedia-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace AlertMedia pro podporu zřizování pomocí Azure AD

1. Přihlaste se k účtu AlertMedia. Přejděte na **> rozhraní API společnosti**.
2. Klikněte na **Přidat nový**.
3. Vyberte, pokud chcete, aby **integrace rozhraní API** poskytovala název, který vám umožní snadno rozpoznat, kde se klíče používají.
4. Vyberte správce, se kterým chcete integraci přidružit.
5. Klikněte na tlačítko **Generovat klíče** a **Uložit** .
6. Zkopírujte **token klienta** z integrace a uložte ho. Tato možnost se používá jako **tajný token** na kartě zřizování aplikace AlertMedia v Azure Portal.


## <a name="step-3-add-alertmedia-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání AlertMedia z Galerie aplikací Azure AD

Přidejte AlertMedia z Galerie aplikací Azure AD a začněte spravovat zřizování pro AlertMedia. Pokud jste již dříve AlertMedia nastavení pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin k AlertMedia je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-alertmedia"></a>Krok 5. Konfigurace automatického zřizování uživatelů na AlertMedia 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-alertmedia-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro AlertMedia ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **AlertMedia**.

    ![Odkaz AlertMedia v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Automatická karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta** AlertMedia jako jednu z následujících možností.
      * (žádná vlastní doména) `https://dashboard.alertmedia.com/api/scim/v3`

      * (vlastní doména) `https://subdomain.alertmedia.com/api/scim/v3`

      Zadejte **tajný token** , který jste dříve získali v kroku 2. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k AlertMedia. Pokud se připojení nepovede, ujistěte se, že má váš účet AlertMedia oprávnění správce, a zkuste to znovu.

      ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé AlertMedia**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do AlertMedia v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v AlertMedia pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), budete muset zajistit, aby rozhraní AlertMedia API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |active|Logická hodnota|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: first_name|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: last_name|Řetězec|
   |urn: IETF: SCIM: schemas: přípona: alertmedia: 2.0: NaAtribut CustomAttribute: User: email|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: email2|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: email3|Řetězec|
   |urn: IETF: parametry: SCIM: schemas: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: title|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: mobile_phone|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: mobile_phone_post_dial|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: mobile_phone2|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: mobile_phone2_post_dial|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: mobile_phone3|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: mobile_phone3_post_dial|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: home_phone|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: home_phone_post_dial|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: office_phone|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: office_phone_post_dial|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: nacustomattribute: User: Address|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: Address2|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: City|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: State|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: Country|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: PSČ|Řetězec|
   |urn: IETF: parametry: SCIM: schemas: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: poznámky|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: customer_user_id|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: alertmedia: 2.0: Atribut CustomAttribute: User: user_type|Řetězec|

10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do AlertMedia**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do AlertMedia v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v AlertMedia pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |členy|Referenční informace|

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro AlertMedia, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro AlertMediaAlertMedia, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
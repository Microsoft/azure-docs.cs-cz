---
title: 'Kurz: Konfigurace PrinterLogic SaaS pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po PrinterLogic SaaS.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 001cfccf-b8a4-46e6-b355-94e8b694b122
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2020
ms.author: Zhchia
ms.openlocfilehash: 6b00067e7f13e609c2999d6949429038e9a7dfd8
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181743"
---
# <a name="tutorial-configure-printerlogic-saas-for-automatic-user-provisioning"></a>Kurz: Konfigurace PrinterLogic SaaS pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v PrinterLogic SaaS a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, aby [PrinterLogic SaaS](https://www.printerlogic.com/) pomocí služby Azure AD Provisioning. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v PrinterLogic SaaS
> * Odebrat uživatele v PrinterLogic SaaS, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a PrinterLogic SaaS
> * Zřizování skupin a členství ve skupinách v PrinterLogic SaaS
> * [Jednotné přihlašování](./printerlogic-saas-tutorial.md) k PrinterLogic SaaS (doporučeno)

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uživatelský účet ve službě Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* Tenant [PrinterLogic SaaS](https://www.printerlogic.com/) .

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a PrinterLogic SaaS](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-printerlogic-saas-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace PrinterLogic SaaS pro podporu zřizování s Azure AD

1. V PrinterLogic přejděte na **nástroje > nastavení > obecné**.

2. Přejděte do části **nastavení zprostředkovatele identity** .

3. Klikněte na možnost **SCIM** .

4. Ujistěte se, že je v rozevírací nabídce vybraná možnost **Azure AD** .

5. Klikněte na **vygenerovat SCIM token**.

      ![Token SCIM](media/printer-logic-saas-provisioning-tutorial/token.png)

6. Zkopírujte a uložte **token nosiče**. Tato hodnota se zadá do pole **token tajného** kódu na kartě zřizování vaší aplikace PrinterLogic SaaS v Azure Portal.

7. Do https://gw.app.printercloud.com/{instance_name}/scim/v2 pole **Adresa URL tenanta** zadejte na kartě zřizování aplikace PrinterLogic SaaS v Azure Portal.

## <a name="step-3-add-printerlogic-saas-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání PrinterLogic SaaS z Galerie aplikací Azure AD

Přidejte PrinterLogic SaaS z Galerie aplikací Azure AD, abyste mohli začít spravovat zřizování pro PrinterLogic SaaS. Pokud jste již dříve nastavili PrinterLogic SaaS pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin k PrinterLogic SaaS je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-printerlogic-saas"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro PrinterLogic SaaS 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-printerlogic-saas-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro PrinterLogic SaaS ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **PrinterLogic SaaS**.

    ![Odkaz PrinterLogic SaaS v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Automatická karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte adresu URL tenanta PrinterLogic SaaS a tajný token načtený z kroku 2. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k PrinterLogic SaaS. Pokud se připojení nepovede, ujistěte se, že váš účet PrinterLogic SaaS má oprávnění správce, a zkuste to znovu.

    ![Token Azure AD](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele a PrinterLogic SaaS**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do PrinterLogic SaaS v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v PrinterLogic SaaS pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), budete muset zajistit, aby rozhraní PrinterLogic SaaS API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |userName|Řetězec|&check;|
   |active|Logická hodnota|
   |title|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |externalId|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: printercloud: 2.0: uživatel: authPin|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: printercloud: 2.0: uživatel: authPinUser|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: printercloud: 2.0: uživatel: badgeId|Řetězec|

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do PrinterLogic SaaS**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do PrinterLogic SaaS v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v PrinterLogic SaaS pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|Podporováno pro filtrování|
      |---|---|---|
      |displayName|Řetězec|&check;|
      |externalId|Řetězec|
      |členy|Referenční informace|

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro PrinterLogic SaaS, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Určete uživatele nebo skupiny, které chcete zřídit pro PrinterLogic SaaS, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

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
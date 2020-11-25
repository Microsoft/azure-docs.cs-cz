---
title: 'Kurz: Konfigurace Smartsheet pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Smartsheet.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 79fa480e0cca590446a0251f43c45b2e04c97cd5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013152"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Kurz: Konfigurace Smartsheet pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Smartsheet a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro [Smartsheet](https://www.smartsheet.com/pricing). Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Smartsheet
> * Odebrat uživatele v Smartsheet, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a Smartsheet
> * Jednotné přihlašování k Smartsheet (doporučeno)

> [!NOTE]
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenanta Azure AD](../develop/quickstart-create-new-tenant.md)
* Uživatelský účet v Azure AD s [oprávněním](../users-groups-roles/directory-assign-admin-roles.md) ke konfiguraci zřizování (např. správce aplikací, správce cloudových aplikací, vlastník aplikací nebo globální správce)
* [Tenant Smartsheet](https://www.smartsheet.com/pricing)
* Uživatelský účet v plánu Smartsheet Enterprise nebo Enterprise Premier s oprávněními správce systému.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a Smartsheet](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Smartsheet pro podporu zřizování pomocí Azure AD

Před konfigurací Smartsheet pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na Smartsheet.

1. Přihlaste se jako **správce** systému na **[portálu Smartsheet](https://app.smartsheet.com/b/home)** a přejděte na **správce účtu**.

    ![Správce účtu Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Přejít na **ovládací prvky zabezpečení > Automatické zřizování uživatelů > upravit**.

    ![Ovládací prvky zabezpečení Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Přidejte a ověřte e-mailové domény pro uživatele, které chcete zřídit z Azure AD do Smartsheet. Pokud chcete zajistit, aby všechny akce zřizování pocházely jenom z Azure AD, a taky zajistit, aby byl váš seznam uživatelů Smartsheet synchronizovaný s přiřazeními Azure AD, vyberte **Nepovoleno** .

    ![Zřizování uživatelů Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Po dokončení ověření bude nutné doménu aktivovat. 

    ![Smartsheet aktivovat doménu](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Vygenerujte **tajný token** potřebný ke konfiguraci automatického zřizování uživatelů pomocí Azure AD tak, že přejdete na **aplikace a integrace**.

    ![Snímek obrazovky stránky pro správu Smartsheet s možností User avatar a Apps & Integration s názvem.](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Vyberte možnost **přístup přes rozhraní API**. Klikněte na **vygenerovat nový přístupový token**.

    ![Snímek obrazovky s dialogem osobní nastavení s rozhraním API přístup a vygenerujte nové možnosti přístupového tokenu, které jsou vyvolány.](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Zadejte název přístupového tokenu rozhraní API. Klikněte na **OK**.

    ![Snímek obrazovky s krokem 1 z 2: vygenerujte přístupový token rozhraní API s možností OK s názvem.](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Zkopírujte přístupový token rozhraní API a uložte ho tak, jak se to bude zobrazovat jenom v takovém případě. To se vyžaduje v poli **tajný token** v Azure AD.

    ![Token Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Smartsheet z Galerie aplikací Azure AD

Přidejte Smartsheet z Galerie aplikací Azure AD a začněte spravovat zřizování pro Smartsheet. Pokud jste dříve nastavili Smartsheet pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin k Smartsheet je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Aby se zajistila parita v přiřazování rolí uživatelů mezi Smartsheet a Azure AD, doporučuje se využít stejné přiřazení rolí, které jsou naplněné v seznamu úplných uživatelů Smartsheet. Pokud chcete načíst tento seznam uživatelů z Smartsheet, přejděte na **správce účtu > Správa uživatelů > další akce > stáhnout seznam uživatelů (CSV)**.

* Pro přístup k určitým funkcím aplikace Smartsheet vyžaduje, aby uživatel měl více rolí. Další informace o uživatelských typech a oprávněních v Smartsheet najdete v [uživatelském typu a oprávnění](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Pokud má uživatel v Smartsheet přiřazených víc rolí, **musíte** zajistit, aby se tato přiřazení rolí replikoval ve službě Azure AD, aby nedocházelo k situaci, kdy uživatelé můžou trvale ztratit přístup k objektům Smartsheet. Každá jedinečná role v Smartsheet **musí** být přiřazena jiné skupině v Azure AD. Pak je **nutné** přidat uživatele do všech skupin odpovídajících rolím, které jsou požadovány. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>Krok 5. Konfigurace automatického zřizování uživatelů na Smartsheet 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Smartsheet na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Smartsheet ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Smartsheet**.

    ![Odkaz Smartsheet v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **základní adresu URL SCIM 2,0 a hodnoty přístupového tokenu** , které jste dříve získali z Smartsheet v **adrese URL tenanta** a **tajném tokenu** , v uvedeném pořadí. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Smartsheet. Pokud se připojení nepovede, ujistěte se, že váš účet Smartsheet má oprávnění správce systému, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Smartsheet**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Smartsheet v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Smartsheet pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |active|Logická hodnota|
   |title|Řetězec|
   |userName|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |phoneNumbers[type eq "work"].value|Řetězec|
   |phoneNumbers[type eq "mobile"].value|Řetězec|
   |phoneNumbers [typ EQ "fax"]. hodnota|Řetězec|
   |externalId|Řetězec|
   |role [Primary EQ "true"]. Display|Řetězec|
   |role [Primary EQ "true"]. Type|Řetězec|
   |roles[primary eq "True"].value|Řetězec|
   |Role|Řetězec|
   urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: divize|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: costCenter|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Řetězec|


10. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro Smartsheet, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro Smartsheet, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Omezení konektoru

* Smartsheet nepodporuje obnovitelné odstranění. Když je **aktivní** atribut uživatele nastaven na hodnotu false, Smartsheet odstraní uživatele trvale.

## <a name="change-log"></a>Protokol změn

* 06/16/2020 – Přidali jsme podporu pro atributy podnikového rozšíření "cost Center", "divize", "manažer" a "oddělení" pro uživatele.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
---
title: 'Kurz: Konfigurace konzoly Oracle Cloud Infrastructure pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřizovat a zrušit zřízení uživatelských účtů z Azure AD až po konzolu cloudové infrastruktury Oracle.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 94de0ca0a5393c891e567e558cbbadd0ca1f453b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97832092"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Kurz: Konfigurace konzoly Oracle Cloud Infrastructure pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v konzole cloudové infrastruktury Oracle, a Azure Active Directory (Azure AD) pro konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny do [konzoly pro cloudovou infrastrukturu Oracle](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytvoření uživatelů v konzole cloudové infrastruktury Oracle
> * Odebrat uživatele v konzole pro cloudovou infrastrukturu Oracle, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a konzolou cloudové infrastruktury Oracle
> * Zřizování skupin a členství ve skupinách v konzole cloudové infrastruktury Oracle
> * [Jednotné přihlašování](./oracle-cloud-tutorial.md) do nástroje Oracle Cloud Infrastructure Console (doporučeno)

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uživatelský účet v Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (např. správce aplikací, správce cloudových aplikací, vlastník aplikací nebo globální správce) 
* [Tenant](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)konzoly cloudové infrastruktury Oracle.
* Uživatelský účet v konzole cloudová infrastruktura Oracle s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a konzolou cloudové infrastruktury Oracle](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace konzoly cloudové infrastruktury Oracle pro podporu zřizování s Azure AD

1. Přihlaste se na portál pro správu konzoly cloudové infrastruktury Oracle. V levém horním rohu obrazovky přejděte na **identita > federace**.

    ![Správce Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Klikněte na adresu URL zobrazenou na stránce vedle položky Oracle identity Cloud Service Console.

    ![Adresa URL pro Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Pokud chcete vytvořit nového zprostředkovatele identity, klikněte na **Přidat zprostředkovatele identity** . Uložte ID IdP, které se má použít jako součást adresy URL klienta. Klikněte na ikonu plus vedle karty **aplikace** a vytvořte klienta OAuth a udělte IDCS správce domény AppRole.

    ![Ikona cloudu Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Pro konfiguraci aplikace postupujte podle následujících snímků obrazovky. Po dokončení konfigurace klikněte na **Uložit**.

    ![Konfigurace Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Zásady vystavování tokenů Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Na kartě Konfigurace aplikace rozbalte možnost **Obecné informace** a načtěte ID klienta a tajný klíč klienta.

    ![Generování tokenů Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Pokud chcete vygenerovat kódování Base64 tokenu tajného klíče, ID klienta a tajný klíč klienta ve formátu **ID klienta: tajný klíč klienta**. Uložte tajný token. Tato hodnota se zadá do pole **token tajného klíče** na kartě zřizování vaší aplikace konzoly cloudové infrastruktury Oracle v Azure Portal.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání konzoly cloudové infrastruktury Oracle z Galerie aplikací Azure AD

Přidejte konzolu cloudové infrastruktury Oracle z Galerie aplikací Azure AD a začněte spravovat zřizování v konzole pro cloudovou infrastrukturu Oracle. Pokud jste dříve nastavili konzolu cloudové infrastruktury Oracle pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin ke konzole cloudové infrastruktury Oracle je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Krok 5. Konfigurace automatického zřizování uživatelů v konzole cloudové infrastruktury Oracle 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro konzolu cloudové infrastruktury Oracle ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Oracle Cloud infrastruktura konzoly**.

    ![Odkaz na konzolu cloudové infrastruktury Oracle v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta** ve formátu `https://<IdP ID>.identity.oraclecloud.com/admin/v1` . Příklad: `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Zadejte hodnotu tajného tokenu získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit ke konzole cloudové infrastruktury Oracle. Pokud se připojení nepovede, zajistěte, aby měl účet konzoly pro cloudovou infrastrukturu Oracle oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky se zobrazí v dialogovém okně přihlašovací údaje správce, kde můžete zadat svého tenanta U R L a tajného tokenu.](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele s konzolou infrastruktura cloudové infrastruktury Oracle**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Oracle Cloud Infrastructure Console v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v konzole cloudové infrastruktury Oracle pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), budete muset zajistit, aby rozhraní API konzoly pro cloudovou infrastrukturu Oracle podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

    |Atribut|Typ|
    |---|---|
    |displayName|Řetězec|
    |userName|Řetězec|
    |active|Logická hodnota|
    |title|Řetězec|
    |emails[type eq "work"].value|Řetězec|
    |preferredLanguage|Řetězec|
    |name.givenName|Řetězec|
    |name.familyName|Řetězec|
    |adresy [Type EQ "Work"]. formátovaný|Řetězec|
    |adresy [typ EQ "Work"].|Řetězec|
    |adresy [typ EQ "Work"]. region|Řetězec|
    |adresy [typ EQ "Work"]. postalCode|Řetězec|
    |adresy [typ EQ "Work"]. Country|Řetězec|
    |adresy [Type EQ "]. streetAddress|Řetězec|
    |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: employeeNumber|Řetězec|
    |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec|
    |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: costCenter|Řetězec|
    |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: divize|Řetězec|
    |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Reference|
    |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Organization|Řetězec|
    |urn: IETF: parametry: SCIM: schémata: Oracle: IDCs: rozšíření: uživatel: uživatel: bypassNotification|Logická hodnota|
    |urn: IETF: parametry: SCIM: schémata: Oracle: IDCs: rozšíření: uživatel: uživatel: isFederatedUser|Logická hodnota|

10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do Oracle Cloud infrastruktura konzoly**.

11. Zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD do Oracle Cloud Infrastructure Console v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v konzole cloudové infrastruktury Oracle pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    | Atribut | Typ |
    |--|--|
    | displayName | Řetězec |
    | externalId | Řetězec |
    | členy | Referenční informace |

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu zřizování Azure AD pro konzolu cloudové infrastruktury Oracle, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Určete uživatele nebo skupiny, které chcete zřídit v konzole pro cloudovou infrastrukturu Oracle, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

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

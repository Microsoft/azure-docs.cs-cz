---
title: 'Kurz: Konfigurace konzoly Oracle Cloud Infrastructure pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřizovat a zrušit zřízení uživatelských účtů z Azure AD až po konzolu cloudové infrastruktury Oracle.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e22c8746-7638-4a0f-ae08-7db0c477cf52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 5aa33529a1957b6e7728b3a87bacf6bb91d987ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81378948"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Kurz: Konfigurace konzoly Oracle Cloud Infrastructure pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v konzole cloudové infrastruktury Oracle, a Azure Active Directory (Azure AD) pro konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny do [konzoly pro cloudovou infrastrukturu Oracle](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) pomocí služby zřizování Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytvoření uživatelů v konzole cloudové infrastruktury Oracle
> * Odebrat uživatele v konzole pro cloudovou infrastrukturu Oracle, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a konzolou cloudové infrastruktury Oracle
> * Zřizování skupin a členství ve skupinách v konzole cloudové infrastruktury Oracle
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) do nástroje Oracle Cloud Infrastructure Console (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* [Tenant](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)řízení infrastruktury cloudu Oracle.
* Uživatelský účet v rámci řízení infrastruktury cloudu Oracle s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si [, jak služba zřizování funguje](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a konzolou cloudové infrastruktury Oracle](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

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

Přidejte konzolu cloudové infrastruktury Oracle z Galerie aplikací Azure AD a začněte spravovat zřizování v konzole pro cloudovou infrastrukturu Oracle. Pokud jste dříve nastavili konzolu cloudové infrastruktury Oracle pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z Galerie [najdete tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování. 

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení do aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů a skupin k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oboru, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin ke konzole cloudové infrastruktury Oracle je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte malým. Než se pustíte do všech uživatelů, testujte je s malou sadou uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastavený na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Krok 5. Konfigurace automatického zřizování uživatelů v konzole cloudové infrastruktury Oracle 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro konzolu cloudové infrastruktury Oracle ve službě Azure AD:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Oracle Cloud infrastruktura konzoly**.

    ![Odkaz na konzolu cloudové infrastruktury Oracle v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta** ve formátu `https://<IdP ID>.identity.oraclecloud.com/admin/v1` . Například `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Zadejte hodnotu tajného tokenu získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit ke konzole cloudové infrastruktury Oracle. Pokud se připojení nepovede, zajistěte, aby měl účet konzoly pro cloudovou infrastrukturu Oracle oprávnění správce, a zkuste to znovu.

    ![zřizování](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele s konzolou infrastruktura cloudové infrastruktury Oracle**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Oracle Cloud Infrastructure Console v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v konzole cloudové infrastruktury Oracle pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní API konzoly pro cloudovou infrastrukturu Oracle podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |userName|Řetězec|
      |aktivně|Logická hodnota|
      |title|Řetězec|
      |e-maily [typ EQ "Work"]. Value|Řetězec|
      |preferredLanguage|Řetězec|
      |název. křestní jméno|Řetězec|
      |název. rodina|Řetězec|
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
      |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Referenční informace|
      |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Organization|Řetězec|
      |urn: IETF: parametry: SCIM: schémata: Oracle: IDCs: rozšíření: uživatel: uživatel: bypassNotification|Logická hodnota|
      |urn: IETF: parametry: SCIM: schémata: Oracle: IDCs: rozšíření: uživatel: uživatel: isFederatedUser|Logická hodnota|

10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do Oracle Cloud infrastruktura konzoly**.

11. Zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD do Oracle Cloud Infrastructure Console v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v konzole cloudové infrastruktury Oracle pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |externalId|Řetězec|
      |členy|Referenční informace|

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu zřizování Azure AD pro konzolu cloudové infrastruktury Oracle, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

14. Určete uživatele nebo skupiny, které chcete zřídit v konzole pro cloudovou infrastrukturu Oracle, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení** . Počáteční cyklus trvá déle než u dalších cyklů, ke kterým dojde přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Jakmile nakonfigurujete zřizování, použijte k monitorování nasazení tyto prostředky:

* Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně nastavili.
* Podívejte se na [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
* Pokud se zdá, že konfigurace zřizování je ve stavu není v pořádku, bude aplikace přejít do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)

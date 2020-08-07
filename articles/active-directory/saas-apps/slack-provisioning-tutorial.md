---
title: 'Kurz: zřizování uživatelů pro časovou rezervu – Azure AD'
description: Naučte se, jak nakonfigurovat Azure Active Directory pro Automatické zřizování a rušení uživatelských účtů pro časovou rezervu.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2020
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aa97595f9a6ab2a866a8c8ebccde7e53854dbd1
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87924542"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Kurz: Konfigurace časové rezervy pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které potřebujete k tomu, abyste v časové rezervě a Azure AD automaticky zřídili a zrušili zřizování uživatelských účtů z Azure AD až po časovou rezervu. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v časové rezervě
> * Odebrat uživatele v časové rezervě, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a časovou rezervou
> * Zřizování skupin a členství ve skupinách v časové rezervě
> * Časová rezerva [jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial) (doporučeno)


## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující položky:

* [Tenant služby Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce).
* Tenant časové rezervy s [plánem plus](https://aadsyncfabric.slack.com/pricing) nebo lepším povoleným.
* Uživatelský účet v časovém rezervě s oprávněními správce týmu.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si [, jak služba zřizování funguje](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a časová rezerva](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>Krok 2. Přidání časové rezervy z Galerie aplikací Azure AD

Přidejte časovou rezervu z Galerie aplikací Azure AD a začněte spravovat zřizování s časovou rezervou. Pokud jste dříve použili časovou rezervu pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z Galerie [najdete tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>Krok 3. Definujte, kdo bude v oboru pro zřizování. 

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení do aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů a skupin k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oboru, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k časové rezervy je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte malým. Než se pustíte do všech uživatelů, testujte je s malou sadou uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastavený na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>Krok 4: Konfigurace automatického zřizování uživatelů pro časovou rezervu 

V této části se seznámíte s připojením k rozhraní API pro zřizování uživatelských účtů Azure AD s časovou rezervou a konfigurací zřizovací služby k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v časové rezervě na základě přiřazení uživatelů a skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů pro časovou rezervu v Azure AD:

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Časová rezerva**.

    ![Odkaz na časovou rezervu v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** klikněte na **autorizovat**. Tím se otevře dialogové okno autorizace časové rezervy v novém okně prohlížeče.

    ![Autorizace](media/slack-provisioning-tutorial/authorization.png)


6. V novém okně se přihlaste k časové rezervě pomocí účtu správce týmu. v dialogovém okně výsledné autorizace vyberte tým časové rezervy, pro který chcete povolit zřizování, a potom vyberte **autorizovat**. Až se dokončí, vraťte se do Azure Portal a dokončete konfiguraci zřizování.

    ![Dialogové okno autorizace](./media/slack-provisioning-tutorial/slackauthorize.png)

7. V Azure Portal klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k vaší aplikaci časové rezervy. Pokud se připojení nepovede, ujistěte se, že váš účet časové rezervy má oprávnění správce týmu, a zkuste znovu provést krok autorizovat.

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mail s oznámením](common/provisioning-notification-email.png)

9. Vyberte **Uložit**.

10. V části mapování vyberte **synchronizovat Azure Active Directory uživatele s časovou rezervou**.

11. V části **mapování atributů** zkontrolujte atributy uživatele, které se budou synchronizovat ze služby Azure AD, s časovou rezervou. Všimněte si, že atributy vybrané jako **odpovídající** vlastnosti budou použity ke spárování uživatelských účtů v časové rezervě pro operace aktualizace. Kliknutím na tlačítko Uložit potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |aktivně|Logická hodnota|
   |externalId|Řetězec|
   |displayName|Řetězec|
   |název. rodina|Řetězec|
   |název. křestní jméno|Řetězec|
   |title|Řetězec|
   |e-maily [typ EQ "Work"]. Value|Řetězec|
   |userName|Řetězec|
   |Zdívek|Řetězec|
   |adresy [Type EQ "untypeed"]. streetAddress|Řetězec|
   |adresy [Type EQ "untypeed"].|Řetězec|
   |adresy [Type EQ "untypeed"]. region|Řetězec|
   |adresy [Type EQ "untyped"]. postalCode|Řetězec|
   |adresy [Type EQ "untypeed"]. Country|Řetězec|
   |phoneNumbers [Type EQ "mobilní"]. Value|Řetězec|
   |phoneNumbers [typ EQ "Work"]. Value|Řetězec|
   |role [primární EQ "true"]. Value|Řetězec|
   |locale|Řetězec|
   |název. honorificPrefix|Řetězec|
   |fotky [Type EQ "fotka"]. Value|Řetězec|
   |profileUrl|Řetězec|
   |údaj|Řetězec|
   |userType|Řetězec|
   |urn: SCIM: schemas: rozšíření: Enterprise: 1.0. Department|Řetězec|
   |urn: SCIM: schemas: rozšíření: Enterprise: 1.0. Manager|Referenční informace|
   |urn: SCIM: schemas: rozšíření: Enterprise: 1.0. employeeNumber|Řetězec|
   |urn: SCIM: schemas: rozšíření: Enterprise: 1.0. costCenter|Řetězec|
   |urn: SCIM: schemas: rozšíření: Enterprise: 1.0. Organization|Řetězec|
   |urn: SCIM: schemas: rozšíření: Enterprise: 1.0. divize|Řetězec|

12. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny s časovou rezervou**.

13. V části **mapování atributů** zkontrolujte atributy skupin, které se budou synchronizovat z Azure AD, s časovou rezervou. Všimněte si, že atributy vybrané jako **odpovídající** vlastnosti budou použity ke spárování skupin v časové rezervě pro operace aktualizace. Kliknutím na tlačítko Uložit potvrďte změny.

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |členy|Referenční informace|

14. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pokud chcete povolit službu Azure AD Provisioning pro časovou rezervu, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

16. Definujte uživatele nebo skupiny, které chcete zřídit pro časovou rezervu, a to výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

17. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení** . Počáteční cyklus trvá déle než u dalších cyklů, ke kterým dojde přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-5-monitor-your-deployment"></a>Krok 5. Monitorování nasazení
Jakmile nakonfigurujete zřizování, použijte k monitorování nasazení tyto prostředky:

1. Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně nastavili.
2. Podívejte se na [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
3. Pokud se zdá, že konfigurace zřizování je ve stavu není v pořádku, bude aplikace přejít do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Tipy pro řešení problémů

* Při konfiguraci atributu **DisplayName** časové rezervy mějte na paměti následující chování:

  * Hodnoty nejsou zcela jedinečné (například 2 uživatelé můžou mít stejný zobrazovaný název).

  * Podporuje jiné než anglické znaky, mezery a velká písmena. 
  
  * Povolené interpunkce obsahuje tečky, podtržítka, spojovníky, apostrofy, hranaté závorky (např. **([{}])**) a oddělovače (např. **/;**).
  
  * Vlastnost DisplayName nemůže obsahovat znak @. Pokud je k dispozici znak @, můžete v protokolech zřizování najít přeskočenou událost s popisem "AttributeValidationFailed".

  * Aktualizuje se jenom v případě, že jsou tato dvě nastavení nakonfigurovaná na pracovišti nebo organizaci **profilu** pracovní rezervy a **uživatelé nemůžou měnit jeho zobrazované jméno**.

* Atribut **uživatelského jména** časové rezervy musí být kratší než 21 znaků a mít jedinečnou hodnotu.

* Časová rezerva povoluje pouze spárování s atributy **username** a **email**.  
  
* Běžné kódy erorr jsou zdokumentovány v oficiální dokumentaci k časové rezervě.https://api.slack.com/scim#errors

## <a name="change-log"></a>Protokol změn

* 06/16/2020 – atribut DisplayName byl změněn tak, aby se aktualizoval pouze při vytváření nového uživatele.

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)
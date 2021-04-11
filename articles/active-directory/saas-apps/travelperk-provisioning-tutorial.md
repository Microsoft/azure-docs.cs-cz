---
title: 'Kurz: Konfigurace TravelPerk pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po TravelPerk.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 3e40f87d-8624-4b14-b098-80ff916103c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2021
ms.author: Zhchia
ms.openlocfilehash: 19436b7faef081757e4500c76e7537ee78081bfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950376"
---
# <a name="tutorial-configure-travelperk-for-automatic-user-provisioning"></a>Kurz: Konfigurace TravelPerk pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v TravelPerk i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a [odTravelPerk](https://www.travelperk.com/) uživatele a skupiny, aby se pomocí služby Azure AD Provisioning. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Podporované funkce

> [!div class="checklist"]
>
> - Vytváření uživatelů v TravelPerk
> - Odebrat uživatele v TravelPerk, když už nevyžadují přístup
> - Udržování uživatelských atributů synchronizovaných mezi Azure AD a TravelPerk
> - [Jednotné přihlašování](./travelperk-tutorial.md) k TravelPerk (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

- [Tenanta Azure AD](../develop/quickstart-create-new-tenant.md)
- Uživatelský účet ve službě Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce).
- Aktivní účet správce [TravelPerk](https://app.travelperk.com/signup) .
- [Plán](https://www.travelperk.com/pricing/)Premium/pro.


## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování

1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a TravelPerk](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-travelperk-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace TravelPerk pro podporu zřizování pomocí Azure AD

1. Přihlaste se k aplikaci [TravelPerk](https://app.travelperk.com/company/integrations/scim) pomocí svého účtu správce.

2. Přechod na **nastavení společnosti**  >  **SCIM Integration**  >  

3. Klikněte na **Povolit rozhraní SCIM API** .

   ![Povolit](./media/travelperk-provisioning-tutorial/configuration.png)

4. Můžete také povolit schvalování prostřednictvím SCIM. Schválení vám pomůžou nastavit další zásady správného řízení tím, že se nejdřív zajišťují schválení cest od zadaných schvalovatelů. Další informace najdete [tady](https://support.travelperk.com/hc/en-us/articles/360044168971-How-do-approval-processes-work-).

5. Můžete určit, jestli chcete, aby se jednotliví Správci jednotlivých uživatelů automaticky stali uživatelem zodpovědným za schválení cest. Proto se schvalovatel přiřadí v odpovídajícím procesu automatického schvalování. TravelPerk bude mapovat hodnotu **manažera** Azure na požadovaného schvalovatele uživatele. Uživatel musí na platformě existovat, než se stane zřízeným schvalovatelem uživatele.
Schvalovatelé se nevytvoří, pokud nejsou správně nakonfigurované na TravelPerk.

6. Vytváření procesu automatického schvalování je k dispozici v **Nastavení SCIM** po povolení SCIM ze stránky Integration. Pokud ho chcete zapnout, vyberte **prostřednictvím poskytovatele identity** a přepněte přepínač pro **Povolení vytvoření procesu automatického schvalování**.

7. Po nakonfigurování potřebného schvalovacího procesu klikněte na **Uložit změny** .

   ![Automatizace](./media/travelperk-provisioning-tutorial/approval.png)

## <a name="step-3-add-travelperk-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání TravelPerk z Galerie aplikací Azure AD

Přidejte TravelPerk z Galerie aplikací Azure AD a začněte spravovat zřizování pro TravelPerk. Pokud jste již dříve TravelPerk nastavení pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

- Při přiřazování uživatelů k TravelPerk je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role.

- Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-travelperk"></a>Krok 5. Konfigurace automatického zřizování uživatelů na TravelPerk

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-travelperk-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro TravelPerk ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

   ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **TravelPerk**.

   ![Odkaz TravelPerk v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

   ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

   ![Automatická karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** klikněte na **autorizovat**. Budete přesměrováni na přihlašovací stránku **TravelPerk**. Zadejte své **uživatelské jméno** a **heslo** a klikněte na tlačítko **Přihlásit** se. Na stránce autorizace klikněte na **autorizovat aplikaci** . Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k TravelPerk. Pokud se připojení nepovede, ujistěte se, že má váš účet SecureLogin oprávnění správce, a zkuste to znovu.

   ![Přihlašovací údaje správce](./media/travelperk-provisioning-tutorial/authorize.png)

   ![Vítáme vás](./media/travelperk-provisioning-tutorial/login.png)

   ![Access](./media/travelperk-provisioning-tutorial/authorization.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

   ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé TravelPerk**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do TravelPerk v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v TravelPerk pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), budete muset zajistit, aby rozhraní TravelPerk API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   | Atribut                                                                         | Typ      | Podporováno pro filtrování |
   | --------------------------------------------------------------------------------- | --------- | ----------------------- |
   | userName                                                                          | Řetězec    | &check;                 |
   | externalId                                                                        | Řetězec    |
   | active                                                                            | Logická hodnota   |
   | name.honorificPrefix                                                              | Řetězec    |
   | name.familyName                                                                   | Řetězec    |
   | name.givenName                                                                    | Řetězec    |
   | název. middleName                                                                   | Řetězec    |
   | preferredLanguage                                                                 | Řetězec    |
   | locale                                                                            | Řetězec    |
   | phoneNumbers[type eq "work"].value                                                | Řetězec    |
   | externalId                                                                        | Řetězec    |
   | title                                                                             | Řetězec    |
   | urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: costCenter             | Řetězec    |
   | urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager                | Reference |
   | urn: IETF: parametry: SCIM: schémata: rozšíření: travelperk: 2.0: uživatel: pohlaví                 | Řetězec    |
   | urn: IETF: parametry: SCIM: schémata: rozšíření: travelperk: 2.0: uživatel: dateOfBirth            | Řetězec    |
   | urn: IETF: parametry: SCIM: schémata: rozšíření: travelperk: 2.0: uživatel: invoiceProfiles        | Pole     |
   | urn:IETF:params:SCIM:schemas:Extension:travelperk: 2.0:User:emergencyContact. Name  | Řetězec    |
   | urn: IETF: parametry: SCIM: schémata: rozšíření: travelperk: 2.0: uživatel: emergencyContact. Phone | Řetězec    |
   | urn: IETF: parametry: SCIM: schémata: rozšíření: travelperk: 2.0: uživatel: travelPolicy           | Řetězec    |

10. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro TravelPerk, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro TravelPerk, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení

Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

- [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
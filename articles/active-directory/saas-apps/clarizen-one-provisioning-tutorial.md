---
title: 'Kurz: Konfigurace Clarizen jednoho pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po Clarizen.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d8021105-eb5b-4a20-8739-f02e0e22c147
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: f3a19d3c3bf3e4340bb36fd683453541fa15eb6c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650823"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>Kurz: Konfigurace Clarizen jednoho pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v Clarizen a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a [odClarizen](https://www.clarizen.com/) uživatele a skupiny, aby je pomocí služby Azure AD Provisioning. Informace o tom, jak tato služba funguje, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro aplikace SaaS (software jako služba) s Azure AD](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Podporované funkce

> [!div class="checklist"]
> * Vytvořte uživatele v Clarizen jednom.
> * Odeberte uživatele v Clarizen, když k nim ještě nepotřebují přístup.
> * Udržujte atributy uživatelů synchronizované mezi Azure AD a Clarizen jednu.
> * Zřizování skupin a členství ve skupinách v Clarizen jednom.
> * Pro Clarizen se doporučuje [jednotné přihlašování (SSO)](./clarizen-tutorial.md) .

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenanta Azure AD](../develop/quickstart-create-new-tenant.md)
* Uživatelský účet ve službě Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování. Příkladem může být správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce.
* Uživatelský účet v Clarizen s [oprávněním](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support)pro **uživatele integrace** a **správce Lite** .

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování

1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
1. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Určete, jaká data se mají [mapovat mezi Azure AD a Clarizen jednu](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Clarizen pro podporu zřizování s Azure AD

1. V závislosti na Clarizen jednom prostředí a datovém centru vyberte jednu ze čtyř následujících adres URL tenanta:
      * Středisko pro provozní data v USA: https://servicesapp2.clarizen.com/scim/v2
      * Středisko pro provozní data v EU: https://serviceseu1.clarizen.com/scim/v2
      * Datové centrum v USA – střed: https://servicesapp.clarizentb.com/scim/v2
      * Datové centrum EU pro Sandbox: https://serviceseu.clarizentb.com/scim/v2

1. Vygenerujte [klíč rozhraní API](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support). Tato hodnota se zadá do pole **tajného tokenu** na kartě **zřizování** vaší Clarizené aplikace v Azure Portal.

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Clarizen z Galerie aplikací Azure AD

Přidejte Clarizen jednu z Galerie aplikací Azure AD a začněte spravovat zřizování pro Clarizen. Pokud jste dříve nastavili Clarizen pro jednotné přihlašování, můžete použít stejnou aplikaci. Když nejprve otestujete integraci, vytvořte samostatnou aplikaci. Další informace o tom, jak přidat aplikaci z Galerie, najdete v tématu [Přidání aplikace do tenanta služby Azure AD](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování

Pomocí služby Azure AD Provisioning můžete nastavit obor, který se zřídí v závislosti na přiřazení aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se bude zřizovat pro vaši aplikaci na základě přiřazení, postupujte podle kroků v části [Správa přiřazení uživatelů pro aplikaci v Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) k přiřazení uživatelů a skupin k aplikaci. Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, použijte filtr oboru, jak je popsáno v tématu [zřizování aplikací založené na atributech s filtry oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Když přiřadíte uživatele a skupiny, aby Clarizen jednu, musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat tak další role.
* Začněte v malém. Před zavedením všech uživatelů proveďte test pomocí malé sady uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete spravovat řízení přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastaven na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>Krok 5. Konfigurace automatického zřizování uživatelů, aby Clarizen jednu

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Clarizen jednu v Azure AD

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**  >  **všechny aplikace**.

      ![Snímek obrazovky, který zobrazuje podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikace vyberte **Clarizen jednu**.

      ![Snímek obrazovky, který zobrazuje Clarizen jeden odkaz v seznamu aplikací](common/all-applications.png)

1. Vyberte kartu **Zřizování**.

      ![Snímek obrazovky zobrazující kartu zřizování](common/provisioning.png)

1. Nastavte **režim zřizování** na **automaticky**.

      ![Snímek obrazovky zobrazující automatickou možnost karty zřizování](common/provisioning-automatic.png)

1. V části **přihlašovací údaje správce** zadejte Clarizen jednu **adresu URL tenanta** a **tajný token**. Vyberte **Test připojení** , aby se služba Azure AD mohla připojit k Clarizen. Pokud se připojení nepovede, zajistěte, aby Clarizen jeden účet měl oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky, který zobrazuje pole tajného tokenu](common/provisioning-testconnection-tenanturltoken.png)

1. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování. Zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![Snímek obrazovky zobrazující okno s oznámením e-mailem](common/provisioning-notification-email.png)

1. Vyberte **Uložit**.

1. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatele, abyste Clarizen jednu**.

1. Zkontrolujte atributy uživatelů synchronizované z Azure AD a Clarizen je v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Clarizen jednom pro operace aktualizace. Pokud změníte [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), je nutné zajistit, aby Clarizen jedno rozhraní API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |displayName|Řetězec|
   |active|Logická hodnota|
   |title|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |e-maily [typ EQ "domů"]. hodnota|Řetězec|
   |e-maily [typ EQ "other"]. hodnota|Řetězec|
   |preferredLanguage|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |název. formátovaný|Řetězec|
   |name.honorificPrefix|Řetězec|
   |název. honorificSuffix|Řetězec|
   |adresy [Type EQ "ostatní"]. formátovaný|Řetězec|
   |adresy [Type EQ "Work"]. formátovaný|Řetězec|
   |adresy [typ EQ "Work"]. Country|Řetězec|
   |adresy [typ EQ "Work"]. region|Řetězec|
   |adresy [typ EQ "Work"].|Řetězec|
   |adresy [typ EQ "Work"]. postalCode|Řetězec|
   |adresy [Type EQ "]. streetAddress|Řetězec|
   |phoneNumbers[type eq "work"].value|Řetězec|
   |phoneNumbers[type eq "mobile"].value|Řetězec|
   |phoneNumbers [typ EQ "fax"]. hodnota|Řetězec|
   |phoneNumbers [typ EQ "domů"]. hodnota|Řetězec|
   |phoneNumbers [typ EQ "other"]. hodnota|Řetězec|
   |phoneNumbers [typ EQ "pager"]. Value|Řetězec|
   |externalId|Řetězec|
   |nickName|Řetězec|
   |locale|Řetězec|
   |role [primární EQ "true". Type]|Řetězec|
   |role [primární EQ "true". Value]|Řetězec|
   |timezone|Řetězec|
   |userType|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: employeeNumber|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Reference|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: costCenter|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: divize|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: divize|Řetězec|

1. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny a Clarizen jednu**.

1. Zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD, a Clarizen je v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v Clarizen jednom pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |externalId|Řetězec|
      |členy|Referenční informace|

1. Pokud chcete nakonfigurovat filtry oborů, přečtěte si pokyny v  [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pokud chcete povolit službu Azure AD Provisioning pro Clarizen jednu, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

      ![Snímek obrazovky, na kterém se zobrazuje stav zřizování zapnuto.](common/provisioning-toggle-on.png)

1. Definujte uživatele nebo skupiny, které chcete zřídit, aby je Clarizen, a to tak, že v části **Nastavení** vyberou požadované hodnoty v **rozsahu** .

      ![Snímek obrazovky zobrazující obor zřizování](common/provisioning-scope.png)

1. Až budete připraveni zřídit, vyberte **Uložit**.

      ![Snímek obrazovky, který ukazuje uložení konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení

Po nakonfigurování zřizování použijte následující prostředky k monitorování vašeho nasazení.

1. Pomocí [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně zřídili.
1. Zkontrolujte indikátor [průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) a zobrazte stav cyklu zřizování a způsob, jakým se má dokončit.
1. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [v tématu zřizování aplikací ve stavu karantény](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

Když přiřadíte uživatele do Clarizen jedné aplikace Galerie, vyberte jenom **uživatelskou** roli. Následující role jsou neplatné:

* Správce (správce)
* Uživatel s e-mailovými zprávami
* Externí uživatel
* Finanční uživatel
* Uživatel sociálních sítí
* Superuživatele
* Uživatel & výdajů na čas

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
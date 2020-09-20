---
title: 'Kurz: Konfigurace Apple School Manageru pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD do Apple School Manageru.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: f006c177-7b35-4af1-84f2-db4a4e2bf96a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: a6543c56f7170f674a227421575ba7f842fb29a8
ms.sourcegitcommit: 4ce82b6df65ebd81157b6168d3aa4e7323355022
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2020
ms.locfileid: "90761494"
---
# <a name="tutorial-configure-apple-school-manager-for-automatic-user-provisioning"></a>Kurz: Konfigurace Apple School Manageru pro Automatické zřizování uživatelů



Tento kurz popisuje kroky, které je třeba provést v Apple School Manageru a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele k [Apple School Manageru](https://school.apple.com/) pomocí služby zřizování Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md). 

## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Apple School Manageru
> * Odebrat uživatele v Apple School Manageru, když už nevyžadují přístup
> * Udržování specifických uživatelských atributů synchronizovaných mezi Azure AD a Apple School Managerem

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* Účet Apple School Manager s rolí správce, Site Manager nebo manažer pro lidi.

> [!NOTE]
> Přenos tokenu do služby Azure AD a navázání úspěšného připojení se musí dokončit 4 kalendářní dny, jinak se musí proces spustit znovu.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si [, jak služba zřizování funguje](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a Apple School Managerem](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-apple-school-manager-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Apple School Manageru pro podporu zřizování s Azure AD

1. V Apple School Manageru se přihlaste pomocí účtu, který má roli správce, Site Manager nebo Správce uživatelů.
2. Klikněte na nastavení v dolní části bočního panelu, klikněte na zdroj dat pod nastavením organizace a pak klikněte na připojit ke zdroji dat.
3. Klikněte na připojit vedle SCIM, pečlivě si přečtěte upozornění, klikněte na tlačítko Kopírovat a pak klikněte na tlačítko Zavřít.
[Okno připojit k SCIM, které poskytuje token a tlačítko pro kopírování pod ním.] Nechejte toto okno otevřené a zkopírujte adresu URL klienta z Apple Business Manageru do Azure AD, což je: https://federation.apple.com/feeds/school/scim

    ![Apple School Manager](media/appleschoolmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> Tajný token by se neměl sdílet s kýmkoli jiným než správce Azure AD.

## <a name="step-3-add-apple-school-manager-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Apple School Manageru z Galerie aplikací Azure AD

Přidejte Apple School Manager z Galerie aplikací Azure AD a začněte spravovat zřizování pro Apple School Manager. Pokud jste dříve nastavili Apple School Manager pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z Galerie [najdete tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definujte, kdo bude v oboru pro zřizování. 

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení do aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů a skupin k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oboru, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů k Apple School Manageru musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte malým. Než se pustíte do všech uživatelů, testujte je s malou sadou uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastavený na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-school-manager"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro Apple School Manager

1. Přihlaste se na web [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Apple School Manager**.

    ![Apple School Manager v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Automatická karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **základní adresu URL SCIM 2,0 a hodnoty přístupového tokenu** načtené z Apple School Manageru na **adrese URL tenanta** a **tajného tokenu** . Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Apple School Manageru. Pokud se připojení nepovede, ujistěte se, že váš účet Apple School Manageru má oprávnění správce, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Pokud je připojení úspěšné, Apple School Manager zobrazí SCIM připojení jako aktivní. Tento proces může v Apple School Manageru trvat až 60 sekund, aby odráželo nejnovější stav připojení.

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatele do Apple School Manageru**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD s Apple School Managerem v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Apple School Manageru pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |aktivně|Logická hodnota|
   |userName|Řetězec|
   |název. křestní jméno|Řetězec|
   |název. rodina|Řetězec|
   |název. křestní jméno|Řetězec|
   |externalId|Řetězec|
   |locale|Řetězec|
   |údaj|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: employeeNumber|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: costCenter|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: divize|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec|

10. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu zřizování Azure AD pro Apple School Manager, změňte **stav zřizování** na **zapnuto** v části nastavení.

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit v Apple School Manageru, a to tak, že v části **Nastavení** vyberete požadované hodnoty v poli **Rozsah** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení

Jakmile nakonfigurujete zřizování, použijte k monitorování nasazení tyto prostředky:

1. Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně nastavili.
2. Podívejte se na [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
3. Pokud se zdá, že konfigurace zřizování je ve stavu není v pořádku, bude aplikace přejít do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Kontrola požadavků na SCIM pro Apple School Manager](URL=https://support.apple.com/guide/apple-school-manager/apdd88331cd6)
* [Jak se v Apple School Manageru používá ID osoby](URL=https://support.apple.com/guide/apple-school-manager/apd69e1e48e9)
* [Použití SCIM k importu uživatelů do Apple School Manageru](URL=https://support.apple.com/guide/apple-school-manager/apd3ec7b95ad)
* [Řešení konfliktů uživatelských účtů SCIM v Apple School Manageru](URL=https://support.apple.com/guide/apple-school-manager/apd313013d12)
* [Odstranění účtů Azure AD, které se zobrazují v Apple School Manageru](URL=https://support.apple.com/guide/apple-school-manager/apdaa5798fbe)
* [Zobrazit aktivitu SCIM v Apple School Manageru](URL=https://support.apple.com/guide/apple-school-manager/apd1bfd8dfde)
* [Správa stávajícího tokenu SCIM a připojení v Apple School Manageru](URL=https://support.apple.com/guide/apple-school-manager/apdc9a823611)
* [Odpojení připojení SCIM v Apple School Manageru](URL=https://support.apple.com/guide/apple-school-manager/apd609be3a61)[Správa stávajících tokenů SCIM a připojení v Apple School Manageru](URL=https://support.apple.com/guide/apple-school-manager/apdc9a8236e9)
* [Řešení potíží s připojením SCIM v Apple School Manageru](URL=https://support.apple.com/guide/apple-school-manager/apd403a0f3bd)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)

---
title: 'Kurz: Konfigurace programu Apple Business Manager pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD do Apple Business Manageru.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4ad30031-9904-4ac3-a4d2-e8c28d44f319
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: b4f24c9beffcd67fb84940c2e159da615496d9aa
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180366"
---
# <a name="tutorial-configure-apple-business-manager-for-automatic-user-provisioning"></a>Kurz: Konfigurace programu Apple Business Manager pro Automatické zřizování uživatelů



Tento kurz popisuje kroky, které je třeba provést v Apple Business Manageru i v Azure Active Directory (Azure AD) pro konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele pro [Apple Business Manager](https://business.apple.com/) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Apple Business Manageru
> * Odebrat uživatele v Apple Business Manageru, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a Apple Business Managerem

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenanta Azure AD](../develop/quickstart-create-new-tenant.md)
* Uživatelský účet ve službě Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce).
* Účet Apple Business Manageru s rolí správce nebo Správce uživatelů.

> [!NOTE]
> Přenos tokenu do služby Azure AD a navázání úspěšného připojení se musí dokončit 4 kalendářní dny, jinak se musí proces spustit znovu.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a Apple Business Managerem](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-apple-business-manager-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Apple Business Manageru pro podporu zřizování s Azure AD

1. V Apple Business Manageru se přihlaste pomocí účtu, který má roli správce nebo lidé.
2. Klikněte na nastavení v dolní části bočního panelu, klikněte na zdroj dat pod nastavením organizace a pak klikněte na připojit ke zdroji dat.
3. Klikněte na připojit vedle SCIM, pečlivě si přečtěte upozornění, klikněte na tlačítko Kopírovat a pak klikněte na tlačítko Zavřít.
[Okno připojit k SCIM, které poskytuje token a tlačítko pro kopírování pod ním.] Nechte toto okno otevřené a zkopírujte adresu URL tenanta z Apple Business Manageru do Azure AD, což je: https://federation.apple.com/feeds/business/scim

    ![Apple Business Manager](media/applebusinessmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> Tajný token by se neměl sdílet s kýmkoli jiným než správce Azure AD.

## <a name="step-3-add-apple-business-manager-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Apple Business Manageru z Galerie aplikací Azure AD

Přidejte Apple Business Manager z Galerie aplikací Azure AD a začněte spravovat zřizování pro Apple Business Manager. Pokud jste již dříve nastavili Apple Business Manager pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů k Apple Business Manageru musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-business-manager"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro Apple Business Manager

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Apple Business Manager**.

    ![Apple Business Manager v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Automatická karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **základní adresu URL SCIM 2,0 a hodnoty přístupového tokenu** načtené z Apple Business MANAGERU v poli **Adresa URL tenanta** a **tajného tokenu** v uvedeném pořadí. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Apple Business Manageru. Pokud se připojení nepovede, ujistěte se, že má váš účet Apple Business Manager oprávnění správce, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Pokud je připojení úspěšné, Apple Business Manager zobrazí SCIM připojení jako aktivní. Tento proces může trvat až 60 sekund, než má Apple Business Manager odrážet nejnovější stav připojení.

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé s Apple Business Managerem**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Apple Business Manageru v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Apple Business Manageru pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |active|Logická hodnota|
   |userName|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |name.givenName|Řetězec|
   |externalId|Řetězec|
   |locale|Řetězec|
   |timezone|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: employeeNumber|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: costCenter|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: divize|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec|

10. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro Apple Business Manager, změňte **stav zřizování** na **zapnuto** v části nastavení.

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro Apple Business Manager, zvolením požadovaných hodnot v části **Rozsah** **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Kontrola požadavků na SCIM pro Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apdd88331cd6)
* [Jak se v Apple Business Manageru používá ID osoby](https://support.apple.com/guide/apple-business-manager/apd69e1e48e9)
* [Import uživatelů do Apple Business Manageru pomocí SCIM](https://support.apple.com/guide/apple-business-manager/apd3ec7b95ad)
* [Řešení konfliktů uživatelských účtů SCIM v Apple Business Manageru](https://support.apple.com/guide/apple-business-manager/apd313013d12)
* [Odstranění účtů Azure AD, které se zobrazují v Apple Business Manageru](https://support.apple.com/guide/apple-business-manager/apdaa5798fbe)
* [Zobrazení aktivity SCIM v Apple Business Manageru](https://support.apple.com/guide/apple-business-manager/apd1bfd8dfde)
* [Správa stávajícího tokenu SCIM a připojení v Apple Business Manageru](https://support.apple.com/guide/apple-business-manager/apdc9a823611)
* [Odpojení připojení SCIM v Apple Business Manageru](https://support.apple.com/guide/apple-business-manager/apd609be3a61)
* [Řešení potíží s připojením SCIM v Apple Business Manageru](https://support.apple.com/guide/apple-business-manager/apd403a0f3bd/web)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
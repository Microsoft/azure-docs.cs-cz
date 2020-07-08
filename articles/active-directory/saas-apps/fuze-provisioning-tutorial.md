---
title: 'Kurz: Konfigurace Fuze pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Fuze.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 34718201-4f0e-4260-9af0-b3b70a1e8265
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: aacaa8ca7e0cd15b34f29479d38d7bc8d95001de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253694"
---
# <a name="tutorial-configure-fuze-for-automatic-user-provisioning"></a>Kurz: Konfigurace Fuze pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Fuze a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro [Fuze](https://www.fuze.com/). Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Fuze
> * Odebrat uživatele v Fuze, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a Fuze
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/fuze-tutorial) k Fuze (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant služby Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce).
* [Tenant Fuze](https://www.fuze.com/)
* Uživatelský účet v fuze s oprávněními správce.


## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si [, jak služba zřizování funguje](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a Fuze](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-fuze-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Fuze pro podporu zřizování pomocí Azure AD

Před konfigurací Fuze pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na Fuze. 

1. Začněte tím, že se obrátíte na zástupce Fuze, kde najdete následující požadované informace:

    * Seznam SKU produktů Fuze aktuálně používaných ve vaší společnosti.
    * Seznam kódů umístění pro umístění vaší společnosti.
    * Seznam kódů oddělení vaší společnosti

2. Tyto SKU a kódy můžete najít v rámci smlouvy Fuze a konfiguračních dokumentů nebo se můžete obrátit na zástupce Fuze.

3. Po přijetí požadavků vám váš zástupce Fuze poskytne ověřovací token Fuze, který je nutný k povolení integrace. Tato hodnota se zadá do pole token tajného kódu na kartě zřizování vaší aplikace Fuze ve Azure Portal.

## <a name="step-3-add-fuze-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Fuze z Galerie aplikací Azure AD

Přidejte Fuze z Galerie aplikací Azure AD a začněte spravovat zřizování pro Fuze. Pokud jste dříve nastavili Fuze pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z Galerie [najdete tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování. 

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení do aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů a skupin k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oboru, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů k Fuze je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte malým. Než se pustíte do všech uživatelů, otestujte s malou sadou uživatelů. Pokud je obor pro zřizování nastavený na přiřazené uživatele, můžete to řídit přiřazením jednoho nebo dvou uživatelů k aplikaci. Pokud je obor nastavený na všichni uživatelé, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configuring-automatic-user-provisioning-to-fuze"></a>Krok 5. Konfigurace automatického zřizování uživatelů na Fuze 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Fuze na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Fuze ve službě Azure AD:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Fuze**.

    ![Odkaz Fuze v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **základní adresu URL SCIM 2,0 a hodnotu SCIM ověřovacího tokenu** , kterou jste dříve získali od zástupce Fuze v **adrese URL tenanta** a **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Fuze. Pokud se připojení nepovede, ujistěte se, že má váš účet Fuze oprávnění správce, a zkuste to znovu.

    ![Token adresy URL klienta](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Fuze**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Fuze v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Fuze pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |název. křestní jméno|Řetězec|
   |název. rodina|Řetězec|
   |e-maily [typ EQ "Work"]. Value|Řetězec|
   |aktivně|Logická hodnota|

10. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro Fuze, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro Fuze, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Jakmile nakonfigurujete zřizování, použijte k monitorování nasazení tyto prostředky:

1. Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně nastavili.
2. Podívejte se na [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
3. Pokud se zdá, že konfigurace zřizování je ve stavu není v pořádku, bude aplikace přejít do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Omezení konektoru

* Fuze podporuje vlastní atributy SCIM s názvem **oprávnění**. Tyto atributy je možné vytvořit a Neaktualizovat. 

## <a name="change-log"></a>Protokol změn

* 06/15/2020 – míra četnosti integrace upravená na 10 požadavků za sekundu.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* Přečtěte si, [Jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování](../app-provisioning/check-status-user-account-provisioning.md).
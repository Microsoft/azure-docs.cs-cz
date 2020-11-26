---
title: 'Kurz: Konfigurace Coda pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD do Coda.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: Zhchia
ms.openlocfilehash: ea2f25d6b03f002448079b16bd261729807d8a03
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96179689"
---
# <a name="tutorial-configure-coda-for-automatic-user-provisioning"></a>Kurz: Konfigurace Coda pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v rámci CODA a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele k [Coda](https://coda.io/) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Coda
> * Odebrat uživatele v Coda, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a Coda
> * [Jednotné přihlašování k systému](./coda-tutorial.md) CODA (doporučeno)

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](../develop/quickstart-create-new-tenant.md)
* Uživatelský účet v Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (např. správce aplikací, správce cloudových aplikací, vlastník aplikací nebo globální správce)
* Účet správce [podnikového účtu CODA](https://help.coda.io/en/articles/3520174-getting-started-with-sso) .

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a Coda](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-coda-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Coda pro podporu zřizování pomocí Azure AD

1. Otevřete konzolu pro správu organizace tak, že vyberete nastavení organizace pod... v nabídce pod pracovním prostorem.

    ![Nastavení SCIM organizace Coda Enterprise](media/coda-provisioning-tutorial/coda-scim-enable.png)

2. Ujistěte se, že je povolené zřízení pomocí SCIM.
3. Poznamenejte si základní adresu URL SCIM a token nosiče SCIM. Pokud neexistuje žádný nosný token, klikněte na vygenerovat nový token.

## <a name="step-3-add-coda-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Coda z Galerie aplikací Azure AD

Přidejte Coda z Galerie aplikací Azure AD a začněte spravovat zřizování pro Coda. Pokud jste dřív nastavili Coda pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování

Služba zřizování Azure AD umožňuje obor, který se zřídí v závislosti na přiřazení aplikace a nebo na základě atributů uživatele. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Pokud se rozhodnete obor, který bude zřízený výhradně na atributech uživatele, můžete použít filtr oboru, jak je popsáno [zde](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Při přiřazování uživatelů k Coda musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role.

* Začněte v malém. Než se pustíte do všech uživatelů, otestujte s malou sadou uživatelů. Pokud je obor pro zřizování nastavený na přiřazené uživatele, můžete to řídit přiřazením jednoho nebo dvou uživatelů k aplikaci. Pokud je obor nastavený na všichni uživatelé, můžete zadat [Filtr oboru založený na atributech](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-coda"></a>Krok 5. Konfigurace automatického zřizování uživatelů na Coda

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů v TestApp na základě přiřazení uživatelů v Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-coda-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Coda v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte položku **Coda**.

    ![Odkaz Coda v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte adresu URL vašeho tenanta CODA a tajný token, který jste dříve získali v kroku 2. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Coda. Pokud se připojení nepovede, ujistěte se, že váš účet Coda má oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky se zobrazí v dialogovém okně přihlašovací údaje správce, kde můžete zadat svého tenanta U R L a tajného tokenu.](./media/coda-provisioning-tutorial/provisioning.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé s Coda**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Coda v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Coda pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), je nutné zajistit, aby rozhraní API Coda podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |active|Logická hodnota|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|


10. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro systém Coda, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

12. Určete uživatele, které chcete zřídit v rámci Coda, a to výběrem požadovaných hodnot v **oblasti** **Nastavení** v části.

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů definovaných v **oboru** v části **Nastavení** . Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná.

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
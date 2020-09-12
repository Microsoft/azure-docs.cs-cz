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
ms.topic: article
ms.date: 08/31/2020
ms.author: Zhchia
ms.openlocfilehash: d5748b9d374a33e0aa3c52d9623f4e73d3bace1c
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89302046"
---
# <a name="tutorial-configure-coda-for-automatic-user-provisioning"></a>Kurz: Konfigurace Coda pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v rámci CODA a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele k [Coda](https://coda.io/) pomocí služby zřizování Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Coda
> * Odebrat uživatele v Coda, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a Coda
> * [Jednotné přihlašování k systému](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial) CODA (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce).
* Účet správce [podnikového účtu CODA](https://help.coda.io/en/articles/3520174-getting-started-with-sso) .

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si [, jak služba zřizování funguje](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a Coda](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-coda-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Coda pro podporu zřizování pomocí Azure AD

1. Otevřete konzolu pro správu organizace tak, že vyberete nastavení organizace pod... v nabídce pod pracovním prostorem.

    ![Nastavení SCIM organizace Coda Enterprise](media/coda-provisioning-tutorial/coda-scim-enable.png)

2. Ujistěte se, že je povolené zřízení pomocí SCIM.
3. Poznamenejte si základní adresu URL SCIM a token nosiče SCIM. Pokud neexistuje žádný nosný token, klikněte na vygenerovat nový token.

## <a name="step-3-add-coda-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Coda z Galerie aplikací Azure AD

Přidejte Coda z Galerie aplikací Azure AD a začněte spravovat zřizování pro Coda. Pokud jste dřív nastavili Coda pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z Galerie [najdete tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definujte, kdo bude v oboru pro zřizování.

Služba zřizování Azure AD umožňuje obor, který se zřídí v závislosti na přiřazení aplikace a nebo na základě atributů uživatele. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Pokud se rozhodnete obor, který bude zřízený výhradně na atributech uživatele, můžete použít filtr oboru, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

* Při přiřazování uživatelů k Coda musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role.

* Začněte malým. Než se pustíte do všech uživatelů, otestujte s malou sadou uživatelů. Pokud je obor pro zřizování nastavený na přiřazené uživatele, můžete to řídit přiřazením jednoho nebo dvou uživatelů k aplikaci. Pokud je obor nastavený na všichni uživatelé, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).


## <a name="step-5-configure-automatic-user-provisioning-to-coda"></a>Krok 5. Konfigurace automatického zřizování uživatelů na Coda

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů v TestApp na základě přiřazení uživatelů v Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-coda-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Coda v Azure AD:

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte položku **Coda**.

    ![Odkaz Coda v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte adresu URL vašeho tenanta CODA a tajný token, který jste dříve získali v kroku 2. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Coda. Pokud se připojení nepovede, ujistěte se, že váš účet Coda má oprávnění správce, a zkuste to znovu.

    ![zřizování](./media/coda-provisioning-tutorial/provisioning.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé s Coda**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Coda v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Coda pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), je nutné zajistit, aby rozhraní API Coda podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |aktivně|Logická hodnota|
   |název. křestní jméno|Řetězec|
   |název. rodina|Řetězec|


10. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro systém Coda, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

12. Určete uživatele, které chcete zřídit v rámci Coda, a to výběrem požadovaných hodnot v **oblasti** **Nastavení** v části.

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů definovaných v **oboru** v části **Nastavení** . Počáteční cyklus trvá déle než u dalších cyklů, ke kterým dojde přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Jakmile nakonfigurujete zřizování, použijte k monitorování nasazení tyto prostředky:

1. Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně nastavili.
2. Podívejte se na [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
3. Pokud se zdá, že konfigurace zřizování je ve stavu není v pořádku, bude aplikace přejít do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)

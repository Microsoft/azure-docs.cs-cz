---
title: 'Kurz: Konfigurace 8x8 pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po 8x8.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 81b4cde7-4938-4a1a-8495-003c06239b27
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2020
ms.author: Zhchia
ms.openlocfilehash: d1359ffb28cb98c0e46a6b7eff32c6faa7d00ba4
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87924764"
---
# <a name="tutorial-configure-8x8-for-automatic-user-provisioning"></a>Kurz: Konfigurace 8x8 pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v 8x8 Configuration Manager a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, které se [8x8](https://www.8x8.com) pomocí služby zřizování Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md). 

## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v 8x8
> * Odebrat uživatele v 8x8, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a 8x8
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8virtualoffice-tutorial) k 8x8 (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce).
* Předplatné 8x8 X řady všech úrovní.
* 8x8 uživatelský účet s oprávněním správce v [Configuration Manager](https://vo-cm.8x8.com).
* [Jednotné přihlašování s Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8virtualoffice-tutorial) už je nakonfigurované.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si [, jak služba zřizování funguje](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a 8x8](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-8x8-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace 8x8 pro podporu zřizování pomocí Azure AD

V této části se seznámíte s postupem konfigurace 8x8 pro podporu zřizování s Azure AD.

### <a name="to-configure-a-user-provisioning-access-token-in-8x8-configuration-manager"></a>Konfigurace přístupového tokenu pro zřizování uživatelů v 8x8 Configuration Manager:

1. Přihlaste se k [Configuration Manager](https://vo-cm.8x8.com). Vyberte **Správa identit**.

   ![Správa identit](./media/8x8-provisioning-tutorial/8x8-identity-management.png)

2. Kliknutím na odkaz **Zobrazit informace o zřizování uživatelů** vygenerujte token.

   ![Zobrazit zřizování uživatelů](./media/8x8-provisioning-tutorial/8x8-show-user-provisioning.png)

3. Zkopírujte hodnoty **8X8 URL** a **tokenu 8x8 API** . Tyto hodnoty se zadají do polí **Adresa URL tenanta** a **tajného tokenu** na kartě zřizování aplikace 8x8 ve Azure Portal.

   ![Kopírovat adresu URL a token](./media/8x8-provisioning-tutorial/8x8-copy-url-token.png)

## <a name="step-3-add-8x8-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání 8x8 z Galerie aplikací Azure AD

Přidejte 8x8 z Galerie aplikací Azure AD a začněte spravovat zřizování pro 8x8. Pokud jste dříve nastavili 8x8 pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z Galerie [najdete tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definujte, kdo bude v oboru pro zřizování.

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení do aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů a skupin k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Tato možnost je jednodušší a používá je většina lidí.

Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oboru, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k 8x8 je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte malým. Než se pustíte do všech uživatelů, testujte je s malou sadou uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastavený na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-8x8"></a>Krok 5. Konfigurace automatického zřizování uživatelů na 8x8 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v 8x8 na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-8x8-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro 8x8 ve službě Azure AD:

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](./media/8x8-provisioning-tutorial/enterprise-applications.png)

    ![Okno Všechny aplikace](./media/8x8-provisioning-tutorial/all-applications.png)

2. V seznamu aplikace vyberte **8x8**.

    ![Odkaz 8x8 v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** . klikněte na **Začínáme**.

    ![Karta zřizování](common/provisioning.png)

   ![Okno Začínáme](./media/8x8-provisioning-tutorial/get-started.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zkopírujte **adresu url 8x8** z Configuration Manager do **adresy URL tenanta**. Zkopírujte **token rozhraní API 8x8** z Configuration Manager do **tajného tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k 8x8. Pokud se připojení nepovede, ujistěte se, že má váš účet 8x8 oprávnění správce, a zkuste to znovu.

    ![Zřizování](./media/8x8-provisioning-tutorial/provisioning.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte možnost **zřídit Azure Active Directory uživatelé**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do 8x8 v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v 8x8 pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní 8x8 API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|Poznámky|
   |---|---|---|
   |userName|Řetězec|Nastaví uživatelské jméno i federační ID.|
   |externalId|Řetězec||
   |aktivně|Logická hodnota||
   |title|Řetězec||
   |e-maily [typ EQ "Work"]. Value|Řetězec||
   |název. křestní jméno|Řetězec||
   |název. rodina|Řetězec||
   |phoneNumbers [Type EQ "mobilní"]. Value|Řetězec|Osobní kontaktní číslo|
   |phoneNumbers [typ EQ "Work"]. Value|Řetězec|Osobní kontaktní číslo|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec||
   |urn: IETF: parametry: SCIM: schémata: rozšíření: 8x8:1.1: uživatel: Web|Řetězec|Po vytvoření uživatele nelze aktualizovat|
   |locale|Řetězec|Ve výchozím nastavení není namapováno.|
   |údaj|Řetězec|Ve výchozím nastavení není namapováno.|

10. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro 8x8, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro 8x8, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení** . Počáteční cyklus trvá déle než u dalších cyklů, ke kterým dojde přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Jakmile nakonfigurujete zřizování, použijte k monitorování nasazení tyto prostředky:

1. Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně zřídili.
2. Zkontrolujte indikátor [průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) a zobrazte stav cyklu zřizování a způsob, jakým se má dokončit.
3. Pokud se zdá, že konfigurace zřizování je ve stavu není v pořádku, bude aplikace přejít do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)

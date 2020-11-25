---
title: 'Kurz: Konfigurace Atlassian cloudu pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů k Atlassian cloudu.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: cee9544dbc2deecafa67d73d3bdea804937c1d7a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005108"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Kurz: Konfigurace Atlassian cloudu pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Atlassian cloudu a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů a skupin pro [Atlassian Cloud](https://www.atlassian.com/licensing/cloud). Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Atlassian cloudu
> * Odebrat uživatele v Atlassian cloudu, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a Atlassian cloudem
> * Zřizování skupin a členství ve skupinách v Atlassian cloudu
> * [Jednotné přihlašování](./atlassian-cloud-tutorial.md) do Atlassian cloudu (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenanta Azure AD](../develop/quickstart-create-new-tenant.md)
* Uživatelský účet v Azure AD s [oprávněním](../users-groups-roles/directory-assign-admin-roles.md) ke konfiguraci zřizování (např. správce aplikací, správce cloudových aplikací, vlastník aplikací nebo globální správce)
* [Cloudový tenant Atlassian](https://www.atlassian.com/licensing/cloud)
* Uživatelský účet v cloudu Atlassian s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a cloudem Atlassian](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-atlassian-cloud-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace cloudu Atlassian pro podporu zřizování s Azure AD

1. Přejděte na [Atlassian Organization Manager](https://admin.atlassian.com) **> vyberte adresář > organizace**.

    ![Snímek stránky pro správu s možností adresáře s názvem.](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

2. Klikněte na **zřizování uživatelů** a pak klikněte na **vytvořit adresář**. Zkopírujte **základní adresu URL adresáře** a **nosný token** , který se zadá do polí **URL tenanta** a **tajného tokenu** na kartě zřizování vaší cloudové aplikace Atlassian na portálu Azure AD.

    ![Snímek obrazovky stránky pro správu s možností zřizování uživatelů s názvem. ](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Snímek obrazovky se stránkou vytvořit token](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)
    ![Snímek stránky s tokenem adresáře demo Time](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)


## <a name="step-3-add-atlassian-cloud-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání cloudu Atlassian z Galerie aplikací Azure AD

Přidejte Cloud Atlassian z Galerie aplikací Azure AD a začněte spravovat zřizování pro Cloud Atlassian. Pokud jste dříve nastavili Atlassian Cloud pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin k Atlassian cloudu musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro Atlassian Cloud 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v cloudu Atlassian na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Atlassian Cloud v Azure AD:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **Cloud Atlassian**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Cloud Atlassian**.

    ![Odkaz Atlassian Cloud v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta** a **tajný token** , který jste dříve získali z účtu Atlassian vašeho cloudu. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Atlassian cloudu. Pokud se připojení nepovede, zajistěte, aby měl váš cloudový účet Atlassian oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé a Atlassian Cloud**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Atlassian cloudu v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Atlassian cloudu pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |active|Logická hodnota|
   |name.familyName|Řetězec|
   |name.givenName|Řetězec|
   |emails[type eq "work"].value|Řetězec|   

10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do cloudu Atlassian**.

11. Zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD do Atlassian cloudu v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v cloudu Atlassian pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |externalId|Řetězec|
      |členy|Referenční informace|

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro Cloud Atlassian, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro Atlassian Cloud, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

16. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Omezení konektoru

* Atlassian Cloud umožňuje zřizování uživatelů jenom z [ověřených domén](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian Cloud nepodporuje přejmenování skupin ještě dnes. To znamená, že jakékoli změny DisplayName ve skupině ve službě Azure AD nebudou aktualizovány a projeví se v Atlassian cloudu.
* Hodnota atributu uživatel **pošty** ve službě Azure AD se naplní jenom v případě, že má uživatel poštovní schránku Microsoft Exchange. Pokud uživatel ho nemá, doporučuje se namapovat jiný požadovaný atribut na atribut **Emails** v Atlassian cloudu.

## <a name="change-log"></a>Protokol změn

* 06/15/2020 – Přidali jsme podporu pro dávkovou opravu pro skupiny.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
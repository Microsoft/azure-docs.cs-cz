---
title: 'Kurz: Konfigurace Atlassian cloudu pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů k Atlassian cloudu.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 95455d389669b89075ca0ea8583cc7858bb532f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85317613"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Kurz: Konfigurace Atlassian cloudu pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Atlassian cloudu a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů a skupin pro [Atlassian Cloud](https://www.atlassian.com/licensing/cloud). Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Atlassian cloudu
> * Odebrat uživatele v Atlassian cloudu, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a Atlassian cloudem
> * Zřizování skupin a členství ve skupinách v Atlassian cloudu
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/atlassian-cloud-tutorial) do Atlassian cloudu (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant služby Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce).
* [Cloudový tenant Atlassian](https://www.atlassian.com/licensing/cloud)
* Uživatelský účet v cloudu Atlassian s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si [, jak služba zřizování funguje](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a cloudem Atlassian](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-atlassian-cloud-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace cloudu Atlassian pro podporu zřizování s Azure AD

1. Přejděte na [Atlassian Organization Manager](https://admin.atlassian.com) **> vyberte adresář > organizace**.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

2. Klikněte na **zřizování uživatelů** a pak klikněte na **vytvořit adresář**. Zkopírujte **základní adresu URL adresáře** a **nosný token** , který se zadá do polí **URL tenanta** a **tajného tokenu** na kartě zřizování vaší cloudové aplikace Atlassian na portálu Azure AD.

    ![Zřizování cloudu Atlassian Atlassian Cloud zřizování ](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![ ](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![ Atlassian cloudu](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)


## <a name="step-3-add-atlassian-cloud-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání cloudu Atlassian z Galerie aplikací Azure AD

Přidejte Cloud Atlassian z Galerie aplikací Azure AD a začněte spravovat zřizování pro Cloud Atlassian. Pokud jste dříve nastavili Atlassian Cloud pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z Galerie [najdete tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování. 

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení do aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů a skupin k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oboru, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k Atlassian cloudu musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte malým. Než se pustíte do všech uživatelů, testujte je s malou sadou uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastavený na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro Atlassian Cloud 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v cloudu Atlassian na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Atlassian Cloud v Azure AD:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Cloud Atlassian**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Cloud Atlassian**.

    ![Odkaz Atlassian Cloud v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta** a **tajný token** , který jste dříve získali z účtu Atlassian vašeho cloudu. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Atlassian cloudu. Pokud se připojení nepovede, zajistěte, aby měl váš cloudový účet Atlassian oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé a Atlassian Cloud**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Atlassian cloudu v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Atlassian cloudu pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |aktivně|Logická hodnota|
   |název. rodina|Řetězec|
   |název. křestní jméno|Řetězec|
   |e-maily [typ EQ "Work"]. Value|Řetězec|   

10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do cloudu Atlassian**.

11. Zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD do Atlassian cloudu v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v cloudu Atlassian pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |externalId|Řetězec|
      |členy|Referenční informace|

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro Cloud Atlassian, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro Atlassian Cloud, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

16. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Jakmile nakonfigurujete zřizování, použijte k monitorování nasazení tyto prostředky:

1. Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně nastavili.
2. Podívejte se na [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
3. Pokud se zdá, že konfigurace zřizování je ve stavu není v pořádku, bude aplikace přejít do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Omezení konektoru

* Atlassian Cloud umožňuje zřizování uživatelů jenom z [ověřených domén](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian Cloud nepodporuje přejmenování skupin ještě dnes. To znamená, že jakékoli změny DisplayName ve skupině ve službě Azure AD nebudou aktualizovány a projeví se v Atlassian cloudu.
* Hodnota atributu uživatel **pošty** ve službě Azure AD se naplní jenom v případě, že má uživatel poštovní schránku Microsoft Exchange. Pokud uživatel ho nemá, doporučuje se namapovat jiný požadovaný atribut na atribut **Emails** v Atlassian cloudu.

## <a name="change-log"></a>Protokol změn

* 06/15/2020 – Přidali jsme podporu pro dávkovou opravu pro skupiny.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
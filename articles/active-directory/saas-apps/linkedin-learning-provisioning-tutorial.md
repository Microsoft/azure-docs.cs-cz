---
title: 'Kurz: Konfigurace programu LinkedIn Learning pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po učení LinkedInu.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 21e2f470-4eb1-472c-adb9-4203c00300be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2020
ms.author: Zhchia
ms.openlocfilehash: fcde2ed4986b367e35965f6524e7d7727eaa07c9
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926769"
---
# <a name="tutorial-configure-linkedin-learning-for-automatic-user-provisioning"></a>Kurz: Konfigurace programu LinkedIn Learning pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést při konfiguraci automatického zřizování uživatelů v LinkedIn Learning a Azure Active Directory (Azure AD). Po nakonfigurování Azure AD automaticky zřídí a zruší pravidla uživatelů a skupin na [LinkedIn Learning](https://learning.linkedin.com/) pomocí služby zřizování Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v učení LinkedIn
> * Odebrat uživatele ve studiu LinkedInu, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi službami Azure AD a LinkedIn Learning
> * Zřizování skupin a členství ve skupinách ve studiu LinkedIn
> * [Jednotné přihlašování](linkedinlearning-tutorial.md) na LinkedIn Learning (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* Schválení a SCIM povolená pro LinkedIn Learning (kontaktujte e-mailem).

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si [, jak služba zřizování funguje](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a učením LinkedInu](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-linkedin-learning-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace učení LinkedInu pro podporu zřizování pomocí Azure AD
1. Přihlaste se do [Nastavení výuky LinkedIn](https://www.linkedin.com/learning-admin/settings/global). Vyberte **Nastavení SCIM** a pak vyberte **Přidat novou konfiguraci SCIM**.

   ![Konfigurace nastavení SCIM](./media/linkedin-learning-provisioning-tutorial/learning-scim-settings.png)

2. Zadejte název konfigurace a nastavte **Automatické přiřazení licencí** na zapnuto. Pak klikněte na **vygenerovat token**.

   ![Název konfigurace SCIM](./media/linkedin-learning-provisioning-tutorial/learning-scim-configuration.png)

3. Po vytvoření konfigurace by měl být vygenerován **přístupový token** . Nechejte toto zkopírování pro pozdější verzi.

   ![Přístupový token SCIM](./media/linkedin-learning-provisioning-tutorial/learning-scim-token.png)

4. Můžete znovu vystavit všechny existující konfigurace (které budou generovat nový token), nebo je odebrat.

## <a name="step-3-add-linkedin-learning-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání učení LinkedInu z Galerie aplikací Azure AD

Pokud chcete začít spravovat zřizování na LinkedIn Learning, přidejte do Galerie aplikací Azure AD učení LinkedInu. Pokud jste dříve nastavili LinkedIn Learning pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z Galerie [najdete tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definujte, kdo bude v oboru pro zřizování. 

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení do aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů a skupin k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oboru, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k učení LinkedInu musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte malým. Než se pustíte do všech uživatelů, testujte je s malou sadou uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastavený na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-linkedin-learning"></a>Krok 5. Konfigurace automatického zřizování uživatelů na LinkedIn Learning 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-linkedin-learning-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro učení LinkedIn ve službě Azure AD:

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **LinkedIn Learning**.

    ![Odkaz učení LinkedInu v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://api.linkedin.com/scim` **adresu URL tenanta**. Zadejte hodnotu přístupového tokenu dříve získanou v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k učení LinkedIn. Pokud se připojení nepovede, ujistěte se, že váš účet učení LinkedIn má oprávnění správce, a zkuste to znovu.

    ![zřizování](./media/linkedin-learning-provisioning-tutorial/provisioning.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte možnost **zřídit Azure Active Directory uživatelé**.

9. Zkontrolujte atributy uživatelů synchronizované z Azure AD až LinkedIn Learning v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů ve studiu LinkedIn pro operace aktualizace. Pokud se rozhodnete, že chcete změnit [odpovídající atribut cíle](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), je nutné zajistit, aby rozhraní API pro učení LinkedIn podporuje filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |externalId|Řetězec|&check;|
   |userName|Řetězec|
   |název. křestní jméno|Řetězec|
   |název. rodina|Řetězec|
   |displayName|Řetězec|
   |adresy [typ EQ "Work"].|Řetězec|
   |title|Řetězec|
   |e-maily [typ EQ "Work"]. Value|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Referenční informace|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec|

10. V části **mapování** vyberte možnost **zřídit Azure Active Directory skupiny**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD až LinkedIn Learning v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin ve studiu LinkedIn pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    |Atribut|Typ|Podporováno pro filtrování|
    |---|---|---|
    |displayName|Řetězec|&check;|
    |členy|Referenční informace|
    |externalId|Řetězec|

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro LinkedIn Learning, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro učení s LinkedInem, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **rozsahu** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení** . Počáteční cyklus trvá déle než u dalších cyklů, ke kterým dojde přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Jakmile nakonfigurujete zřizování, použijte k monitorování nasazení tyto prostředky:

1. Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně nastavili.
2. Podívejte se na [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
3. Pokud se zdá, že konfigurace zřizování je ve stavu není v pořádku, bude aplikace přejít do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)

---
title: 'Kurz: Konfigurace konektoru Azure Databricks SCIM pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po Azure Databricks konektor SCIM.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b16eaf27-4bd1-4509-be2c-9a4f66b6badc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2020
ms.author: Zhchia
ms.openlocfilehash: fe1260982edc877c049716bd74f1bb3e90d33b0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77370532"
---
# <a name="tutorial-configure-azure-databricks-scim-connector-for-automatic-user-provisioning"></a>Kurz: Konfigurace konektoru Azure Databricks SCIM pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v Azure Databricks konektoru SCIM a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, aby [Azure Databricks konektor SCIM](https://databricks.com/) pomocí služby Azure AD Provisioning. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytvoření uživatelů v konektoru Azure Databricks SCIM
> * Odebrat uživatele v konektoru Azure Databricks SCIM, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi službou Azure AD a konektorem Azure Databricks SCIM
> * Zřizování skupin a členství ve skupinách v konektoru Azure Databricks SCIM

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* Účet Azure Databricks s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si [, jak služba zřizování funguje](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a konektorem Azure DATABRICKS SCIM](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-azure-databricks-scim-connector-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace konektoru Azure Databricks SCIM pro podporu zřizování s Azure AD

1. Pokud chcete nastavit Azure Databricks zřizování SCIM, přidejte ho jako prostředek do Azure Active Directory tenanta a nakonfigurujte ho pomocí níže uvedených nastavení.

    ![Instalace Azure Databricks](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/setup.png)

2. K vygenerování osobního přístupového tokenu v [Azure Databricks odkazují.](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#token-management)

3. Zkopírujte **token**. Tato hodnota se zadává do pole token tajného kódu na kartě zřizování aplikace konektoru Azure Databricks SCIM v Azure Portal.

## <a name="step-3-add-azure-databricks-scim-connector-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání konektoru Azure Databricks SCIM z Galerie aplikací Azure AD

Přidejte Azure Databricks konektor SCIM z Galerie aplikací Azure AD a začněte spravovat zřizování pro Azure Databricks konektor SCIM. Pokud jste dříve nastavili Azure Databricks konektor SCIM pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z Galerie [najdete tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování. 

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení do aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů a skupin k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oboru, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k Azure Databricks konektoru SCIM je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte malým. Než se pustíte do všech uživatelů, testujte je s malou sadou uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastavený na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-azure-databricks-scim-connector"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro Azure Databricks konektor SCIM 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!NOTE]
> Další informace o Azure Databricks koncový bod SCIM najdete v [tomto](https://docs.databricks.com/dev-tools/api/latest/scim.html
)tématu.

### <a name="to-configure-automatic-user-provisioning-for-azure-databricks-scim-connector-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro konektor Azure Databricks SCIM ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Azure Databricks konektor SCIM**.

    ![Odkaz Azure Databricks konektoru SCIM v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte hodnotu koncového bodu SCIM v **adrese URL klienta**. Adresa URL tenanta by měla být ve formátu `https://<region>.azuredatabricks.net/api/2.0/preview/scim` , ve kterém se **oblast** nachází na adrese URL domovské stránky Azure Databricks. Například koncový bod SCIM pro oblast **westus** bude `https://westus.azuredatabricks.net/api/2.0/preview/scim`. Zadejte hodnotu tokenu získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** , aby se služba Azure AD mohla připojit k Azure Databricks konektoru SCIM. Pokud se připojení nepovede, ujistěte se, že váš účet konektoru Azure Databricks SCIM má oprávnění správce, a zkuste to znovu.

    ![zřizování](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/provisioning.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatelů a Azure Databricks konektor SCIM**.

9. Zkontrolujte atributy uživatelů synchronizované z Azure AD a Azure Databricks konektor SCIM v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v konektoru Azure Databricks SCIM pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní API konektoru Azure Databricks SCIM podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |displayName|Řetězec|
   |aktivně|Logická hodnota|

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do Azure Databricks konektor SCIM**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD a Azure Databricks konektor SCIM v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v konektoru Azure Databricks SCIM pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

     |Atribut|Typ|
     |---|---|
     |displayName|Řetězec|
     |členy|Referenční informace|

11. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do Azure Databricks konektor SCIM**.

12. Pokud chcete povolit službu zřizování Azure AD pro konektor Azure Databricks SCIM, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit pro Azure Databricks konektor SCIM, a to výběrem požadovaných hodnot v **oblasti** **Nastavení** v části.

    ![Rozsah zřizování](common/provisioning-scope.png)

14. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení** . Počáteční cyklus trvá déle než u dalších cyklů, ke kterým dojde přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Jakmile nakonfigurujete zřizování, použijte k monitorování nasazení tyto prostředky:

* Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně nastavili.
* Podívejte se na [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
* Pokud se zdá, že konfigurace zřizování je ve stavu není v pořádku, bude aplikace přejít do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží
* Datacihly vždycky převádí své hodnoty uživatelského jména na malá písmena při ukládání do svého adresáře bez ohledu na to, co se jim odesílají přes SCIM.
* V současné době jsou požadavky na Azure Databricks rozhraní API SCIM pro uživatele citlivé na velká a malá písmena, USER@contoso.com takže pokud se dotazování na ni bude zobrazovat jako user@contoso.com, bude se jednat o 0 výsledků.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)

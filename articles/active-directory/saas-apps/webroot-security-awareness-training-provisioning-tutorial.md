---
title: 'Kurz: Konfigurace školení na vědomí zabezpečení společnosti Webroot pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů od Azure AD až po školení o povědomí o zabezpečení společnosti Webroot.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 455f4396-930e-4db5-a167-d3ea6a860a17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2020
ms.author: Zhchia
ms.openlocfilehash: 5bdcf197b4e8a619e55895faf7637ebac0ac12ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361122"
---
# <a name="tutorial-configure-webroot-security-awareness-training-for-automatic-user-provisioning"></a>Kurz: Konfigurace školení na vědomí zabezpečení společnosti Webroot pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v rámci školicích kurzů zabezpečení Webroot a Azure Active Directory (Azure AD) pro konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny pro [školení o povědomí o zabezpečení společnosti Webroot](https://www.webroot.com/) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v školení o povědomí o zabezpečení společnosti Webroot
> * Odebrat uživatele ve školicích kurzech zabezpečení společnosti Webroot, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi službou Azure AD a školením o povědomí o zabezpečení služby Webroot
> * Zřizování skupin a členství ve skupinách ve školicích kurzech zabezpečení společnosti Webroot

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce).
* Konzola poskytovatele spravované služby se službou Webroot Security Training je povolená pro aspoň jednu z vašich webů.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Zjistěte, kdo bude v [rozsahu zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se [namapují mezi školeními ke sledování povědomí Azure AD a Webroot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-webroot-security-awareness-training-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace školení služby Webroot Security pro podporu zřizování ve službě Azure AD

### <a name="obtain-a-secret-token"></a>Získání tajného tokenu

Pokud chcete připojit svůj web ke službě Azure AD, budete muset pro tuto lokalitu v konzole pro správu služby Webroot získat **tajný token** .

1. Přihlaste se ke [konzole pro správu služby Webroot](https://identity.webrootanywhere.com/v1/Account/login#tab_customers)

2. Na kartě **weby** klikněte na ikonu ozubeného kolečka ve sloupci školení pro sledování zabezpečení pro lokalitu, kterou chcete připojit ke službě Azure AD.

    ![Ikona ozubeného kolečka](./media/webroot-security-awareness-training-provisioning-tutorial/gear-icon.png)

3. Kliknutím na toto tlačítko **nakonfigurujte integraci služby Azure AD**.

    ![Konfigurace integrace služby Azure AD](./media/webroot-security-awareness-training-provisioning-tutorial/configure-azure-ad-integration.png)

4. Zkopírujte a uložte **tajný token**. Tato hodnota se zadá do pole token tajného kódu na kartě zřizování aplikace školení pro sledování zabezpečení služby Webroot v Azure Portal.

5. Klikněte na **Hotovo**.

    ![Kopírovat tajný token](./media/webroot-security-awareness-training-provisioning-tutorial/copy-secret-token.png)

## <a name="step-3-add-webroot-security-awareness-training-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání školení k povědomí o zabezpečení Webroot z Galerie aplikací Azure AD

Přidejte školení o povědomí o zabezpečení Webroot z Galerie aplikací Azure AD, abyste mohli začít spravovat zřizování pro školení týkající se zabezpečení Webroot. Další informace o přidání aplikace z galerie najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k školení o povědomí o zabezpečení společnosti Webroot musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-webroot-security-awareness-training"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro školení o povědomí o zabezpečení společnosti Webroot 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-webroot-security-awareness-training-in-azure-ad"></a>Postup při konfiguraci automatického zřizování uživatelů pro školení o povědomí o zabezpečení služby Webroot v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **školení zabezpečení společnosti Webroot**.

    ![Odkaz na školení o povědomí o zabezpečení Webroot v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://awarenessapi.webrootanywhere.com/api/v2/scim` **adresu URL tenanta**. Zadejte hodnotu tajného tokenu získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k školicímu školení zabezpečení služby Webroot. Pokud se připojení nepovede, zajistěte, aby měl váš účet školení o zabezpečení služby Webroot oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky se zobrazí v dialogovém okně přihlašovací údaje správce, kde můžete zadat svého tenanta U R L a tajného tokenu.](./media/webroot-security-awareness-training-provisioning-tutorial/provisioning.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte možnost **zřídit Azure Active Directory uživatelé**.

9. Zkontrolujte atributy uživatele, které se synchronizují z Azure AD až do školení o povědomí o zabezpečení společnosti Webroot v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů ve školicích kurzech zabezpečení služby Webroot pro operace aktualizace. Pokud se rozhodnete, že chcete změnit [odpovídající atribut cíle](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), je nutné zajistit, aby rozhraní API pro školení ke sledování zabezpečení Webroot podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |externalId|Řetězec|&check;|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |emails[type eq "work"].value|Řetězec|

10. V části **mapování** vyberte možnost **zřídit Azure Active Directory skupiny**.

11. Projděte si atributy skupiny, které se synchronizují z Azure AD až po školení o povědomí o zabezpečení společnosti Webroot v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin ve školicím cvičení zabezpečení služby Webroot pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|Podporováno pro filtrování|
      |---|---|---|
      |displayName|Řetězec|&check;|
      |členy|Referenční informace|
      |externalId|Řetězec|

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro školení o povědomí o zabezpečení společnosti Webroot, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro školení o povědomí o zabezpečení společnosti Webroot, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **rozsahu** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../manage-apps/check-status-user-account-provisioning.md).

---
title: 'Kurz: Konfigurace aplikace TeamViewer pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak automaticky zřídit a odpojit uživatelské účty z Azure AD do TeamViewer.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 73dd5494-f572-42bb-9071-d599361b9bd7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2020
ms.author: Zhchia
ms.openlocfilehash: dc33cf9249a5b804ef46e75ff1822eaa5c6f9d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77366436"
---
# <a name="tutorial-configure-teamviewer-for-automatic-user-provisioning"></a>Kurz: Konfigurace aplikace TeamViewer pro automatické zřizování uživatelů

Tento kurz popisuje kroky, které je potřeba provést v TeamViewer a Azure Active Directory (Azure AD) ke konfiguraci automatickézřižení služby uživatelů. Při konfiguraci Azure AD automaticky zřídí a de-zřaže uživatelů a skupin [teamviewer](https://www.teamviewer.com/buy-now/) pomocí služby Azure AD zřizování. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v teamvieweru
> * Odebrání uživatelů v aplikaci TeamViewer, pokud již nevyžadují přístup
> * Zachování synchronizace atributů uživatelů mezi Azure AD a TeamViewer
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial) k TeamViewer (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* [Klient Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikací, správce cloudových aplikací, vlastník aplikace nebo globální správce). 
* Platná [licence Tensor](https://www.teamviewer.com/de/teamviewer-tensor/) pro TeamViewer.
* K dispozici je platný vlastní identifikátor z konfigurace [jednotného přihlášení.](https://community.teamviewer.com/t5/Knowledge-Base/Single-Sign-On-with-Azure-Active-Directory/ta-p/60209#toc-hId--473669723)

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si o [tom, jak funguje služba zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se [mají mapovat mezi Azure AD a TeamViewer](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-teamviewer-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace aplikace TeamViewer pro podporu zřizování pomocí Azure AD

1. Přihlaste se do [konzoly TeamViewer Management Console](https://login.teamviewer.com). Přejděte na **Upravit profil**.

    ![Konzola pro správu aplikace TeamViewer](./media/teamviewer-provisioning-tutorial/admin.png)

2.  Přejděte na **Aplikace**. Klikněte na **Vytvořit token skriptu**.

    ![TeamViewer vytvořit token](./media/teamviewer-provisioning-tutorial/createtoken.png)

3.  Zadejte název tokenu skriptu. Klikněte na tlačítko **Uložit.**

    ![Název tokenu TeamViewer](./media/teamviewer-provisioning-tutorial/tokenname.png)

4. Zkopírujte **token** a klepněte na tlačítko **OK**. Tato hodnota se zadá do pole **Tajný token** vaší aplikace TeamViewer na webu Azure Portal.

    ![Žeton TeamViewer](./media/teamviewer-provisioning-tutorial/token.png)

## <a name="step-3-add-teamviewer-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání aplikace TeamViewer z galerie aplikací Azure AD

Přidejte TeamViewer z galerie aplikací Azure AD a začněte spravovat zřizování do TeamViewer. Pokud jste již dříve nastavili TeamViewer pro SSO, můžete použít stejnou aplikaci. Doporučujeme však vytvořit samostatnou aplikaci při testování integrace zpočátku. Další informace o přidání aplikace z galerie [naleznete zde](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování 

Služba zřizování Azure AD umožňuje obor, který bude zřízen na základě přiřazení k aplikaci a nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete obor, kdo bude zřízen do vaší aplikace na základě přiřazení, můžete použít následující [kroky](../manage-apps/assign-user-or-group-access-portal.md) k přiřazení uživatelů a skupin k aplikaci. Pokud se rozhodnete obor, který bude zřízen výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oborů, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin do aplikace TeamViewer je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí výchozí přístup jsou vyloučeni z zřizování a budou označeny jako neefektivně oprávněné v protokolech zřizování. Pokud je k dispozici pouze role v aplikaci výchozí přístupová role, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Vyzkoušejte s malou sadou uživatelů a skupin před zavedením pro všechny. Pokud je obor zřizování nastaven na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin do aplikace. Pokud je obor nastaven na všechny uživatele a skupiny, můžete určit [filtr oborů založených na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-teamviewer"></a>Krok 5. Konfigurace automatického zřizování uživatelů do aplikace TeamViewer 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Aplikaci TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-teamviewer-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro TeamViewer ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **TeamViewer**.

    ![Odkaz TeamViewer v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `ttps://webapi.teamviewer.com/scim/v2` zadejte do pole **Adresa URL tentant** a zadejte token skriptu vytvořený dříve v **tajném tokenu**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k TeamViewer. Pokud se připojení nezdaří, ujistěte se, že váš účet TeamViewer má oprávnění správce a zkuste to znovu.

    ![Zajišťování](./media/teamViewer-provisioning-tutorial/provisioning.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě.**

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části Mapování vyberte **Synchronizovat uživatele služby Azure Active Directory s aplikací TeamViewer**. **Mappings**

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do TeamViewer v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v teamviewer pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní API aplikace TeamViewer podporovalo filtrování uživatelů na základě tohoto atributu. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |displayName|Řetězec|
   |Aktivní|Logická hodnota|

10. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Chcete-li povolit službu zřizování Azure AD pro TeamViewer, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro TeamViewer výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

13. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení.** Počáteční cyklus trvá déle než následující cykly, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po konfiguraci zřizování můžete ke sledování nasazení použít následující prostředky:

* Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé byli zřízeni úspěšně nebo neúspěšně
* Zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) zobrazíte stav cyklu zřizování a jak blízko je k dokončení
* Pokud se zdá, že konfigurace zřizování je v nestavu, aplikace přejde do karantény. Další informace o stavech karantény naleznete [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../manage-apps/check-status-user-account-provisioning.md)

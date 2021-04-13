---
title: 'Kurz: Konfigurace Jostle pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po Jostle.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6dbb744f-8b8e-4988-b293-ebe079c8c5c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2021
ms.author: Zhchia
ms.openlocfilehash: d2ab0009f036afa38dc9e401223854a034d45e42
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368600"
---
# <a name="tutorial-configure-jostle-for-automatic-user-provisioning"></a>Kurz: Konfigurace Jostle pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v Jostle i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, které se [Jostle](https://www.jostle.me/) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Jostle
> * Odebrat uživatele v Jostle, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a Jostle
> * [Jednotné přihlašování](jostle-tutorial.md) k Jostle (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uživatelský účet ve službě Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* [Tenant Jostle](https://www.jostle.me/)
* Uživatelský účet v Jostle s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování

1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
1. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Určete, jaká data se mají [mapovat mezi Azure AD a Jostle](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-jostle-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Jostle pro podporu zřizování pomocí Azure AD

### <a name="automation-account"></a>Účet Automation

Než začnete, budete muset vytvořit **uživatele Automation** v intranetu Jostle. Bude to účet, který budete používat ke konfiguraci pomocí Azure. Uživatele služby Automation je možné vytvořit v **nastavení pro správu > uživatelské účty a data > spravovat uživatele automatizace**.

Další podrobnosti o uživatelích automatizace a o tom, jak ho vytvořit, najdete v [tomto článku](https://forum.jostle.us/hc/en-us/articles/360057364073).

Po vytvoření **musí být** uživatelský účet Automation aktivovaný (tj. přihlášený do intranetu aspoň jednou), než se dá použít ke konfiguraci Azure.

### <a name="manage-user-provisioning"></a>Spravovat zřizování uživatelů

Než začnete, ujistěte se, že vaše předplatné účtu **obsahuje funkce jednotného přihlašování/zřizování uživatelů**. Pokud tomu tak není, můžete se obrátit na správce úspěšnosti zákazníků <success@jostle.me> a může vám pomoct ho přidat do svého účtu.

Dalším krokem je získání **adresy URL rozhraní** API a **klíče rozhraní API** z Jostle:

1. Přejděte do hlavní navigace a klikněte na **Nastavení správce**.
1. V části **uživatelská data do/z jiných systémů** klikněte na **Spravovat zřizování uživatelů** . Pokud tady nevidíte možnost spravovat zřizování uživatelů a ověříte, že váš účet zahrnuje jednotné přihlašování/zřizování uživatelů, obraťte <support@jostle.me> se na podporu s povoleným nastavením této stránky v nastavení správce.
1. V části **Podrobnosti rozhraní API zřizování uživatelů** přejděte do pole **základní adresa URL** , klikněte na tlačítko Kopírovat a uložte adresu URL někam, kde k ní můžete snadno získat přístup později.                                                           

   ![Zřizování](media/jostle-provisioning-tutorial/manage-user-provisioning.png)
                
1. Potom klikněte na **Přidat nový klíč**... tlačítko
1. Na následující obrazovce přejděte do pole **uživatel automatizace** a pomocí rozevírací nabídky vyberte uživatelský účet automatizace. 

   ![Účet pro integraci](media/jostle-provisioning-tutorial/select-integration-account.png)                                                                                                                                     
1. V poli **Popis klíče rozhraní API zřizování** zadejte svůj klíč jako název (tj. "Azure") a pak klikněte na tlačítko **Přidat** .

1. Po vygenerování klíče **ho nezapomeňte hned zkopírovat** a Uložit do umístění, kam jste uložili adresu URL (protože to bude jenom čas, kdy se klíč zobrazí).                                                               
1. V dalším kroku použijete **adresu URL API** a **klíč rozhraní API** ke konfiguraci integrace v Azure.
## <a name="step-3-add-jostle-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Jostle z Galerie aplikací Azure AD

Přidejte Jostle z Galerie aplikací Azure AD a začněte spravovat zřizování pro Jostle. Pokud jste již dříve Jostle nastavení pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-gallery-app.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin k Jostle je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat tak další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-jostle"></a>Krok 5. Konfigurace automatického zřizování uživatelů na Jostle 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů a skupin v aplikaci Jostle na základě přiřazení uživatelů a skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-jostle-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Jostle ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikace vyberte **Jostle**.

    ![Odkaz Jostle v seznamu aplikací](common/all-applications.png)

1. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

1.  Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Automatická karta zřizování](common/provisioning-automatic.png)

1. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta** Jostle a informace o **tajném tokenu** . Vyberte **Test připojení** , aby se služba Azure AD mohla připojit k Jostle. Pokud se připojení nepovede, ujistěte se, že má váš účet Jostle oprávnění správce, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování. Zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

1. Vyberte **Uložit**.

1. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Jostle**.

1. Zkontrolujte atributy uživatele synchronizované z Azure AD do Jostle v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Jostle pro operace aktualizace. Pokud změníte [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), budete muset zajistit, aby rozhraní Jostle API podporovalo filtrování uživatelů na základě tohoto atributu. Vyberte **Uložit** a potvrďte všechny změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |userName|Řetězec|&check;|
   |active|Logická hodnota|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |e-maily [typ EQ "osobní"]. hodnota|Řetězec|
   |e-maily [Type EQ "alternate1"]. Value|Řetězec|
   |e-maily [Type EQ "alternate2"]. Value|Řetězec|  
   |urn: IETF: parametry: SCIM: schémata: rozšíření: jostle: 2.0: uživatel: alternateEmail1Label|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: jostle: 2.0: uživatel: alternateEmail2Label |Řetězec|  

1. Pokud chcete nakonfigurovat filtry oborů, přečtěte si pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pokud chcete povolit službu Azure AD Provisioning pro Jostle, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

1. Definujte uživatele nebo skupiny, které chcete zřídit pro Jostle, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

1. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než u dalších cyklů, ke kterému dochází každých 40 minut, pokud je služba zřizování Azure AD spuštěná.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení

Po dokončení konfigurace zřizování použijte následující prostředky k monitorování nasazení:

* Pomocí [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně zřídili.
* Podívejte se na [indikátor průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
* Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete v tématu [stav zřizování aplikace v karanténě](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="more-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
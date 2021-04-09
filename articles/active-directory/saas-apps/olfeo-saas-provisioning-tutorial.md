---
title: 'Kurz: Konfigurace Olfeo SAAS pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po Olfeo SAAS.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f6b0320-dfe7-451c-8cd8-6ba7f2e40434
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2021
ms.author: Zhchia
ms.openlocfilehash: b74175c7847bb19aa9410edd613afbfe1d762d05
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105549281"
---
# <a name="tutorial-configure-olfeo-saas-for-automatic-user-provisioning"></a>Kurz: Konfigurace Olfeo SAAS pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v Olfeo SAAS a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, aby [OLFEO SaaS](https://www.olfeo.com) pomocí služby Azure AD Provisioning. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Olfeo SAAS
> * Odebrat uživatele v Olfeo SAAS, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a Olfeo SAAS
> * Zřizování skupin a členství ve skupinách v Olfeo SAAS
> * [Jednotné přihlašování](olfeo-saas-tutorial.md) k Olfeo SaaS (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uživatelský účet ve službě Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* [Tenant OLFEO SaaS](https://www.olfeo.com/).
* Uživatelský účet v Olfeo SAAS s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování

1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
1. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Určete, jaká data se mají [mapovat mezi Azure AD a OLFEO SaaS](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-olfeo-saas-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Olfeo SAAS pro podporu zřizování s Azure AD

1. Přihlaste se ke konzole správce Olfeo SAAS. 
1. Přejděte do **konfigurace > Annuaires**.
1. Vytvořte nový adresář a pak ho pojmenujte.
1. Vyberte poskytovatele **Azure** a pak kliknutím na **CR ER** uložte nový adresář. 
1. Přejděte na kartu **synchronizace** a podívejte se na **adresu URL klienta** a **tajný kód Jeton**. Tyto hodnoty se zkopírují a vloží do polí **Adresa URL tenanta** a **tajného tokenu** na kartě zřizování aplikace Olfeo SaaS v Azure Portal.

## <a name="step-3-add-olfeo-saas-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Olfeo SAAS z Galerie aplikací Azure AD

Přidejte Olfeo SAAS z Galerie aplikací Azure AD, abyste mohli začít spravovat zřizování pro Olfeo SAAS. Pokud jste již dříve nastavili Olfeo SAAS pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-gallery-app.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin k Olfeo SAAS je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat tak další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-olfeo-saas"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro Olfeo SAAS 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů a skupin v aplikaci Olfeo SAAS na základě přiřazení uživatelů a skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-olfeo-saas-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Olfeo SAAS ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikace vyberte možnost **OLFEO SaaS**.

    ![Odkaz Olfeo SAAS v seznamu aplikací](common/all-applications.png)

1. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

1.  Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Automatická karta zřizování](common/provisioning-automatic.png)

1. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta** Olfeo SaaS a informace o **tajném tokenu** . Vyberte **Test připojení** , aby se služba Azure AD mohla připojit k Olfeo SaaS. Pokud se připojení nepovede, ujistěte se, že účet Olfeo SAAS má oprávnění správce, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování. Zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

1. Vyberte **Uložit**.

1. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele a Olfeo SaaS**.

1. Zkontrolujte atributy uživatele synchronizované z Azure AD do Olfeo SAAS v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Olfeo SaaS pro operace aktualizace. Pokud změníte [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), budete muset zajistit, aby rozhraní OLFEO SaaS API podporovalo filtrování uživatelů na základě tohoto atributu. Vyberte **Uložit** a potvrďte všechny změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |userName|Řetězec|&check;|
   |displayName|Řetězec|
   |active|Logická hodnota|
   |emails[type eq "work"].value|Řetězec|
   |preferredLanguage|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |název. formátovaný|Řetězec|
   |externalId|Řetězec|  

1. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do Olfeo SaaS**.

1. Zkontrolujte atributy skupiny synchronizované z Azure AD do Olfeo SAAS v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v Olfeo SaaS pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|Podporováno pro filtrování|
      |---|---|---|
      |displayName|Řetězec|&check;|
      |externalId|Řetězec|
      |členy|Referenční informace|

1. Pokud chcete nakonfigurovat filtry oborů, přečtěte si pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pokud chcete povolit službu Azure AD Provisioning pro Olfeo SAAS, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

1. Určete uživatele nebo skupiny, které chcete zřídit pro Olfeo SAAS, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

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
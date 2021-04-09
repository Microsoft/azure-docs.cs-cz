---
title: 'Kurz: Konfigurace Gtmhub pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po Gtmhub.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 10b68d00-a544-480b-9bd6-f6ac291a90d0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2020
ms.author: Zhchia
ms.openlocfilehash: 0e160def31a43bc94e4f6151b46efe72e585e953
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735703"
---
# <a name="tutorial-configure-gtmhub-for-automatic-user-provisioning"></a>Kurz: Konfigurace Gtmhub pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v Gtmhub i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, které se [Gtmhub](https://www.gtmhub.com/) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 

>[!NOTE]
>Když se v současné době konfiguruje Automatické zřizování uživatelů, Azure AD automaticky zruší pravidla uživatelů a skupin, aby se Gtmhub, a také namapuje uživatele na příslušné týmy pomocí služby zřizování Azure AD. Ale v 2021 po povolení jednotného přihlašování s Gtmhub se uživatelé automaticky zřídí při přihlášení pomocí jednotného přihlašování a budou přiřazeni k příslušnému týmu.


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Odeberte uživatele v Gtmhub, když nepotřebují přístup už.
> * Udržujte atributy uživatele synchronizované mezi Azure AD a Gtmhub.
> * Namapujte uživatele na své týmy automaticky a zarovnejte je.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenanta Azure AD](../develop/quickstart-create-new-tenant.md)
* Uživatelský účet ve službě Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* Účet Enterprise Gtmhub

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a Gtmhub](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-gtmhub-to-support-team-mapping-and-user-de-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Gtmhub pro podporu mapování týmů a zrušení registrace uživatelů pomocí Azure AD

Aby se vaše zřizovací aplikace připojovala k vašemu účtu Gtmhub, budete muset vystavit token SCIM a zkompilovat adresu URL tenanta.

### <a name="to-issue-a-new-scim-token"></a>Postup při vydávání nového tokenu SCIM:

1. Přihlaste se ke svému **účtu Gtmhub**. Přejděte na **nastavení > konfigurační > tokeny rozhraní API**.

    ![Karta tokeny API](media/gtmhub-provisioning-tutorial/api-tokens.png)
2. Klikněte na **token vystavení** a vyberte **SCIM**. Zadejte název tokenu a klikněte na tlačítko **vygenerovat token rozhraní API** .

    ![Karta generovat tokeny](media/gtmhub-provisioning-tutorial/generate-token.png)
3. Po vygenerování tokenu ho můžete zkopírovat a použít v aplikaci pro zřizování Azure AD.

    ![Kopírovat token](media/gtmhub-provisioning-tutorial/token.png)

### <a name="to-compile-the-tenant-url"></a>Kompilace adresy URL klienta:

1. Adresa URL vašeho tenanta musí být v následujícím formátu:

    `https://app.gtmhub.com/api/v1/scim/azure/{account_id}`

2. Pokud se účet Gtmhub nachází v datacentru USA, budete muset také přidat datové centrum k adrese URL:
    
     `https://app.us.gtmhub.com/api/v1/scim/azure/{account_id}`

3. Pokud chcete ID účtu přejít na **Nastavení** , vyberte kartu **tokeny rozhraní API** a ZKOPÍRUJTE ID účtu:  ![ ID účtu.](media/gtmhub-provisioning-tutorial/account-id.png)

## <a name="step-3-add-gtmhub-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Gtmhub z Galerie aplikací Azure AD

Přidejte Gtmhub z Galerie aplikací Azure AD a začněte spravovat zřizování pro Gtmhub. Pokud jste dříve nastavili Gtmhub pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin k Gtmhub je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-gtmhub"></a>Krok 5. Konfigurace automatického zřizování uživatelů na Gtmhub 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-gtmhub-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Gtmhub ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Gtmhub**.

    ![Odkaz Gtmhub v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Automatická karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte adresu URL vašeho tenanta Gtmhub a tajný token. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Gtmhub. Pokud se připojení nepovede, ujistěte se, že má váš účet Gtmhub oprávnění správce, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Gtmhub**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Gtmhub v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Gtmhub pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), budete muset zajistit, aby rozhraní Gtmhub API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |userName|Řetězec|&check;|
   |externalId|Řetězec|&check;|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Referenční informace|

10. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro Gtmhub, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro Gtmhub, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

* S využitím [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
* Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
* Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
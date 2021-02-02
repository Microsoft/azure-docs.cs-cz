---
title: 'Kurz: Konfigurace Atea pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po Atea.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b788328b-10fd-4eaa-a4bc-909d738d8b8b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 51410bd86fa9679aea76f6d5c48f267ddec79026
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430692"
---
# <a name="tutorial-configure-atea-for-automatic-user-provisioning"></a>Kurz: Konfigurace Atea pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v Atea i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, které se [Atea](https://www.atea.com/) pomocí služby zřizování Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení uživatelů při SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Atea
> * Odebrat uživatele v Atea, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a Atea

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* Uživatelský účet v Atea s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Zjistěte, kdo bude v [rozsahu zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a Atea](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-atea-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Atea pro podporu zřizování pomocí Azure AD

Aby bylo možné nakonfigurovat intranet v aplikaci Iris tak, aby podporoval zřizování s Azure AD One, musí získat **adresu URL tenanta** a **tajný token** tím, že vyřadí e-mail [týmu podpory Atea](mailto:servicedesk@atea.dk). Tyto hodnoty se zadají do pole **token tajného klíče** a **adresy URL klienta** na kartě zřizování aplikace Atea v Azure Portal.

## <a name="step-3-add-atea-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Atea z Galerie aplikací Azure AD

Přidejte Atea z Galerie aplikací Azure AD a začněte spravovat zřizování pro Atea. Pokud jste již dříve Atea nastavení pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení do aplikace, nebo na základě atributů uživatele a skupiny. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k Atea je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat tak další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete ho řídit přiřazením jednoho nebo dvou uživatelů nebo skupin do aplikace. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-atea"></a>Krok 5. Konfigurace automatického zřizování uživatelů na Atea 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů a skupin v Atea podle přiřazení uživatelů a skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-atea-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Atea ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Atea**.

    ![Odkaz Atea v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Automatická karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** vyberte **autorizovat**. Otevře se dialogové okno přihlášení Atea v novém okně prohlížeče.

     ![Autorizovat Atea](media/atea-provisioning-tutorial/provisioning-authorize.png)

6. V dialogovém okně přihlášení k Atea se přihlaste ke svému tenantovi Atea a ověřte svoji identitu.
       
      ![Přihlašovací dialogové okno Atea](media/atea-provisioning-tutorial/atea-login.png)

7. Po dokončení kroků 5 a 6 klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Atea. Pokud se připojení nepovede, ujistěte se, že má Atea oprávnění správce, a zkuste to znovu.
        
      ![Atea test připojení](media/atea-provisioning-tutorial/test-connection.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování. A potom zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

9. Vyberte **Uložit**.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Atea**.

11. Zkontrolujte atributy uživatele synchronizované z Azure AD do Atea v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Atea pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), musíte zajistit, aby rozhraní Atea API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|Podporováno pro filtrování|
      |---|---|---|
      |userName|Řetězec|&check;|
      |active|Logická hodnota|
      |emails[type eq "work"].value|Řetězec|
      |name.givenName|Řetězec|
      |name.familyName|Řetězec|
      |název. formátovaný|Řetězec|
      |phoneNumbers[type eq "mobile"].value|Řetězec|
      |locale|Řetězec|
      |nickName|Řetězec|

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro Atea, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele a skupiny, které chcete zřídit pro Atea, výběrem příslušné hodnoty v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než u dalších cyklů, ke kterému dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

* Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně zřídili.
* Podívejte se na [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
* Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../manage-apps/check-status-user-account-provisioning.md).

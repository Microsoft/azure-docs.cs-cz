---
title: 'Kurz: Konfigurace analyzovatelné pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD, které se mají analyzovat.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 1ec33ea6-bff4-4665-bf2b-f4037ff28c09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 817b6b373f521543234cf02818cde8c4b4ba40c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100526421"
---
# <a name="tutorial-configure-parsable-for-automatic-user-provisioning"></a>Kurz: Konfigurace analyzovatelné pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v analyzovatelné i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny, které se mají [analyzovat](https://www.parsable.com/) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v analyzovatelné
> * Odebrat uživatele v analyzovatelné, pokud už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi službou Azure AD a analyzovatelné
> * Zřizování skupin a členství ve skupinách v analyzovatelné

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uživatelský účet v Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (např. správce aplikací, správce cloudových aplikací, vlastník aplikací nebo globální správce) 
* Tenanta, který je možné analyzovat (tým).
* Uživatelský účet v analyzovatelné s oprávněními správce.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a analyzovatelné](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-parsable-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace analyzovatelné pro podporu zřizování pomocí Azure AD

1. Pokud se chcete přihlásit k této předběžné verzi, obraťte se na zástupce analyzovat úspěšnost zákazníků.
2. Budou vám dál pomáhat při vyvolávání lístku podpory pro získání potřebného **tokenu nosiče** (tajného tokenu).
3. Zkopírujte a uložte **token nosiče**. Tato hodnota se zadá do pole **tajný token** * na kartě zřizování aplikace analyzovatelné v Azure Portal.

## <a name="step-3-add-parsable-from-the-azure-ad-application-gallery"></a>Krok 3. Přidat analyzovatelné z Galerie aplikací Azure AD

Přidejte analyzovatelné z Galerie aplikací Azure AD, abyste mohli začít spravovat zřizování pro analyzovatelné. Pokud jste již dříve nastavili možnost analyzovat pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin k analyzovat je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-parsable"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro analyzovatelné 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-parsable-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro možnost analyzovat v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **analyzovatelné**.

    ![Odkaz analyzovat v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Automatická karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte adresu URL klienta s možností analyzovat a tajný token. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k analyzovatelné. Pokud se připojení nepovede, ujistěte se, že váš účet analyzovat má oprávnění správce, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelů můžete analyzovat**.

9. Zkontrolujte atributy uživatelů synchronizované z Azure AD a analyzovatelné v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v možnosti analyzovatelné pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), budete muset zajistit, aby ANALYZOVATELNÉ rozhraní API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |userName|Řetězec|&check;|
   |displayName|Řetězec|

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny a analyzovatelné**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD a analyzovatelné v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v analyzovatelné pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|Podporováno pro filtrování|
      |---|---|---|
      |displayName|Řetězec|&check;|
      |členy|Referenční informace|
12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro analyzovatelné, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete analyzovat, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="change-log"></a>Protokol změn

* 02/15/2021 – zřizování skupiny je povolené.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
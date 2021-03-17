---
title: 'Kurz: zřizování uživatelů pro ThousandEyes – Azure AD'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro ThousandEyes.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: fcb53dc22cfb4bf7308b92ee76e5aaaf3bb0dead
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735081"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Kurz: Konfigurace ThousandEyes pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které musíte v ThousandEyes a Azure AD použít k automatickému zřízení a zrušení zřízení uživatelských účtů z Azure AD až ThousandEyes. 

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující:

* Tenant Azure Active Directory
* Tenant ThousandEyes se [standardním plánem](https://www.thousandeyes.com/pricing) nebo lepším povoleným 
* Uživatelský účet v ThousandEyes s oprávněními správce 

> [!NOTE]
> Integrace zřizování Azure AD spoléhá na [rozhraní THOUSANDEYES SCIM API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK), které je dostupné pro ThousandEyes týmy na standardním plánu nebo lepší.

## <a name="assigning-users-to-thousandeyes"></a>Přiřazování uživatelů k ThousandEyes

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů se synchronizují jenom uživatelé a skupiny přiřazené k aplikaci v Azure AD. 

Než nakonfigurujete a povolíte službu zřizování, musíte se rozhodnout, co uživatelé a skupiny ve službě Azure AD reprezentují uživatelé, kteří potřebují přístup k aplikaci ThousandEyes. Po rozhodnutí můžete tyto uživatele přiřadit do aplikace ThousandEyes podle pokynů uvedených tady:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Důležité tipy pro přiřazení uživatelů k ThousandEyes

* Doporučuje se, abyste k testování konfigurace zřizování přiřadili jednoho uživatele Azure AD ThousandEyes. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele k ThousandEyes, musíte vybrat buď roli **uživatele** , nebo jinou platnou roli specifickou pro aplikaci (Pokud je dostupná) v dialogovém okně přiřazení. **Výchozí přístupová** role nefunguje pro zřizování a tito uživatelé se přeskočí.

## <a name="configuring-user-provisioning-to-thousandeyes"></a>Konfigurace zřizování uživatelů na ThousandEyes 

V této části se seznámíte s připojením k rozhraní API pro zřizování uživatelských účtů ve službě Azure AD a konfigurací služby zřizování k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v ThousandEyes na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit pro ThousandEyes jednu Sign-On založenou na SAML, a to podle pokynů uvedených v [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když se tyto dvě funkce navzájem doplňují.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů na ThousandEyes ve službě Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. Pokud jste už nakonfigurovali ThousandEyes pro jednotné přihlašování, vyhledejte vaši instanci ThousandEyes pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **ThousandEyes** v galerii aplikací. Ve výsledcích hledání vyberte ThousandEyes a přidejte je do seznamu aplikací.

    ![Odkaz ThousandEyes v seznamu aplikací](common/all-applications.png)
    
3. Vyberte svou instanci ThousandEyes a pak vyberte kartu **zřizování** .

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

![Snímek obrazovky zobrazuje kartu zřizování pro ThousandEyes s automatickým vybraným pro režim zřizování.](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)
    

5. V části **přihlašovací údaje správce**  zadejte **token nosiče OAuth** generovaný účtem ThousandEyes (můžete najít nebo vygenerovat token v části **profil** účtu ThousandEyes).

    ![Snímek obrazovky ukazuje, kde najít odkaz nastavení účtu pro aktuální skupinu účtů.](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. V Azure Portal klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k vaší aplikaci ThousandEyes. Pokud se připojení nepovede, ujistěte se, že má váš účet ThousandEyes oprávnění správce, a zkuste krok 5 znovu.

7. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

8. Klikněte na **Uložit**.

9. V části mapování vyberte **synchronizovat Azure Active Directory uživatelé ThousandEyes**.

10. Zkontrolujte atributy uživatele synchronizované z Azure AD do ThousandEyes v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v možnosti analyzovatelné pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), budete muset zajistit, aby ANALYZOVATELNÉ rozhraní API podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

     |Atribut|Typ|Podporováno pro filtrování|
     |---|---|---|
     |externalId|Řetězec|&check;|
     |userName|Řetězec|&check;|
     |active|Logická hodnota|
     |displayName|Řetězec|
     |emails[type eq "work"].value|Řetězec|
     |název. formátovaný|Řetězec|


11. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Pokud chcete povolit službu Azure AD Provisioning pro ThousandEyes, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit pro ThousandEyes, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

14. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
---
title: 'Kurz: zřizování uživatelů pro ThousandEyes – Azure AD'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro ThousandEyes.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: eaf019303c311519c4b7d483d8f9193f432b8385
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278832"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Kurz: Konfigurace ThousandEyes pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které musíte v ThousandEyes a Azure AD použít k automatickému zřízení a zrušení zřízení uživatelských účtů z Azure AD až ThousandEyes. 

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující položky:

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

V této části se seznámíte s připojením k rozhraní API pro zřizování uživatelských účtů ve službě Azure AD a konfigurací služby zřizování k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v ThousandEyes na základě přiřazení uživatelů a skupin ve službě Azure AD. .

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro ThousandEyes, a to podle pokynů uvedených v tématu [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když se tyto dvě funkce navzájem doplňují.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů na ThousandEyes ve službě Azure AD

1. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Enterprise Apps > všechny aplikace** .

2. Pokud jste už nakonfigurovali ThousandEyes pro jednotné přihlašování, vyhledejte vaši instanci ThousandEyes pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **ThousandEyes** v galerii aplikací. Ve výsledcích hledání vyberte ThousandEyes a přidejte je do seznamu aplikací.

3. Vyberte svou instanci ThousandEyes a pak vyberte kartu **zřizování** .

4. Nastavte **režim zřizování** na **automaticky**.

    ![Zřizování ThousandEyes](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. V části **přihlašovací údaje správce** zadejte **token nosiče OAuth** generovaný účtem ThousandEyes (můžete najít nebo vygenerovat token v části **profil** účtu ThousandEyes).

    ![Zřizování ThousandEyes](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. V Azure Portal klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k vaší aplikaci ThousandEyes. Pokud se připojení nepovede, ujistěte se, že má váš účet ThousandEyes oprávnění správce, a zkuste krok 5 znovu.

7. Zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování v poli **e-mail s oznámením** , a zaškrtněte políčko Odeslat e-mailové oznámení, když dojde k chybě.

8. Klikněte na možnost **Uložit**.

9. V části mapování vyberte **synchronizovat Azure Active Directory uživatelé ThousandEyes**.

10. V části **mapování atributů** zkontrolujte atributy uživatelů synchronizované z Azure AD do ThousandEyes. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v ThousandEyes pro operace aktualizace. Vyberte tlačítko Uložit potvrďte změny.

11. Pokud chcete povolit službu Azure AD Provisioning pro ThousandEyes, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

12. Klikněte na možnost **Uložit**.

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin přiřazených ThousandEyes v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit, které popisují všechny akce prováděné službou zřizování.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)

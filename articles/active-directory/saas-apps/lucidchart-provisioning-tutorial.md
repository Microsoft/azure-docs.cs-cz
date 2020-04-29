---
title: 'Kurz: zřizování uživatelů pro LucidChart – Azure AD'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro LucidChart.
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
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5d946c6e257c7676178f9bc3c234f66ba6fe622
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77057324"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Kurz: Konfigurace LucidChart pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které musíte v LucidChart a Azure AD použít k automatickému zřízení a zrušení zřízení uživatelských účtů z Azure AD až LucidChart. 

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující položky:

* Tenant Azure Active Directory
* Tenant LucidChart s [plánem Enterprise](https://www.lucidchart.com/user/117598685#/subscriptionLevel) nebo lepším povoleným
* Uživatelský účet v LucidChart s oprávněními správce

## <a name="assigning-users-to-lucidchart"></a>Přiřazování uživatelů k LucidChart

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů se synchronizují jenom uživatelé a skupiny přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte službu zřizování, musíte se rozhodnout, co uživatelé a skupiny ve službě Azure AD reprezentují uživatelé, kteří potřebují přístup k aplikaci LucidChart. Po rozhodnutí můžete tyto uživatele přiřadit do aplikace LucidChart podle pokynů uvedených tady:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Důležité tipy pro přiřazení uživatelů k LucidChart

* Doporučuje se, abyste k testování konfigurace zřizování přiřadili jednoho uživatele Azure AD LucidChart. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele k LucidChart, musíte vybrat buď roli **uživatele** , nebo jinou platnou roli specifickou pro aplikaci (Pokud je dostupná) v dialogovém okně přiřazení. **Výchozí přístupová** role nefunguje pro zřizování a tito uživatelé se přeskočí.

## <a name="configuring-user-provisioning-to-lucidchart"></a>Konfigurace zřizování uživatelů na LucidChart

V této části se seznámíte s připojením k rozhraní API pro zřizování uživatelských účtů ve službě Azure AD a konfigurací služby zřizování k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v LucidChart na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro LucidChart, a to podle pokynů uvedených v tématu [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když se tyto dvě funkce navzájem doplňují.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů na LucidChart ve službě Azure AD

1. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Enterprise Apps > všechny aplikace** .

2. Pokud jste už nakonfigurovali LucidChart pro jednotné přihlašování, vyhledejte vaši instanci LucidChart pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **LucidChart** v galerii aplikací. Ve výsledcích hledání vyberte LucidChart a přidejte je do seznamu aplikací.

3. Vyberte svou instanci LucidChart a pak vyberte kartu **zřizování** .

4. Nastavte **režim zřizování** na **automaticky**.

    ![Zřizování LucidChart](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. V části **přihlašovací údaje správce** zadejte **tajný token** generovaný účtem LucidChart (token najdete pod účtem: > **SCIM**pro integraci **týmových** > **aplikací**).

    ![Zřizování LucidChart](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. V Azure Portal klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k vaší aplikaci LucidChart. Pokud se připojení nepovede, ujistěte se, že má váš účet LucidChart oprávnění správce, a zkuste krok 5 znovu.

7. Zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování v poli **e-mail s oznámením** , a zaškrtněte políčko Odeslat e-mailové oznámení, když dojde k chybě.

8. Klikněte na **Uložit**.

9. V části mapování vyberte **synchronizovat Azure Active Directory uživatelé LucidChart**.

10. V části **mapování atributů** zkontrolujte atributy uživatelů synchronizované z Azure AD do LucidChart. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v LucidChart pro operace aktualizace. Kliknutím na tlačítko Uložit potvrďte změny.

11. Pokud chcete povolit službu Azure AD Provisioning pro LucidChart, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

12. Klikněte na **Uložit**.

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin přiřazených LucidChart v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit, které popisují všechny akce prováděné službou zřizování.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

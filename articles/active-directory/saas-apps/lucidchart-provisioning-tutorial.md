---
title: 'Kurz: Zřizování uživatelů pro LucidChart – Azure AD'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a odpočatou uživatelské účty do LucidChartu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057324"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Kurz: Konfigurace přehledového grafu pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky, které je potřeba provést v LucidChart a Azure AD automaticky zřídit a de-provision uživatelské účty z Azure AD na LucidChart. 

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

* Klient adresáře Azure Active Directory
* Klient LucidChart s [plánem Enterprise](https://www.lucidchart.com/user/117598685#/subscriptionLevel) nebo lépe povolený
* Uživatelský účet v LucidChart s oprávněními správce

## <a name="assigning-users-to-lucidchart"></a>Přiřazení uživatelů k LucidChart

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů jsou synchronizováni pouze uživatelé a skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD.

Před konfigurací a povolením zřizovací služby se musíte rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci LucidChart. Jakmile se rozhodnete, můžete tyto uživatele přiřadit k aplikaci LucidChart podle pokynů zde:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Důležité tipy pro přiřazení uživatelů k LucidChart

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a LucidChart otestovat konfiguraci zřizování. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k LucidChart, musíte vybrat buď roli **uživatele** nebo jinou platnou roli specifické pro aplikaci (pokud je k dispozici) v dialogovém okně přiřazení. **Výchozí přístup** role nefunguje pro zřizování a tito uživatelé jsou přeskočeny.

## <a name="configuring-user-provisioning-to-lucidchart"></a>Konfigurace zřizování uživatelů na LucidChart

Tato část vás provede připojením azure ad k rozhraní API pro zřizování uživatelských účtů LucidChart a konfigurací zřizovací služby pro vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v LucidChart u na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování na saml pro LucidChart podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce kompliment navzájem.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů do Přehlednítabulky ve službě Azure AD

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Podnikové aplikace > všechny aplikace.**

2. Pokud jste již nakonfigurovali LucidChart pro jednotné přihlašování, vyhledejte svou instanci LucidChart pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **LucidChart** v galerii aplikací. Ve výsledcích hledání vyberte LucidChart a přidejte ho do seznamu aplikací.

3. Vyberte svou instanci LucidChart a pak vyberte kartu **Zřizování.**

4. Nastavte **režim zřizování** na **automatické**.

    ![LucidChart Zřizování](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. V části **Přihlašovací údaje správce** zadejte tajný **token** generovaný vaším účtem LucidChart (token najdete pod svým účtem: **Team** > **App Integration** > **SCIM**).

    ![LucidChart Zřizování](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. Na webu Azure Portal klikněte na **Test Connection** a ujistěte se, že se Azure AD může připojit k vaší aplikaci LucidChart. Pokud se připojení nezdaří, ujistěte se, že váš účet LucidChart má oprávnění správce a zkuste krok 5 znovu.

7. Do pole **E-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko Odeslat e-mailové oznámení, když dojde k chybě.

8. Klikněte na **Uložit**.

9. V části Mapování vyberte **Synchronizovat uživatele služby Azure Active Directory s přehledem LucidChart**.

10. V části **Mapování atributů** zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na LucidChart. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v LucidChart pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko Uložit.

11. Chcete-li povolit službu zřizování Azure AD pro LucidChart, změňte **stav zřizování** **na Zapnuto** v části **Nastavení**

12. Klikněte na **Uložit**.

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin přiřazených k LucidChart v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěna. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na protokoly aktivit zřizování, které popisují všechny akce prováděné službou zřizování.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

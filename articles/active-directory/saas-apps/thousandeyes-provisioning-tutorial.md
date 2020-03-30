---
title: 'Kurz: Zřizování uživatelů pro ThousandEyes – Azure AD'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřizovala uživatelské účty thousandeyes.
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
ms.openlocfilehash: d87cffce636146eac3e557670ffc4fb2fc34ae38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062876"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Kurz: Konfigurace ThousandEyes pro automatické zřizování uživatelů

Cílem tohoto kurzu je ukázat kroky, které je potřeba provést v ThousandEyes a Azure AD automaticky zřídit a de-zřizování uživatelských účtů z Azure AD na ThousandEyes. 

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

* Klient adresáře Azure Active Directory
* Tenant ThousandEyes s [plánem Standard](https://www.thousandeyes.com/pricing) nebo lépe povolený 
* Uživatelský účet v ThousandEyes s oprávněními správce 

> [!NOTE]
> Integrace zřizování Azure AD závisí na [rozhraní API SCIM ThousandEyes](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK), které je k dispozici týmům ThousandEyes na standardním plánu nebo lepší.

## <a name="assigning-users-to-thousandeyes"></a>Přiřazení uživatelů thousandeyes

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů jsou synchronizováni pouze uživatelé a skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD. 

Před konfigurací a povolením zřizovací služby se musíte rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci ThousandEyes. Jakmile se rozhodnete, můžete přiřadit tyto uživatele k aplikaci ThousandEyes podle pokynů zde:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Důležité tipy pro přiřazení uživatelů thousandeyes

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen thousandeyes k testování konfigurace zřizování. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele thousandeyes, musíte vybrat buď roli **uživatele** nebo jinou platnou roli specifické pro aplikaci (pokud je k dispozici) v dialogovém okně přiřazení. **Výchozí přístup** role nefunguje pro zřizování a tito uživatelé jsou přeskočeny.

## <a name="configuring-user-provisioning-to-thousandeyes"></a>Konfigurace zřizování uživatelů na ThousandEyes 

Tato část vás provede připojením azure ad k rozhraní API pro zřizování uživatelských účtů ThousandEyes a konfigurací zřizovací služby pro vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v ThousandEyes na základě přiřazení uživatelů a skupin ve službě Azure AD .

> [!TIP]
> Můžete také povolit jednotné přihlašování založené na SAML pro ThousandEyes podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce kompliment navzájem.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů na ThousandEyes ve službě Azure AD

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Podnikové aplikace > všechny aplikace.**

2. Pokud jste již nakonfigurovali ThousandEyes pro jednotné přihlašování, vyhledejte svou instanci ThousandEyes pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **ThousandEyes** v galerii aplikací. Ve výsledcích hledání vyberte ThousandEyes a přidejte je do seznamu aplikací.

3. Vyberte svou instanci ThousandEyes a pak vyberte kartu **Zřizování.**

4. Nastavte **režim zřizování** na **automatické**.

    ![TisícEyes Zřizování](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. V části Přihlašovací údaje správce zadejte **token Nosiče OAuth** generovaný vaším účtem ThousandEyes (můžete najít a nebo **vygenerovat** token v části **Profil** účtu ThousandEyes).

    ![TisícEyes Zřizování](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. Na webu Azure Portal klikněte na **Test Connection** a ujistěte se, že se Azure AD může připojit k vaší aplikaci ThousandEyes. Pokud se připojení nezdaří, ujistěte se, že váš účet ThousandEyes má oprávnění správce a zkuste krok 5 znovu.

7. Do pole **E-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko Odeslat e-mailové oznámení, když dojde k chybě.

8. Klikněte na **Uložit**.

9. V části Mapování vyberte **Synchronizovat uživatele služby Azure Active Directory s ThousandEyes**.

10. V části **Mapování atributů** zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na ThousandEyes. Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v ThousandEyes pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko Uložit.

11. Chcete-li povolit službu zřizování Azure AD pro ThousandEyes, změňte **stav zřizování** **na Zapnuto** v části **Nastavení**

12. Klikněte na **Uložit**.

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin přiřazených ThousandEyes v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěna. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na protokoly aktivit zřizování, které popisují všechny akce prováděné službou zřizování.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

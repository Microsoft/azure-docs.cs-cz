---
title: 'Kurz: Zřizování uživatelů pro slack – Azure AD'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty do Slacku.
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
ms.openlocfilehash: cdc912c2df435f9b7e591d7c5475e126e6b0aeb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062825"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Kurz: Konfigurace časovky pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky, které je potřeba provést v Slack a Azure AD automaticky zřídit a de-zřizování uživatelských účtů z Azure AD do Slack.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

* Tenanta Azure Active Directory.
* Tenant Slack s [plánem Plus](https://aadsyncfabric.slack.com/pricing) nebo lépe povolený
* Uživatelský účet ve Slacku s oprávněními správce týmu

Poznámka: Integrace zřizování Azure AD závisí na [rozhraní API Slack SCIM](https://api.slack.com/scim), které je k dispozici týmům Slack v plánu Plus nebo lepší.

## <a name="assigning-users-to-slack"></a>Přiřazení uživatelů k slacku

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů budou synchronizováni pouze uživatelé a skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD.

Před konfigurací a povolením zřizovací služby se budete muset rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci Slack. Jakmile se rozhodnete, můžete tyto uživatele přiřadit k aplikaci Slack podle pokynů zde:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Důležité tipy pro přiřazení uživatelů do Slacku

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a Slack otestovat konfiguraci zřizování. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k slacku musíte v dialogovém okně přiřazení vybrat roli **Uživatel** nebo Skupina. Role "Výchozí přístup" nefunguje pro zřizování.

## <a name="configuring-user-provisioning-to-slack"></a>Konfigurace zřizování uživatelů na Slack 

Tato část vás provede připojením azure ad k rozhraní API pro zřizování uživatelského účtu Slack a konfigurací zřizovací služby pro vytváření, aktualizaci a zakázání přiřazených uživatelských účtů ve Slacku na základě přiřazení uživatelů a skupin ve službě Azure AD.

**Tip:** Můžete se také rozhodnout povolit jednotné přihlašování na saml pro Slack podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů na Slack ve službě Azure AD:

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Podnikové aplikace > všechny aplikace.**

2. Pokud jste již nakonfigurovali Slack pro jednotné přihlašování, vyhledejte svou instanci Slack pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **Slack** v galerii aplikací. Ve výsledcích hledání vyberte Slack a přidejte ho do seznamu aplikací.

3. Vyberte svou instanci Slack a pak vyberte kartu **Zřizování.**

4. Nastavte **režim zřizování** na **automatické**.

   ![Zřizování časovky](./media/slack-provisioning-tutorial/slack1.png)

5. V části **Pověření správce** klikněte na **autorizovat**. Tím se otevře dialogové okno autorizace Slack v novém okně prohlížeče.

6. V novém okně se přihlaste ke Slacku pomocí účtu správce týmu. v dialogovém okně výsledné autorizace vyberte tým Slack, pro který chcete povolit zřizování, a pak vyberte **Autorizovat**. Po dokončení se vraťte na portál Azure a dokončete konfiguraci zřizování.

    ![Dialogové okno Autorizace](./media/slack-provisioning-tutorial/slackauthorize.png)

7. Na webu Azure Portal klikněte na **Test Connection** a ujistěte se, že se Azure AD může připojit k vaší aplikaci Slack. Pokud se připojení nezdaří, ujistěte se, že váš účet Slack má oprávnění správce týmu a zkuste krok "Autorizovat" znovu.

8. Do pole **E-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko níže.

9. Klikněte na **Uložit**.

10. V části Mapování vyberte **Synchronizovat uživatele služby Azure Active Directory s časovou rezervou**.

11. V části **Mapování atributů** zkontrolujte atributy uživatele, které budou synchronizovány z Azure AD do Slack. Všimněte si, že atributy vybrané jako **odpovídající** vlastnosti budou použity tak, aby odpovídaly uživatelským účtům v Slack pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko Uložit.

12. Chcete-li povolit službu zřizování Azure AD pro Slack, změňte **stav zřizování** **na Zapnuto** v části **Nastavení**

13. Klikněte na **Uložit**.

Tím se spustí počáteční synchronizace všech uživatelů nebo skupin přiřazených slackv části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace bude trvat déle než následné synchronizace, ke kterým dochází přibližně každých 10 minut, pokud je služba spuštěna. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavy aktivit zřizování, které popisují všechny akce prováděné službou zřizování v aplikaci Slack.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Nepovinné] Konfigurace zřizování objektů skupiny na Slack

Volitelně můžete povolit zřizování objektů skupiny z Azure AD do Slack. To se liší od "přiřazení skupin uživatelů", v tom, že skutečný objekt skupiny kromě jeho členy budou replikovány z Azure AD do Slack. Například pokud máte skupinu s názvem "Moje skupina" ve službě Azure AD, identické skupiny s názvem "Moje skupina" se vytvoří uvnitř Slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Povolení zřizování objektů skupiny:

1. V části Mapování vyberte **Synchronizovat skupiny služby Azure Active Directory s časovou rezervou**.

2. V okně Mapování atributů nastavte možnost Povoleno na Ano.

3. V části **Mapování atributů** zkontrolujte atributy skupiny, které budou synchronizovány z Azure AD do Slack. Všimněte si, že atributy vybrané jako **Odpovídající** vlastnosti budou použity tak, aby odpovídaly skupinám v Slack pro operace aktualizace. 

4. Klikněte na **Uložit**.

Výsledkem je, že všechny objekty skupiny přiřazené slackv části **Uživatelé a skupiny** jsou plně synchronizovány z Azure AD do Slack. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na protokoly zřizování aktivit, které popisují všechny akce prováděné službou zřizování v aplikaci Slack.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Při konfiguraci atributu **Slack displayName** mějte na paměti následující chování:

  * Hodnoty nejsou zcela jedinečné (např. 2 uživatelé mohou mít stejný zobrazovaný název)

  * Podporuje neanglické znaky, mezery, velká písmena. 
  
  * Povolená interpunkce zahrnuje tečky, podtržítka, pomlčky, apostrofy, závorky (např. **( [ { } )**) a oddělovače (např. **, / ;**
  
  * Aktualizace se aktualizují pouze v případě, že jsou tato dvě nastavení nakonfigurována na pracovišti nebo organizaci Slacku – **synchronizace profilu je povolena** a **uživatelé nemohou změnit svůj zobrazovaný název**.
  
* Atribut **userName** uživatele Slack musí být pod 21 znaků a musí mít jedinečnou hodnotu.

* Slack umožňuje pouze párování s atributy **userName** a **e-mail**.  

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

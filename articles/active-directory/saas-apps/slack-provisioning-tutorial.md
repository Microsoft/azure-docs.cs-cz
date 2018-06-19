---
title: 'Kurz: Konfigurace Slack pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Informace o konfiguraci Azure Active Directory automaticky zřizovat a deaktivace zřízení uživatelským účtům k systému Slack.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.reviewer: asmalser
ms.openlocfilehash: e00b9b73d7668ccae9cec85bbde30b18af2474e6
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35903163"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Kurz: Konfigurace Slack pro zřizování automatické uživatelů


Cílem tohoto kurzu je tak, aby zobrazovalo kroky je potřeba provést v systému Slack a Azure AD automaticky zřizovat a deaktivace zřízení uživatelských účtů ze služby Azure AD k systému Slack. 

## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující položky:

*   Klienta služby Azure Active Active directory
*   Systému Slack klienta s [Plus plán](https://aadsyncfabric.slack.com/pricing) nebo lépe povolena. 
*   Uživatelský účet v systému Slack s oprávněními správce Team 

Poznámka: Zřizování integrace Azure AD spoléhá na [Slack SCIM API](https://api.slack.com/scim) která je k dispozici do Slack týmů na Plus plánu nebo vyšší.

## <a name="assigning-users-to-slack"></a>Přiřazení uživatele k systému Slack

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování účtu se budou synchronizovat pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD. 

Před konfigurací a povolení zřizování služby, musíte se rozhodnout, jaké uživatelů nebo skupin ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci Slack. Jakmile se rozhodli, můžete přiřadit těmto uživatelům aplikace Slack podle pokynů tady:

[Přiřazení uživatele nebo skupiny do aplikace enterprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Důležité tipy pro přiřazení uživatele k systému Slack

*   Dále je doporučeno jednoho uživatele Azure AD pro testování zřizování konfigurace přidělí k systému Slack. Další uživatele nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k systému Slack, je nutné vybrat **uživatele** nebo role "Skupina" v dialogovém okně přiřazení. Roli "Výchozí přístup" nefunguje pro zřizování.


## <a name="configuring-user-provisioning-to-slack"></a>Konfiguraci zřizování uživatelů k systému Slack 

Tato část vás provede připojení k systému Slack na uživatelský účet zřizování rozhraní API služby Azure AD a konfiguraci zřizování služby vytvářet, aktualizovat a zakázat přiřadit uživatelské účty v systému Slack na základě uživatele a přiřazení skupiny ve službě Azure AD.

**Tip:** můžete také povolit na základě SAML jednotné přihlašování pro Slack, postupujte podle pokynů uvedených v (portál Azure) [https://portal.azure.com]. Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce doplnění navzájem.


### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Konfigurace automatického účet zřizování uživatelů k systému Slack ve službě Azure AD:


1)  V [portál Azure](https://portal.azure.com), vyhledejte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

2) Pokud jste již nakonfigurovali Slack pro jednotné přihlašování, vyhledejte instanci Slack pomocí pole hledání. Jinak vyberte možnost **přidat** a vyhledejte **Slack** v galerii aplikací. Vyberte Slack ve výsledcích hledání a přidejte ji do seznamu aplikací.

3)  Vyberte instanci systému Slack a pak vyberte **zřizování** kartě.

4)  Nastavte **režimu zřizování** k **automatické**.

![Slack zřizování](./media/slack-provisioning-tutorial/Slack1.PNG)

5)  V části **přihlašovací údaje správce** klikněte na tlačítko **Authorize**. Otevře se dialogové okno Slack autorizace v nové okno prohlížeče. 

6) V novém okně se přihlaste pomocí účtu správce Team Slack. v dialogovém okně výsledné autorizace, vyberte Slack tým, který chcete povolit zajišťování pro a pak vyberte **Authorize**. Po dokončení se vraťte k portálu Azure k dokončení konfigurace zřizování.

![Dialogové okno autorizace](./media/slack-provisioning-tutorial/Slack3.PNG)

7) Na portálu Azure klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k aplikaci Slack. Pokud se nepovede připojit, zajistěte, aby byl že váš Slack účet má oprávnění správce týmu a opakujte krok "Ověřit".

8) Zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování Chyba v **e-mailové oznámení** pole a zaškrtněte políčko níže.

9) Klikněte na **Uložit**. 

10) V části mapování vyberte **synchronizaci Azure Active Directory Users k systému Slack**.

11) V **mapování atributů** , projděte si uživatelské atributy, které se mají synchronizovat ze služby Azure AD k systému Slack. Všimněte si, že atributy vybrán jako **párování** vlastnosti se použije tak, aby odpovídaly uživatelské účty v systému Slack pro operace aktualizace. Kliknutím na tlačítko Uložit potvrzení změny.

12) Povolit zřizování služby pro Slack Azure AD, změňte **Stav zřizování** k **na** v **nastavení** části

13) Klikněte na **Uložit**. 

Tato akce spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k systému Slack v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace bude trvat déle než následné synchronizace, ke kterým dochází přibližně každých 10 minut, dokud je služba spuštěná. Můžete použít **podrobnosti synchronizace** části monitorovat průběh a odkazech zřízení sestavy aktivity, které popisují všechny akce, které provádí službu zřizování na Slack aplikace.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Nepovinné] Konfigurace skupiny objekt zřizování k systému Slack 

Volitelně můžete povolit zajišťování objektů skupiny z Azure AD k systému Slack. To se liší od "přiřazení skupiny uživatelů", v této skupině skutečné objektu kromě svých členů bude replikován z Azure AD k systému Slack. Například pokud máte skupinu s názvem "Moje skupina" ve službě Azure AD, bude vytvořen identitical skupinu s názvem "Moje skupina" uvnitř Slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Chcete-li povolit zřizování objektů skupiny:

1) V části mapování vyberte **synchronizaci skupinám Azure Active Directory k systému Slack**.

2) V okně mapování atributů nastavenou na Ano povoleno.

3) V **mapování atributů** , projděte si skupiny atributů, které se mají synchronizovat ze služby Azure AD k systému Slack. Všimněte si, že atributy vybrán jako **párování** vlastnosti se použije tak, aby odpovídaly skupiny v systému Slack pro operace aktualizace. 

4) Klikněte na **Uložit**.

Tento výsledek v žádné objekty skupiny přiřazené k systému Slack v **uživatelů a skupin** část plně synchronizovaných z Azure AD k systému Slack. Můžete použít **podrobnosti synchronizace** části monitorovat průběh a odkazech zřízení protokoly aktivity, které popisují všechny akce, které provádí službu zřizování na Slack aplikace.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](../active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>Další prostředky

* [Správa uživatelů zřizování účtu pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

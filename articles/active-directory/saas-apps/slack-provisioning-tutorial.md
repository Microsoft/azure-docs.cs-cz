---
title: 'Kurz: zřizování uživatelů pro časovou rezervu – Azure AD'
description: Naučte se, jak nakonfigurovat Azure Active Directory pro Automatické zřizování a rušení uživatelských účtů pro časovou rezervu.
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
ms.openlocfilehash: a90151679b71364d93446d1acc46a461d2a9d8f9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278172"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Kurz: Konfigurace časové rezervy pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které potřebujete k tomu, abyste v časové rezervě a Azure AD automaticky zřídili a zrušili zřizování uživatelských účtů z Azure AD až po časovou rezervu.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující položky:

* Tenanta Azure Active Directory.
* Tenant časové rezervy s [plánem plus](https://aadsyncfabric.slack.com/pricing) nebo vyšší povoleno
* Uživatelský účet v časové rezervě s oprávněními správce týmu

Poznámka: integrace zřizování Azure AD spoléhá na [rozhraní API pro časová rezervu](https://api.slack.com/scim), které je dostupné pro časové rezervy týmů na plánu plus nebo vyšší.

## <a name="assigning-users-to-slack"></a>Přiřazování uživatelů k časové rezervě

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů budou synchronizováni pouze uživatelé a skupiny, které byly přiřazeny do aplikace ve službě Azure AD.

Než nakonfigurujete a povolíte službu zřizování, budete se muset rozhodnout, co můžou uživatelé a skupiny v Azure AD zastupovat s uživateli, kteří potřebují přístup k vaší aplikaci časové rezervy. Po rozhodnutí můžete těmto uživatelům přiřadit aplikaci pro časovou rezervu podle pokynů uvedených tady:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Důležité tipy pro přiřazení uživatelů k časové rezervě

* Doporučujeme, aby se k otestování konfigurace zřizování přiřadil jeden uživatel Azure AD k časové rezervě. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k časové rezervě musíte v dialogovém okně přiřazení vybrat roli **uživatel** nebo skupina. Role výchozí přístup nefunguje pro zřizování.

## <a name="configuring-user-provisioning-to-slack"></a>Konfigurace zřizování uživatelů na časovou rezervu 

V této části se seznámíte s připojením k rozhraní API pro zřizování uživatelských účtů Azure AD s časovou rezervou a konfigurací zřizovací služby k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v časové rezervě na základě přiřazení uživatelů a skupin ve službě Azure AD.

**Tip:** Můžete se také rozhodnout povolit pro časovou rezervu jednotné přihlašování založené na SAML podle pokynů [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů pro časovou rezervu v Azure AD:

1. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Enterprise Apps > všechny aplikace** .

2. Pokud jste již nakonfigurovali časovou rezervu pro jednotné přihlašování, vyhledejte vaši instanci časové rezervy pomocí vyhledávacího pole. V opačném případě vyberte možnost **Přidat** a hledat **časovou rezervu** v galerii aplikací. Z výsledků hledání vyberte časovou rezervu a přidejte ji do seznamu aplikací.

3. Vyberte svou instanci časové rezervy a pak vyberte kartu **zřizování** .

4. Nastavte **režim zřizování** na **automaticky**.

   ![Zřizování časové rezervy](./media/slack-provisioning-tutorial/Slack1.PNG)

5. V části **přihlašovací údaje správce** klikněte na **autorizovat**. Tím se otevře dialogové okno autorizace časové rezervy v novém okně prohlížeče.

6. V novém okně se přihlaste k časové rezervě pomocí účtu správce týmu. v dialogovém okně výsledné autorizace vyberte tým časové rezervy, pro který chcete povolit zřizování, a potom vyberte **autorizovat**. Až se dokončí, vraťte se do Azure Portal a dokončete konfiguraci zřizování.

    ![Dialogové okno autorizace](./media/slack-provisioning-tutorial/Slack3.PNG)

7. V Azure Portal klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k vaší aplikaci časové rezervy. Pokud se připojení nepovede, ujistěte se, že váš účet časové rezervy má oprávnění správce týmu, a zkuste znovu provést krok autorizovat.

8. Zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování v poli **e-mail s oznámením** , a zaškrtněte políčko níže.

9. Klikněte na možnost **Uložit**.

10. V části mapování vyberte **synchronizovat Azure Active Directory uživatele s časovou rezervou**.

11. V části **mapování atributů** zkontrolujte atributy uživatele, které se budou synchronizovat ze služby Azure AD, s časovou rezervou. Všimněte si, že atributy vybrané jako **odpovídající** vlastnosti budou použity ke spárování uživatelských účtů v časové rezervě pro operace aktualizace. Vyberte tlačítko Uložit potvrďte změny.

12. Pokud chcete povolit službu Azure AD Provisioning pro časovou rezervu, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

13. Klikněte na možnost **Uložit**.

Tím se spustí počáteční synchronizace všech uživatelů nebo skupin přiřazených k časové rezervě v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace bude trvat déle než další synchronizace, ke kterým dojde přibližně každých 10 minut, pokud je služba spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavy aktivit zřizování, které popisují všechny akce prováděné službou zřizování v aplikaci časové rezervy.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>Volitelné Konfigurace zřizování skupin objektů na časovou rezervu

Volitelně můžete povolit zřizování skupin objektů z Azure AD do časové rezervy. To se liší od "přiřazování skupin uživatelů", v tom, že vlastní objekt skupiny kromě jeho členů bude replikován z Azure AD do časové rezervy. Pokud máte například skupinu s názvem "moje skupina" ve službě Azure AD, vytvoří se v rámci časové rezervy stejná skupina s názvem "moje skupina".

### <a name="to-enable-provisioning-of-group-objects"></a>Povolení zřizování skupinových objektů:

1. V části mapování vyberte možnost **synchronizovat Azure Active Directory skupiny s časovou rezervou**.

2. V okně mapování atributů nastavte možnost povoleno na Ano.

3. V části **mapování atributů** zkontrolujte atributy skupin, které se budou synchronizovat z Azure AD, s časovou rezervou. Všimněte si, že atributy vybrané jako **odpovídající** vlastnosti budou použity ke spárování skupin v časové rezervě pro operace aktualizace. 

4. Klikněte na možnost **Uložit**.

To má za následek to, že všechny objekty skupiny přiřazené k časové rezervě v části **Uživatelé a skupiny** jsou plně synchronizované z Azure AD do časové rezervy. Pomocí části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit, které popisují všechny akce prováděné službou zřizování v aplikaci pro časovou rezervu.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Při konfiguraci atributu **DisplayName** časové rezervy mějte na paměti následující chování:

  * Hodnoty nejsou zcela jedinečné (například 2 uživatelé můžou mít stejný zobrazovaný název).

  * Podporuje jiné než anglické znaky, mezery a velká písmena. 
  
  * Povolené interpunkce obsahuje tečky, podtržítka, spojovníky, apostrofy, hranaté závorky (např. **([{}])** ) a oddělovače (např. **/;** ).
  
  * Aktualizuje se jenom v případě, že jsou tato dvě nastavení nakonfigurovaná na pracovišti nebo organizaci **profilu** pracovní rezervy a **uživatelé nemůžou měnit jeho zobrazované jméno**.
  
* Atribut **uživatelského jména** časové rezervy musí být kratší než 21 znaků a mít jedinečnou hodnotu.

* Časová rezerva povoluje pouze spárování s atributy **username** a **email**.  

## <a name="additional-resources"></a>Další prostředky

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

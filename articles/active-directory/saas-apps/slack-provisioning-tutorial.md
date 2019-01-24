---
title: 'Kurz: Konfigurace Slack pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat služby Azure Active Directory tak, aby automaticky zřizovat a rušit přístup uživatelských účtů na Slack.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.reviewer: asmalser
ms.openlocfilehash: 5702f59a7924a3ca9eefae33dfcae5c3c16274d8
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54827206"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Kurz: Konfigurace Slack pro automatické zřizování uživatelů


Cílem tohoto kurzu je zobrazit kroky je třeba provést v Slack a Azure AD, aby automaticky zřizovat a rušit přístup uživatelských účtů ze služby Azure AD na Slack. 

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

*   Tenanta Azure Active Directory.
*   Slack klienta s [Plus plán](https://aadsyncfabric.slack.com/pricing) nebo lépe povoleno 
*   Uživatelský účet v Slack s oprávněními správce týmu 

Poznámka: Zřizování integrace Azure AD spoléhá na [Slack SCIM API](https://api.slack.com/scim), který je dostupný pro Slack týmy na symbol Plus plán nebo vyšší.

## <a name="assigning-users-to-slack"></a>Přiřazování uživatelů k Slack

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů se budou synchronizovat jenom uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD. 

Před konfigurací a povolení služby zřizování, je potřeba rozhodnout, jaké uživatele a/nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k aplikaci Slack. Jakmile se rozhodli, můžete přiřadit tito uživatelé aplikaci Slack podle zde uvedených pokynů:

[Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Důležité tipy pro přiřazování uživatelů k Slack

*   Dále je doporučeno jednoho uživatele Azure AD, je přiřazená Slack k otestování konfigurace zřizování. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k Slack, je nutné vybrat **uživatele** nebo role "Skupina" v dialogovém okně přiřazení. Tuto roli "Výchozí přístupu" nefunguje pro zřizování.


## <a name="configuring-user-provisioning-to-slack"></a>Konfigurace zřizování uživatelů pro Slack 

Tato část vás provede připojením služby Azure AD na Slack pro uživatelský účet rozhraní API zřizování a konfigurace služby zřizování, pokud chcete vytvořit, aktualizovat a zakázat přiřazené uživatelské účty v Slack podle přiřazení uživatelů a skupin ve službě Azure AD.

**Tip:** Můžete také povolena založené na SAML jednotného přihlašování pro Slack, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce návrzích mezi sebou.


### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Postup konfigurace automatického zřizování uživatelských účtů na Slack ve službě Azure AD:


1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

2. Pokud jste už nakonfigurovali Slack pro jednotné přihlašování, vyhledejte svoji instanci služby Slack, pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **Slack** v galerii aplikací. Ve výsledcích hledání vyberte Slack a přidat do seznamu aplikací.

3. Vyberte instanci Slack a potom **zřizování** kartu.

4. Nastavte **režim zřizování** k **automatické**.

   ![Slack zřizování](./media/slack-provisioning-tutorial/Slack1.PNG)

5. V části **přihlašovacích údajů správce** klikněte na tlačítko **Authorize**. Otevře se dialogové okno Slack autorizace v novém okně prohlížeče. 

6. V novém okně se přihlaste pomocí účtu správce týmu Slack. v dialogovém okně výsledný autorizace, vyberte Slack, který chcete povolit zajišťování pro tým a pak vyberte **Authorize**. Po dokončení vrátí k webu Azure portal k dokončení konfigurace zřizování.

   ![Dialogové okno autorizace](./media/slack-provisioning-tutorial/Slack3.PNG)

7. Na webu Azure Portal, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k aplikaci Slack. Pokud se nepovede, ujistěte se, že váš účet Slack má oprávnění správce týmu a opakujte krok "Ověřit".

8. Zadejte e-mailovou adresu osoby nebo skupiny, která má obdržet oznámení zřizování chyby v **e-mailové oznámení** pole a zaškrtněte políčko níže.

9. Klikněte na **Uložit**. 

10. V oddílu mapování, vyberte **synchronizace Azure Active Directory uživatelům Slack**.

11. V **mapování atributů** , projděte si atributy uživatele, které se budou synchronizovat ze služby Azure AD na Slack. Všimněte si, že vybrané atributy jako **odpovídající** použije vlastnosti tak, aby odpovídaly uživatelské účty v Slack pro operace update. Vyberte tlačítko Uložit potvrďte změny.

12. Služba pro Slack zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu

13. Klikněte na **Uložit**. 

Tím se spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k Slack v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace bude trvat déle než následné synchronizace, ke kterým dochází přibližně každých 10 minut za předpokladu, že služba běží. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestavy aktivit, které popisují všechny akce provedené v aplikaci Slack zřizovací služba.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Volitelné] Konfigurace skupiny objekt zřizování na Slack 

Volitelně můžete povolit zajišťování skupiny objektů ze služby Azure AD na Slack. To se liší od "přiřazení skupiny uživatelů", v této skupině skutečný objekt kromě jejích členů bude replikovat ze služby Azure AD na Slack. Například pokud máte skupinu s názvem "Moje skupina" ve službě Azure AD, se vytvoří skupinu shodné s názvem "Moje skupina" uvnitř Slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Pokud chcete povolit zřizování objektů skupiny:

1. V oddílu mapování, vyberte **synchronizaci skupinám Azure Active Directory pro Slack**.

2. V okně mapování atributu nastavenou na Ano povoleno.

3. V **mapování atributů** , projděte si skupiny atributů, které se budou synchronizovat ze služby Azure AD na Slack. Všimněte si, že vybrané atributy jako **odpovídající** vlastností se použije k odpovídající skupinám v Slack pro operace update. 

4. Klikněte na **Uložit**.

Tento výsledek v jakýchkoli objektů skupiny přiřazené k Slack v **uživatelů a skupin** části plně synchronizovány ze služby Azure AD na Slack. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokoly aktivit, které popisují všechny akce provedené v aplikaci Slack zřizovací služba.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

  * Při konfiguraci pro Slack **displayName** atribut, mějte na paměti následující chování: 
  * Hodnoty nejsou zcela jedinečné (například 2 uživatelé mohou mít stejný zobrazovaný název)
  * Podporuje jiných než anglických znaků, mezer, malá a velká písmena. 
  * Povolené obsahuje interpunkční znaménka, tečky, podtržítka, pomlčky, apostrofy, hranaté závorky (třeba **([{}])**) a oddělovače (třeba **, /;**).
  * Aktualizuje pouze pokud jsou tato dvě nastavení nakonfigurované v síti na pracovišti na Slack a organizace – **synchronizaci profilu je povolená** a **uživatelé nemohou změnit jejich zobrazovaného jména**.
  * Na Slack **uživatelské jméno** atribut musí být v části 21 znaků a mít jedinečnou hodnotu. 

## <a name="additional-resources"></a>Další prostředky

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

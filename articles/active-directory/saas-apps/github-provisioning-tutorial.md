---
title: 'Kurz: Konfigurace Githubu pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Githubu.
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
ms.openlocfilehash: 7d24e11a0361e5f01e688179680587131eedc01a
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823806"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Kurz: Konfigurace Githubu pro automatické zřizování uživatelů


Cílem tohoto kurzu je zobrazit kroky, které je třeba provést v Githubu a Azure AD automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure AD na Githubu. 

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

*   Tenanta služby Azure Active directory
*   GitHub tenantovi se [plán cloudové obchodní](https://help.github.com/articles/organization-billing-plans/#github-business-cloud) nebo lépe povoleno 
*   Uživatelský účet v Githubu s oprávněními správce 

> [!NOTE]
> Zřizování integrace Azure AD spoléhá na [rozhraní API Githubu SCIM](https://developer.github.com/v3/scim/), což je k dispozici na Githubu týmy na obchodní plán nebo vyšší.

## <a name="assigning-users-to-github"></a>Přiřazování uživatelů do Githubu

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů je synchronizovat jenom uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD. 

Před konfigurací a povolení služby zřizování, je potřeba rozhodnout, jaké uživatele a/nebo skupiny ve službě Azure AD představují, kteří potřebují přístup k vaší aplikaci Githubu. Po se rozhodli, můžete přiřadit tito uživatelé do vaší aplikace Githubu podle zde uvedených pokynů:

[Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Důležité tipy pro přiřazování uživatelů do Githubu

*   Dále je doporučeno jednoho uživatele Azure AD je přiřazen ke Githubu k otestování konfigurace zřizování. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k Githubu, je nutné vybrat buď **uživatele** roli, nebo jinou platnou specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. **Výchozího přístupu k** role nefunguje pro zřizování a tito uživatelé se přeskočí.


## <a name="configuring-user-provisioning-to-github"></a>Konfigurace zřizování uživatelů pro GitHub 

Tato část vás provede připojením služby Azure AD na Githubu uživatelský účet rozhraní API zřizování a konfigurace služby zřizování, pokud chcete vytvořit, aktualizovat a zakázat přiřazené uživatelské účty v Githubu podle přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolena založené na SAML jednotného přihlašování pro GitHub, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce návrzích mezi sebou.


### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů ke Githubu v Azure AD


1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

2. Pokud jste už nakonfigurovali GitHub pro jednotné přihlašování, vyhledejte svoji instanci služby GitHub pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **Githubu** v galerii aplikací. Ve výsledcích hledání vyberte GitHub a přidat do seznamu aplikací.

3. Vyberte instanci služby GitHub a potom **zřizování** kartu.

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování Githubu](./media/github-provisioning-tutorial/GitHub1.png)

5. V části **přihlašovacích údajů správce** klikněte na tlačítko **Authorize**. Tato operace se otevře dialogové okno autorizace Githubu v novém okně prohlížeče. 

6. V novém okně se přihlaste pomocí svého správce účtu GitHub. V dialogovém okně výsledný autorizace, vyberte tým Githubu, který chcete povolit zajišťování pro a pak vyberte **Authorize**. Po dokončení vrátí k webu Azure portal k dokončení konfigurace zřizování.

    ![Dialogové okno autorizace](./media/github-provisioning-tutorial/GitHub2.png)

7. Na webu Azure Portal, vstup **adresy URL Tenanta** a klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k aplikaci Githubu. Pokud se nepovede, ověřte účet GitHub má oprávnění správce a **adresy URl Tenanta** je detekován správně a pak opakujte krok "Ověřit" (mohou představovat **adresy URL Tenanta** pravidlem: `https://api.github.com/scim/v2/organizations/<Organization_name>` , vaše organizace najdete pod vaším účtem Githubu: **Nastavení** > **organizace**).

    ![Dialogové okno autorizace](./media/github-provisioning-tutorial/GitHub3.png)

8. Zadejte e-mailovou adresu osoby nebo skupiny, která má obdržet oznámení zřizování chyby v **e-mailové oznámení** pole a zaškrtněte políčko "Odeslat e-mailové oznámení, když dojde k chybě."

9. Klikněte na **Uložit**. 

10. V oddílu mapování, vyberte **synchronizace Azure Active Directory Users na GitHub**.

11. V **mapování atributů** , projděte si atributy uživatele, které se synchronizují ze služby Azure AD na Githubu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelských účtů ve službě GitHub pro operace update. Vyberte tlačítko Uložit potvrďte změny.

12. Povolit zřizování služby pro GitHub Azure AD, změňte **stavu zřizování** k **na** v **nastavení** oddílu

13. Klikněte na **Uložit**. 

Tato operace spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k Githubu v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokoly aktivit, které popisují všechny akce provedené v zřizovací služba.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

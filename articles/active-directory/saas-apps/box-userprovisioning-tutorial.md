---
title: 'Kurz: Konfigurace pole pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a pole.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: jeedes
ms.openlocfilehash: 3dac1d18a021c8d6c1b6e3db370c60b9aa782f1c
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51625347"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Kurz: Konfigurace pole pro automatické zřizování uživatelů

Cílem tohoto kurzu je k ilustraci kroků, že které potřebujete provést do boxu tak Azure AD a automaticky zřizovat a rušit přístup uživatelských účtů ze služby Azure AD na pole.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s polem, potřebujete následující položky:

- Klient služby Azure AD
- Pole obchodní plán nebo lepší

> [!NOTE]
> Při testování kroky v tomto kurzu doporučujeme, abyste udělali *není* použijte produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Přiřazování uživatelů k poli 

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů je synchronizovat jenom uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Před konfigurací a povolení služby zřizování, je potřeba rozhodnout, jaké uživatele a/nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k aplikaci Box. Po se rozhodli, můžete přiřadit tyto uživatele k aplikaci Box podle zde uvedených pokynů:

[Přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Přiřadit uživatele a skupiny
**Pole > Uživatelé a skupiny** karta na portálu Azure portal umožňuje určit, kteří uživatelé a skupiny by měl být udělen přístup k poli. Přiřazení uživatele nebo skupiny způsobí, že dojde k následující věci:

* Azure AD umožňuje přiřazené uživateli (buď pomocí přímého přiřazení nebo členství ve skupině) k ověření pole. Pokud uživatel není přiřazen, Azure AD je k přihlášení do pole neumožňuje a vrátí chybu na přihlašovací stránce služby Azure AD.
* Dlaždice aplikace pro pole se přidá do daného uživatele [Spouštěče aplikací](../manage-apps/end-user-experiences.md).
* Pokud je povoleno automatické zřizování, pak přiřazené uživatele a/nebo skupiny se přidají do zřizovacího fronty automaticky zřídit.
  
  * Pokud pouze uživatelské objekty byly nakonfigurovány zřídit, pak všechny přímo přiřazené uživatele jsou umístěné ve frontě zřizování a všechny uživatele, kteří jsou členy jakékoli přiřazených skupin jsou umístěné ve frontě zřizování. 
  * Pokud objekty skupiny byly nakonfigurované pro zřídí, jsou všechny objekty přiřazené skupiny zřízené do pole a všechny uživatele, kteří jsou členy těchto skupin. Skupiny a uživatele ve skupinách jsou zachovány při zapisovaných do pole.

Můžete použít **atributy > Single Sign-On** kartu Konfigurace, které atributy uživatele (nebo deklarace identity) se uživateli zobrazí pole během ověřování na základě SAML a **atributy > zřizování** záložku Nakonfigurujte, jak se atributy uživatelů a skupin toku ze služby Azure AD do pole během zřizování operace.

### <a name="important-tips-for-assigning-users-to-box"></a>Důležité tipy pro přiřazování uživatelů k poli 

*   Dále je doporučeno jednoho pole přiřazené k otestování konfigurace zřizování uživatele Azure AD. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazování uživatele do pole, musíte vybrat platné uživatelské role. Tuto roli "Výchozí přístupu" nefunguje pro zřizování.

## <a name="enable-automated-user-provisioning"></a>Povolit automatické zřizování uživatelů

Tato část provede připojení služby Azure AD k pole uživatelského účtu rozhraní API zřizování a konfigurace služby zřizování, pokud chcete vytvořit, aktualizovat a zakázat přiřazené uživatelské účty v poli podle přiřazení uživatelů a skupin ve službě Azure AD.

Pokud je povoleno automatické zřizování, pak přiřazené uživatele a/nebo skupiny se přidají do zřizovacího fronty automaticky zřídit.
    
 * Pokud pouze uživatelské objekty jsou nakonfigurované jako zřízené a pak přímo přiřazené uživatele jsou umístěné ve frontě zřizování a všechny uživatele, kteří jsou členy jakékoli přiřazených skupin jsou umístěné ve frontě zřizování. 
    
 * Pokud objekty skupiny byly nakonfigurované pro zřídí, jsou všechny objekty přiřazené skupiny zřízené do pole a všechny uživatele, kteří jsou členy těchto skupin. Skupiny a uživatele ve skupinách jsou zachovány při zapisovaných do pole.

> [!TIP] 
> Můžete také povolena založené na SAML jednotného přihlašování pro pole, podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-account-provisioning"></a>Postup konfigurace zřizování automatické uživatelských účtů:

Cílem této části se popisují, jak povolit zřizování uživatelských účtů služby Active Directory do pole.

1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

2. Pokud jste už nakonfigurovali políčko pro jednotné přihlašování, hledání instance z boxu pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **pole** v galerii aplikací. Ve výsledcích hledání vyberte pole a přidejte do seznamu aplikací.

3. Vyberte instanci pole a potom **zřizování** kartu.

4. Nastavte **režim zřizování** k **automatické**. 

    ![zřizování](./media/box-userprovisioning-tutorial/provisioning.png)

5. V části **přihlašovacích údajů správce** klikněte na tlačítko **Authorize** otevřete dialogové okno přihlášení pole v novém okně prohlížeče.

6. Na **přihlášení udělit přístup k poli** stránky, zadejte požadované přihlašovací údaje a klikněte na **Authorize**. 
   
    ![Povolit automatické zřizování uživatelů](./media/box-userprovisioning-tutorial/IC769546.png "povolit automatické zřizování uživatelů")

7. Klikněte na tlačítko **udělit přístup k poli** povolit tuto operaci a vrátit se k webu Azure portal. 
   
    ![Povolit automatické zřizování uživatelů](./media/box-userprovisioning-tutorial/IC769549.png "povolit automatické zřizování uživatelů")

8. Na webu Azure Portal, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k aplikaci Box. Pokud se nepovede, ověřte svůj účet boxu má oprávnění správce týmu a zkuste **"Ověřit"** krok znovu.

9. Zadejte e-mailovou adresu osoby nebo skupiny, která má obdržet oznámení zřizování chyby v **e-mailové oznámení** pole a zaškrtněte políčko.

10. Klikněte na tlačítko **uložit.**

11. V oddílu mapování, vyberte **synchronizace Azure Active Directory Users na pole.**

12. V **mapování atributů** , projděte si atributy uživatele, které se synchronizují ze služby Azure AD do pole. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty do pole pro operace update. Vyberte tlačítko Uložit potvrďte změny.

13. Chcete-li povolit služba pro pole zřizování Azure AD, změňte **stavu zřizování** k **na** v sekci nastavení

14. Klikněte na tlačítko **uložit.**

Která spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k poli v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokoly aktivit, které popisují všechny akce provedené v aplikaci Box zřizovací služba.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

Ve vašem tenantovi pole synchronizovaní uživatelé patří **spravovaných uživatelů** v **konzoly pro správu**.

![Stav integrace](./media/box-userprovisioning-tutorial/IC769556.png "stav integrace")


## <a name="additional-resources"></a>Další zdroje informací:

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](box-tutorial.md)

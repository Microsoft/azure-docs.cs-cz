---
title: 'Kurz: Konfigurace Velpic pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Velpic.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 059ce3a23a9bdacfb978ccad775c7da853772e3f
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344686"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Kurz: Konfigurace Velpic pro automatické zřizování uživatelů


Cílem tohoto kurzu je zobrazit kroky, které je potřeba provést Velpic a Azure AD a automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure AD do Velpic. 

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

*   Tenanta Azure Active Directory.
*   Velpic tenantovi se [podnikový plán](https://www.velpic.com/pricing.html) nebo lépe povoleno 
*   Uživatelský účet v Velpic s oprávněními správce 

## <a name="assigning-users-to-velpic"></a>Přiřazování uživatelů k Velpic

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů se budou synchronizovat jenom uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD. 

Před konfigurací a povolení služby zřizování, je potřeba rozhodnout, jaké uživatele a/nebo skupiny ve službě Azure AD, kteří potřebují přístup k vaší aplikaci Velpic představují. Po se rozhodli, můžete přiřadit tito uživatelé do vaší aplikace Velpic podle zde uvedených pokynů:

[Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Důležité tipy pro přiřazování uživatelů k Velpic

*   Dále je doporučeno jednoho uživatele Azure AD pro Velpic přidělí k otestování konfigurace zřizování. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k Velpic, je nutné vybrat buď **uživatele** roli, nebo jinou platnou specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Všimněte si, že **výchozího přístupu k** role nefunguje pro zřizování a tito uživatelé se přeskočí.


## <a name="configuring-user-provisioning-to-velpic"></a>Konfigurace zřizování uživatelů pro Velpic 

Tato část vás provede připojení služby Azure AD k Velpic pro uživatelský účet rozhraní API zřizování a konfigurace služby zřizování vytvářet, aktualizovat a vypnout přiřadit uživatelské účty v Velpic na základě uživatele a přiřazení skupiny ve službě Azure AD.

>[!TIP]
>Můžete také pro Velpic povoleno založené na SAML jednotného přihlašování, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce návrzích mezi sebou.


### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Postup konfigurace automatického zřizování uživatelských účtů do Velpic ve službě Azure AD:

1.  V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

2. Pokud jste už nakonfigurovali Velpic pro jednotné přihlašování, vyhledejte svoji instanci služby Velpic pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **Velpic** v galerii aplikací. Ve výsledcích hledání vyberte Velpic a přidat do seznamu aplikací.

3.  Vyberte instanci Velpic a potom **zřizování** kartu.

4.  Nastavte **režim zřizování** k **automatické**.

    ![Velpic zřizování](./media/velpic-provisioning-tutorial/Velpic1.png)

5.  V části **přihlašovacích údajů správce** části, zadejte **URL klienta a tajný klíč tokenu** z Velpic. () Tyto hodnoty můžete najít v rámci vašeho účtu Velpic: **Manage** > **Integration** > **Plugin** > **SCIM**)

    ![Hodnoty autorizace](./media/velpic-provisioning-tutorial/Velpic2.png)

6. Na webu Azure Portal, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k aplikaci Velpic. Pokud se nepovede, ujistěte se, že váš účet Velpic má oprávnění správce a opakujte krok 5.

7. Zadejte e-mailovou adresu osoby nebo skupiny, která má obdržet oznámení zřizování chyby v **e-mailové oznámení** pole a zaškrtněte políčko níže.

8. Klikněte na **Uložit**. 

9. V oddílu mapování, vyberte **synchronizace Azure Active Directory uživatelům Velpic**.

10. V **mapování atributů** , projděte si atributy uživatele, které se budou synchronizovat ze služby Azure AD na Velpic. Všimněte si, že vybrané atributy jako **odpovídající** použije vlastnosti tak, aby odpovídaly uživatelské účty v Velpic pro operace update. Vyberte tlačítko Uložit potvrďte změny.

11. Služba pro Velpic zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu

12. Klikněte na **Uložit**. 

Tím se spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k Velpic v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace bude trvat déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestavy aktivit, které popisují všechny akce provedené v zřizovací služba.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)
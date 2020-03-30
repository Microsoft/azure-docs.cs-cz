---
title: 'Kurz: Konfigurace velpicu pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a odpočitelně zřizovala uživatelské účty ve službě Velpic.
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
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b7a6c2c9b7ecb0b160f7481d95f7682f3f7a109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064117"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Kurz: Konfigurace velpic pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky, které je potřeba provést ve Velpic a Azure AD automaticky zřídit a de-zřizování uživatelských účtů z Azure AD na Velpic.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

* Tenanta Azure Active Directory.
* Velpic tenant s [plánem Enterprise](https://www.velpic.com/pricing.html) nebo lépe povolené
* Uživatelský účet ve Velpicu s oprávněními správce

## <a name="assigning-users-to-velpic"></a>Přiřazení uživatelů k Velpic

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů budou synchronizováni pouze uživatelé a skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD. 

Před konfigurací a povolením zřizovací služby se budete muset rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci Velpic. Jakmile se rozhodnete, můžete tyto uživatele přiřadit k aplikaci Velpic podle pokynů zde:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Důležité tipy pro přiřazení uživatelů do Velpic

* Doporučuje se, aby jeden uživatel Azure AD přiřazenvElpic otestovat konfiguraci zřizování. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k velpic, musíte vybrat buď roli **uživatele** nebo jinou platnou roli specifické pro aplikaci (pokud je k dispozici) v dialogovém okně přiřazení. Všimněte si, že **výchozí přístup** role nefunguje pro zřizování a tito uživatelé budou přeskočeni.

## <a name="configuring-user-provisioning-to-velpic"></a>Konfigurace zřizování uživatelů na Velpic

Tato část vás provede připojením azure ad k rozhraní API pro zřizování uživatelského účtu Velpic a konfigurací zřizovací služby pro vytváření, aktualizaci a zakázání přiřazených uživatelských účtů ve Velpicu na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování na saml pro Velpic podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů pro Velpic ve službě Azure AD:

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Podnikové aplikace > všechny aplikace.**

2. Pokud jste již nakonfigurovali Velpic pro jednotné přihlašování, vyhledejte svou instanci Velpic pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a **vyhledejte Velpic** v galerii aplikací. Ve výsledcích hledání vyberte Velpic a přidejte ho do seznamu aplikací.

3. Vyberte svou instanci Velpic a pak vyberte kartu **Zřizování.**

4. Nastavte **režim zřizování** na **automatické**.

    ![Velpic Zřizování](./media/velpic-provisioning-tutorial/Velpic1.png)

5. V části **Přihlašovací údaje správce** zadejte adresu URL **klienta&tajný token** Velpic. (Tyto hodnoty najdete pod svým účtem Velpic: **Správa** > **integration** > **pluginu** > **SCIM)**

    ![Hodnoty autorizace](./media/velpic-provisioning-tutorial/Velpic2.png)

6. Na webu Azure Portal klikněte na **Test Connection** a ujistěte se, že se Azure AD může připojit k vaší aplikaci Velpic. Pokud se připojení nezdaří, ujistěte se, že váš účet Velpic má oprávnění správce a zkuste krok 5 znovu.

7. Do pole **E-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko níže.

8. Klikněte na **Uložit**.

9. V části Mapování vyberte **Synchronizovat uživatele služby Azure Active Directory s velpic**.

10. V části **Mapování atributů** zkontrolujte atributy uživatele, které budou synchronizovány z Azure AD do Velpic. Všimněte si, že atributy vybrané jako **odpovídající** vlastnosti budou použity tak, aby odpovídaly uživatelským účtům ve Velpic pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko Uložit.

11. Chcete-li povolit službu zřizování Azure AD pro Velpic, změňte **stav zřizování** **na Zapnuto** v části **Nastavení**

12. Klikněte na **Uložit**.

Tím se spustí počáteční synchronizace všech uživatelů nebo skupin přiřazených velpic v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace bude trvat déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěna. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavy aktivit zřizování, které popisují všechny akce prováděné službou zřizování.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
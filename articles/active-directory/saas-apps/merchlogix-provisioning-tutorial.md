---
title: 'Kurz: Konfigurace merchLogix u automatického zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřaštila a zřaštila uživatelské účty do merchLogixu.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 9df4c7c5-9a58-478e-93b7-2f77aae12807
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4aa60fb565552961a3c85346c39c318a90c8adc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061290"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Kurz: Konfigurace merchLogix pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v MerchLogix a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-provision uživatelů nebo skupin merchLogix.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* Tenant MerchLogix
* Technický kontakt společnosti MerchLogix, který může poskytnout adresu URL koncového bodu SCIM a tajný token požadovaný pro zřizování uživatelů

## <a name="adding-merchlogix-from-the-gallery"></a>Přidání MerchLogix z galerie

Před konfigurací MerchLogix pro automatické zřizování uživatelů pomocí Azure AD, je potřeba přidat MerchLogix z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat MerchLogix z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.** 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikových aplikací** > **Všechny aplikace**.

    ![Oddíl Podnikové aplikace][2]

3. Chcete-li přidat MerchLogix, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace][3]

4. Do vyhledávacího pole zadejte **MerchLogix**.

5. V panelu výsledků vyberte **MerchLogix**a klepnutím na tlačítko **Přidat** přidejte MerchLogix do seznamu aplikací SaaS.

    ![MerchLogix Zřizování][4]

## <a name="assigning-users-to-merchlogix"></a>Přiřazení uživatelů ke společnosti MerchLogix

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD. 

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k MerchLogix. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny MerchLogix podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Důležité tipy pro přiřazení uživatelů k MerchLogix

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a MerchLogix otestovat počáteční automatické zřizování konfigurace uživatele. Další uživatelé nebo skupiny mohou být přiřazeny později, jakmile jsou testy úspěšné.

* Při přiřazování uživatele ke společnosti MerchLogix je nutné v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Konfigurace automatického zřizování uživatelů na MerchLogix 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v MerchLogix na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování založené na SAML pro MerchLogix podle pokynů uvedených v [kurzu jednotného přihlášení MerchLogix](merchlogix-tutorial.md). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro MerchLogix ve službě Azure AD:

1. Přihlaste se na [portál Azure portal](https://portal.azure.com) a vyhledejte aplikace Azure Active Directory > Enterprise > všechny **aplikace**.

2. V seznamu aplikací SaaS vyberte MerchLogix.

3. Vyberte kartu **Zřizování.**

4. Nastavte **režim zřizování** na **automatické**.

    ![MerchLogix Zřizování](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. V části **Přihlašovací údaje správce:**

    * Do pole **Adresa URL klienta** zadejte adresu URL koncového bodu SCIM poskytovo technickým kontaktem MerchLogix.

    * Do pole **Tajný token** zadejte tajný token poskytnutý technickým kontaktem MerchLogix.

6. Po naplnění polí uvedených v kroku 5 klikněte na **test připojení** a ujistěte se, že Azure AD můžete připojit k MerchLogix. Pokud se připojení nezdaří, ujistěte se, že váš účet MerchLogix má oprávnění správce a zkuste to znovu.

7. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

8. Klikněte na **Uložit**.

9. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s merchLogix**.

10. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na MerchLogix v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v MerchLogix pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

11. V části **Mapování** vyberte **Synchronizovat skupiny služby Azure Active Directory s merchLogix**.

12. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na MerchLogix v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v MerchLogix pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

13. Chcete-li povolit službu zřizování Azure AD pro MerchLogix, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

14. Až budete připraveni k zřízení, klikněte na **Uložit**.

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na MerchLogix.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png

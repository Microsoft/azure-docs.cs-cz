---
title: 'Kurz: Konfigurace bitaBIZ pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty do BitaBIZ.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d0d38abe-c041-482a-9d3f-ca340678c226
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: ad9176614c4a5235e5138444d4197286204a747f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059179"
---
# <a name="tutorial-configure-bitabiz-for-automatic-user-provisioning"></a>Kurz: Konfigurace bitaBIZ pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v BitaBIZ a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-provision uživatelů a/nebo skupin bitaBIZ.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Tenanta Azure AD.
* [Tenant BitaBIZ](https://bitabiz.dk/en/price/).
* Uživatelský účet v BitaBIZ s oprávněními správce.

## <a name="assigning-users-to-bitabiz"></a>Přiřazení uživatelů k BitaBIZ

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k BitaBIZ. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny BitaBIZ podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-bitabiz"></a>Důležité tipy pro přiřazení uživatelů k BitaBIZ

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k BitaBIZ k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k BitaBIZ, musíte vybrat všechny platné role specifické pro aplikaci (pokud jsou k dispozici) v dialogovém okně přiřazení. Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="setup-bitabiz-for-provisioning"></a>Nastavení BitaBIZ pro zřizování

Před konfigurací BitaBIZ pro automatické zřizování uživatelů s Azure AD, budete muset povolit zřizování SCIM na BitaBIZ.

1. Přihlaste se do [konzole BitaBIZ Admin Console](https://www.bitabiz.com/login?lang=en). Klikněte na **SETUP ADMIN**.

    ![Konzola pro správu BitaBIZ](media/bitabiz-provisioning-tutorial/setup-admin.png)

2.  Přejděte na **integraci**.

    ![Konzola pro správu BitaBIZ](media/bitabiz-provisioning-tutorial/integration.png)

2.  Přejděte na **Zřizování AD Microsoft Azure**.  V možnosti Automatické zřizování uživatelů vyberte **Možnost Povoleno.** Zkopírujte hodnoty adresy **URL koncového bodu zřizování SCIM** a **tokenu nosiče**. Tyto hodnoty se zanesou do polí ADRESA URL klienta a tajný token na kartě Zřizování aplikace BitaBIZ na webu Azure Portal.

    ![BitaBIZ Přidat SCIM](media/bitabiz-provisioning-tutorial/authentication.png)


## <a name="add-bitabiz-from-the-gallery"></a>Přidat BitaBIZ z galerie

Chcete-li nakonfigurovat BitaBIZ pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat BitaBIZ z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat BitaBIZ z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **BitaBIZ**, vyberte **BitaBIZ** v panelu výsledků a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![BitaBIZ v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-bitabiz"></a>Konfigurace automatického zřizování uživatelů na BitaBIZ 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v BitaBIZ na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování na bázi SAML pro BitaBIZ podle pokynů uvedených v [kurzu pro jednotné přihlášení BitaBIZ](BitaBIZ-tutorial.md). Jednotné přihlašování lze konfigurovat nezávisle na automatickézřivací službě uživatelů, i když tyto dvě funkce se vzájemně doplňují

### <a name="to-configure-automatic-user-provisioning-for-bitabiz-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro BitaBIZ ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **BitaBIZ**.

    ![Odkaz BitaBIZ v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části Přihlašovací údaje správce zadejte **adresu URL koncového bodu zřizování SCIM** a hodnoty **tokenu nosiče** načtené dříve v adrese URL klienta a tajném tokenu. Klikněte na **test připojení** a ujistěte se, že Azure AD můžete připojit k BitaBIZ. Pokud se připojení nezdaří, ujistěte se, že váš účet BitaBIZ má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s BitaBIZ**.

    ![Mapování uživatelů BitaBIZ](media/bitabiz-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do BitaBIZ v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v BitaBIZ pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy BitaBIZ](media/bitabiz-provisioning-tutorial/user-attribute.png)


10. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Chcete-li povolit službu zřizování Azure AD pro BitaBIZ, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

12. Definujte uživatele a/nebo skupiny, které chcete zřídit BitaBIZ výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

13. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na BitaBIZ.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* BitaBIZ vyžaduje jako povinné atributy **uživatelské jméno**, **e-mail**, **firstName** a **lastName.** 
* BitaBIZ nepodporuje pevné mazání v současné době.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování](../app-provisioning/check-status-user-account-provisioning.md).

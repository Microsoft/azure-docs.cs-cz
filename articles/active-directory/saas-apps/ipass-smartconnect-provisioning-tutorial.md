---
title: 'Kurz: Konfigurace aplikace iPass SmartConnect pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat Službu Azure Active Directory tak, aby automaticky zřašovala a odpojila uživatelské účty pro iPass SmartConnect.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 43d6de4a-b3a2-439b-95f2-8883fffded52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: fb3a5d03f390b88f9856f03bdc97a35b845874ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057513"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>Kurz: Konfigurace aplikace iPass SmartConnect pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které je třeba provést v iPass SmartConnect a Azure Active Directory (Azure AD) nakonfigurovat Azure AD tak, aby automaticky zřizování a de-zřizování uživatelů nebo skupin pro iPass SmartConnect.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Tenanta Azure AD.
* [Tenant iPass SmartConnect](https://www.ipass.com/buy-ipass/).
* Uživatelský účet v iPass SmartConnect s oprávněními správce.

## <a name="assigning-users-to-ipass-smartconnect"></a>Přiřazení uživatelů k iPass SmartConnect

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k iPass SmartConnect. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny k iPass SmartConnect podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>Důležité tipy pro přiřazení uživatelů do aplikace iPass SmartConnect

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k iPass SmartConnect k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k aplikaci iPass SmartConnect musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="setup-ipass-smartconnect-for-provisioning"></a>Nastavení iPass SmartConnect pro zřizování

Před konfigurací iPass SmartConnect pro automatické zřizování uživatelů pomocí Azure AD budete muset načíst informace o konfiguraci z admin konzole iPass SmartConnect:

1. Chcete-li získat nosný token, který je potřebný k ověření proti vašemu koncovému bodu iPass SmartConnect SCIM, podívejte se na první čas, kdy jste nastavili iPass SmartConnect, protože tato hodnota je poskytována pouze tehdy. 
2. Pokud nemáte žeton nosiče, oslovte [tým podpory iPass SmartConnect,](mailto:help@ipass.com) abyste získali nový.

## <a name="add-ipass-smartconnect-from-the-gallery"></a>Přidání iPass SmartConnect z galerie

Chcete-li nakonfigurovat iPass SmartConnect pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat iPass SmartConnect z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat iPass SmartConnect z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **iPass SmartConnect**, vyberte **iPass SmartConnect** v panelu výsledků a pak klikněte na tlačítko **Přidat** a přidejte aplikaci.

    ![iPass SmartConnect v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>Konfigurace automatického zřizování uživatelů pro iPass SmartConnect 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v iPass SmartConnect na základě přiřazení uživatelů a/nebo skupin ve službě Azure AD.

> [!TIP]
>  Můžete se také rozhodnout povolit jednotné přihlašování na bázi SAML pro BitaBIZ podle pokynů uvedených v [tutoriálu iPass SmartConnect Single sign-on](ipasssmartconnect-tutorial.md). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro iPass SmartConnect v Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **iPass SmartConnect**.

    ![Odkaz iPass SmartConnect v seznamu Aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://openmobile.ipass.com/moservices/scim/v1` zadejte adresu **URL klienta**. Zadejte žeton nosiče načtený dříve v **tajném tokenu**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k iPass SmartConnect. Pokud se připojení nezdaří, ujistěte se, že váš účet iPass SmartConnect má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části Mapování vyberte **Synchronizovat uživatele služby Azure Active Directory s iPass SmartConnect**. **Mappings**

    ![Mapování uživatelů aplikace iPass SmartConnect](media/ipass-smartconnect-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na iPass SmartConnect v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v iPass SmartConnect pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Mapování uživatelů aplikace iPass SmartConnect](media/ipass-smartconnect-provisioning-tutorial/userattribute.png)


10. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Chcete-li povolit službu zřizování Azure AD pro iPass SmartConnect, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

12. Definujte uživatele a /nebo skupiny, které chcete zřídit iPass SmartConnect výběrem požadovaných hodnot v **rozsahu** v sekci **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

13. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na iPass SmartConnect.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* iPass SmartConnect přijímá pouze uživatelská jména, která mají své domény registrované v admin konzoli iPass SmartConnect.  

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

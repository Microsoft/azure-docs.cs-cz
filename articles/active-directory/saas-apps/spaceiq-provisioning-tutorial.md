---
title: 'Kurz: Konfigurace rozhraní SpaceIQ pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřizovala uživatelské účty do služby SpaceIQ.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 207c8214-6304-4687-afc6-61562f0041a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: e59e9d86f394104752ef966b2a9cad2b78a1bc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062757"
---
# <a name="tutorial-configure-spaceiq-for-automatic-user-provisioning"></a>Kurz: Konfigurace rozhraní SpaceIQ pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v SpaceIQ a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-provision uživatelů nebo skupin spaceIQ.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Nájemce SpaceIQ](https://spaceiq.com/)
* Uživatelský účet v SpaceIQ s oprávněními správce.

## <a name="assigning-users-to-spaceiq"></a>Přiřazení uživatelů k SpaceIQ

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k SpaceIQ. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny SpaceIQ podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-spaceiq"></a>Důležité tipy pro přiřazení uživatelů k SpaceIQ

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k SpaceIQ k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k programu SpaceIQ musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="set-up-spaceiq-for-provisioning"></a>Nastavit SpaceIQ pro zřizování

1. Přihlaste se do [konzole SpaceIQ Admin Console](https://main.spaceiq.com/login/). Přejděte do **nastavení** tak, že ho vyberete z rozbalovací nabídky v pravém horním rohu obrazovky.

    ![Konzola spaceIQ Admin](media/spaceiq-provisioning-tutorial/admin.png)

2.  Na stránce **Nastavení** vyberte **integrace třetích stran**.

    ![SpaceIQ Přidat SCIM](media/spaceiq-provisioning-tutorial/thirdparty.png)

3.  Přejděte na kartu **Zřizování a přiřazování** k šaše. **Azure** Klikněte na **Aktivovat**.

    ![SpaceIQ Zřizování a sondování](media/spaceiq-provisioning-tutorial/provisioning.png)

    ![SpaceIQ Aktivace Azure ](media/spaceiq-provisioning-tutorial/azure.png)

3.  Zkopírujte **token nosiče SCIM**. Tato hodnota se zadá do pole Tajný token na kartě Zřizování aplikace SpaceIQ na webu Azure Portal. Klikněte na **Aktivovat.**

    ![SpaceIQ vytvořit token](media/spaceiq-provisioning-tutorial/token.png)

## <a name="add-spaceiq-from-the-gallery"></a>Přidat SpaceIQ z galerie

Před konfigurací SpaceIQ pro automatické zřizování uživatelů pomocí Azure AD, je třeba přidat SpaceIQ z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat SpaceIQ z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SpaceIQ**, vyberte **SpaceIQ** v panelu výsledků a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![SpaceIQ v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-spaceiq"></a>Konfigurace automatického zřizování uživatelů do SpaceIQ 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v programu SpaceIQ na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování pro SpaceIQ založené na SAML podle pokynů uvedených v [kurzu jednotného přihlašování SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-tutorial). Jednotné přihlašování lze konfigurovat nezávisle na automatickézřivací službě uživatelů, i když tyto dvě funkce se vzájemně doplňují

### <a name="to-configure-automatic-user-provisioning-for-spaceiq-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro SpaceIQ ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **SpaceIQ**.

    ![Odkaz SpaceIQ v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://api.spaceiq.com/scim` zadejte adresu **URL klienta**. Zadejte hodnotu **ověřovacího tokenu SCIM** načtenou dříve v **tokenu tajného klíče**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k SpaceIQ. Pokud se připojení nezdaří, ujistěte se, že váš účet SpaceIQ má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s programem SpaceIQ**.

    ![Mapování uživatelů SpaceIQ](media/spaceiq-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do SpaceIQ v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v SpaceIQ pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy SpaceIQ](media/spaceiq-provisioning-tutorial/userattributes.png)

11. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Chcete-li povolit službu zřizování Azure AD pro SpaceIQ, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit spaceiq výběrem požadovaných hodnot v **scope** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

14. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na SpaceIQ.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
---
title: 'Kurz: Konfigurace aplikace Promapp pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat Službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty pro Promapp.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 41190ba0-9032-4725-9d2c-b3dd9c7786e6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: Zhchia
ms.openlocfilehash: 1a4a27846196e7eb134b834647b2a2d65672f385
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060998"
---
# <a name="tutorial-configure-promapp-for-automatic-user-provisioning"></a>Kurz: Konfigurace aplikace Promapp pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Promapp a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-provision uživatelů nebo skupin pro Promapp.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Tenant Promapp](https://www.promapp.com/licensing/)
* Uživatelský účet v Promappu s oprávněními správce.

## <a name="assigning-users-to-promapp"></a>Přiřazení uživatelů k Promappu

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Promappu. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny pro promapp podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-promapp"></a>Důležité tipy pro přiřazení uživatelů k Promappu

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k Promapp otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k Aplikaci Promapp musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="setup-promapp-for-provisioning"></a>Nastavení promapp pro zřizování

1. Přihlaste se do [konzole Promapp Admin Console](https://freetrial.promapp.com/axelerate/Login.aspx). Pod uživatelským jménem přejděte na **Můj profil**.

    ![Konzola pro správu Promapp](media/promapp-provisioning-tutorial/admin.png)

2.  V části **Přístupové tokeny** klikněte na tlačítko **Vytvořit token.**

    ![Promapp Přidat SCIM](media/promapp-provisioning-tutorial/addtoken.png)

3.  Do pole **Popis** zadejte libovolný název a v rozevírací nabídce **Obor** vyberte **Scim.** Klikněte na ikonu uložit.

    ![Promapp Přidat jméno](media/promapp-provisioning-tutorial/addname.png)

4.  Zkopírujte přístupový token a uložte jej, protože to bude jediný čas, kdy jej můžete zobrazit. Tato hodnota se zadá do pole Tajný token na kartě Zřizování vaší aplikace Promapp na webu Azure Portal.

    ![Promapp vytvořit token](media/promapp-provisioning-tutorial/token.png)

## <a name="add-promapp-from-the-gallery"></a>Přidat Promapp z galerie

Před konfigurací Promapp pro automatické zřizování uživatelů s Azure AD, je potřeba přidat Promapp z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Promapp z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Promapp**, vyberte **Promapp** v panelu výsledků a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![Promapp v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-promapp"></a>Konfigurace automatického zřizování uživatelů pro Promapp 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Aplikaci Promapp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování pro Promapp založené na SAML podle pokynů uvedených v [kurzu pro jednotné přihlášení Promapp](https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-tutorial). Jednotné přihlašování lze konfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce se vzájemně doplňují.

### <a name="to-configure-automatic-user-provisioning-for-promapp-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Promapp ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Promapp**.

    ![Odkaz Promapp v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://api.promapp.com/api/scim` zadejte adresu **URL klienta**. Zadejte hodnotu **ověřovacího tokenu SCIM** načtenou dříve v **tokenu tajného klíče**. Klikněte na **Testovat připojení** a ujistěte se, že Azure AD můžete připojit k Promapp. Pokud se připojení nezdaří, ujistěte se, že váš účet Promapp má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s aplikací Promapp**.

    ![Mapování uživatelů Promapp](media/promapp-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Promapp v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Promapp pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy Aplikace Promapp](media/promapp-provisioning-tutorial/userattributes.png)

11. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Chcete-li povolit službu zřizování Azure AD pro Promapp, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

13. Definujte uživatele a/nebo skupiny, které chcete zřídit pro promapp výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

14. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Promappu.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)


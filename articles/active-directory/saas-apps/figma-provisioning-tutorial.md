---
title: 'Kurz: Konfigurace automatického zřizování uživatelů Figma pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřizovala uživatelské účty pro Figmu.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: a50f1c81f5eda78ee6834aba3085f685c197b4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057941"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Kurz: Konfigurace figma pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Figma a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-zřizování uživatelů nebo skupin figma.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Tenanta Azure AD.
* [Figma nájemce](https://www.figma.com/pricing/).
* Uživatelský účet ve službě Figma s oprávněními správce.

## <a name="assign-users-to-figma"></a>Přiřaďte uživatele k Figmě.
Azure Active Directory používá koncept s názvem přiřazení k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Figmě. Jakmile se rozhodnete, můžete těmto uživatelům a/nebo skupinám přiřadit figma podle pokynů zde:
 
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Důležité tipy pro přiřazení uživatelů k Figma

 * Doporučuje se, aby jeden uživatel Azure AD je přiřazen a Figma otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k figmě musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí Výchozí přístup jsou z zřizování vyloučeni.

## <a name="set-up-figma-for-provisioning"></a>Nastavit Figma pro zřizování

Před konfigurací Figma pro automatické zřizování uživatelů s Azure AD, budete muset načíst některé informace zřizování z Figma.

1. Přihlaste se do [konzole Figma Admin Console](https://www.Figma.com/). Klikněte na ikonu ozubeného kola vedle svého tenanta.

    ![FigmaFigma-zaměstnanec-poskytování](media/Figma-provisioning-tutorial/image0.png)

2. Přejděte v nastavení do **obecného protokolu aktualizací >**.

    ![FigmaFigma-zaměstnanec-poskytování](media/Figma-provisioning-tutorial/figma03.png)

3. Zkopírujte **ID klienta**. Tato hodnota se použije k vytvoření adresy URL koncového bodu SCIM, která se zadá do pole **ADRESA URL klienta** na kartě Zřizování vaší aplikace Figma na portálu Azure.

    ![Figma vytvořit token](media/Figma-provisioning-tutorial/figma-tenantid.png)

4. Posuňte se dolů a klikněte na **generovat token rozhraní API**.

    ![Figma vytvořit token](media/Figma-provisioning-tutorial/token.png)

5. Zkopírujte hodnotu **tokenu rozhraní API.** Tato hodnota se zadá do pole **Tajný token** na kartě Zřizování vaší aplikace Figma na webu Azure Portal. 

    ![Figma vytvořit token](media/Figma-provisioning-tutorial/figma04.png)

## <a name="add-figma-from-the-gallery"></a>Přidat Figma z galerie

Chcete-li nakonfigurovat Figma pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat Figma z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Figma**, vyberte **figma** v panelu výsledků a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![Figma v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Konfigurace automatického zřizování uživatelů na Figma 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin ve Figmě na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování pro Figma založené na SAML podle pokynů uvedených v [kurzu pro jednotné přihlašování Figma](figma-tutorial.md). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Figmu ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **možnost Figma**.

    ![Odkaz Figma v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části Přihlašovací údaje `https://www.figma.com/scim/v2/<TenantID>` **správce,** vstup v adrese URL **klienta,** kde **TenantID** je hodnota, kterou jste načetli z Figma dříve. Zadejte hodnotu **tokenu rozhraní API** do **tajného tokenu**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k Figmě. Pokud se připojení nezdaří, ujistěte se, že váš účet Figma má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

8. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s figma**.

    ![Mapování uživatelů Figma](media/Figma-provisioning-tutorial/figma05.png)

11. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Figma v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům ve Figmě pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy Figma](media/Figma-provisioning-tutorial/figma06.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro Figma , změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit figma výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Figmě.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
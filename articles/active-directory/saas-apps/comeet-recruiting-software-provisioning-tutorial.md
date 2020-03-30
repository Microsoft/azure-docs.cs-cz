---
title: 'Kurz: Konfigurace náborového softwaru Comeet pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a odpočovávala uživatelské účty do softwaru Comeet Recruiting.
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: f427fb75cfaeda79b037c327992e4ad482a7e689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058327"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Kurz: Konfigurace náborového softwaru Comeet pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Comeet Nábor software a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-provision uživatelů nebo skupin comeet náboru software.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Comeet Nábor software nájemce](https://www.comeet.co/)
* Uživatelský účet v comeet náborovém softwaru s oprávněními správce.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Přidat Comeet Nábor software z galerie

Před konfigurací Comeet Nábor software pro automatické zřizování uživatelů s Azure AD, je třeba přidat Comeet Nábor software z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat software Comeet Recruiting Software z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **comeet Recruiting Software**, vpanelu s výsledky vyberte **comeet Recruiting Software** a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![Comeet Recruiting Software v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Přiřazení uživatelů k náborovému softwaru Comeet

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k náborovému softwaru Comeet. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny Comeet Nábor Software podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Důležité tipy pro přiřazení uživatelů k comeet Nábor software

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen ke softwaru Comeet Recruiting Software k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele ke softwaru Comeet Recruiting Software musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Konfigurace automatického zřizování uživatelů na software Comeet Recruiting 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v softwaru Comeet Recruiting software na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na saml pro Comeet Recruiting Software podle pokynů uvedených v [kurzu comeet Nábor ového softwaru pro jednotné přihlášení](comeetrecruitingsoftware-tutorial.md). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro náborový software Comeet ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **možnost Comeet Recruiting Software**.

    ![Odkaz Comeet Recruiting Software v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Přihlašovací údaje správce** zadejte adresu URL **klienta** a **tajný token** účtu vašeho náborového softwaru Comeet, jak je popsáno v kroku 6.

6. V [konzole pro správu náborového softwaru Comeet](https://app.comeet.co/)přejděte do pole **Comeet > Nastavení > ověřování > Microsoft Azure**a zkopírujte tajný token pro hodnotu vaší **společnosti** do pole **Tajný token** ve službě Azure AD.

    ![Comeet Nábor Software Zřizování](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. Po vyplnění polí uvedených v kroku 5 klikněte na **test připojení** a ujistěte se, že Azure AD můžete připojit k Comeet Nábor software. Pokud se připojení nezdaří, ujistěte se, že váš účet Náborový software Comeet má oprávnění správce a zkuste to znovu.

    ![Podpisový](common/provisioning-testconnection-token.png)

8. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory na comeet**.

    ![Comeet Nábor Software User Mapování](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Comeet Nábor software v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v Comeet Nábor software pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy skupiny náborového softwaru Comeet](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro software comeet náboru, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit comeet náborového softwaru výběrem požadovaných hodnot v **scope** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v softwaru Comeet Recruiting Software.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektorů

* Comeet Recruiting Software v současné době nepodporuje skupiny.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)


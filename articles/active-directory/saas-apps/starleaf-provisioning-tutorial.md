---
title: 'Kurz: Konfigurace starleafu pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřizovala uživatelské účty na StarLeaf.
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
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 520373fc6a05bcaada973273e3553f9da623c669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064287"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Kurz: Konfigurace StarLeafu pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny ve StarLeaf a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-zřizování uživatelů nebo skupin starLeaf.

> [!NOTE]
>  Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Tenanta Azure AD.
* [Nájemce StarLeaf](https://www.starleaf.com/solutions/).
* Uživatelský účet ve StarLeafu s oprávněními správce.

## <a name="assign-users-to-starleaf"></a>Přiřazení uživatelů k StarLeafu
Azure Active Directory používá koncept s názvem přiřazení k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, měli byste se rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k StarLeafu. Poté můžete přiřadit uživatele a skupiny starleafu podle [těchto pokynů](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Důležité tipy pro přiřazení uživatelů k StarLeaf

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a StarLeaf otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a skupiny mohou být přiřazeny později.

* Když přiřadíte uživatele starleafu, musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí Výchozí přístup jsou z zřizování vyloučeni.

## <a name="set-up-starleaf-for-provisioning"></a>Nastavit StarLeaf pro zřizování

Než nakonfigurujete StarLeaf pro automatické zřizování uživatelů pomocí Azure AD, budete muset nakonfigurovat zřizování SCIM v StarLeaf:

1. Přihlaste se do [své Administrátorské konzole StarLeaf](https://portal.starleaf.com/#page=login). Přejděte na **Integrace** > **Přidat integraci**.

    ![StarLeaf Přidat SCIM](media/starleaf-provisioning-tutorial/image00.png)

2. Vyberte **typ, který** má být službou Microsoft Azure Active Directory. Do pole **Název**zadejte vhodný název . Klikněte na **Použít**.

    ![StarLeaf Přidat SCIM](media/starleaf-provisioning-tutorial/image01.png)

3.  Poté se zobrazí **základní adresa URL SCIM** a hodnoty **tokenu aplikace Access.** Tyto hodnoty se zanesou do polí **ADRESA URL klienta** a **tajný token** na kartě Zřizování aplikace StarLeaf na webu Azure Portal. 

    ![StarLeaf vytvořit token](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>Přidat StarLeaf z galerie

Chcete-li konfigurovat StarLeaf pro automatické zřizování uživatelů s Azure AD, je třeba přidat StarLeaf z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat StarLeaf z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **StarLeaf**, vyberte **StarLeaf** v panelu výsledků.
    ![StarLeaf v seznamu výsledků](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Konfigurace automatického zřizování uživatelů na StarLeaf

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v StarLeafu na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **StarLeaf**.

    ![Odkaz StarLeaf v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části Přihlašovací údaje správce zadejte hodnoty **základní adresy URL SCIM** a **přístupového tokenu** načtené dříve v **adrese URL klienta** a **tajný token.** Klikněte na **Testovat připojení** a ujistěte se, že Azure AD můžete připojit k StarLeaf. Pokud se připojení nezdaří, ujistěte se, že váš účet StarLeaf má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě.**

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s programem StarLeaf**.

    ![StarLeaf vytvořit token](media/starleaf-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na StarLeaf v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v StarLeaf pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![StarLeaf vytvořit token](media/starleaf-provisioning-tutorial/userattribute.png)


10. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


11. Chcete-li povolit službu zřizování Azure AD pro StarLeaf, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit StarLeaf výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

13. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na StarLeafu.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v [tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Omezení konektoru

* StarLeaf aktuálně nepodporuje zřizování skupiny. 
* StarLeaf vyžaduje, aby hodnoty **e-mailu** a **userName** měly stejnou zdrojovou hodnotu.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [zkontrolovat protokoly a získat sestavy o aktivitě zřizování](../app-provisioning/check-status-user-account-provisioning.md).

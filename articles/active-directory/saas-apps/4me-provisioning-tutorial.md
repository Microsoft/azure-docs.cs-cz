---
title: 'Kurz: Konfigurace 4m pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřizovala uživatelské účty na 4me.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 423ba8c7aea9659a4c91f68a01392954c2ba6db2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059156"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Kurz: Konfigurace 4m pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v 4me a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-zřizování uživatelů a/nebo skupin na 4me.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [4m nájemce](https://www.4me.com/trial/)
* Uživatelský účet ve 4me s oprávněními správce.

## <a name="add-4me-from-the-gallery"></a>Přidat 4m z galerie

Před konfigurací 4me pro automatické zřizování uživatelů pomocí Azure AD, je potřeba přidat 4me z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat 4m z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **4me**, vyberte **4me** v panelu výsledků a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![4m v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Přiřazení uživatelů k 4me

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k 4me. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny 4me podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Důležité tipy pro přiřazení uživatelů ke 4me

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a 4me k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele ke 4me musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Konfigurace automatického zřizování uživatelů na 4m 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v 4me na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit saml-založené jednotné přihlašování pro 4me, podle pokynů uvedených v [4me jednotnépřihlášení-na tutorial](4me-tutorial.md). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro 4m ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **4m**.

    ![Odkaz 4me v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. Chcete-li načíst **adresu URL klienta** a **tajný token** vašeho účtu 4me, postupujte podle návodu, jak je popsáno v kroku 6.

6. Přihlaste se ke své 4me Admin Console. Přejděte do **nastavení**.

    ![Nastavení 4me](media/4me-provisioning-tutorial/4me01.png)

    Zadejte **aplikace** na panel u vyhledávacího panelu.

    ![4m aplikace](media/4me-provisioning-tutorial/4me02.png)

    Otevřete rozevírací přehled **SCIM** pro načtení tajného tokenu a koncového bodu SCIM.

    ![4m SCIM](media/4me-provisioning-tutorial/4me03.png)

7. Po naplnění polí uvedených v kroku 5 klikněte na **test připojení** a ujistěte se, že Azure AD můžete připojit k 4me. Pokud se připojení nezdaří, ujistěte se, že váš účet 4me má oprávnění správce a zkuste to znovu.

    ![Podpisový](common/provisioning-testconnection-tenanturltoken.png)

8. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory na 4me**.

    ![4m mapování uživatelů](media/4me-provisioning-tutorial/4me-user-mapping.png)
    
11. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do 4me v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům ve 4me pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![4m mapování uživatelů](media/4me-provisioning-tutorial/4me-user-attributes.png)
    
12. V části **Mapování** vyberte **Synchronizovat skupiny Služby Active Directory Azure na 4me**.

    ![4m mapování uživatelů](media/4me-provisioning-tutorial/4me-group-mapping.png)
    
13. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do 4me v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám ve 4me pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![4m skupinová mapování](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Chcete-li povolit službu zřizování Azure AD pro 4me, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

16. Definujte uživatele nebo skupiny, které chcete zřídit do 4me, výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

17. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na 4me.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektorů

* 4me má různé adresy URL koncových bodů SCIM pro testovací a produkční prostředí. První končí **.qa,** zatímco druhá končí **.com**
* 4m generované tajné tokeny mají datum vypršení platnosti měsíce od generování.
* 4me nepodporuje operace **DELETE**

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
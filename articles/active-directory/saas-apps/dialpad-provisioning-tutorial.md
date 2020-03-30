---
title: 'Kurz: Konfigurace číselníku pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty na číselník.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 9f39277644547a625d87a39681f0c5520996cbd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058336"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Kurz: Konfigurace číselníku pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Dialpad a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-zřizování uživatelů nebo skupin na Dialpad.

> [!NOTE]
>  Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).

> Tento konektor je aktuálně ve verzi Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Tenanta Azure AD.
* [Tenant a Dialpad](https://www.dialpad.com/pricing/).
* Uživatelský účet v dialpadu s oprávněními správce.

## <a name="assign-users-to-dialpad"></a>Přiřazení uživatelů k číselníku
Azure Active Directory používá koncept s názvem přiřazení k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k dialpadu. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny dialpad podle pokynů zde:
 
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Důležité tipy pro přiřazení uživatelů k číselníku

 * Doporučuje se, aby jeden uživatel Azure AD je přiřazen k dialpad k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k programu Dialpad musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí Výchozí přístup jsou z zřizování vyloučeni.

## <a name="setup-dialpad-for-provisioning"></a>Instalační číselník pro zřizování

Před konfigurací Dialpad pro automatické zřizování uživatelů s Azure AD, budete muset načíst některé zřizování informace z dialpadu.

1. Přihlaste se ke [konzoli Pro správu číselníku Administrátora](https://dialpadbeta.com/login) a vyberte **nastavení správce**. Ujistěte se, že je v rozevírací seznamně vybrána moje **společnost.** Přejděte na **adresu Authentication > API Keys**.

    ![Dialpad Přidat SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Vygenerujte nový klíč kliknutím na **tlačítko Přidat klíč** a konfigurací vlastností tajného tokenu.

    ![Dialpad Přidat SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Dialpad Přidat SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Kliknutím na **tlačítko Kliknutím zobrazíte** tlačítko hodnota pro naposledy vytvořený klíč rozhraní API a zkopírujte zobrazenou hodnotu. Tato hodnota se zadá do pole **Tajný token** na kartě Zřizování aplikace Dialpad na webu Azure Portal. 

    ![Vytvořit token číselníku](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Přidání číselníku z galerie

Chcete-li konfigurovat Dialpad pro automatické zřizování uživatelů pomocí Azure AD, je třeba přidat Dialpad z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat číselník z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Číselník**, vpanelu výsledky vyberte **Číselník.**
    ![Číselník v seznamu výsledků](common/search-new-app.png)

5. Přejděte na **adresu URL** zvýrazněnou níže v samostatném prohlížeči. 

    ![Dialpad Přidat SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. V pravém horním rohu vyberte **Přihlásit > použít číselník online**.

    ![Dialpad Přidat SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Vzhledem k tomu, že dialpad je aplikace OpenIDConnect, zvolte přihlášení k dialpadu pomocí pracovního účtu Microsoft.

    ![Dialpad Přidat SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. Po úspěšném ověření přijměte výzvu k souhlasu pro stránku souhlasu. Aplikace pak bude automaticky přidána do vašeho tenanta a budete přesměrováni na váš účet Dialpad.

    ![Dialpad Přidat SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Konfigurace automatického zřizování uživatelů na číselník

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v dialpadu na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro dialpad ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **položku Dialpad**.

    ![Odkaz Číselník v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://dialpad.com/scim` zadejte adresu **URL klienta**. Zadejte hodnotu, kterou jste načetli a uložili dříve z číselníku v **tajném tokenu**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k dialpadu. Pokud se připojení nezdaří, ujistěte se, že váš účet Dialpad má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s číselníkem**.

    ![Mapování uživatelů číselníku](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na dialpad v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v číselníku pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy číselníku](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Chcete-li povolit službu zřizování Azure AD pro dialpad, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit na dialpad výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

13. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na číselníku.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v [tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Omezení konektoru
* Dialpad dnes nepodporuje přejmenování skupin. To znamená, že žádné změny **displayName** skupiny ve službě Azure AD nebudou aktualizovány a projeví v dialpadu.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

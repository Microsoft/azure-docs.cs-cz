---
title: 'Kurz: Konfigurace looopu pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a odpočatou uživatelské účty do Looopu.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0efe2262-43c3-4e0c-97fa-9344385638e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: e3e25a8c27b9a5c1bc1e7673300ac8aca9377c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057432"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Kurz: Konfigurace loooppro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Looop a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-zřizování uživatelů a/nebo skupin na Looop.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Looop nájemce](https://www.looop.co/pricing/)
* Uživatelský účet na záchodě s oprávněními správce.

## <a name="assign-users-to-looop"></a>Přiřazení uživatelů k Looop

Azure Active Directory používá koncept s názvem přiřazení k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Looop. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny looop podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Důležité tipy pro přiřazení uživatelů do Looop

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a Looop otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k Looop, musíte vybrat všechny platné role specifické pro aplikaci (pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="set-up-looop-for-provisioning"></a>Nastavení looop pro zřizování

Před konfigurací Looop pro automatické zřizování uživatelů s Azure AD, budete muset načíst některé zřizování informace z Looop.

1. Přihlaste se ke své [Konzoli pro správu Looop](https://app.looop.co/#/login) a vyberte **Účet**. V části **Nastavení účtu** vyberte **Ověřování**.

    ![Looop Přidat SCIM](media/looop-provisioning-tutorial/admin.png)

2. Vygenerujte nový token kliknutím na **obnovit token** v části **Integrace SCIM**.

    ![Looop Přidat SCIM](media/looop-provisioning-tutorial/resettoken.png)

3. Zkopírujte **koncový bod SCIM** a **token**. Tyto hodnoty se zanesou do polí **ADRESA URL klienta** a **tajný token** na kartě Zřizování aplikace Looop na webu Azure Portal. 

    ![Looop vytvořit token](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Přidat Looop z galerie

Chcete-li nakonfigurovat Looop pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat Looop z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Looop**, vyberte **looop** v panelu výsledků. 

    ![Looop v seznamu výsledků](common/search-new-app.png)

5. Vyberte tlačítko **Registrace pro Looop,** které vás přesměruje na přihlašovací stránku Looop. 

    ![Looop OIDC Přidat](media/looop-provisioning-tutorial/signup.png)

6. Vzhledem k tomu, že Looop je aplikace OpenIDConnect, zvolte přihlášení do Looop pomocí pracovního účtu Microsoft.

    ![Looop OIDC přihlášení](media/looop-provisioning-tutorial/msftlogin.png)

7. Po úspěšném ověření přijměte výzvu k souhlasu pro stránku souhlasu. Aplikace pak bude automaticky přidána do vašeho tenanta a budete přesměrováni na svůj účet Looop.

    ![Looop OIDc souhlas](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Konfigurace automatického zřizování uživatelů na Looop 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Looop u uživatelů a/nebo skupin ových přiřazení ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Záchod ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Looop**.

    ![Odkaz Looop v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://<organisation_domain>.looop.co/scim/v2` zadejte adresu **URL klienta**. Například `https://demo.looop.co/scim/v2`. Zadejte hodnotu, kterou jste načetli a uložili dříve z Looop v **tajném tokenu**. Klikněte na **Testovat připojení** a ujistěte se, že Azure AD můžete připojit k Looop. Pokud se připojení nezdaří, ujistěte se, že váš účet Looop má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s lokoop**.

    ![Mapování uživatelů Looop](media/looop-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Looop v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v Looop pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy uživatele Looop](media/looop-provisioning-tutorial/userattributes.png)

10. V části **Mapování** vyberte **Synchronizovat skupiny Služby Azure Active Directory s konektorem Meta Networks .**

    ![Mapování skupiny Looop](media/looop-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na konektor meta sítí v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v aplikaci Meta Networks Connector pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy skupiny Looop](media/looop-provisioning-tutorial/groupattributes.png)

10. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Chcete-li povolit službu zřizování Azure AD pro Looop, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit looop výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

13. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Looop.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)



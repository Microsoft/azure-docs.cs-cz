---
title: 'Kurz: Konfigurace sněhové vločky pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřizovala uživatelské účty pro Snowflake.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2c5d91894ba35233f3fbebffdff9104edcfdd27b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063135"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Kurz: Konfigurace sněhové vločky pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny ve Snowflake a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-zřizování uživatelů nebo skupin snowflake.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Tenanta Azure AD.
* [Nájemce sněhové vločky](https://www.Snowflake.com/pricing/).
* Uživatelský účet ve sněhové vločkě s oprávněními správce.

## <a name="assigning-users-to-snowflake"></a>Přiřazení uživatelů k Sněhové vločkě

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Snowflake. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny Snowflake podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Důležité tipy pro přiřazení uživatelů k Snowflake

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a Snowflake otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k snowflake, musíte vybrat všechny platné role specifické pro aplikaci (pokud jsou k dispozici) v dialogovém okně přiřazení. Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="setup-snowflake-for-provisioning"></a>Nastavení sněhové vločky pro zřizování

Před konfigurací Snowflake pro automatické zřizování uživatelů s Azure AD, budete muset povolit zřizování SCIM na Vločky.

1. Přihlaste se do konzole Snowflake Admin Console. Do zvýrazněného listu zadejte dotaz uvedený níže a klepněte na tlačítko **Spustit**.

    ![Snowflake Admin Console](media/Snowflake-provisioning-tutorial/image00.png)

2.  Pro vašeho tenanta Snowflake bude vygenerován přístupový token SCIM. Chcete-li jej načíst, klikněte na níže zvýrazněný odkaz.

    ![Sněhová vločka Přidat SCIM](media/Snowflake-provisioning-tutorial/image01.png)

3. Zkopírujte vygenerovanou hodnotu tokenu a klepněte na **tlačítko Hotovo**. Tato hodnota se zadá do pole **Tajný token** na kartě Zřizování aplikace Snowflake na portálu Azure.

    ![Sněhová vločka Přidat SCIM](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>Přidat Sněhová vločka z galerie

Chcete-li nakonfigurovat Snowflake pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat Sněhová vločka z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat vločku z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Sněhová vločka**, vpanelu výsledků vyberte **Sněhová vločka** a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![Sněhová vločka v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Konfigurace automatického zřizování uživatelů na Sněhová vločka 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin ve Snowflake na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování pro Snowflake založené na SAML podle pokynů uvedených v [kurzu sněhové vločky single sign-on](Snowflake-tutorial.md). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro snowflake ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Sněhová vločka**.

    ![Odkaz Sněhová vločka v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části Přihlašovací údaje `https://<Snowflake Account URL>/scim/v2` správce zadejte adresu URL klienta. Příklad adresy URL klienta:`https://acme.snowflakecomputing.com/scim/v2`

6. Zadejte hodnotu **ověřovacího tokenu SCIM** načtenou dříve v **tokenu tajného klíče**. Klikněte na **Testovat připojení** a ujistěte se, že Azure AD můžete připojit k Snowflake. Pokud se připojení nezdaří, ujistěte se, že váš účet Snowflake má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

7. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

8. Klikněte na **Uložit**.

9. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory se sněhovou vločkou**.

    ![Mapování uživatelů sněhových vloček](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Vločky v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Snowflake pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy uživatele sněhové vločky](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. V části **Mapování** vyberte **Synchronizovat skupiny služby Azure Active Directory se sněhovou vločkou**.

    ![Mapování skupiny sněhových vloček](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na Snowflake v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v Snowflake pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy skupiny sněhových vloček](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Chcete-li povolit službu zřizování Azure AD pro Snowflake, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

15. Definujte uživatele nebo skupiny, které chcete zřídit Snowflake výběrem požadovaných hodnot v **scope** v části **Nastavení.** Pokud tato možnost není k dispozici, nakonfigurujte požadovaná pole v části Pověření správce, klikněte na **Uložit** a aktualizujte stránku. 

    ![Obor zřizování](common/provisioning-scope.png)

16. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

    Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Snowflake.

    Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v [tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Omezení konektoru

* Sněhové vločky generované SCIM tokeny vyprší za 6 měsíců. Uvědomte si, že tyto je třeba aktualizovat před vypršením jejich platnosti povolit synchronizace zřizování pokračovat v práci. 

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky
* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování](../app-provisioning/check-status-user-account-provisioning.md).

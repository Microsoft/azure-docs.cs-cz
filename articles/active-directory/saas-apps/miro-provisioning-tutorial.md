---
title: 'Kurz: Konfigurace Miro pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty miro.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 81eecaff-d888-472b-a1c2-0b7b0c9ccd8d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2019
ms.author: Zhchia
ms.openlocfilehash: b7e1907e3fa1eb9d775fb7662445b08d5671e0b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063404"
---
# <a name="tutorial-configure-miro-for-automatic-user-provisioning"></a>Kurz: Konfigurace Miro pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Miro a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-provision uživatelů a/nebo skupin miro.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Nájemce Miro](https://miro.com/pricing/)
* Uživatelský účet v Miro s oprávněními správce.

## <a name="assigning-users-to-miro"></a>Přiřazení uživatelů k Miro

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Miro. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny Miro podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-miro"></a>Důležité tipy pro přiřazení uživatelů k Miro

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a Miro otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele společnosti Miro musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="set-up-miro-for-provisioning"></a>Nastavit Miro pro zřizování

1.  Chcete-li získat potřebný **tajný token,** kontaktujte tým podpory Miro na adrese support@miro.com. Tato hodnota se zadá do pole Tajný token na kartě Zřizování vaší aplikace Miro na webu Azure Portal.

## <a name="add-miro-from-the-gallery"></a>Přidat Miro z galerie

Před konfigurací Miro pro automatické zřizování uživatelů s Azure AD, je potřeba přidat Miro z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Miro z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Miro**, vyberte **miro** v panelu výsledků a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![Miro v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-miro"></a>Konfigurace automatického zřizování uživatelů do Miro 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Miro na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování na saml pro Miro podle pokynů uvedených v [kurzu jednotného přihlášení Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-tutorial). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

> [!NOTE]
> Chcete-li se dozvědět více o koncovém bodu SCIM společnosti Miro, podívejte se na [toto](https://help.miro.com/hc/en-us/articles/360036777814).

### <a name="to-configure-automatic-user-provisioning-for-miro-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Miro ve službě Azure AD

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Miro**.

    ![Odkaz Miro v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://miro.com/api/v1/scim` zadejte adresu **URL klienta**. Zadejte hodnotu **ověřovacího tokenu SCIM** načtenou dříve v **tokenu tajného klíče**. Klikněte na **Testovat připojení** a ujistěte se, že Azure AD se může připojit k Miro. Pokud se připojení nezdaří, ujistěte se, že váš účet Miro má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s miro**.

    ![Mapování uživatelů Miro](media/miro-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Miro v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Miro pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy Miro](media/miro-provisioning-tutorial/userattributes.png)

10. V části **Mapování** vyberte **Synchronizovat skupiny Služby Active Directory Azure s Mirem**.

    ![Mapování skupiny Miro](media/miro-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do Miro v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v Miro pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.** Zrušte zaškrtnutí políčka **Vytvořit** a **odstranit** v části **Akce cílového objektu,** protože rozhraní MIRO SCIM API nepodporuje vytváření a odstraňování skupin.

    ![Atributy skupiny Miro](media/miro-provisioning-tutorial/groupattributes.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro Miro, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit Miro výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Miro.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Koncový bod SCIM společnosti Miro neumožňuje **operace vytvoření** a **odstranění** ve skupinách. Podporuje pouze operaci **aktualizace** skupiny.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)


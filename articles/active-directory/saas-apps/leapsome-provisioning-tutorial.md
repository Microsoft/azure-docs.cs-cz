---
title: 'Kurz: Konfigurace Leapsome pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Leapsome.
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
ms.author: jeedes
ms.openlocfilehash: e02deaa29b40e64b63d9afb471717feef78b3cee
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672682"
---
# <a name="tutorial-configure-leapsome-for-automatic-user-provisioning"></a>Kurz: Konfigurace Leapsome pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v Leapsome a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se Leapsome.

> [!NOTE]
>  Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Preview. Další informace o obecných Microsoft Azure podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

* Tenanta Azure AD.
* A [Leapsome](https://www.Leapsome.com/en/pricing) tenanta.
* Účet uživatele s oprávněními správce v Leapsome.

## <a name="assigning-users-to-leapsome"></a>Přiřazování uživatelů k Leapsome

Azure Active Directory používá koncept volá *přiřazení* určit, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze na uživatele a/nebo skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k Leapsome. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny Leapsome podle zde uvedených pokynů:
* [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-leapsome"></a>Důležité tipy pro přiřazování uživatelů k Leapsome

* Dále je doporučeno jednoho uživatele Azure AD, je přiřazená Leapsome otestovat automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazení uživatele k Leapsome, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.


## <a name="setup-leapsome-for-provisioning"></a>Instalační program Leapsome pro zřizování

1. Přihlaste se k vaší [konzoly pro správu Leapsome](https://www.Leapsome.com/app/#/login). Přejděte do **Nastavení > Nastavení správy**.

    ![Leapsome konzoly pro správu](media/Leapsome-provisioning-tutorial/leapsome-admin-console.png)

2.  Přejděte do **integrace > zřizování SCIM uživatelů**.

    ![Přidat Leapsome SCIM](media/Leapsome-provisioning-tutorial/leapsome-add-scim.png)

3.  Kopírovat **SCIM ověřovací Token**. Tuto hodnotu zadá do tajný klíč tokenu pole na kartě zřizování Leapsome aplikace na webu Azure Portal.

    ![Leapsome vytvořit Token](media/Leapsome-provisioning-tutorial/leapsome-create-token.png)

## <a name="add-leapsome-from-the-gallery"></a>Přidání Leapsome z Galerie

Před konfigurací Leapsome pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Leapsome z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Leapsome z Galerie aplikací Azure AD, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Leapsome**vyberte **Leapsome** panel výsledků a pak klikněte na **přidat** tlačítko pro přidání aplikace.

    ![Leapsome v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-leapsome"></a>Konfigurace automatické zřizování uživatelů pro Leapsome 

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v Leapsome podle přiřazení uživatele a/nebo skupiny ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro Leapsome, postupujte podle pokynů uvedených v [Leapsome Single sign-on kurzu](Leapsome-tutorial.md). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou

### <a name="to-configure-automatic-user-provisioning-for-leapsome-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro Leapsome ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Leapsome**.

    ![Odkaz Leapsome v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování](common/provisioning-automatic.png)

5. V části **přihlašovacích údajů správce** části zadejte `https://www.leapsome.com/api/scim` v **adresy URL Tenanta**. Vstup **SCIM ověřovací Token** hodnotu získali dříve v **tajný klíč tokenu**. Klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k Leapsome. Pokud se nepovede, ujistěte se, že váš účet Leapsome má oprávnění správce a zkuste to znovu.

    ![Adresa URL tenanta + Token](common/provisioning-testconnection-tenanturltoken.png)

6. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![E-mailové oznámení](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům Leapsome**.

    ![Mapování uživatelů Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-user-mappings.png)

9. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Leapsome v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Leapsome pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Atributy uživatele Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-user-attributes.png)

10. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory k Leapsome**.

    ![Mapování Leapsome skupin](media/Leapsome-provisioning-tutorial/Leapsome-group-mappings.png)

11. Zkontrolujte skupiny atributů, které se synchronizují ze služby Azure AD do Leapsome v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají k vyhodnocení skupiny v Leapsome pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Atributy Leapsome skupiny](media/Leapsome-provisioning-tutorial/Leapsome-group-attributes.png)

12. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Služba pro Leapsome zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Stav zřizování zapnutém](common/provisioning-toggle-on.png)

14. Definovat uživatele a/nebo skupiny, které chcete k poskytování Leapsome výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Zřizování oboru](common/provisioning-scope.png)

15. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Ukládá se konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba na Leapsome zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Vyžaduje Leapsome **uživatelské jméno** být jedinečný.
* Leapsome povoluje jenom pracovní e-mailové adresy se má uložit.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

---
title: 'Kurz: Konfigurace Zscalerem Beta pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Zscalerem Beta.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 83db6b8d-503b-48f3-b918-f9fba1369d53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 54cf2924a94dda1d29baf048c866f019b82e1402
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256834"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Kurz: Konfigurace Zscalerem Beta pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v Zscalerem Beta a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se Zscalerem Beta.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných Microsoft Azure podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že již máte následující:

* Klient služby Azure AD
* Tenanta Zscaler Beta
* Uživatelský účet ve verzi Beta Zscalerem s oprávněními správce

> [!NOTE]
> Zřizování integrace služby Azure AD spoléhá na rozhraní API SCIM Zscalerem beta verze, která je k dispozici Beta Zscalerem vývojářům pro účty s balíčkem Enterprise.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Přidání Zscalerem Beta z Galerie

Před konfigurací Zscalerem Beta pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Zscalerem Beta z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Přidat Zscalerem Beta z Galerie aplikací Azure AD, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscalerem Beta**vyberte **Zscalerem Beta** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Beta verze Zscalerem v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Přiřazování uživatelů k Zscaler Beta

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele a/nebo skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k Zscalerem Beta. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny Zscalerem Beta podle zde uvedených pokynů:

* [Přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Důležité tipy pro přiřazování uživatelů k Zscaler Beta

* Dále je doporučeno jednoho uživatele Azure AD je přiřazená Zscalerem Beta testování automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazení uživatele k Zscalerem Beta, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Konfigurace automatické zřizování uživatelů pro Zscaler Beta

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny ve verzi Beta Zscalerem podle uživatele a/nebo přiřazení skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro Zscalerem Beta, postupujte podle pokynů uvedených v [Zscalerem Beta jednotné přihlašování – kurz](zscaler-beta-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro Beta Zscalerem ve službě Azure AD:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Zscalerem Beta**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zscalerem Beta**.

    ![Odkaz Zscalerem Beta v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. V části **přihlašovacích údajů správce** části, zadejte **adresy URL Tenanta** a **tajný klíč tokenu** účtu Zscalerem Beta, jak je popsáno v kroku 6.

6. Získat **adresy URL Tenanta** a **tajný klíč tokenu**, přejděte na **Správa > Nastavení ověřování** v Beta Zscalerem portálu uživatelské rozhraní a klikněte na  **SAML** pod **typ ověřování**.

    ![Zřizování Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Klikněte na **konfigurace SAML** otevřít **konfigurace SAML** možnosti.

    ![Zřizování Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Vyberte **Enable SCIM-Based zřizování** načíst **základní adresu URL** a **nosného tokenu**, uložte nastavení. Kopírovat **základní adresu URL** k **adresy URL Tenanta**, a **nosného tokenu** k **tajný klíč tokenu** na webu Azure Portal.

7. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k Zscalerem Beta. Pokud se nepovede, ujistěte se, že váš účet Zscalerem Beta má oprávnění správce a zkuste to znovu.

    ![Zřizování Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtněte políčko **odeslání e-mailové oznámení, když dojde k selhání**.

    ![Zřizování Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizace Azure Active Directory Users na verzi Beta Zscalerem**.

    ![Zřizování Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Zscalerem Beta v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty ve verzi Beta Zscalerem pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Zřizování Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory na verzi Beta Zscalerem**.

    ![Zřizování Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Zkontrolujte skupiny atributů, které jsou synchronizovány ze služby Azure AD na verzi Beta Zscalerem v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají k vyhodnocení skupiny ve verzi Beta Zscalerem pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Zřizování Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](./../active-directory-saas-scoping-filters.md).

15. Služba pro Beta Zscalerem zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Zřizování Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Definovat uživatele a/nebo skupiny, které chcete ke zřízení na verzi Beta Zscalerem výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Zřizování Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Zřizování Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba na Beta Zscalerem zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png

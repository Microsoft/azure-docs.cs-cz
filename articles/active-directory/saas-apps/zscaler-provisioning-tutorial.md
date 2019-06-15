---
title: 'Kurz: Konfigurace Zscalerem pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Zscalerem.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 31f67481-360d-4471-88c9-1cc9bdafee24
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 3ea502477cc5b380c99a183d9270c2b2e94375a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67049387"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Kurz: Konfigurace Zscalerem pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v Zscalerem a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se Zscalerem.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných Microsoft Azure podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že již máte následující:

* Klient služby Azure AD
* Tenanta Zscaler
* Uživatelský účet v Zscalerem s oprávněními správce

> [!NOTE]
> Zřizování integrace služby Azure AD spoléhá na rozhraní API SCIM Zscalerem, která je k dispozici pro vývojáře Zscalerem pro účty s balíčkem Enterprise.

## <a name="adding-zscaler-from-the-gallery"></a>Přidání Zscalerem z Galerie

Před konfigurací Zscalerem pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Zscalerem z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Zscalerem z Galerie aplikací Azure AD, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscalerem**vyberte **Zscalerem** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![V seznamu výsledků Zscaler](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Přiřazování uživatelů k Zscaler

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele a/nebo skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k Zscalerem. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny Zscalerem podle zde uvedených pokynů:

* [Přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Důležité tipy pro přiřazování uživatelů k Zscaler

* Dále je doporučeno jednoho uživatele Azure AD je přiřazená Zscalerem otestovat automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazení uživatele k Zscalerem, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Konfigurace automatické zřizování uživatelů pro Zscaler

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v Zscalerem podle přiřazení uživatele a/nebo skupiny ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro Zscalerem, postupujte podle pokynů uvedených v [Zscalerem jednotné přihlašování – kurz](zscaler-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro Zscalerem ve službě Azure AD:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Zscalerem**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zscalerem**.

    ![V seznamu aplikací na odkaz Zscaler](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování Zscaler](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování Zscaler](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. V části **přihlašovacích údajů správce** části, zadejte **adresy URL Tenanta** a **tajný klíč tokenu** účtu Zscalerem, jak je popsáno v kroku 6.

6. Získat **adresy URL Tenanta** a **tajný klíč tokenu**, přejděte na **Správa > Nastavení ověřování** Zscalerem portálu uživatelské rozhraní a klikněte na **SAML** pod **typ ověřování**.

    ![Zřizování Zscaler](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Klikněte na **konfigurace SAML** otevřete **konfigurace SAML** možnosti.

    ![Zřizování Zscaler](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Vyberte **Enable SCIM-Based zřizování** načíst **základní adresu URL** a **nosného tokenu**, uložte nastavení. Kopírovat **základní adresu URL** k **adresy URL Tenanta**, a **nosného tokenu** k **tajný klíč tokenu** na webu Azure Portal.

7. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k Zscalerem. Pokud se nepovede, ujistěte se, že váš účet Zscalerem má oprávnění správce a zkuste to znovu.

    ![Zřizování Zscaler](./media/zscaler-provisioning-tutorial/test-connection.png)

8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtněte políčko **odeslání e-mailové oznámení, když dojde k selhání**.

    ![Zřizování Zscaler](./media/zscaler-provisioning-tutorial/notification.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům Zscalerem**.

    ![Zřizování Zscaler](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Zscalerem v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Zscalerem pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Zřizování Zscaler](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory k Zscalerem**.

    ![Zřizování Zscaler](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Zkontrolujte skupiny atributů, které se synchronizují ze služby Azure AD do Zscalerem v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají k vyhodnocení skupiny v Zscalerem pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Zřizování Zscaler](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](./../active-directory-saas-scoping-filters.md).

15. Služba pro Zscalerem zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Zřizování Zscaler](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Definovat uživatele a/nebo skupiny, které chcete k poskytování Zscalerem výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Zřizování Zscaler](./media/zscaler-provisioning-tutorial/scoping.png)

17. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Zřizování Zscaler](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba na Zscalerem zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png

---
title: 'Kurz: Nakonfigurujte jeden Zscalerem pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a zrušit jejich zřízení uživatelských účtů do Zscalerem jeden.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 5319b0ac06c4ddf1a7627a4e7fe0bfb2694f79f6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64706606"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Kurz: Nakonfigurujte jeden Zscalerem pro automatické zřizování uživatelů

V tomto kurzu si ukážeme postup provést v Zscalerem jeden a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a zrušit jejich zřízení uživatelů a skupin a jedna Zscaler.

> [!NOTE]
> Tento kurz popisuje konektor, který je postavený na služba zřizování uživatelů Azure AD. Informace o význam této služby, jak to funguje a nejčastější dotazy, naleznete v tématu [automatizace zřizování uživatelů a jeho rušení pro software-as-a-service (SaaS) aplikací pomocí Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Tento konektor je teď dostupná ve verzi preview. Další informace o obecných Microsoft Azure podmínky použití pro funkce ve verzi preview, najdete v části [dodatečnými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte:

* Tenanta Azure AD.
* Zscalerem jednoho tenanta.
* Účet uživatele s oprávněními správce v Zscalerem jeden.

> [!NOTE]
> Zřizování integrace Azure AD spoléhá na rozhraní API Zscalerem jeden SCIM. Toto rozhraní API je k dispozici jeden Zscalerem vývojářům pro účty s balíčkem Enterprise.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Přidat Zscalerem jedním z Azure Marketplace

Než začnete konfigurovat Zscalerem jeden pro automatické zřizování uživatelů pomocí Azure AD, přidejte si Zscalerem jeden z Azure Marketplace na seznam spravovaných aplikací SaaS.

Chcete-li přidat Zscalerem jeden z webu Marketplace, postupujte takto.

1. V [webu Azure portal](https://portal.azure.com), v navigačním podokně na levé straně vyberte **Azure Active Directory**.

    ![Ikona služby Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscalerem jeden** a vyberte **Zscalerem jeden** na panelu výsledků. Chcete-li přidat aplikaci, **přidat**.

    ![Zscalerem jeden v seznamu výsledků](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Přiřadit uživatele k jednomu Zscaler

Azure Active Directory používá koncept volá *přiřazení* určit, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Než začnete konfigurovat a povolit automatické zřizování uživatelů, rozhodněte, které uživatele nebo skupiny ve službě Azure AD potřebují přístup k jednomu Zscaler. Přiřazení těchto uživatelů nebo skupin Zscalerem k jednomu, postupujte podle pokynů v [přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Důležité tipy pro přiřazování uživatelů k jednomu Zscaler

* Doporučujeme vám, přiřadit jeden uživatele Azure AD k Zscalerem jeden test automatické konfigurace zřizování uživatelů. Můžete přiřadit dalším uživatelům nebo skupinám později.

* Když přiřadíte uživatele k Zscalerem jeden, vyberte jakékoli platné role specifické pro aplikaci, pokud je k dispozici v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Konfigurovat automatické zřizování uživatelů pro jeden Zscaler

Tato část vás provede kroky konfigurace zřizování služby Azure AD. Umožňuje vytvářet, aktualizovat a zakázat uživatele nebo skupiny v Zscalerem jeden podle uživatele nebo skupiny přiřazení ve službě Azure AD.

> [!TIP]
> Také můžete povolit na základě SAML jednotného přihlašování pro Zscalerem jeden. Postupujte podle pokynů [Zscalerem jeden jednotné přihlašování – kurz](zscaler-One-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce se vzájemně doplňují.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Konfigurovat automatické zřizování uživatelů pro Zscalerem jeden ve službě Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace** > **všechny aplikace** > **Zscalerem jeden**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zscalerem jeden**.

    ![Zscalerem jedno propojení v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování Zscalerem jeden](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Jeden režim zřizování Zscaler](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. V části **přihlašovacích údajů správce** části, zadejte **adresy URL Tenanta** a **tajný klíč tokenu** polí s nastavením pro vaše Zscalerem jeden účet, jak je popsáno v kroku 6.

6. Získání tenanta adresy URL a token tajného kódu, přejděte na **správu** > **nastavení ověřování** portálu Zscalerem jednoho uživatelského rozhraní. V části **typ ověřování**vyberte **SAML**.

    ![Nastavení ověřování Zscalerem jeden](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Vyberte **konfigurace SAML** otevřít **konfigurace SAML** možnosti.

    ![Zscalerem jedna konfigurace SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Vyberte **Enable SCIM-Based zřizování** zobrazíte nastavení **základní adresu URL** a **nosného tokenu**. Uložte nastavení. Kopírovat **základní adresu URL** nastavení **adresy URL Tenanta** na webu Azure Portal. Kopírovat **nosného tokenu** nastavení **tajný klíč tokenu** na webu Azure Portal.

7. Po vyplnění polí uvedené v kroku 5, vyberte **Test připojení** Ujistěte se, že Azure AD můžete připojit k Zscalerem jeden. Pokud se nepovede, ujistěte se, že vaše Zscalerem jeden účet má oprávnění správce a zkuste to znovu.

    ![Zscalerem jeden Test připojení](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. V **e-mailové oznámení** pole, zadejte e-mailovou adresu osoby nebo skupiny, ke zřizování chyba oznámení vůbec dostat. Vyberte **odeslání e-mailové oznámení, když dojde k selhání** zaškrtávací políčko.

    ![E-mail s oznámením o jeden Zscaler](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Vyberte **Uložit**.

10. V části **mapování** vyberte **synchronizace Azure Active Directory Users Zscalerem k jednomu**.

    ![Jeden Zscalerem synchronizace uživatelů](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Zscalerem jeden v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v jedné Zscalerem pro operace update. Chcete-li uložit všechny změny, vyberte **Uložit**.

    ![Zscalerem jeden odpovídající atributy uživatele](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory k jednomu Zscalerem**.

    ![Zscalerem jednu skupinu synchronizace](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Zkontrolujte skupiny atributů, které jsou synchronizovány ze služby Azure AD Zscalerem jednomu v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají k vyhodnocení skupiny v Zscalerem jeden pro operace update. Chcete-li uložit všechny změny, vyberte **Uložit**.

    ![Zscalerem jeden odpovídající atributy skupin](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Konfigurace filtrů oborů, postupujte podle pokynů [oboru filtru kurzu](./../active-directory-saas-scoping-filters.md).

15. Povolit zřizování služby pro jeden Zscalerem v Azure AD **nastavení** oddíl, změna **stavu zřizování** k **na**.

    ![Stav zřizování jeden Zscaler](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Definujte uživatele nebo skupiny, které chcete ke zřízení Zscalerem k jednomu. V **nastavení** vyberte hodnoty, které chcete v **oboru**.

    ![Zscalerem jeden obor](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Jakmile budete připraveni ke zřízení, vyberte **Uložit**.

    ![Zscalerem jeden uložit.](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupinám definovaným v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než novější se synchronizuje. Odehrávalo přibližně každých 40 minut za předpokladu, spustí služba zřizování Azure AD. 

Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na sestavu aktivit zřizování. Sestava popisuje všechny akce prováděné služba na jedné Zscalerem zřizování Azure AD.

Informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png

---
title: 'Kurz: Konfigurace platformy Zendesk pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a zrušit jejich zřízení uživatelských účtů do Zendesku.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 166b7727362549aaf054e3f0282c564eca687eb9
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672880"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Kurz: Konfigurace platformy Zendesk pro automatické zřizování uživatelů

Tento kurz nabízí postup provést v systému Zendesk a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a zrušit jejich zřízení uživatelů a skupin do Zendesku.

> [!NOTE]
> Tento kurz popisuje konektor, který je postavený na služba zřizování uživatelů Azure AD. Informace o význam této služby, jak to funguje a nejčastější dotazy, naleznete v tématu [automatizace zřizování uživatelů a jeho rušení pro software-as-a-service (SaaS) aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte:

* Tenanta Azure AD.
* Zendesk tenantovi se [Enterprise](https://www.zendesk.com/product/pricing/) plán nebo lépe povolena.
* Účet uživatele s oprávněními správce v systému Zendesk.

> [!NOTE]
> Zřizování integrace Azure AD spoléhá na [rozhraní Rest API služby Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction). Toto rozhraní API je k dispozici pro týmy Zendesku v plánu Enterprise nebo vyšší.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Přidat Zendesku z Azure Marketplace

Než nakonfigurujete Zendesk pro automatické zřizování uživatelů pomocí Azure AD, přidejte do seznamu spravovaných aplikací SaaS na Azure markeplace Zendesku.

Chcete-li přidat Zendesku z webu Marketplace, postupujte takto.

1. V [webu Azure portal](https://portal.azure.com), v navigačním podokně na levé straně vyberte **Azure Active Directory**.

    ![Ikona služby Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zendesku** a vyberte **Zendesku** na panelu výsledků. Chcete-li přidat aplikaci, **přidat**.

    ![Zendesku v seznamu výsledků](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Přiřazení uživatelů k Zendesku.

Azure Active Directory používá koncept volá *přiřazení* určit, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Než začnete konfigurovat a povolit automatické zřizování uživatelů, rozhodněte, které uživatele nebo skupiny ve službě Azure AD potřebují přístup k službě Zendesk. Přiřazení těchto uživatelů nebo skupin k službě Zendesk, postupujte podle pokynů v [přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Důležité tipy pro přiřazování uživatelů k Zendesku.

* V současné době Zendesku role automaticky a dynamicky zaplnění na webu Azure Portal uživatelského rozhraní. Předtím, než můžete uživatelům přiřadit role Zendesku, ujistěte se, že dokončení počáteční synchronizace proti Zendesku načíst nejnovější role ve vašem tenantovi Zendesku.

* Doporučujeme vám, přiřadit jeden uživatele Azure AD k službě Zendesk k testování vašich počáteční automatické zřizování uživatelů konfigurace. Můžete přiřadit dalším uživatelům nebo skupinám později po testy jsou úspěšné.

* Když přiřadíte uživatele k službě Zendesk, vyberte libovolný platný specifické pro aplikaci roli, pokud je k dispozici v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Konfigurovat automatické zřizování uživatelů do Zendesku. 

Tato část vás provede kroky konfigurace zřizování služby Azure AD. Umožňuje vytvářet, aktualizovat a zakázat uživatele nebo skupiny v systému Zendesk podle přiřazení uživatele nebo skupinu ve službě Azure AD.

> [!TIP]
> Také můžete povolit na základě SAML jednotného přihlašování pro Zendesku. Postupujte podle pokynů [Zendesku jednotné přihlašování – kurz](zendesk-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce se vzájemně doplňují.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurovat automatické zřizování uživatelů pro Zendesk ve službě Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace** > **všechny aplikace** > **Zendesku**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zendesku**.

    ![Propojení Zendesku v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Režim zřizování Zendesku.](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. V části **přihlašovacích údajů správce** části, zadejte uživatelské jméno správce, token tajného kódu a doménu vašeho účtu Zendesku. Mezi tyto hodnoty patří:

   * V **uživatelské jméno správce** pole, zadejte uživatelské jméno účtu správce ve svém tenantovi Zendesku. Příklad: admin@contoso.com.

   * V **tajný klíč tokenu** pole, zadejte token tajného kódu, jak je popsáno v kroku 6.

   * V **domény** pole, zadejte subdoménu tenanta Zendesku. Například pro účet s adresou URL tenanta `https://my-tenant.zendesk.com`, je vaší subdoménu **Moje tenanta**.

6. Token tajného kódu pro vašeho účtu Zendesku se nachází v **správce** > **API** > **nastavení**. Ujistěte se, že **přístup pomocí tokenu** je nastavena na **povoleno**.

    ![Nastavení správce Zendesku.](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Token tajného kódu Zendesku.](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Po vyplnění polí uvedené v kroku 5, vyberte **Test připojení** abyste měli jistotu, že Azure AD můžete připojit k službě Zendesk. Pokud se nepovede, ujistěte se, že má oprávnění správce vašeho účtu Zendesku a zkuste to znovu.

    ![Zendesk Test připojení](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. V **e-mailové oznámení** pole, zadejte e-mailovou adresu osoby nebo skupiny, ke zřizování chyba oznámení vůbec dostat. Vyberte **odeslání e-mailové oznámení, když dojde k selhání** zaškrtávací políčko.

    ![E-mail s oznámením o Zendesku.](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Vyberte **Uložit**.

10. V části **mapování** vyberte **synchronizaci Azure uživatelé služby Active Directory po Zendesk**.

    ![Synchronizace uživatelů Zendesku.](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Zendesku v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelských účtů v systému Zendesk pro operace update. Chcete-li uložit všechny změny, vyberte **Uložit**.

    ![Zendesk odpovídající atributy uživatele](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory po Zendesk**.

    ![Synchronizaci skupiny Zendesku.](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Zkontrolujte skupiny atributů, které jsou synchronizovány ze služby Azure AD k Zendesku v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají k vyhodnocení skupiny v systému Zendesk pro operace update. Chcete-li uložit všechny změny, vyberte **Uložit**.

    ![Zendesk odpovídající skupinu atributů](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Konfigurace filtrů oborů, postupujte podle pokynů [oboru filtru kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Povolit zřizování služby Zendesk, v Azure AD **nastavení** oddíl, změna **stavu zřizování** k **na**.

    ![Stav zřizování Zendesku.](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definujte uživatele nebo skupiny, které chcete ke zřízení do Zendesku. V **nastavení** vyberte hodnoty, které chcete v **oboru**.

    ![Obor Zendesku.](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Jakmile budete připraveni ke zřízení, vyberte **Uložit**.

    ![Uložit Zendesku.](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupinám definovaným v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než novější se synchronizuje. Odehrávalo přibližně každých 40 minut za předpokladu, spustí služba zřizování Azure AD. 

Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na sestavu aktivit zřizování. Sestava popisuje všechny akce prováděné služba v Zendesku zřizování Azure AD.

Informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Zendesk podporuje použití skupin pro uživatele s **agenta** pouze role. Další informace najdete v tématu [dokumentace ke službě Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Při vlastní role je přiřazená uživateli nebo skupině, Azure AD automatické zřizování uživatelů služby také přiřadí výchozí role **agenta**. Pouze agenty je možné přiřadit vlastní roli. Další informace najdete v tématu [dokumentace k rozhraní API platformy Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png

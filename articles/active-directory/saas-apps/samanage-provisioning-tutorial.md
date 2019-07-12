---
title: 'Kurz: Konfigurace Samanage pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a zrušit jejich zřízení uživatelských účtů do Samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67cfe5a26740837508ea3a3e76295a896c3cc107
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670926"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Kurz: Konfigurace Samanage pro automatické zřizování uživatelů

V tomto kurzu si ukážeme postup provést v Samanage a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a zrušit jejich zřízení uživatelů a skupin a Samanage.

> [!NOTE]
> Tento kurz popisuje konektor, který je postavený na služba zřizování uživatelů Azure AD. Informace o význam této služby, jak to funguje a nejčastější dotazy, naleznete v tématu [automatizace zřizování uživatelů a jeho rušení pro software-as-a-service (SaaS) aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte:

* Tenanta Azure AD.
* A [Samanage tenanta](https://www.samanage.com/pricing/) s profesionální balíčku.
* Účet uživatele s oprávněními správce v Samanage.

> [!NOTE]
> Zřizování integrace Azure AD spoléhá na [rozhraní Rest API služby Samanage](https://www.samanage.com/api/). Toto rozhraní API je k dispozici pro vývojáře Samanage pro účty s profesionální balíčku.

## <a name="add-samanage-from-the-azure-marketplace"></a>Přidat Samanage na webu Azure Marketplace

Než začnete konfigurovat Samanage pro automatické zřizování uživatelů pomocí Azure AD, přidejte do seznamu spravovaných aplikací SaaS Samanage z Azure Marketplace.

Chcete-li přidat Samanage z webu Marketplace, postupujte takto.

1. V [webu Azure portal](https://portal.azure.com), v navigačním podokně na levé straně vyberte **Azure Active Directory**.

    ![Ikona služby Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Samanage** a vyberte **Samanage** na panelu výsledků. Chcete-li přidat aplikaci, **přidat**.

    ![Samanage v seznamu výsledků](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Přiřazení uživatelů k Samanage

Azure Active Directory používá koncept volá *přiřazení* určit, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Než začnete konfigurovat a povolit automatické zřizování uživatelů, rozhodněte, které uživatele nebo skupiny ve službě Azure AD potřebují přístup k Samanage. Chcete-li přiřadit Samanage tyto uživatele nebo skupiny, postupujte podle pokynů v [přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Důležité tipy pro přiřazování uživatelů k Samanage

*    V současné době Samanage role automaticky a dynamicky zaplnění na webu Azure Portal uživatelského rozhraní. Před přiřazením Samanage role pro uživatele, ujistěte se, že počáteční synchronizace je dokončena proti Samanage načíst nejnovější role ve vašem tenantovi Samanage.

*    Doporučujeme vám, přiřadit jeden uživatele Azure AD k Samanage k testování vašich počáteční automatické zřizování uživatelů konfigurace. Můžete přiřadit dalším uživatelům a skupinám později po testy jsou úspěšné.

*    Když přiřadíte uživatele k Samanage, vyberte libovolný platný specifické pro aplikaci roli, pokud je k dispozici, v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Konfigurovat automatické zřizování uživatelů pro Samanage

Tato část vás provede kroky konfigurace zřizování služby Azure AD. Umožňuje vytvářet, aktualizovat a zakázat uživatele nebo skupiny v Samanage podle přiřazení uživatele nebo skupinu ve službě Azure AD.

> [!TIP]
> Také můžete povolit na základě SAML jednotného přihlašování pro Samanage. Postupujte podle pokynů [Samanage jednotné přihlašování – kurz](samanage-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce se vzájemně doplňují.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurovat automatické zřizování uživatelů pro Samanage ve službě Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace** > **všechny aplikace** > **Samanage**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Samanage**.

    ![Odkaz Samanage v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Samanage zřizování](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Režim zřizování Samanage](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **přihlašovacích údajů správce** části, zadejte uživatelské jméno admin a heslo správce vašeho účtu Samanage. Mezi tyto hodnoty patří:

   * V **uživatelské jméno správce** pole, zadejte uživatelské jméno účtu správce ve svém tenantovi Samanage. Příklad: admin@contoso.com.

   * V **heslo správce** pole, zadejte heslo účtu správce, který odpovídá uživatelské jméno správce.

6. Po vyplnění polí uvedené v kroku 5, vyberte **Test připojení** Ujistěte se, že Azure AD můžete připojit k Samanage. Pokud se nepovede, ujistěte se, že váš účet Samanage má oprávnění správce a zkuste to znovu.

    ![Samanage Test připojení](./media/samanage-provisioning-tutorial/TestConnection.png)

7. V **e-mailové oznámení** pole, zadejte e-mailovou adresu osoby nebo skupiny, ke zřizování chyba oznámení vůbec dostat. Vyberte **odeslání e-mailové oznámení, když dojde k selhání** zaškrtávací políčko.

    ![Samanage oznamovacího e-mailu](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Vyberte **Uložit**.

9. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům Samanage**.

    ![Synchronizace uživatelů Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

10. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Samanage v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Samanage pro operace update. Chcete-li uložit všechny změny, vyberte **Uložit**.

    ![Samanage odpovídající atributy uživatele](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. V části Povolit mapování skupin **mapování** vyberte **synchronizaci skupinám Azure Active Directory k Samanage**.

    ![Samanage skupiny synchronizace](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Nastavte **povoleno** k **Ano** synchronizace skupiny. Zkontrolujte skupiny atributů, které se synchronizují ze služby Azure AD do Samanage v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Samanage pro operace update. Chcete-li uložit všechny změny, vyberte **Uložit**.

    ![Samanage odpovídající skupinu atributů](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Konfigurace filtrů oborů, postupujte podle pokynů [oboru filtru kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Povolit služba pro Samanage, zřizování v Azure AD **nastavení** oddíl, změna **stavu zřizování** k **na**.

    ![Stav zřizování Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Definujte uživatele nebo skupiny, které chcete k poskytování Samanage. V **nastavení** vyberte hodnoty, které chcete v **oboru**. Když vyberete **synchronizovat všechny uživatele a skupiny** možnost, zvažte omezení, jak je popsáno v části "Konektor omezení."

    ![Samanage oboru](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Jakmile budete připraveni ke zřízení, vyberte **Uložit**.

    ![Uložit Samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupinám definovaným v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než novější se synchronizuje. Odehrávalo přibližně každých 40 minut za předpokladu, spustí služba zřizování Azure AD. 

Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na sestavu aktivit zřizování. Sestava popisuje všechny akce prováděné služba na Samanage zřizování Azure AD.

Informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

Pokud vyberete **synchronizovat všechny uživatele a skupiny** možnosti a hodnoty pro Samanage **role** hodnotu v rámci atributu **výchozí hodnotu, pokud má hodnotu null (je volitelný)** pole musí být vyjádřena v následujícím formátu:

- {"displayName": "role"}, kde chcete, aby výchozí hodnota je role.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png

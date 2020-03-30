---
title: 'Kurz: Konfigurace Zendesku pro automatické zřizování uživatelů pomocí Služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat Službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty do Zendesku.
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
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a480119ee88521b920be88669f6d80e3754d24d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062737"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Kurz: Konfigurace Zendesku pro automatické zřizování uživatelů

Tento kurz ukazuje kroky, které je třeba provést ve Službách Zendesk a Azure Active Directory (Azure AD) pro konfiguraci Azure AD tak, aby automaticky zřazovala a odstavovala uživatele a skupiny do Zendesku.

> [!NOTE]
> Tento kurz popisuje konektor, který je postavený na nad službou zřizování uživatelů Azure AD. Informace o tom, co tato služba dělá, jak funguje, a nejčastější dotazy naleznete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací masu jako služby (SaaS) pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že máte:

* Tenanta Azure AD.
* Tenant Zendesk s profesionálním plánem nebo lépe povoleným.
* Uživatelský účet v Zendesku s oprávněními správce.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Přidání Zendesku z Azure Marketplace

Než nakonfigurujete Zendesk pro automatické zřizování uživatelů pomocí Azure AD, přidejte Zendesk z Azure Marketplace do seznamu spravovaných aplikací SaaS.

Chcete-li zendesk přidat z webu Marketplace, postupujte takto.

1. Na [webu Azure Portal](https://portal.azure.com)vyberte v navigačním podokně vlevo **službu Azure Active Directory**.

    ![Ikona služby Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte v horní části dialogového okna možnost **Nová aplikace.**

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zendesk** a z panelu výsledků vyberte **Zendesk.** Chcete-li aplikaci přidat, vyberte **přidat**.

    ![Zendesk v seznamu výsledků](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Přiřazení uživatelů k Zendesku

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů jsou synchronizováni pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů se rozhodněte, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Zendesku. Chcete-li tyto uživatele nebo skupiny přiřadit společnosti Zendesk, postupujte podle pokynů v části [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Důležité tipy pro přiřazení uživatelů do Zendesku

* Role Zendesku se dnes automaticky a dynamicky naplní v unovém rozhraní portálu Azure. Než uživatelům přiřadíte role Zendesk, ujistěte se, že je dokončena počáteční synchronizace oproti Zendesku, abyste získali nejnovější role ve vašem tenantovi Zendesk.

* Doporučujeme přiřadit jediného uživatele Azure AD do Zendesk u otestovat počáteční automatické zřizování konfigurace uživatele. Další uživatele nebo skupiny můžete přiřadit později po úspěšném testování.

* Když přiřadíte uživatele do Zendesku, vyberte v dialogovém okně přiřazení libovolnou platnou roli specifickou pro aplikaci, pokud je k dispozici. Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Konfigurace automatického zřizování uživatelů do Zendesku 

Tato část vás provede kroky ke konfiguraci služby zřizování Azure AD. Použijte ji k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Zendesku na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování na saml pro Zendesk. Postupujte podle pokynů v [lekci jednotného přihlašování Zendesk](zendesk-tutorial.md). Jednotné přihlašování lze konfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce se vzájemně doplňují.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Zendesk ve službě Azure AD

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace** > **Všechny aplikace** > **Zendesk**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zendesk**.

    ![Odkaz Zendesk v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Zřizování Zendesku](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Režim zřizování Zendesku](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. V části **Přihlašovací údaje správce** zadejte uživatelské jméno správce, tajný token a doménu svého účtu Zendesk. Příklady těchto hodnot jsou:

   * V poli **Uživatelské jméno správce** vyplňte uživatelské jméno účtu správce ve vašem tenantovi Zendesk. Příklad: admin@contoso.com.

   * Do pole **Tajný token** vyplňte tajný token, jak je popsáno v kroku 6.

   * V poli **Doména** vyplňte subdoménu klienta Zendesk. Například pro účet s adresou `https://my-tenant.zendesk.com`URL klienta je vaše subdoména **můj tenant**.

6. Tajný token pro váš účet Zendesk se nachází v**nastavení****rozhraní API** >  **pro správu** > . Ujistěte se, že **je přístup k tokenu** nastaven na **povoleno**.

    ![Nastavení správce Zendesku](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk tajný token](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Po vyplnění polí uvedených v kroku 5 vyberte **testovat připojení** a ujistěte se, že Azure AD můžete připojit k Zendesk. Pokud se připojení nezdaří, ujistěte se, že váš účet Zendesk má oprávnění správce, a zkuste to znovu.

    ![Testovací připojení Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, aby se zjištovací oznámení o chybách zřizování zjištovací oznámení. Zaškrtněte **políčko Odeslat e-mailové oznámení, když dojde k chybě.**

    ![E-mail s oznámením zendesku](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Vyberte **Uložit**.

10. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory se zendeskem**.

    ![Synchronizace uživatelů Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Zendesk v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Zendesk pro operace aktualizace. Chcete-li uložit změny, vyberte **uložit**.

    ![Zendesk odpovídající uživatelské atributy](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. V části **Mapování** vyberte **Synchronizovat skupiny Služby Azure Active Directory se Zendeskem**.

    ![Synchronizace skupinzendesku](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do Zendesk v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v Zendesk u operací aktualizace. Chcete-li uložit změny, vyberte **uložit**.

    ![Zendesk odpovídající atributy skupiny](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Chcete-li konfigurovat filtry oborů, postupujte podle pokynů v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Chcete-li povolit službu zřizování Azure AD pro Zendesk, v části **Nastavení** změňte **stav zřizování** na **Zapnuto**.

    ![Stav zřizování Zendesku](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definujte uživatele nebo skupiny, které chcete zřídit zendesku. V části **Nastavení** vyberte požadované hodnoty v **oboru**.

    ![Zendesk Rozsah](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Až budete připraveni k zřízení, vyberte **Uložit**.

    ![Zendesk Uložit](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než pozdější synchronizace. Dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. 

Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivity zřizování. Sestava popisuje všechny akce prováděné službou zřizování Azure AD na Zendesku.

Informace o tom, jak číst protokoly zřizování Azure AD, naleznete v [tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Zendesk podporuje použití skupin pouze pro uživatele s rolemi **agenta.** Další informace naleznete v [dokumentaci zendesku](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Když je vlastní role přiřazena uživateli nebo skupině, služba automatického zřizování uživatelů Azure AD také přiřadí výchozí roli **Agent**. Vlastní roli lze přiřadit pouze agentům. Další informace naleznete v [dokumentaci k rozhraní ZENDESK API](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png

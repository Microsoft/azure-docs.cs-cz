---
title: 'Kurz: Konfigurace nástroje Zscaler One pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty do Zscaler One.
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
ms.author: jeedes
ms.openlocfilehash: 87413932acee576934ee50b59546371b03ceaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064168"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Kurz: Konfigurace zscaleru One pro automatické zřizování uživatelů

Tento kurz ukazuje kroky, které je třeba provést ve Zscaler One a Azure Active Directory (Azure AD) pro konfiguraci Azure AD tak, aby automaticky zřizovala a odstavovala uživatele a skupiny do Zscaler One.

> [!NOTE]
> Tento kurz popisuje konektor, který je postavený na nad službou zřizování uživatelů Azure AD. Informace o tom, co tato služba dělá, jak funguje, a nejčastější dotazy naleznete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací masu jako služby (SaaS) pomocí služby Azure Active Directory](../active-directory-saas-app-provisioning.md).


## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že máte:

* Tenanta Azure AD.
* Tenant Zscaler One.
* Uživatelský účet v Zscaler One s oprávněními správce.

> [!NOTE]
> Integrace zřizování Azure AD závisí na rozhraní API Zscaler One SCIM. Toto rozhraní API je k dispozici vývojářům Zscaler One pro účty s balíčkem Enterprise.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Přidání Zscaler One z Azure Marketplace

Než nakonfigurujete Zscaler One pro automatické zřizování uživatelů pomocí Azure AD, přidejte Zscaler One z Azure Marketplace do seznamu spravovaných aplikací SaaS.

Chcete-li přidat Zscaler One z marketplace, postupujte takto.

1. Na [webu Azure Portal](https://portal.azure.com)vyberte v navigačním podokně vlevo **službu Azure Active Directory**.

    ![Ikona služby Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte v horní části dialogového okna možnost **Nová aplikace.**

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscaler One** a z výsledkového panelu vyberte **Zscaler One.** Chcete-li aplikaci přidat, vyberte **přidat**.

    ![Zscaler One v seznamu výsledků](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Přiřazení uživatelů k Zscaler One

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů jsou synchronizováni pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů se rozhodněte, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Zscaler One. Chcete-li tyto uživatele nebo skupiny přiřadit k aplikaci Zscaler One, postupujte podle pokynů v části [Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Důležité tipy pro přiřazení uživatelů k Zscaler One

* Doporučujeme přiřadit jednoho uživatele Azure AD na Zscaler One k testování konfigurace automatického zřizování uživatelů. Později můžete přiřadit další uživatele nebo skupiny.

* Když přiřadíte uživatele k Zscaler One, vyberte v dialogovém okně přiřazení libovolnou platnou roli specifickou pro aplikaci, pokud je k dispozici. Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Konfigurace automatického zřizování uživatelů na Zscaler One

Tato část vás provede kroky ke konfiguraci služby zřizování Azure AD. Použijte ji k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Zscaler One na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování na základě SAML pro Zscaler One. Postupujte podle pokynů v [zscaler one single sign-on tutorial](zscaler-One-tutorial.md). Jednotné přihlašování lze konfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce se vzájemně doplňují.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Zscaler One ve službě Azure AD

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace** > **Všechny aplikace** > **Zscaler One**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zscaler One**.

    ![Odkaz Zscaler One v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Zscaler One Zřizování](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Zscaler one zřizovací režim](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. V části **Přihlašovací údaje správce** **vyplňte** pole url klienta a **tajný token** nastavením účtu Zscaler One, jak je popsáno v kroku 6.

6. Chcete-li získat adresu URL klienta a tajný token, přejděte na**nastavení ověřování** **správy** > v uzdu portálu Zscaler One. V části **Typ ověřování**vyberte **možnost SAML**.

    ![Nastavení ověřování Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Chcete-li otevřít možnosti **Konfigurovat saml,** vyberte Konfigurovat **SAML.**

    ![Zscaler One Konfigurace SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Chcete-li získat nastavení v **základní adrese URL** a **nosnétokenu**, vyberte **možnost Povolit zřizování založené na SCIM** . Pak uložte nastavení. Zkopírujte nastavení **základní adresy URL** na adresu URL **klienta** na webu Azure Portal. Zkopírujte nastavení **tokenu nosiče** do **tajného tokenu** na webu Azure Portal.

7. Po vyplnění polí uvedených v kroku 5 vyberte **testovat připojení** a ujistěte se, že Azure AD můžete připojit k Zscaler One. Pokud se připojení nezdaří, ujistěte se, že váš účet Zscaler One má oprávnění správce, a zkuste to znovu.

    ![Testovací připojení Zscaler One](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, aby se zjištovací oznámení o chybách zřizování zjištovací oznámení. Zaškrtněte **políčko Odeslat e-mailové oznámení, když dojde k chybě.**

    ![Zscaler one oznámení e-mail](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Vyberte **Uložit**.

10. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s programem Zscaler One**.

    ![Synchronizace uživatelů Zscaler One](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Zscaler One v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Zscaler One pro operace aktualizace. Chcete-li uložit změny, vyberte **uložit**.

    ![Zscaler One odpovídající uživatelské atributy](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. V části **Mapování** vyberte **Synchronizovat skupiny Služby Active Directory Azure s programem Zscaler One**.

    ![Synchronizace skupiny Zscaler One](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na Zscaler One v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v Zscaler One pro operace aktualizace. Chcete-li uložit změny, vyberte **uložit**.

    ![Atributy odpovídající skupiny Zscaler One](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Chcete-li konfigurovat filtry oborů, postupujte podle pokynů v [kurzu filtru oborů](./../active-directory-saas-scoping-filters.md).

15. Chcete-li povolit službu zřizování Azure AD pro Zscaler One, v části **Nastavení** změňte **stav zřizování** na **Zapnuto**.

    ![Stav zřizování Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Definujte uživatele nebo skupiny, které chcete zřídit zzír1. V části **Nastavení** vyberte požadované hodnoty v **oboru**.

    ![Zscaler jeden obor](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Až budete připraveni k zřízení, vyberte **Uložit**.

    ![Zscaler Jeden Uložit](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než pozdější synchronizace. Dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. 

Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivity zřizování. Sestava popisuje všechny akce prováděné službou zřizování Azure AD na Zscaler One.

Informace o tom, jak číst protokoly zřizování Azure AD, naleznete v [tématu Vytváření sestav na automatické zřizování uživatelských účtů](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png

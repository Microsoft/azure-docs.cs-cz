---
title: 'Kurz: Konfigurace zscaleru tři pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: V tomto kurzu se dozvíte, jak nakonfigurovat Službu Azure Active Directory tak, aby automaticky zřašla a zřaštila uživatelské účty do Zscaleru tři.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 385a1153-0f47-4e41-8f44-da1b49d7629e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 177bc34162c2b5e4dadc54e1166c5f6061068bae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77064100"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>Kurz: Konfigurace zscaleru tři pro automatické zřizování uživatelů

V tomto kurzu se dozvíte, jak nakonfigurovat Azure Active Directory (Azure AD) automaticky zřídit a deprovision uživatelů a/nebo skupin z Zscaler tři.

> [!NOTE]
> Tento kurz popisuje konektor, který je postaven na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete [v tématu Automatizace zřizování uživatelů a deprovisioning u aplikací SaaS pomocí služby Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit kroky popsané v tomto kurzu, potřebujete následující:

* Tenanta Azure AD.
* Nájemník Zscaler Tři.
* Uživatelský účet ve Zscaler Tři s oprávněními správce.

> [!NOTE]
> Integrace zřizování Azure AD závisí na rozhraní API Zscaler ZSCloud SCIM, které je dostupné pro podnikové účty.

## <a name="adding-zscaler-three-from-the-gallery"></a>Přidání Zscaler tři z galerie

Než nakonfigurujete Zscaler 3 pro automatické zřizování uživatelů pomocí Azure AD, je potřeba přidat Zscaler Tři z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

Na [webu Azure Portal](https://portal.azure.com)v levém podokně vyberte **Službu Azure Active Directory**:

![Vyberte Azure Active Directory.](common/select-azuread.png)

Přejděte do **podnikové aplikace** a vyberte **všechny aplikace**:

![Podnikové aplikace](common/enterprise-applications.png)

Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace:**

![Vybrat novou aplikaci](common/add-new-app.png)

Do vyhledávacího pole zadejte **Zscaler Three**. Ve výsledcích vyberte **Zscaler 3** a pak vyberte **Přidat**.

![Seznam výsledků](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>Přiřazení uživatelů k Zscaleru 3

Uživatelům Azure AD je potřeba před použitím přiřadit přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů jsou synchronizováni pouze uživatelé nebo skupiny, které jsou přiřazeny k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, měli byste se rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Zscaler Three. Poté, co se rozhodnete, že můžete přiřadit tyto uživatele a skupiny Zscaler tři podle pokynů v [Přiřadit uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>Důležité tipy pro přiřazení uživatelů k Zscaler Tři

* Doporučujeme nejprve přiřadit jednoho uživatele Azure AD zscaler tři otestovat konfiguraci automatického zřizování uživatelů. Později můžete přiřadit více uživatelů a skupin.

* Když přiřadíte uživatele ke zscaleru 3, musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="set-up-automatic-user-provisioning"></a>Nastavení automatického zřizování uživatelů

Tato část vás provede kroky pro konfiguraci služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů a skupin v Zscaler tři na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování na základě SAML pro Zscaler Tři. Pokud tak učiníte, postupujte podle pokynů v [Zscaler tři jednotné přihlášení-na tutorial](zscaler-three-tutorial.md). Jednotné přihlašování lze konfigurovat nezávisle na automatické zřizování uživatelů, ale obě funkce se vzájemně doplňují.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a vyberte **podnikové aplikace** > **Všechny aplikace** > **Zscaler Three**:

    ![Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zscaler Tři**:

    ![Seznam aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování:**

    ![Zscaler tři zřizování](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. Nastavte **režim zřizování** na **automatický**:

    ![Nastavení režimu zřizování](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. V části **Přihlašovací údaje správce** zadejte adresu URL **klienta** a **tajný token** vašeho účtu Zscaler Three, jak je popsáno v dalším kroku.

6. Chcete-li získat **adresu URL klienta** a **tajný token**, přejděte na**nastavení ověřování** **správy** > na portálu Zscaler Three a v části **Typ ověřování**vyberte **možnost SAML** :

    ![Zscaler tři nastavení ověřování](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    Chcete-li otevřít okno **Konfigurovat saml,** vyberte **Konfigurovat saml:**

    ![Konfigurace okna SAML](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    Vyberte **Povolit zřizování založené na SCIM** a zkopírujte **základní adresu URL** a token **nosiče**a pak uložte nastavení. Na webu Azure portal vložte **základní adresu URL** do pole URL **klienta** a **token nosiče** do pole **tajný token.**

7. Po zadání hodnot do polí **adresa URL klienta** a **tajný token,** vyberte **Test připojení** a ujistěte se, že Azure AD můžete připojit k Zscaler tři. Pokud se připojení nezdaří, ujistěte se, že váš účet Zscaler Three má oprávnění správce, a zkuste to znovu.

    ![Otestování připojení](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování. Vyberte **Odeslat e-mailové oznámení, když dojde k chybě**:

    ![Nastavení e-mailu s oznámením](./media/zscaler-three-provisioning-tutorial/notification.png)

9. Vyberte **Uložit**.

10. V části Mapování vyberte **Synchronizovat uživatele služby Azure Active Directory se zscalerem Tři**: **Mappings**

    ![Synchronizace uživatelů Azure AD](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Zscaler tři v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Zscaler Tři pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte **uložit.**

    ![Mapování atributů](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. V části **Mapování** vyberte **Synchronizovat skupiny služby Azure Active Directory se zscalerem Tři**:

    ![Synchronizace skupin Azure AD](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na Zscaler tři v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v Zscaler Tři pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte **uložit.**

    ![Mapování atributů](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. Chcete-li konfigurovat filtry oborů, naleznete v pokynech v [kurzu filtru oborů](./../active-directory-saas-scoping-filters.md).

15. Chcete-li povolit službu zřizování Azure AD pro Zscaler tři, změňte **stav zřizování** **na Zapnuto** v části **Nastavení:**

    ![Stav zřizování](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. V části **Nastavení** definujte uživatele a/nebo skupiny, které chcete zřídit změřítko MA Tři: **Scope**

    ![Hodnoty oboru](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. Až budete připraveni k zřízení, vyberte **Uložit**:

    ![Vyberte možnost Uložit.](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a skupin definovaných v části **Obor** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, tak dlouho, dokud je spuštěna služba zřizování Azure AD. Průběh můžete sledovat v části **Podrobnosti synchronizace.** Můžete také sledovat odkazy na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Zscaler Tři.

Informace o tom, jak číst protokoly zřizování Azure AD, naleznete v [tématu Vytváření sestav na automatické zřizování uživatelských účtů](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png

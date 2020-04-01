---
title: 'Kurz: Konfigurace zscaleru ZSCloud pro automatické zřizování uživatelů pomocí Služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: V tomto kurzu se dozvíte, jak nakonfigurovat Službu Azure Active Directory tak, aby automaticky zřašla a zřaštila uživatelské účty z zscaleru ZSCloud.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: a752be80-d3ef-45d1-ac8f-4fb814c07b07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: af9d4aa15a4ff2784d2e1ca1334d9c24a8d12251
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77062672"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Kurz: Konfigurace Zscaleru ZSCloud pro automatické zřizování uživatelů

V tomto kurzu se dozvíte, jak nakonfigurovat Azure Active Directory (Azure AD) automaticky zřídit a deprovision uživatelů a/nebo skupin z Zscaler ZSCloud.

> [!NOTE]
> Tento kurz popisuje konektor, který je postaven na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete [v tématu Automatizace zřizování uživatelů a deprovisioning u aplikací SaaS pomocí služby Azure Active Directory](../active-directory-saas-app-provisioning.md).


## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit kroky popsané v tomto kurzu, potřebujete následující:

* Tenanta Azure AD.
* Tenant Zscaler ZSCloud.
* Uživatelský účet v Zscaler ZSCloud s oprávněními správce.

> [!NOTE]
> Integrace zřizování Azure AD závisí na rozhraní API Zscaler ZSCloud SCIM, které je dostupné pro podnikové účty.

## <a name="add-zscaler-zscloud-from-the-gallery"></a>Přidání Zscaleru ZSCloud z galerie

Než nakonfigurujete Zscaler ZSCloud pro automatické zřizování uživatelů pomocí Azure AD, je potřeba přidat Zscaler ZSCloud z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

Na [webu Azure Portal](https://portal.azure.com)v levém podokně vyberte **Službu Azure Active Directory**:

![Vyberte Azure Active Directory.](common/select-azuread.png)

Přejděte do **podnikové aplikace** a vyberte **všechny aplikace**:

![Podnikové aplikace](common/enterprise-applications.png)

Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace:**

![Vybrat novou aplikaci](common/add-new-app.png)

Do vyhledávacího pole zadejte **Zscaler ZSCloud**. Ve výsledcích vyberte **Zscaler ZSCloud** a pak vyberte **Přidat**.

![Seznam výsledků](common/search-new-app.png)

## <a name="assign-users-to-zscaler-zscloud"></a>Přiřazení uživatelů k Zscaler ZSCloud

Uživatelům Azure AD je potřeba před použitím přiřadit přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů jsou synchronizováni pouze uživatelé nebo skupiny, které jsou přiřazeny k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, měli byste se rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Zscaler ZSCloud. Poté, co se rozhodnete, že můžete přiřadit tyto uživatele a skupiny Zscaler ZSCloud podle pokynů v [Přiřadit uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Důležité tipy pro přiřazení uživatelů do Zscaler ZSCloud

* Doporučujeme nejprve přiřadit jednoho uživatele Azure AD zscaler ZSCloud k testování konfigurace automatického zřizování uživatelů. Později můžete přiřadit více uživatelů a skupin.

* Když přiřadíte uživatele k Zscaler ZSCloud, musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="set-up-automatic-user-provisioning"></a>Nastavení automatického zřizování uživatelů

Tato část vás provede kroky pro konfiguraci služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů a skupin v Zscaler ZSCloud na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování na saml pro Zscaler ZSCloud. Pokud tak učiníte, postupujte podle pokynů v [Zscaler ZSCloud jednotné přihlášení-na tutorial](zscaler-zsCloud-tutorial.md). Jednotné přihlašování lze konfigurovat nezávisle na automatické zřizování uživatelů, ale obě funkce se vzájemně doplňují.

1. Přihlaste se na [portál Azure](https://portal.azure.com) a vyberte **podnikové aplikace** > **Všechny aplikace** > **Zscaler ZSCloud**:

    ![Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zscaler ZSCloud**:

    ![Seznam aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování:**

    ![Zscaler ZSCloud Zřizování](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. Nastavte **režim zřizování** na **automatický**:

    ![Nastavení režimu zřizování](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. V části **Přihlašovací údaje správce** zadejte adresu URL **klienta** a **tajný token** vašeho účtu Zscaler ZSCloud, jak je popsáno v dalším kroku.

6. Chcete-li získat **adresu URL klienta** a **tajný token**, přejděte na**nastavení ověřování** **správy** > na portálu Zscaler ZSCloud a v části **Typ ověřování**vyberte **možnost SAML** :

    ![Nastavení ověřování Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    Chcete-li otevřít okno **Konfigurovat saml,** vyberte **Konfigurovat saml:**

    ![Konfigurace okna SAML](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)

    Vyberte **Povolit zřizování založené na SCIM** a zkopírujte **základní adresu URL** a token **nosiče**a pak uložte nastavení. Na webu Azure portal vložte **základní adresu URL** do pole URL **klienta** a **token nosiče** do pole **tajný token.**

7. Po zadání hodnot do polí **Adresa URL klienta** a **tajný token,** vyberte **Test připojení** a ujistěte se, že Azure AD můžete připojit k Zscaler ZSCloud. Pokud se připojení nezdaří, ujistěte se, že váš účet Zscaler ZSCloud má oprávnění správce a zkuste to znovu.

    ![Otestování připojení](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)

8. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování. Vyberte **Odeslat e-mailové oznámení, když dojde k chybě**:

    ![Nastavení e-mailu s oznámením](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. Vyberte **Uložit**.

10. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s zscalerZSCloud**:

    ![Synchronizace uživatelů Azure AD](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Zscaler ZSCloud v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Zscaler ZSCloud pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte **uložit.**

    ![Mapování atributů](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. V části **Mapování** vyberte **Synchronizovat skupiny Služby Active Directory Azure s zscalerZSCloud**:

    ![Synchronizace skupin Azure AD](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na Zscaler ZSCloud v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v Zscaler ZSCloud pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte **uložit.**

    ![Mapování atributů](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. Chcete-li konfigurovat filtry oborů, naleznete v pokynech v [kurzu filtru oborů](./../active-directory-saas-scoping-filters.md).

15. Chcete-li povolit službu zřizování Azure AD pro Zscaler ZSCloud, změňte **stav zřizování** **na Zapnuto** v části **Nastavení:**

    ![Stav zřizování](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. Definujte uživatele a/nebo skupiny, které chcete zřídit Zscaler ZSCloud, výběrem požadovaných hodnot v části **Obor** v části **Nastavení:**

    ![Hodnoty oboru](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. Až budete připraveni k zřízení, vyberte **Uložit**:

    ![Vyberte možnost Uložit.](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a skupin definovaných v části **Obor** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, tak dlouho, dokud je spuštěna služba zřizování Azure AD. Průběh můžete sledovat v části **Podrobnosti synchronizace.** Můžete také sledovat odkazy na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Zscaler ZSCloud.

Informace o tom, jak číst protokoly zřizování Azure AD, naleznete v [tématu Vytváření sestav na automatické zřizování uživatelských účtů](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png

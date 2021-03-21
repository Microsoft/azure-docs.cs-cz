---
title: 'Kurz: Konfigurace Zendesk pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro Automatické zřizování a rušení uživatelských účtů pro Zendesk.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 620dd8fd586352ebeaf097a8f870a606f8e06c01
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359710"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Kurz: Konfigurace Zendesk pro Automatické zřizování uživatelů

V tomto kurzu se dozvíte, jak pomocí Zendesk a Azure Active Directory (Azure AD) nakonfigurovat službu Azure AD tak, aby automaticky zřídila a zrušila zřízení uživatelů a skupin na Zendesk.

> [!NOTE]
> Tento kurz popisuje konektor, který je založený na službě zřizování uživatelů Azure AD. Informace o tom, jak tato služba funguje, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro aplikace typu software jako služba (SaaS) s Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že máte následující:

* Tenanta Azure AD.
* Zendesk tenant s plánem Professional nebo vyšším povoleným.
* Uživatelský účet v Zendesk s oprávněními správce.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Přidat Zendesk z Azure Marketplace

Před konfigurací Zendesk pro Automatické zřizování uživatelů pomocí Azure AD přidejte Zendesk z Azure Marketplace do seznamu spravovaných aplikací SaaS.

Pokud chcete přidat Zendesk z webu Marketplace, postupujte podle těchto kroků.

1. V [Azure Portal](https://portal.azure.com)v navigačním podokně na levé straně vyberte **Azure Active Directory**.

    ![Ikona Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte v horní části dialogového okna možnost **Nová aplikace** .

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zendesk** a na panelu výsledek vyberte **Zendesk** . Chcete-li přidat aplikaci, vyberte možnost **Přidat**.

    ![Zendesk v seznamu výsledků](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Přiřazení uživatelů k Zendesk

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé nebo skupiny, které jsou přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, rozhodněte se, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Zendesk. Pokud chcete přiřadit tyto uživatele nebo skupiny k Zendesk, postupujte podle pokynů v tématu [přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Důležité tipy pro přiřazení uživatelů k Zendesk

* V současné době se role Zendesk automaticky a dynamicky naplní v uživatelském rozhraní Azure Portal. Než přiřadíte role Zendesk uživatelům, ujistěte se, že počáteční synchronizace byla dokončena proti Zendesk pro získání nejnovějších rolí v tenantovi Zendesk.

* Doporučujeme, abyste jednomu uživateli Azure AD přiřadili Zendesk k otestování počáteční automatické konfigurace zřizování uživatelů. Později, až budou testy úspěšné, můžete přiřadit další uživatele nebo skupiny.

* Když přiřadíte uživatele k Zendesk, vyberte libovolnou platnou roli specifickou pro aplikaci, pokud je k dispozici v dialogovém okně přiřazení. Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Konfigurace automatického zřizování uživatelů na Zendesk 

Tato část vás provede postupem konfigurace služby zřizování Azure AD. Slouží k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Zendesk na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování založené na SAML pro Zendesk. Postupujte podle pokynů v [kurzu Zendesk jednotného přihlašování](zendesk-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Zendesk ve službě Azure AD

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte možnost **podnikové aplikace**  >  **všechny aplikace**  >  **Zendesk**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Zendesk**.

    ![Odkaz Zendesk v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Zřizování Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Režim zřizování Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. V části **přihlašovací údaje správce** zadejte uživatelské jméno správce, tajný token a doménu vašeho účtu Zendesk. Příklady těchto hodnot:

   * V poli **uživatelské jméno správce** Vyplňte uživatelské jméno účtu správce v tenantovi Zendesk. Příklad: admin@contoso.com.

   * Do pole **tajný token** zadejte token tajného kódu, jak je popsáno v kroku 6.

   * V poli **doména** vyplňte subdoménu klienta Zendesk. Například pro účet s adresou URL klienta `https://my-tenant.zendesk.com` je vaše subdoména **můj tenant**.

6. Tajný token pro účet Zendesk se nachází v   >  **nastavení rozhraní API** pro správu  >  . Ujistěte se, že je **přístup k tokenu** nastavený na **povoleno**.

    ![Nastavení správce Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Tajný token Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Po vyplnění polí zobrazených v kroku 5 vyberte **Test připojení** , abyste se ujistili, že se služba Azure AD může připojit k Zendesk. Pokud se připojení nepovede, ujistěte se, že má váš účet Zendesk oprávnění správce, a zkuste to znovu.

    ![Zendesk test připojení](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, ve které se budou zobrazovat oznámení o chybách zřizování. Zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mailová oznámení Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Vyberte **Uložit**.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Zendesk**.

    ![Synchronizace uživatelů Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Zkontrolujte atributy uživatele synchronizované z Azure AD do Zendesk v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Zendesk pro operace aktualizace. Chcete-li uložit všechny změny, vyberte možnost **Uložit**.

    ![Zendesk se shodují atributy uživatele.](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do Zendesk**.

    ![Synchronizace skupiny Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Zkontrolujte atributy skupiny synchronizované z Azure AD do Zendesk v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v Zendesk pro operace aktualizace. Chcete-li uložit všechny změny, vyberte možnost **Uložit**.

    ![Zendesk vyhovující atributy skupiny](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Pokud chcete nakonfigurovat filtry oborů, postupujte podle pokynů v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pokud chcete povolit službu Azure AD Provisioning pro Zendesk, v části **Nastavení** změňte **stav zřizování** na **zapnuto**.

    ![Stav zřizování Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definujte uživatele nebo skupiny, které chcete zřídit pro Zendesk. V části **Nastavení** vyberte hodnoty, které chcete v **oboru**.

    ![Obor Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Zendesk Uložit](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než pozdější synchronizace. Vyskytují se přibližně každých 40 minut, pokud se služba zřizování Azure AD spouští. 

V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování. Tato sestava popisuje všechny akce prováděné službou zřizování Azure AD v Zendesk.

Informace o tom, jak číst protokoly zřizování služby Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Zendesk podporuje používání skupin jenom pro uživatele s rolemi **agenta** . Další informace najdete v [dokumentaci k Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Při přiřazení vlastní role uživateli nebo skupině přiřadí služba Automatické zřizování uživatelů Azure AD také výchozího **agenta** rolí. Vlastní roli lze přiřadit pouze agentům. Další informace najdete v [dokumentaci k rozhraní Zendesk API](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png

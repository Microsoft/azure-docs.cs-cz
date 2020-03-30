---
title: 'Kurz: Konfigurace cloudu Atlassian pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat Službu Azure Active Directory tak, aby automaticky zřašovala a odpočitate uživatelské účty do cloudu Atlassian.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 7ddccef00cf1b5ad524c0e1eaa7aed52c0e55197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059330"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Kurz: Konfigurace cloudu Atlassian pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které je třeba provést v Atlassian Cloud a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-provision uživatelů a/nebo skupin atlassian Cloud.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Tenant atlassianského cloudu](https://www.atlassian.com/licensing/cloud)
* Uživatelský účet v cloudu Atlassian s oprávněními správce.

> [!NOTE]
> Integrace zřizování Azure AD závisí na **rozhraní API ATLASSIAN Cloud SCIM**, které je k dispozici pro týmy Atlassian Cloud.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Přidání atlassian cloudu z galerie

Před konfigurací Atlassian Cloud pro automatické zřizování uživatelů pomocí Azure AD, je třeba přidat Atlassian Cloud z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Atlassian Cloud z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Atlassian Cloud**, vpanelu s výsledky vyberte **Atlassian Cloud** a pak kliknutím na tlačítko **Přidat** aplikaci přidejte.

    ![Atlassian Cloud v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Přiřazení uživatelů ke službě Atlassian Cloud

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup ke cloudu Atlassian. Jakmile se rozhodnete, můžete tyto uživatele a/nebo skupiny přiřadit ke službě Atlassian Cloud podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Důležité tipy pro přiřazení uživatelů do cloudu Atlassian

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k Atlassian Cloud k testování automatické konfigurace zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele ke službě Atlassian Cloud musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Konfigurace automatického zřizování uživatelů do cloudu atlassian 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v atlassian cloudu na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování společnosti Atlassian Cloud založené na saml podle pokynů uvedených v [kurzu jednotného přihlašování atlassian Cloud](atlassian-cloud-tutorial.md). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Atlassian Cloud ve službě Azure AD:

1. Přihlaste se na [portál Azure](https://portal.azure.com) a vyberte **Podnikové aplikace**, vyberte Všechny **aplikace**a pak vyberte **Atlassian Cloud**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Atlassian Cloud**.

    ![Odkaz Atlassian Cloud v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Přejděte na [správce organizace Atlassian](https://admin.atlassian.com) **> vyberte adresář org >**.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. Klikněte na **User Provisioning** a **klikněte**na Vytvořit adresář . Zkopírujte **základní adresu URL adresáře** a token **nosiče** do polí **URL klienta** a **tajného tokenu.**

    ![Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Provisioning Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![Cloud Provisioning Atlassian Cloud Provisioning Atlassian Cloud Provisioning Atlassian Cloud Provisioning Atlassian Cloud Provisioning Atlassian Cloud Provisioning Atlassian Cloud Provisioning Atlassian Cloud Provisioning Atlassi](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. V části **Přihlašovací údaje správce** zadejte adresu URL **klienta** a **tajný token** účtu vašeho atlassianu Cloud. Příklady těchto hodnot jsou:

   * V poli **Adresa URL klienta** vyplňte konkrétní koncový bod klienta, který obdržíte od atlasu, jak je popsáno v kroku 6. Například: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * V poli **Tajný token** naplňte tajný token, jak je popsáno v kroku 6.

8. Po naplnění polí uvedených v kroku 7 klikněte na **testovat připojení** a ujistěte se, že se Azure AD může připojit ke cloudu Atlassian. Pokud se připojení nezdaří, ujistěte se, že váš účet Atlassian Cloud má oprávnění správce a zkuste to znovu.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. Klikněte na **Uložit**.

11. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory se službou Atlassian Cloud**.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Atlassian Cloud v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v atlassian cloudu pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. V části **Mapování** vyberte **Synchronizovat skupiny Služby Active Directory Azure se službou Atlassian Cloud**.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do Atlassian Cloud v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v atlassian cloudu pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

16. Pokud chcete povolit zřizovací službu Azure AD pro Atlassian Cloud, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. Definujte uživatele a/nebo skupiny, které chcete zřídit společnosti Atlassian Cloud, výběrem požadovaných hodnot v **části Nastavení.** **Settings**

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v cloudu Atlassian.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektorů

* Atlassian Cloud umožňuje zřizování uživatelů pouze z [ověřených domén](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian Cloud dnes nepodporuje přejmenování skupin. To znamená, že žádné změny v displayName skupiny ve službě Azure AD nebudou aktualizovány a projeví se v cloudu Atlassian.
* Hodnota atributu uživatele **pošty** ve službě Azure AD je vyplněna pouze v případě, že uživatel má poštovní schránku Microsoft Exchange. Pokud uživatel nemá, doporučujeme namapovat jiný požadovaný atribut na atribut **e-mailů** v atlassian cloudu.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
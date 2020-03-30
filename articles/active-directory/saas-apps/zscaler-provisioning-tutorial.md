---
title: 'Kurz: Konfigurace zscaleru pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty do Zscaleru.
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
ms.author: jeedes
ms.openlocfilehash: 8add1f57b566d746d464c1ca165938fc112a9784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062706"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Kurz: Konfigurace Zscaleru pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Zscaler a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-zřizování uživatelů nebo skupin z Zscaler.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující:

* Tenanta Azure AD.
* Zscaler nájemník.
* Uživatelský účet v Zscaleru s oprávněními správce.

> [!NOTE]
> Integrace zřizování Azure AD závisí na rozhraní API Zscaler SCIM, které je k dispozici vývojářům Zscaler pro účty s balíčkem Enterprise.

## <a name="adding-zscaler-from-the-gallery"></a>Přidání Zscaleru z galerie

Před konfigurací Zscaler pro automatické zřizování uživatelů s Azure AD, je potřeba přidat Zscaler z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Zscaler z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscaler**, z panelu výsledků vyberte **Zscaler** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

    ![Zscaler v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Přiřazení uživatelů k Zscaleru

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Zscaleru. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny Zscaler podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Důležité tipy pro přiřazení uživatelů do Zscaleru

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a Zscaler otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k zscaleru musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Konfigurace automatického zřizování uživatelů do Zscaleru

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Zscaleru na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování na saml pro Zscaler podle pokynů uvedených v [kurzu jednotného přihlášení Zscaler](zscaler-tutorial.md). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Zscaler ve službě Azure AD:

1. Přihlaste se na [portál Azure](https://portal.azure.com) portal a vyberte **Podnikové aplikace**, vyberte Všechny **aplikace**a pak vyberte **Zscaler**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zscaler**.

    ![Propojení Zscaler v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Zscaler Zzásobování](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Zscaler Zzásobování](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. V části **Přihlašovací údaje správce** zadejte adresu URL **klienta** a **tajný token** vašeho účtu Zscaler, jak je popsáno v kroku 6.

6. Chcete-li získat **adresu URL klienta** a **tajný token**, přejděte na nastavení ověřování **> správy** v uživatelském rozhraní portálu Zscaler a klikněte na **SAML** v části **Typ ověřování**.

    ![Zscaler Zzásobování](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Kliknutím na **Konfigurovat SAML** otevřete **možnosti konfigurace SAML.**

    ![Zscaler Zzásobování](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Chcete-li načíst **základní adresu URL** a token **nosiče**, vyberte **možnost Povolit zřizování založené na SCIM** a uložte nastavení. Zkopírujte **základní adresu URL** na adresu URL **klienta**a **token nosiče** na **tajný token** na webu Azure Portal.

7. Po vyplnění polí uvedených v kroku 5 klikněte na **test připojení** a ujistěte se, že Azure AD můžete připojit k Zscaler. Pokud se připojení nezdaří, ujistěte se, že váš účet Zscaler má oprávnění správce a zkuste to znovu.

    ![Zscaler Zzásobování](./media/zscaler-provisioning-tutorial/test-connection.png)

8. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![Zscaler Zzásobování](./media/zscaler-provisioning-tutorial/notification.png)

9. Klikněte na **Uložit**.

10. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s programem Zscaler**.

    ![Zscaler Zzásobování](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Zscaler v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Zscaler pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Zscaler Zzásobování](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. V části **Mapování** vyberte **Synchronizovat skupiny Služby Azure Active Directory se zscalerem**.

    ![Zscaler Zzásobování](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do Zscaler v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v Zscaler pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Zscaler Zzásobování](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](./../active-directory-saas-scoping-filters.md).

15. Chcete-li povolit službu zřizování Azure AD pro Zscaler, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Zscaler Zzásobování](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Definujte uživatele nebo skupiny, které chcete zřídit Zscaler výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Zscaler Zzásobování](./media/zscaler-provisioning-tutorial/scoping.png)

17. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Zscaler Zzásobování](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Zscaleru.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png

---
title: 'Kurz: Konfigurace dvou Zscalerem pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: V tomto kurzu se dozvíte, jak konfigurovat Azure Active Directory a automaticky zřizovat a zrušit jejich zřízení uživatelských účtů do dvou Zscalerem.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0a250fcd-6ca1-47c2-a780-7a6278186a69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 0147f0bea30a13cea17baab8d40e550c0910a3fa
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672818"
---
# <a name="tutorial-configure-zscaler-two-for-automatic-user-provisioning"></a>Kurz: Konfigurace dvou Zscalerem pro automatické zřizování uživatelů

V tomto kurzu se dozvíte, jak konfigurovat Azure Active Directory (Azure AD) pro automatické zřizování a zrušení zřízení uživatele a/nebo skupiny, které se Zscalerem dvě.

> [!NOTE]
> Tento kurz popisuje konektor, který je založený na služba zřizování uživatelů Azure AD. Důležité podrobnosti o této službě dělá a jak funguje a odpovědi na nejčastější dotazy, naleznete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o Azure obecné podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

K dokončení kroků uvedených v tomto kurzu, budete potřebovat následující:

* Tenanta Azure AD.
* Dvě Zscalerem tenanta.
* Účet uživatele s oprávněními správce v Zscalerem dvě.

> [!NOTE]
> Zřizování integrace služby Azure AD spoléhá na rozhraní API Zscalerem dvě SCIM, která je dostupná pro účty organizace.

## <a name="add-zscaler-two-from-the-gallery"></a>Přidání dvou Zscalerem z Galerie

Před konfigurací Zscalerem dvě pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Zscalerem dvě z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**:

![Vyberte Azure Active Directory.](common/select-azuread.png)

Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**:

![Podnikové aplikace](common/enterprise-applications.png)

Chcete-li přidat aplikaci, vyberte **novou aplikaci** v horní části okna:

![Vyberte novou aplikaci](common/add-new-app.png)

Do vyhledávacího pole zadejte **Zscalerem dvě**. Vyberte **Zscalerem dvě** výsledků a pak vyberte **přidat**.

![Seznam výsledků](common/search-new-app.png)

## <a name="assign-users-to-zscaler-two"></a>Přiřazení uživatelů ke dvěma Zscaler

Uživatelé Azure AD je potřeba přiřadit přístup k vybrané aplikace předtím, než budou je moct používat. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele nebo skupiny, které jsou přiřazeny k aplikaci ve službě Azure AD.

Než začnete konfigurovat a povolit automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k Zscalerem dvě. Po zvolení, které můžete přiřadit tyto uživatele a skupiny Zscalerem dvě podle pokynů v [přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-two"></a>Důležité tipy pro přiřazování uživatelů do dvou Zscaler

* Doporučujeme nejprve přiřaďte jednoho uživatele Azure AD k Zscalerem dvě testování automatické konfigurace zřizování uživatelů. Další uživatelé a skupiny můžete později přiřadit.

* Když přiřadíte uživatele k Zscalerem dvě, budete muset vybrat roli žádné platné specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="set-up-automatic-user-provisioning"></a>Nastavení automatické zřizování uživatelů

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a skupiny v Zscalerem dvě na základě uživatele a přiřazení skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro Zscalerem dvě. Je-li provést, postupujte podle pokynů [Zscalerem dvě jednotné přihlašování – kurz](zscaler-two-tutorial.md). Jednotné přihlašování, můžete nakonfigurovat nezávisle na automatické zřizování uživatelů, ale dvě funkce se vzájemně doplňují.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a vyberte **podnikové aplikace** > **všechny aplikace** > **Zscalerem dvě**:

    ![Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zscalerem dvě**:

    ![Seznam aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu:

    ![Zřizování Zscalerem dvě](./media/zscaler-two-provisioning-tutorial/provisioning-tab.png)

4. Nastavte **režim zřizování** k **automatické**:

    ![Nastavit režim zřizování](./media/zscaler-two-provisioning-tutorial/provisioning-credentials.png)

5. V **přihlašovacích údajů správce** části, zadejte **adresy URL Tenanta** a **tajný klíč tokenu** účtu Zscalerem dvě, jak je popsáno v dalším kroku.

6. Chcete-li získat **adresy URL Tenanta** a **tajný klíč tokenu**, přejděte na stránku **správu** > **nastavení ověřování** ve dvou Zscaler portál a vyberte **SAML** pod **typ ověřování**:

    ![Zscalerem dvě nastavení ověřování](./media/zscaler-two-provisioning-tutorial/secret-token-1.png)

    Vyberte **konfigurace SAML** otevřít **konfigurace SAML** okno:

    ![Konfigurovat časový interval pro SAML](./media/zscaler-two-provisioning-tutorial/secret-token-2.png)

    Vyberte **Enable SCIM-Based zřizování** a zkopírujte **základní adresu URL** a **nosného tokenu**a potom uložte nastavení. Na webu Azure Portal, vložte **základní adresu URL** do **adresy URL Tenanta** pole a **nosného tokenu** do **tajný klíč tokenu** pole.

7. Po zadání hodnoty v **adresy URL Tenanta** a **tajný klíč tokenu** polí, vyberte **Test připojení** k Ujistěte se, že Azure AD se můžete připojit k Zscalerem dvě. Pokud se nepovede, ujistěte se, že dvě Zscalerem účet má oprávnění správce a zkuste to znovu.

    ![Otestování připojení](./media/zscaler-two-provisioning-tutorial/test-connection.png)

8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, která chcete přijímat oznámení chyba zřizování. Vyberte **odeslání e-mailové oznámení, když dojde k selhání**:

    ![Nastavení e-mailové oznámení](./media/zscaler-two-provisioning-tutorial/notification.png)

9. Vyberte **Uložit**.

10. V **mapování** vyberte **synchronizace Azure Active Directory uživatelům ZscalerTwo**:

    ![Synchronizace uživatelů Azure AD](./media/zscaler-two-provisioning-tutorial/user-mappings.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do dvou Zscalerem v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty ve dvou Zscalerem pro operace update. Vyberte **Uložit** potvrďte všechny změny.

    ![Mapování atributů](./media/zscaler-two-provisioning-tutorial/user-attribute-mappings.png)

12. V **mapování** vyberte **synchronizaci skupinám Azure Active Directory k ZscalerTwo**:

    ![Synchronizovat skupiny Azure AD](./media/zscaler-two-provisioning-tutorial/group-mappings.png)

13. Zkontrolujte skupiny atributů, které se synchronizují ze služby Azure AD do dvou Zscalerem v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají k vyhodnocení skupiny ve dvou Zscalerem pro operace update. Vyberte **Uložit** potvrďte všechny změny.

    ![Mapování atributů](./media/zscaler-two-provisioning-tutorial/group-attribute-mappings.png)

14. Konfigurace filtrů oborů, použijte pokyny v [Scoping filtr kurzu](./../active-directory-saas-scoping-filters.md).

15. Povolit zřizování Zscalerem dvě služby Azure AD, změňte **stavu zřizování** k **na** v **nastavení** části:

    ![Stav zřizování](./media/zscaler-two-provisioning-tutorial/provisioning-status.png)

16. Definovat uživatele a/nebo skupiny, které chcete ke zřízení na dvou Zscalerem výběrem hodnoty, které chcete v rámci **oboru** v **nastavení** části:

    ![Hodnoty oboru](./media/zscaler-two-provisioning-tutorial/scoping.png)

17. Jakmile budete připraveni ke zřízení, vyberte **Uložit**:

    ![Vyberte Uložit.](./media/zscaler-two-provisioning-tutorial/save-provisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a skupiny definované v části **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle, než se synchronizuje následné, ke kterým dochází každých 40 minut, tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete sledovat průběh **podrobnosti synchronizace** oddílu. Můžete začít sledovat také odkazy na sestavu aktivit zřizování, který popisuje všechny akce, které provádí služba na dvou Zscalerem zřizování Azure AD.

Informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-03.png

---
title: 'Kurz: Konfigurace Zscalerem ZSCloud pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: V tomto kurzu se dozvíte, jak konfigurovat Azure Active Directory a automaticky zřizovat a zrušit jejich zřízení uživatelských účtů do Zscalerem ZSCloud.
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
ms.author: v-ant-msft
ms.openlocfilehash: 13231fe75ff173999f3a7fa4728f583c6f04c54d
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579168"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Kurz: Konfigurace Zscalerem ZSCloud pro automatické zřizování uživatelů

V tomto kurzu se dozvíte, jak konfigurovat Azure Active Directory (Azure AD) a automaticky zřizovat a zrušit jejich zřízení uživatele a/nebo skupiny, které se Zscalerem ZSCloud.

> [!NOTE]
> Tento kurz popisuje konektor, který je založený na služba zřizování uživatelů Azure AD. Důležité podrobnosti o této službě dělá a jak funguje a odpovědi na nejčastější dotazy, naleznete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o Azure obecné podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

K dokončení kroků uvedených v tomto kurzu, budete potřebovat následující:

* Tenanta Azure AD.
* Tenanta Zscaler ZSCloud.
* Účet uživatele s oprávněními správce v Zscalerem ZSCloud.

> [!NOTE]
> Zřizování integrace služby Azure AD spoléhá na Zscalerem ZSCloud SCIM rozhraní API, která je dostupná pro účty organizace.

## <a name="add-zscaler-zscloud-from-the-gallery"></a>Přidat z Galerie Zscaler ZSCloud

Než začnete konfigurovat Zscalerem ZSCloud pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat Zscalerem ZSCloud z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**:

![Vyberte Azure Active Directory.](common/select-azuread.png)

Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**:

![Podnikové aplikace](common/enterprise-applications.png)

Chcete-li přidat aplikaci, vyberte **novou aplikaci** v horní části okna:

![Vyberte novou aplikaci](common/add-new-app.png)

Do vyhledávacího pole zadejte **Zscalerem ZSCloud**. Vyberte **Zscalerem ZSCloud** výsledků a pak vyberte **přidat**.

![Seznam výsledků](common/search-new-app.png)

## <a name="assign-users-to-zscaler-zscloud"></a>Přiřazení uživatelů k Zscaler ZSCloud

Uživatelé Azure AD je potřeba přiřadit přístup k vybrané aplikace předtím, než budou je moct používat. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele nebo skupiny, které jsou přiřazeny k aplikaci ve službě Azure AD.

Než začnete konfigurovat a povolit automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k Zscalerem ZSCloud. Po zvolení, které můžete přiřadit tyto uživatele a skupiny Zscalerem ZSCloud podle pokynů v [přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Důležité tipy pro přiřazování uživatelů k Zscaler ZSCloud

* Doporučujeme nejprve přiřaďte jednoho uživatele Azure AD k Zscalerem ZSCloud otestovat automatické konfigurace zřizování uživatelů. Další uživatelé a skupiny můžete později přiřadit.

* Když přiřadíte uživatele k Zscalerem ZSCloud, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="set-up-automatic-user-provisioning"></a>Nastavení automatické zřizování uživatelů

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a skupiny v Zscalerem ZSCloud na základě uživatele a přiřazení skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro Zscalerem ZSCloud. Je-li provést, postupujte podle pokynů [Zscalerem ZSCloud jednotné přihlašování – kurz](zscaler-zsCloud-tutorial.md). Jednotné přihlašování, můžete nakonfigurovat nezávisle na automatické zřizování uživatelů, ale dvě funkce se vzájemně doplňují.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a vyberte **podnikové aplikace** > **všechny aplikace** > **Zscaler ZSCloud**:

    ![Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zscalerem ZSCloud**:

    ![Seznam aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu:

    ![Zřizování Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. Nastavte **režim zřizování** k **automatické**:

    ![Nastavit režim zřizování](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. V **přihlašovacích údajů správce** části, zadejte **adresy URL Tenanta** a **tajný klíč tokenu** účtu Zscalerem ZSCloud, jak je popsáno v dalším kroku.

6. Chcete-li získat **adresy URL Tenanta** a **tajný klíč tokenu**, přejděte na **správu** > **nastavení ověřování** v Zscaler ZSCloud portal a vyberte **SAML** pod **typ ověřování**:

    ![Nastavení ověřování Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    Vyberte **konfigurace SAML** otevřít **konfigurace SAML** okno:

    ![Konfigurovat časový interval pro SAML](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)

    Vyberte **Enable SCIM-Based zřizování** a zkopírujte **základní adresu URL** a **nosného tokenu**a potom uložte nastavení. Na webu Azure Portal, vložte **základní adresu URL** do **adresy URL Tenanta** pole a **nosného tokenu** do **tajný klíč tokenu** pole.

7. Po zadání hodnoty **adresy URL Tenanta** a **tajný klíč tokenu** polí, vyberte **Test připojení** k Ujistěte se, že Azure AD se můžete připojit k Zscaler ZSCloud. Pokud se nepovede, ujistěte se, že váš účet Zscalerem ZSCloud má oprávnění správce a zkuste to znovu.

    ![Otestování připojení](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)

8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, která chcete přijímat oznámení chyba zřizování. Vyberte **odeslání e-mailové oznámení, když dojde k selhání**:

    ![Nastavení e-mailové oznámení](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. Vyberte **Uložit**.

10. V **mapování** vyberte **synchronizace Azure Active Directory uživatelům ZscalerZSCloud**:

    ![Synchronizace uživatelů Azure AD](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Zscalerem ZSCloud v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Zscalerem ZSCloud pro operace update. Vyberte **Uložit** potvrďte všechny změny.

    ![Mapování atributů](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. V **mapování** vyberte **synchronizaci skupinám Azure Active Directory k ZscalerZSCloud**:

    ![Synchronizovat skupiny Azure AD](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. Zkontrolujte skupiny atributů, které se synchronizují ze služby Azure AD do Zscalerem ZSCloud v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají k vyhodnocení skupiny v Zscalerem ZSCloud pro operace update. Vyberte **Uložit** potvrďte všechny změny.

    ![Mapování atributů](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. Konfigurace filtrů oborů, použijte pokyny v [Scoping filtr kurzu](./../active-directory-saas-scoping-filters.md).

15. Služba pro Zscalerem ZSCloud zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** části:

    ![Stav zřizování](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. Definovat uživatele a/nebo skupiny, které chcete k poskytování Zscalerem ZSCloud výběrem hodnoty, které chcete v rámci **oboru** v **nastavení** části:

    ![Hodnoty oboru](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. Jakmile budete připraveni ke zřízení, vyberte **Uložit**:

    ![Vyberte Uložit.](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a skupiny definované v části **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle, než se synchronizuje následné, ke kterým dochází každých 40 minut, tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete sledovat průběh **podrobnosti synchronizace** oddílu. Můžete začít sledovat také odkazy na sestavu aktivit zřizování, který popisuje všechny akce, které provádí služba na Zscalerem ZSCloud zřizování Azure AD.

Informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png

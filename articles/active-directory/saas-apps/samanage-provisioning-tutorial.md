---
title: 'Kurz: Konfigurace Samanage pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro Automatické zřizování a rušení uživatelských účtů pro Samanage.
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
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77060444"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Kurz: Konfigurace Samanage pro Automatické zřizování uživatelů

V tomto kurzu se dozvíte, jak pomocí Samanage a Azure Active Directory (Azure AD) nakonfigurovat službu Azure AD tak, aby automaticky zřídila a zrušila zřízení uživatelů a skupin na Samanage.

> [!NOTE]
> Tento kurz popisuje konektor, který je založený na službě zřizování uživatelů Azure AD. Informace o tom, jak tato služba funguje, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro aplikace typu software jako služba (SaaS) s Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že máte následující:

* Tenanta Azure AD.
* [Samanage tenant](https://www.samanage.com/pricing/) s balíčkem Professional.
* Uživatelský účet v Samanage s oprávněními správce.

> [!NOTE]
> Integrace zřizování Azure AD spoléhá na [rozhraní Samanage REST API](https://www.samanage.com/api/). Toto rozhraní API je k dispozici pro vývojáře Samanage pro účty s profesionálním balíčkem.

## <a name="add-samanage-from-the-azure-marketplace"></a>Přidat Samanage z Azure Marketplace

Před konfigurací Samanage pro Automatické zřizování uživatelů pomocí Azure AD přidejte Samanage z Azure Marketplace do seznamu spravovaných aplikací SaaS.

Pokud chcete přidat Samanage z webu Marketplace, postupujte podle těchto kroků.

1. V [Azure Portal](https://portal.azure.com)v navigačním podokně na levé straně vyberte **Azure Active Directory**.

    ![Ikona Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte v horní části dialogového okna možnost **Nová aplikace** .

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Samanage** a na panelu výsledek vyberte **Samanage** . Chcete-li přidat aplikaci, vyberte možnost **Přidat**.

    ![Samanage v seznamu výsledků](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Přiřazení uživatelů k Samanage

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé nebo skupiny, které jsou přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, rozhodněte se, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Samanage. Pokud chcete přiřadit tyto uživatele nebo skupiny k Samanage, postupujte podle pokynů v tématu [přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Důležité tipy pro přiřazení uživatelů k Samanage

*    V současné době se role Samanage automaticky a dynamicky naplní v uživatelském rozhraní Azure Portal. Než přiřadíte role Samanage uživatelům, ujistěte se, že počáteční synchronizace byla dokončena proti Samanage pro získání nejnovějších rolí v tenantovi Samanage.

*    Doporučujeme, abyste jednomu uživateli Azure AD přiřadili Samanage k otestování počáteční automatické konfigurace zřizování uživatelů. Další uživatele a skupiny můžete přiřadit později po úspěšném provedení testů.

*    Když přiřadíte uživatele k Samanage, vyberte libovolnou platnou roli specifickou pro aplikaci, pokud je k dispozici v dialogovém okně přiřazení. Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Konfigurace automatického zřizování uživatelů na Samanage

Tato část vás provede postupem konfigurace služby zřizování Azure AD. Slouží k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Samanage na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování založené na SAML pro Samanage. Postupujte podle pokynů v [kurzu Samanage jednotného přihlašování](samanage-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Samanage ve službě Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte možnost **podnikové aplikace** > **všechny aplikace** > **Samanage**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Samanage**.

    ![Odkaz Samanage v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Zřizování Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL vašeho tenanta** Samanage a **tajný token**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Samanage. Pokud se připojení nepovede, ujistěte se, že má váš účet Samanage oprávnění správce, a zkuste to znovu.

    ![Samanage test připojení](./media/samanage-provisioning-tutorial/provisioning.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, ve které se budou zobrazovat oznámení o chybách zřizování. Zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mailová oznámení Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Samanage**.

    ![Synchronizace uživatelů Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Samanage v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Samanage pro operace aktualizace. Chcete-li uložit všechny změny, vyberte možnost **Uložit**.

    ![Samanage se shodují atributy uživatele.](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. Pokud chcete povolit mapování skupin, vyberte v části **mapování** možnost **synchronizovat Azure Active Directory skupiny do Samanage**.

    ![Synchronizace skupiny Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. Pro synchronizaci skupin nastavte možnost **povoleno** na **Ano** . Zkontrolujte atributy skupiny synchronizované z Azure AD do Samanage v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Samanage pro operace aktualizace. Chcete-li uložit všechny změny, vyberte možnost **Uložit**.

    ![Samanage vyhovující atributy skupiny](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Pokud chcete nakonfigurovat filtry oborů, postupujte podle pokynů v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro Samanage, v části **Nastavení** změňte **stav zřizování** na **zapnuto**.

    ![Stav zřizování Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro Samanage. V části **Nastavení** vyberte hodnoty, které chcete v **oboru**. Když vyberete možnost **synchronizovat všechny uživatele a skupiny** , zvažte omezení, jak je popsáno v následující části "omezení konektoru".

    ![Obor Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Samanage Uložit](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než pozdější synchronizace. Vyskytují se přibližně každých 40 minut, pokud se služba zřizování Azure AD spouští. 

V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování. Tato sestava popisuje všechny akce prováděné službou zřizování Azure AD v Samanage.

Informace o tom, jak číst protokoly zřizování služby Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

Pokud zaškrtnete možnost **synchronizovat všechny uživatele a skupiny** a nakonfigurujete hodnotu atributu **role** Samanage, hodnota pod **výchozí hodnotou, pokud je pole null (volitelné)** , musí být vyjádřena v následujícím formátu:

- {"DisplayName": "role"}, kde role je výchozí požadovaná hodnota.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png

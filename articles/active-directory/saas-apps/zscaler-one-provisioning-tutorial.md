---
title: 'Kurz: Konfigurace Zscaler jednoho pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se, jak nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů, abyste je Zscaler.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: b8b6383c7808fd6c298d7776fc10572631bc6ddc
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359557"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Kurz: Konfigurace Zscaler jednoho pro Automatické zřizování uživatelů

V tomto kurzu se dozvíte, jak v Zscaler a Azure Active Directory (Azure AD) nakonfigurovat službu Azure AD tak, aby automaticky zřídila a zrušila zřízení uživatelů a skupin a Zscaler jednu.

> [!NOTE]
> Tento kurz popisuje konektor, který je založený na službě zřizování uživatelů Azure AD. Informace o tom, jak tato služba funguje, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro aplikace typu software jako služba (SaaS) s Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že máte následující:

* Tenanta Azure AD.
* Zscaler jednoho tenanta.
* Uživatelský účet v Zscaler, který má oprávnění správce.

> [!NOTE]
> Integrace zřizování Azure AD spoléhá na Zscaler jedno SCIM rozhraní API. Toto rozhraní API je k dispozici pro Zscaler jednoho vývojáře pro účty s podnikovým balíčkem.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Přidat Zscaler jednu z Azure Marketplace

Před konfigurací Zscaler jednu pro Automatické zřizování uživatelů pomocí Azure AD přidejte Zscaler jednu z Azure Marketplace do seznamu spravovaných aplikací SaaS.

Pokud chcete přidat Zscaler z Marketplace, postupujte podle těchto kroků.

1. V [Azure Portal](https://portal.azure.com)v navigačním podokně na levé straně vyberte **Azure Active Directory**.

    ![Ikona Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte v horní části dialogového okna možnost **Nová aplikace** .

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscaler** a na panelu výsledek vyberte **Zscaler** . Chcete-li přidat aplikaci, vyberte možnost **Přidat**.

    ![Zscaler jednu v seznamu výsledků](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Přiřaďte uživatele, aby Zscaler jednu

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé nebo skupiny, které jsou přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, rozhodněte se, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Zscaler. Pokud chcete přiřadit tyto uživatele nebo skupiny, abyste je Zscaler, postupujte podle pokynů v tématu [přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Důležité tipy pro přiřazení uživatelů k Zscaler

* Doporučujeme, abyste jednomu uživateli Azure AD přiřadili Zscaler, abyste mohli otestovat automatickou konfiguraci zřizování uživatelů. Později můžete přiřadit další uživatele nebo skupiny.

* Když přiřadíte uživatele, který ho Zscaler, vyberte libovolnou platnou roli specifickou pro aplikaci, pokud je k dispozici v dialogovém okně přiřazení. Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Konfigurace automatického zřizování uživatelů, aby Zscaler jednu

Tato část vás provede postupem konfigurace služby zřizování Azure AD. Použijte ho k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Zscaler jedné na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování založené na SAML pro Zscaler One. Postupujte podle pokynů v [jednom kurzu Zscaler jednotného přihlašování](zscaler-One-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Zscaler jednu v Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte možnost **podnikové aplikace**  >  **všechny aplikace**  >  **Zscaler jednu**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Zscaler jednu**.

    ![Zscaler jeden odkaz v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Zscaler jedno zřizování](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Zscaler jeden režim zřizování](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. V části **přihlašovací údaje správce** vyplňte pole **Adresa URL tenanta** a **tajný token** s nastavením Zscaler jednoho účtu, jak je popsáno v kroku 6.

6. Pokud chcete získat adresu URL tenanta a tajný token, použijte **Administration**  >  **nastavení ověřování** pro správu v uživatelském rozhraní Zscaler s jedním portálem. V části **typ ověřování** vyberte **SAML**.

    ![Zscaler jedno nastavení ověřování](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Vyberte **Konfigurovat SAML** a otevřete tak možnosti **Konfigurace možností SAML** .

    ![Zscaler jednu konfiguraci SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Pokud chcete získat nastavení v **základní adrese URL** a **nosném tokenu** , vyberte **Povolit SCIM-Based zřizování** . Pak nastavení uložte. Zkopírujte nastavení **základní adresy URL** do **adresy url tenanta** v Azure Portal. Zkopírujte nastavení **tokenu nosiče** do **tajného tokenu** v Azure Portal.

7. Po vyplnění polí zobrazených v kroku 5 vyberte **Test připojení** , abyste se ujistili, že se služba Azure AD může připojit k Zscaler. Pokud se připojení nepovede, ujistěte se, že má Zscaler jeden účet oprávnění správce, a zkuste to znovu.

    ![Zscaler jedno testovací připojení](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, ve které se budou zobrazovat oznámení o chybách zřizování. Zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![Zscaler jeden E-mail s oznámením](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Vyberte **Uložit**.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatele, abyste Zscaler jednu**.

    ![Zscaler jednu synchronizaci uživatelů](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Zkontrolujte atributy uživatelů synchronizované z Azure AD a Zscaler je v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Zscaler jednom pro operace aktualizace. Chcete-li uložit všechny změny, vyberte možnost **Uložit**.

    ![Zscaler jednu vyhovující atributy uživatele](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny a Zscaler jednu**.

    ![Zscaler synchronizaci jedné skupiny](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD, a Zscaler je v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v Zscaler jednom pro operace aktualizace. Chcete-li uložit všechny změny, vyberte možnost **Uložit**.

    ![Zscaler jednu vyhovující atributy skupiny](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Pokud chcete nakonfigurovat filtry oborů, postupujte podle pokynů v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pokud chcete povolit službu Azure AD Provisioning pro Zscaler jednu, v části **Nastavení** změňte **stav zřizování** na **zapnuto**.

    ![Zscaler jeden stav zřizování](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Definujte uživatele nebo skupiny, které chcete zřídit, aby Zscaler jednu. V části **Nastavení** vyberte hodnoty, které chcete v **oboru**.

    ![Zscaler jeden obor](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Zscaler jedno uložení](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než pozdější synchronizace. Vyskytují se přibližně každých 40 minut, pokud se služba zřizování Azure AD spouští. 

V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování. Tato sestava popisuje všechny akce prováděné službou zřizování Azure AD v Zscaler.

Informace o tom, jak číst protokoly zřizování služby Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
---
title: 'Kurz: Konfigurace iPass SmartConnect pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se, jak nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro iPass SmartConnect.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 397aab743da25da3882c66d0fdf32c4c4d202586
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356990"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>Kurz: Konfigurace iPass SmartConnect pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést v iPass SmartConnect a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD tak, aby automaticky zřídily a zrušily zřizování uživatelů a/nebo skupin pro iPass SmartConnect.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant IPASS SmartConnect](https://www.ipass.com/buy-ipass/).
* Uživatelský účet v iPass SmartConnect s oprávněními správce.

## <a name="assigning-users-to-ipass-smartconnect"></a>Přiřazení uživatelů k iPass SmartConnect

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k iPass SmartConnect. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k iPass SmartConnect podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>Důležité tipy pro přiřazení uživatelů k iPass SmartConnect

* Doporučujeme, abyste k testování automatické konfigurace zřizování uživatelů přiřadili jediného uživatele Azure AD, který je přiřazený k iPass SmartConnect. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele k iPass SmartConnect, musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-ipass-smartconnect-for-provisioning"></a>Nastavení iPass SmartConnect pro zřizování

Před konfigurací iPass SmartConnect pro Automatické zřizování uživatelů pomocí Azure AD bude nutné načíst informace o konfiguraci z konzoly pro správu iPass SmartConnect:

1. Pokud chcete načíst nosný token, který je potřeba k ověření vůči iPassmu koncovému bodu SmartConnect, přečtěte si velmi poprvé, co nastavíte iPass SmartConnect, protože tato hodnota je poskytována pouze potom. 
2. Pokud nemáte nosný token, přihlaste se k [IPASS týmu podpory SmartConnect](mailto:help@ipass.com) , abyste načetli nový.

## <a name="add-ipass-smartconnect-from-the-gallery"></a>Přidání iPass SmartConnect z Galerie

Pokud chcete nakonfigurovat iPass SmartConnect pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat iPass SmartConnect z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat iPass SmartConnect z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **IPASS SmartConnect** , na panelu výsledků vyberte **IPASS SmartConnect** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![iPass SmartConnect v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>Konfigurace automatického zřizování uživatelů pro iPass SmartConnect 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v iPass SmartConnect na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
>  Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro BitaBIZ podle pokynů uvedených v [kurzu IPASS SmartConnect jednotného přihlašování](ipasssmartconnect-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro iPass SmartConnect ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **IPASS SmartConnect**.

    ![Odkaz iPass SmartConnect v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://openmobile.ipass.com/moservices/scim/v1` **adresu URL tenanta**. Zadejte token nosiče načtený dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k IPASS SmartConnect. Pokud se připojení nepovede, ujistěte se, že účet iPass SmartConnect má oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele a IPASS SmartConnect**.

    :::image type="content" source="media/ipass-smartconnect-provisioning-tutorial/usermapping.png" alt-text="Snímek obrazovky oddílu mapování V části název je synchronizace Azure Active Directorych uživatelů na iPass SmartConnect viditelná." border="false":::

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do iPass SmartConnect v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v IPASS SmartConnect pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    :::image type="content" source="media/ipass-smartconnect-provisioning-tutorial/userattribute.png" alt-text="Snímek obrazovky se stránkou mapování atributů. V tabulce jsou uvedeny Azure Active Directory a iPass atributy SmartConnect a Priorita porovnání." border="false":::


10. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro iPass SmartConnect, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

12. Určete uživatele nebo skupiny, které chcete zřídit pro iPass SmartConnect, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na IPASS SmartConnect.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* iPass SmartConnect přijímá jenom uživatelská jména, která mají své domény zaregistrované v konzole pro správu iPass SmartConnect.  

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

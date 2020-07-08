---
title: 'Kurz: Konfigurace Looop pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Looop.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0efe2262-43c3-4e0c-97fa-9344385638e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: e3e25a8c27b9a5c1bc1e7673300ac8aca9377c08
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77057432"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Kurz: Konfigurace Looop pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Looop a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro Looop.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant Looop](https://www.looop.co/pricing/)
* Uživatelský účet v Looop s oprávněními správce.

## <a name="assign-users-to-looop"></a>Přiřazení uživatelů k Looop

Azure Active Directory používá koncept nazvaný přiřazení k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Looop. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Looop podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Důležité tipy pro přiřazení uživatelů k Looop

* Doporučuje se, aby se k Looop k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Looop musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-looop-for-provisioning"></a>Nastavení Looop pro zřizování

Před konfigurací Looop pro Automatické zřizování uživatelů se službou Azure AD bude nutné načíst některé informace o zřizování z Looop.

1. Přihlaste se ke [konzole správce Looop](https://app.looop.co/#/login) a vyberte **účet**. V části **Nastavení účtu** vyberte **ověřování**.

    ![Looop přidat SCIM](media/looop-provisioning-tutorial/admin.png)

2. Kliknutím na **resetovat token** v části **integrace SCIM**Vygenerujte nový token.

    ![Looop přidat SCIM](media/looop-provisioning-tutorial/resettoken.png)

3. Zkopírujte **koncový bod SCIM** a **token**. Tyto hodnoty se zadají do polí **Adresa URL tenanta** a **tajného tokenu** na kartě zřizování vaší aplikace Looop ve Azure Portal. 

    ![Looop vytvořit token](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Přidání Looop z Galerie

Pokud chcete nakonfigurovat Looop pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat Looop z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Looop**, na panelu výsledků vyberte **Looop** . 

    ![Looop v seznamu výsledků](common/search-new-app.png)

5. Vyberte tlačítko **zaregistrovat se k Looop** , které vás přesměruje na přihlašovací stránku Looop. 

    ![Looop OIDC přidat](media/looop-provisioning-tutorial/signup.png)

6. Looop je aplikace OpenIDConnect, která se rozhodne přihlásit k Looop pomocí pracovního účtu Microsoft.

    ![Looop OIDC přihlášení](media/looop-provisioning-tutorial/msftlogin.png)

7. Po úspěšném ověření Přijměte výzvu k zadání souhlasu pro stránku souhlasu. Aplikace se pak automaticky přidá do vašeho tenanta a budete přesměrováni na svůj účet Looop.

    ![Looop OIDc – souhlas](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Konfigurace automatického zřizování uživatelů na Looop 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Looop na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Looop ve službě Azure AD:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Looop**.

    ![Odkaz Looop v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://<organisation_domain>.looop.co/scim/v2` **adresu URL tenanta**. Například `https://demo.looop.co/scim/v2`. Zadejte hodnotu, kterou jste načetli a uložili dříve ze Looop v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Looop. Pokud se připojení nepovede, ujistěte se, že má váš účet Looop oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Looop**.

    ![Mapování uživatelů Looop](media/looop-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Looop v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Looop pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele Looop](media/looop-provisioning-tutorial/userattributes.png)

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny s konektorem meta Networks**.

    ![Mapování skupin Looop](media/looop-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD do meta Networks Connectoru v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v části meta Networks Connector pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Looop – atributy skupiny](media/looop-provisioning-tutorial/groupattributes.png)

10. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro Looop, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro Looop, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Looop.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)



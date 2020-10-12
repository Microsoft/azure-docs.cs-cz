---
title: 'Kurz: Konfigurace Storegate pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Storegate.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: cf71246caef377fb607a9ca7a765ec9ca5999744
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91285909"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Kurz: Konfigurace Storegate pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Storegate a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro Storegate.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant Storegate](https://www.storegate.com)
* Uživatelský účet v Storegate s oprávněními správce.

## <a name="assign-users-to-storegate"></a>Přiřazení uživatelů k Storegate

Azure Active Directory používá koncept nazvaný přiřazení k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Storegate. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Storegate podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Důležité tipy pro přiřazení uživatelů k Storegate

* Doporučuje se, aby se k Storegate k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Storegate musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-storegate-for-provisioning"></a>Nastavení Storegate pro zřizování

Před konfigurací Storegate pro Automatické zřizování uživatelů se službou Azure AD bude nutné načíst některé informace o zřizování z Storegate.

1. Přihlaste se ke [konzole správce Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) a přejděte do nastavení kliknutím na ikonu uživatele v pravém horním rohu a vyberte **Nastavení účtu**.

    ![Storegate přidat SCIM](media/storegate-provisioning-tutorial/admin.png)

2. V nastavení přejděte na **Nastavení týmové >** a ověřte, že je v části **jednotné přihlašování** zapnut přepínač přepínání.

    ![Nastavení Storegate](media/storegate-provisioning-tutorial/team.png)

    ![Přepínací tlačítko Storegate](media/storegate-provisioning-tutorial/sso.png)

3. Zkopírujte **adresu URL** a **token**klienta. Tyto hodnoty se zadají do polí **Adresa URL tenanta** a **tajného tokenu** na kartě zřizování aplikace Storegate ve Azure Portal. 

    ![Storegate vytvořit token](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Přidání Storegate z Galerie

Pokud chcete nakonfigurovat Storegate pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat Storegate z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Storegate**, na panelu výsledků vyberte **Storegate** . 

    ![Storegate v seznamu výsledků](common/search-new-app.png)

5. Vyberte tlačítko **zaregistrovat se k Storegate** , které vás přesměruje na přihlašovací stránku Storegate. 

    ![Storegate OIDC přidat](media/storegate-provisioning-tutorial/signup.png)

6.  Přihlaste se ke [konzole správce Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) a přejděte do nastavení kliknutím na ikonu uživatele v pravém horním rohu a vyberte **Nastavení účtu**.

    ![Přihlášení Storegate](media/storegate-provisioning-tutorial/admin.png)

7. V nastavení přejděte na **Nastavení týmu >** a v části jednotné přihlašování klikněte na přepínač přepnout. tím se spustí postup souhlasu. Klikněte na **aktivovat**.

    ![Storegate tým](media/storegate-provisioning-tutorial/team.png)

    ![Jednotné přihlašování Storegate](media/storegate-provisioning-tutorial/sso.png)

    ![Storegate aktivovat](media/storegate-provisioning-tutorial/activate.png)

8. Storegate je aplikace OpenIDConnect, která se rozhodne přihlásit k Storegate pomocí pracovního účtu Microsoft.

    ![Storegate OIDC přihlášení](media/storegate-provisioning-tutorial/login.png)

9. Po úspěšném ověření Přijměte výzvu k zadání souhlasu pro stránku souhlasu. Aplikace se pak automaticky přidá do vašeho tenanta a budete přesměrováni na svůj účet Storegate.

    ![Storegate OIDc – souhlas](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Konfigurace automatického zřizování uživatelů na Storegate 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Storegate na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!NOTE]
> Další informace o koncovém bodu SCIM pro Storegate najdete v [tomto](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/)tématu.

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Storegate ve službě Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Storegate**.

    ![Odkaz Storegate v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://dialpad.com/scim` **adresu URL tenanta**. Zadejte hodnotu, kterou jste načetli a uložili dříve ze Storegate v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Storegate. Pokud se připojení nepovede, ujistěte se, že má váš účet Storegate oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Storegate**.

    ![Mapování uživatelů Storegate](media/storegate-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Storegate v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Storegate pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele Storegate](media/storegate-provisioning-tutorial/userattributes.png)

10. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro Storegate, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro Storegate, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Storegate.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

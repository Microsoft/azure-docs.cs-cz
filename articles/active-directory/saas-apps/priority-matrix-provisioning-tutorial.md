---
title: 'Kurz: Konfigurace matrice priority pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro Automatické zřizování a rušení uživatelských účtů s maticí priorit.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: e79f21300325c6b451dd564bf2c69830f003f55c
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357857"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Kurz: Konfigurace matrice priority pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést v matrici priority a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD tak, aby automaticky zřídily a zrušily zřizování uživatelů a skupin na prioritní matrici.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant prioritní matice](https://appfluence.com/pricing/)
* Uživatelský účet v matrici priority s oprávněními správce.

## <a name="assign-users-to-priority-matrix"></a>Přiřadit uživatele k matrici priority

Azure Active Directory používá koncept nazvaný přiřazení k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k matrici priority. Po rozhodnutí můžete přiřadit tyto uživatele a skupiny do matice priority podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Důležité tipy pro přiřazování uživatelů k matrici priority

* Doporučujeme, aby se k otestování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD k matrici priority. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k matrici priority musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-priority-matrix-for-provisioning"></a>Nastavení matrice priority pro zřizování

Před konfigurací matrice priority pro Automatické zřizování uživatelů se službou Azure AD bude nutné načíst některé informace o zřizování z matrice priority.

1. Přihlaste se ke [konzole pro správu vaší prioritní matrice](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning).

3. Klikněte na **token pro přihlášení OAuth** pro matrici priority.

    ![Matice priority přidat SCIM](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Klikněte na tlačítko **získat nový token** . Zkopírujte **řetězec tokenu**. Tato hodnota se zadá do pole **token tajného klíče** na kartě zřizování aplikace Matrix s prioritou v Azure Portal. 

    ![Vytvoření tokenu prioritní matice](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Přidat matrici priority z Galerie

Pokud chcete nakonfigurovat matrici priority pro Automatické zřizování uživatelů pomocí Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat matrici priority z Galerie aplikací Azure AD.

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **matrici** priority a na panelu výsledky vyberte **matrice priorita** . 

    ![Matice priority v seznamu výsledků](common/search-new-app.png)

5. Vyberte tlačítko **zaregistrovat pro matrici priority** , které vás přesměruje na přihlašovací stránku prioritní matice. 

    ![OIDC matice priority přidat](media/priority-matrix-provisioning-tutorial/signup.png)

6. Jako matrice priority je aplikace OpenIDConnect, která se rozhodne přihlásit k matrici priority pomocí pracovního účtu Microsoft.

    ![OIDC přihlášení k matrici priority](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Po úspěšném ověření Přijměte výzvu k zadání souhlasu pro stránku souhlasu. Aplikace se pak automaticky přidá do vašeho tenanta a budete přesměrováni na účet Matrix s prioritou.

    ![OIDc souhlasu matic priority](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Konfigurace automatického zřizování uživatelů na prioritní matici 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v matrici priority na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!NOTE]
> Další informace o koncovém bodu SCIM matrice najdete v tématu [matice zřizování a priority uživatelů](https://appfluence.com/help/article/user-provisioning/).

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro matrici priority ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **prioritní matice**.

    ![Odkaz matice priority v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://sync.appfluence.com/scim/v2/` **adresu URL tenanta**. Zadejte hodnotu, kterou jste načetli a uložili dříve z matrice priority v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k matrici priority. Pokud se připojení nepovede, zajistěte, aby měl účet Matrix s prioritou oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé s prioritou**.

    ![Mapování uživatelů matic priority](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizované z Azure AD do matice priorit v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v matrici priority pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele matice priority](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro matrici priority, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

12. V části **Nastavení** definujte uživatele nebo skupiny, které chcete vytvořit pro matrici priority, a to tak, že vyberete požadované hodnoty v **rozsahu** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Část **Podrobnosti o synchronizaci** můžete použít ke sledování průběhu a následného odkazu na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v matrici priority.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).



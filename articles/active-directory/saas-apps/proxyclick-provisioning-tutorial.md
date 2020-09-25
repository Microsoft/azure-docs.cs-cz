---
title: 'Kurz: Konfigurace Proxyclick pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Proxyclick.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 8fdb89e4e85e18e00179b894f2587bcf4127dc22
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91255836"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Kurz: Konfigurace Proxyclick pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Proxyclick a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro Proxyclick.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant Proxyclick](https://www.proxyclick.com/pricing)
* Uživatelský účet v Proxyclick s oprávněními správce.

## <a name="add-proxyclick-from-the-gallery"></a>Přidání Proxyclick z Galerie

Před konfigurací Proxyclick pro Automatické zřizování uživatelů se službou Azure AD je nutné přidat Proxyclick z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Proxyclick z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Proxyclick**, na panelu výsledků vyberte **Proxyclick** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Proxyclick v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Přiřazování uživatelů k Proxyclick

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Proxyclick. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Proxyclick podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Důležité tipy pro přiřazení uživatelů k Proxyclick

* Doporučuje se, aby se k Proxyclick k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Proxyclick musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Konfigurace automatického zřizování uživatelů na Proxyclick 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Proxyclick na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Proxyclick podle pokynů uvedených v [kurzu Proxyclick jednotného přihlašování](proxyclick-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Proxyclick ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Proxyclick**.

    ![Odkaz Proxyclick v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. Pokud chcete načíst **adresu URL tenanta** a **tajný token** účtu Proxyclick, postupujte podle návodu popsaného v kroku 6.

6. Přihlaste se ke [konzole pro správu Proxyclick](https://app.proxyclick.com/login//?destination=%2Fdefault). Přejděte do **Nastavení**  >  **integrace**  >  **Procházet Marketplace**.

    ![Nastavení Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Integrace Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Tržiště Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Vyberte **Azure AD**. Klikněte na **instalovat hned**.

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Instalace Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Vyberte **zřizování uživatelů** a klikněte na **Spustit integraci**. 

    ![Zřizování uživatelů Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    V části **Nastavení**integrace by se teď mělo zobrazit příslušné uživatelské rozhraní konfigurace nastavení  >  **Integrations**. V části Azure AD vyberte **Nastavení** **(zřizování uživatelů)**.

    ![Proxyclick vytvořit](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Sem můžete najít **adresu URL tenanta** a **tajný token** .

    ![Proxyclick vytvořit token](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Po vyplnění polí zobrazených v kroku 5 klikněte na **Test připojení** , aby se služba Azure AD mohla připojit k Proxyclick. Pokud se připojení nepovede, ujistěte se, že má váš účet Proxyclick oprávnění správce, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Proxyclick**.

    ![Mapování uživatelů Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Zkontrolujte atributy uživatele synchronizované z Azure AD do Proxyclick v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Proxyclick pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Pokud chcete povolit službu Azure AD Provisioning pro Proxyclick, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

15. Definujte uživatele nebo skupiny, které chcete zřídit pro Proxyclick, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

16. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Proxyclick.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Proxyclick vyžaduje, aby **e-maily** a **uživatelské jméno** měly stejnou zdrojovou hodnotu. Jakékoli aktualizace obou atributů změní další hodnotu.
* Proxyclick nepodporuje zřizování pro skupiny.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).


---
title: 'Kurz: Konfigurace Zscaler Beta pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Zscaler Beta verzi.
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
ms.openlocfilehash: 0d4945ee97a46c78aac3c4ac508c5f89f5942296
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937157"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Kurz: Konfigurace Zscaler Beta pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Zscaler Beta a Azure Active Directory (Azure AD) a nakonfigurovat tak, aby služba Azure AD automaticky zřídila a zrušila zřizování uživatelů a skupin Zscaler Beta.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>


## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující:

* Tenant Azure AD
* Tenant Zscaler Beta
* Uživatelský účet ve službě Zscaler Beta s oprávněními správce

> [!NOTE]
> Integrace zřizování Azure AD spoléhá na rozhraní Zscaler Beta SCIM API, které je k dispozici pro vývojáře Zscaler Beta pro účty s podnikovým balíčkem.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Přidání beta verze Zscaler z Galerie

Před konfigurací Zscaler Beta pro Automatické zřizování uživatelů se službou Azure AD je nutné přidat Zscaler Beta z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Zscaler Beta z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscaler Beta**, vyberte **Zscaler Beta** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Zscaler Beta v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Přiřazení uživatelů k Zscaler Beta

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Zscaler Beta verzi. Až se rozhodnete, můžete těmto uživatelům nebo skupinám přiřadit Zscaler Beta podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Důležité tipy pro přiřazení uživatelů k Zscaler Beta

* Doporučujeme, aby se k otestování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD Zscaler Beta. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Zscaler Beta verzi musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Konfigurace automatického zřizování uživatelů pro Zscaler Beta

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin ve službě Zscaler Beta na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Zscaler Beta, a to podle pokynů uvedených v [kurzu Zscaler Beta jednotného přihlašování](zscaler-beta-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

> [!NOTE]
> Pokud jsou uživatelé a skupiny zřízené nebo nezřízené, doporučujeme, abyste pravidelně restartovali zřizování, aby se zajistilo, že členství ve skupině bude správně aktualizováno. Při restartování dojde k vynucení, aby naše služba znovu vyhodnotila všechny skupiny a aktualizovala členství.  

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Zscaler Beta verze ve službě Azure AD:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **Zscaler Beta**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Zscaler Beta**.

    ![Odkaz Zscaler Beta v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Seznam karet je uspořádaný do kategorií a s názvem ZScaler beta zřízení/podniková aplikace. Je vybrána karta zřízení v kategorii spravovat.](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![V rozevíracím seznamu režim zřizování byl vybrán automatický režim. K dispozici jsou pole pro přihlašovací údaje správce, které se používá pro připojení k rozhraní Zscaler Beta verze API a tlačítko Test Connection.](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta** a **tajný token** účtu Zscaler Beta, jak je popsáno v kroku 6.

6. Pokud chcete získat **adresu URL tenanta** a **tajný token**, přejděte do části **Správa > nastavení ověřování** v uživatelském rozhraní portálu Zscaler Beta a klikněte na protokol **SAML** pod položkou **typ ověřování**.

    ![V nastavení ověřování je v profilu ověřování vybraný typ adresáře hostovaná databáze a vybraný typ ověřování je SAML.](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Kliknutím na **nakonfigurovat SAML** otevřete konfiguraci možností protokolu **SAML** .

    ![V části Konfigurace SAML se vyberou možnosti povolit Automatické zřizování SAML a povolit SCIM-Based zřizování. Základní adresa URL a textová pole tokenu nosiče jsou zvýrazněny.](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Vyberte **povolit SCIM-Based zřizování** pro načtení **základní adresy URL** a **nosného tokenu** a pak nastavení uložte. Zkopírujte **základní adresu URL** na **adresu URL tenanta** a **token nosiče**  do **tajného tokenu** v Azure Portal.

7. Po vyplnění polí zobrazených v kroku 5 klikněte na **Test připojení** , aby se služba Azure AD mohla připojit k Zscaler Beta. Pokud se připojení nepovede, zajistěte, aby měl účet Zscaler Beta oprávnění správce, a zkuste to znovu.

    ![V přihlašovacích údajích správce mají pole Adresa URL tenanta a tajný token hodnotu a zvýrazní se tlačítko Test připojení.](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k selhání**.

    ![Textové pole pro oznamovací E-mail je prázdné a zaškrtnutí políčka Odeslat e-mailové oznámení v případě, že dojde k chybě, je vymazáno.](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele a Zscaler Beta verzi**.

    ![Je vybraná a povolená možnost Synchronizovat Azure Active Directory uživatele do ZScalerBeta.](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Zkontrolujte atributy uživatele synchronizované z Azure AD do Zscaler Beta v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Zscaler Beta pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![V oddílu mapování atributů pro atributy uživatele jsou atributy služby Active Directory zobrazeny vedle atributů Zscalar beta, se kterými jsou synchronizovány. Jako párování se zobrazí jedna dvojice atributů.](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do Zscaler Beta verze**.

    ![Je vybraná a povolená možnost Synchronizovat Azure Active Directory skupiny do ZScalerBeta.](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Zkontrolujte atributy skupiny synchronizované z Azure AD do Zscaler Beta v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v Zscaler Beta pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![V oddílu mapování atributů pro atributy skupiny jsou atributy služby Active Directory zobrazeny vedle atributů Zscalar beta, se kterými jsou synchronizovány. Jako párování se zobrazí jedna dvojice atributů.](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pokud chcete povolit službu Azure AD Provisioning pro Zscaler Beta, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Stav zřizování se zobrazí a nastaví na zapnuto.](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Definujte uživatele nebo skupiny, které chcete zřídit pro Zscaler Beta, výběrem požadovaných hodnot v **rozsahu** v části **Nastavení** .

    ![Zobrazí se rozevírací seznam rozsah a je vybrána možnost synchronizovat pouze přiřazené uživatele a skupiny. Druhá dostupná hodnota synchronizuje všechny uživatele a skupiny.](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Zvýrazní se tlačítko Uložit v horní části Zscaler Beta zřízení. K dispozici je také tlačítko zahodit.](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Zscaler Beta verzi.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
---
title: 'Kurz: pro Automatické zřizování uživatelů pomocí Azure Active Directory nakonfigurujte bonus | Microsoft Docs'
description: Naučte se, jak nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro bonus.
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
ms.openlocfilehash: d8c3f64e5cb5269bfe7e555615f874ac3443c6eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357823"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Kurz: konfigurace pro Automatické zřizování uživatelů v bonusovém prostředí

Cílem tohoto kurzu je předvést kroky, které je třeba provést v bonusu a Azure Active Directory (Azure AD), abyste mohli nakonfigurovat službu Azure AD tak, aby automaticky zřídila a zrušila zřizování uživatelů a skupin.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující:

* Tenant Azure AD
* [Bonusový tenant](https://bonus.ly/pricing)
* Účet uživatele v bonusu s oprávněními správce

> [!NOTE]
> Integrace zřizování Azure AD spoléhá na [bonusové rozhraní REST API](https://konghq.com/solutions/gateway/), které je dostupné pro mimořádné vývojáře.

## <a name="adding-bonusly-from-the-gallery"></a>Přidávání bonusů z Galerie

Předtím, než v rámci služby Azure AD nakonfigurujete bonus pro Automatické zřizování uživatelů, budete muset přidat bonus z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat bonus z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **bonus**, z panelu výsledků vyberte **bonus** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Bonusně v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Přiřazení uživatelů k bonusu

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci ve službě Azure AD. 

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD budou potřebovat přístup za bonus. Po rozhodnutí můžete tyto uživatele a skupiny přiřadit za bonus podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Důležité tipy pro přiřazení uživatelů k bonusu

* Doporučujeme, aby se k otestování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD za bonus. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele Bonusně, musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Konfigurace automatického zřizování uživatelů pro bonus

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů a skupin v bonusovém prostředí na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit pro prémii jednotné přihlašování založené na SAML, a to podle pokynů uvedených v [kurzu jednotného přihlašování v bonusovém](bonus-tutorial.md)rámci. Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Postup při konfiguraci automatického zřizování uživatelů pro prémii v Azure AD:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **bonusně**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **bonusně**.

    ![Bonusový odkaz v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningTab.png" alt-text="Snímek obrazovky s bonusovým zřizováním V části Správa se zvýrazní zřizování." border="false":::

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png" alt-text="Snímek obrazovky s polem se seznamem režimu zřizování s automatickým vybraným a zvýrazněným zobrazením" border="false":::

5. V části **přihlašovací údaje správce** zadejte **tajný token** účtu, který je popsaný v kroku 6.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/secrettoken.png" alt-text="Snímek obrazovky oddílu přihlašovací údaje správce Pole tajného tokenu je prázdné, ale pole je zvýrazněno." border="false":::

6. **Tajný token** pro váš účet v bonusovém účtu najdete v části **správce > integrace > společnosti**. V části **Pokud chcete Code Code** klikněte na **rozhraní API > vytvořit nový přístupový token rozhraní API** k vytvoření nového tajného tokenu.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png" alt-text="Snímek obrazovky bonusové nabídky V části správce se zvýrazní společnost. V části společnost se zvýrazní integrace." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonsulyRestApi.png" alt-text="Snímek obrazovky s kódem, který má být v bonusovém webu, se zvýrazněnou možností P" border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/CreateToken.png" alt-text="Snímek obrazovky bonusového webu Karta služby je otevřená. V případě přístupových tokenů P, vytvořte nový přístupový token, který je zvýrazněný." border="false":::

7. Na následující obrazovce zadejte do zadaného textového pole název přístupového tokenu a pak stiskněte tlačítko **vytvořit klíč rozhraní API**. Nový přístupový token se v automaticky otevíraném okně zobrazí během několika sekund.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token01.png" alt-text="Snímek obrazovky se stránkou nového přístupového tokenu webu v bonusu Pole bez popisku obsahuje token a zvýrazní se tlačítko vytvořit klíč P." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token02.png" alt-text="Snímek obrazovky bonusového webu Zobrazí se oznámení s vytvořeným novým přístupovým tokenem, za kterým následuje nečitelné tokeny." border="false":::

8. Po vyplnění polí zobrazených v kroku 5 klikněte na **Test připojení** , aby se služba Azure AD mohla připojit k bonusům. Pokud se připojení nepovede, zajistěte, aby měl účet v bonusovém účtu oprávnění správce, a zkuste to znovu.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/TestConnection.png" alt-text="Snímek obrazovky části s přihlašovacími údaji správce Azure Portal. Tlačítko textové připojení je zvýrazněné." border="false":::

9. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k selhání**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/EmailNotification.png" alt-text="Snímek obrazovky s prázdným polem e-mailu s oznámením Možnost je viditelná, která je označena jako odeslat e-mailové oznámení, když dojde k selhání." border="false":::

10. Klikněte na **Uložit**.

11. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatelé pro bonus**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserMappings.png" alt-text="Snímek obrazovky oddílu mapování V části název synchronizujte Azure Active Directory uživatele, aby byly zvýrazněné bonusem." border="false":::

12. Zkontrolujte atributy uživatele synchronizované z Azure AD, aby byly v oddílu **mapování atributů** bonusně. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v bonusu za operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserAttributeMapping.png" alt-text="Snímek obrazovky se stránkou mapování atributů. Tabulka obsahuje seznam atributů Azure Active Directory, odpovídajících nebonusových atributů a stavu odpovídající." border="false":::

13. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Pokud chcete povolit službu Azure AD Provisioning pro bonus, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningStatus.png" alt-text="Snímek obrazovky oddílu nastavení. Přepínač stav zřizování je nastaven na vypnuto." border="false":::

15. Definujte uživatele nebo skupiny, které chcete zřídit, aby se vybraly bonusem, a to výběrem požadovaných hodnot v **rozsahu** v části **Nastavení** .

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ScopeSync.png" alt-text="Snímek obrazovky se seznamem oborů V poli je vybrána možnost synchronizovat pouze přiřazené uživatele a skupiny." border="false":::

16. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/SaveProvisioning.png" alt-text="Snímek stránky bonus-zřizování s zvýrazněným tlačítkem Uložit." border="false":::

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Část **Podrobnosti o synchronizaci** můžete použít ke sledování průběhu a následnému odkazu na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na bonus.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
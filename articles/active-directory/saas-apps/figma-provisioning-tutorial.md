---
title: 'Kurz: Konfigurace automatického zřizování uživatelů Figma pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Figma.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 789dafc61c89515f4b2ef64933262252d1232f16
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357024"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Kurz: Konfigurace Figma pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Figma a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro Figma.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant Figma](https://www.figma.com/pricing/)
* Uživatelský účet v Figma s oprávněními správce.

## <a name="assign-users-to-figma"></a>Přiřaďte uživatele k Figma.
Azure Active Directory používá koncept nazvaný přiřazení k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Figma. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Figma podle pokynů uvedených tady:
 
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Důležité tipy pro přiřazení uživatelů k Figma

 * Doporučuje se, aby se k Figma k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Figma musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s výchozí rolí přístupu se z zřizování vylučují.

## <a name="set-up-figma-for-provisioning"></a>Nastavení Figma pro zřizování

Před konfigurací Figma pro Automatické zřizování uživatelů se službou Azure AD bude nutné načíst některé informace o zřizování z Figma.

1. Přihlaste se ke [konzole pro správu Figma](https://www.Figma.com/). Klikněte na ikonu ozubeného kolečka vedle vašeho tenanta.

    :::image type="content" source="media/Figma-provisioning-tutorial/image0.png" alt-text="Snímek obrazovky s konzolou pro správu Figma Je viditelný tenant s názvem a D SCIM test. Vedle klienta se zvýrazní ikona ozubeného kolečka." border="false":::

2. Přejděte na **obecné > aktualizace nastavení přihlášení**.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma03.png" alt-text="Snímek obrazovky s kartou Obecné v konzole pro správu Figma V části přihlášení a zřizování je zvýrazněná možnost aktualizovat nastavení přihlášení." border="false":::

3. Zkopírujte **ID tenanta**. Tato hodnota se použije k vytvoření adresy URL koncového bodu SCIM, která se má zadat do pole **Adresa URL tenanta** na kartě zřizování vaší aplikace Figma v Azure Portal.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma-tenantid.png" alt-text="Snímek obrazovky S oddílem A M L S S O v konzole pro správu Figma Popisek ID tenanta a sousedící odkaz, který říká kopírování, se zvýrazní." border="false":::

4. Posuňte se dolů a klikněte na **vygenerovat token rozhraní API**.

    :::image type="content" source="media/Figma-provisioning-tutorial/token.png" alt-text="Snímek obrazovky oddílu zřizování S C/M v konzole pro správu Figma Byl zvýrazněn odkaz, který vygeneroval token P I." border="false":::

5. Zkopírujte hodnotu  **tokenu rozhraní API** . Tato hodnota se zadá do pole **token tajného** kódu na kartě zřizování vaší aplikace Figma ve Azure Portal. 

    :::image type="content" source="media/Figma-provisioning-tutorial/figma04.png" alt-text="Snímek obrazovky stránky v konzole pro správu Figma V rámci vašeho zřizování tokenu P I se zvýrazní zástupný symbol pro token." border="false":::

## <a name="add-figma-from-the-gallery"></a>Přidání Figma z Galerie

Pokud chcete nakonfigurovat Figma pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat Figma z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Figma** , na panelu výsledků vyberte **Figma** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Figma v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Konfigurace automatického zřizování uživatelů na Figma 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Figma na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Figma podle pokynů uvedených v [kurzu Figma jednotného přihlašování](figma-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Figma ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Figma**.

    ![Odkaz Figma v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://www.figma.com/scim/v2/<TenantID>` **adresu URL tenanta** , kde **TenantID** je hodnota, kterou jste dříve získali ze Figma. Zadejte hodnotu **tokenu rozhraní API** v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Figma. Pokud se připojení nepovede, ujistěte se, že má váš účet Figma oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Figma**.

    ![Mapování uživatelů Figma](media/Figma-provisioning-tutorial/figma05.png)

11. Zkontrolujte atributy uživatele synchronizované z Azure AD do Figma v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Figma pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele Figma](media/Figma-provisioning-tutorial/figma06.png)

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro Figma, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro Figma, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Figma.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
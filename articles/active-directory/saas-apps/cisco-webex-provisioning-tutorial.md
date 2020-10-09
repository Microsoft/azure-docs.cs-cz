---
title: 'Kurz: Konfigurace Cisco Webex pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro Automatické zřizování a rušení uživatelských účtů pro Cisco Webex.
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
ms.openlocfilehash: 069c8a8e2a595248afe45bbb90de877b3b6fc87d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91849288"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Kurz: Konfigurace Cisco Webex pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést v rámci Cisco Webex a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů na Cisco Webex.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je momentálně ve verzi Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant Cisco Webex](https://www.webex.com/pricing/index.html).
* Uživatelský účet v Cisco Webex s oprávněními správce.

## <a name="adding-cisco-webex-from-the-gallery"></a>Přidání Cisco Webex z Galerie

Než začnete konfigurovat Cisco Webex pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat Cisco Webex z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Cisco Webex z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Cisco Webex**, vyberte **Cisco Webex** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Cisco Webex v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Přiřazení uživatelů k Cisco Webex

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé ve službě Azure AD potřebují přístup k Cisco Webex. Po rozhodnutí můžete tyto uživatele přiřadit k Cisco Webex podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Důležité tipy pro přiřazení uživatelů k Cisco Webex

* Doporučujeme, aby se k otestování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD k Cisco Webex. Další uživatele je možné přiřadit později.

* Když přiřadíte uživatele k Cisco Webex, musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Konfigurace automatického zřizování uživatelů pro Cisco Webex

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů v Cisco Webex na základě přiřazení uživatelů v Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Cisco Webex ve službě Azure AD:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Cisco Webex**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Cisco Webex**.

    ![Odkaz Cisco Webex v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    :::image type="content" source="common/provisioning.png" alt-text="Snímek obrazovky nabídky Azure Portal. V části Správa se zvýrazní zřizování." border="false":::

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    :::image type="content" source="common/provisioning-automatic.png" alt-text="Snímek obrazovky nabídky Azure Portal. V části Správa se zvýrazní zřizování." border="false":::

5. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta**a **tajný token** vašeho účtu Cisco Webex.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/secrettoken1.png" alt-text="Snímek obrazovky nabídky Azure Portal. V části Správa se zvýrazní zřizování." border="false":::

6.  Do pole **Adresa URL tenanta** zadejte hodnotu ve formátu `https://api.ciscospark.com/v1/scim/[OrgId]` . Pokud se chcete získat `[OrgId]` , přihlaste se do [centra řízení Cisco Webex](https://admin.webex.com/login). V levém dolním rohu klikněte na název vaší organizace a zkopírujte hodnotu z **ID organizace**. 

    * Pokud chcete získat hodnotu **tajného tokenu**, přejděte na tuto [adresu URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). Na zobrazené stránce pro přihlášení k WebEx se přihlaste pomocí úplného účtu správce Cisco Webex pro vaši organizaci. Zobrazí se chybová stránka s informací o tom, že lokalita není dostupná, ale je normální.

        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test.png" alt-text="Snímek obrazovky nabídky Azure Portal. V části Správa se zvýrazní zřizování." border="false":::
 
    * Zkopírujte hodnotu vygenerovaného tokenu nosiče z adresy URL, jak je zvýrazněno níže. Tento token je platný po dobu 365 dnů.
        
        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test1.png" alt-text="Snímek obrazovky nabídky Azure Portal. V části Správa se zvýrazní zřizování." border="false":::

7. Po vyplnění polí zobrazených v kroku 5 klikněte na **Test připojení** , aby se služba Azure AD mohla připojit k Cisco Webex. Pokud se připojení nepovede, ujistěte se, že váš účet Cisco Webex má oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)
   
8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelů na Cisco Webex**.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermapping.png" alt-text="Snímek obrazovky nabídky Azure Portal. V části Správa se zvýrazní zřizování." border="false":::

11. Zkontrolujte atributy uživatele synchronizované z Azure AD až Cisco Webex v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Cisco Webex pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermappingattributes.png" alt-text="Snímek obrazovky nabídky Azure Portal. V části Správa se zvýrazní zřizování." border="false":::

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro Cisco Webex, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro Cisco Webex, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Část **Podrobnosti o synchronizaci** můžete použít ke sledování průběhu a následnému odkazu na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Cisco Webex.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Cisco Webex je v současné době ve fázi EFT pro testování v prvním poli společnosti Cisco. Pokud chcete získat další informace, obraťte se na [tým podpory společnosti Cisco](https://www.webex.co.in/support/support-overview.html). 
* Další informace o konfiguraci Cisco Webex najdete [tady](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub)v dokumentaci k Cisco.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

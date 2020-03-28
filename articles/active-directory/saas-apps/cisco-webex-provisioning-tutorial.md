---
title: 'Kurz: Konfigurace cisco webexu pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty společnosti Cisco Webex.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 0075783c049e7f48645f768026dd9d5ec0ead821
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77058491"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Kurz: Konfigurace cisco webexu pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Cisco Webex a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-provision uživatelů Cisco Webex.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Tenanta Azure AD.
* [Klient společnosti Cisco Webex](https://www.webex.com/pricing/index.html).
* Uživatelský účet v cisco webexu s oprávněními správce.

## <a name="adding-cisco-webex-from-the-gallery"></a>Přidání Cisco Webex z galerie

Před konfigurací Cisco Webex pro automatické zřizování uživatelů pomocí Azure AD, je třeba přidat Cisco Webex z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Cisco Webex z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Cisco Webex**, z panelu výsledků vyberte **cisco Webex** a klepnutím na tlačítko **Přidat** aplikaci přidejte.

    ![Cisco Webex v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Přiřazení uživatelů společnosti Cisco Webex

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé ve službě Azure AD potřebují přístup k Cisco Webex. Jakmile se rozhodnete, můžete tyto uživatele přiřadit společnosti Cisco Webex podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Důležité tipy pro přiřazení uživatelů k Cisco Webex

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a Cisco Webex otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé mohou být přiřazeny později.

* Při přiřazování uživatele k cisco webexu je nutné v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Konfigurace automatického zřizování uživatelů pro Cisco Webex

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů v Cisco Webex na základě přiřazení uživatelů ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Cisco Webex ve službě Azure AD:

1. Přihlaste se na [portál Azure](https://portal.azure.com) portal a vyberte **Podnikové aplikace**, vyberte Všechny **aplikace**a pak vyberte **Cisco Webex**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **možnost Cisco Webex**.

    ![Odkaz Cisco Webex v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Zřizování společnosti Cisco Webex](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Zřizování společnosti Cisco Webex](common/provisioning-automatic.png)

5. V části **Přihlašovací údaje správce** zadejte adresu URL **klienta**a **tajný token** vašeho účtu Cisco Webex.

    ![Zřizování společnosti Cisco Webex](./media/cisco-webex-provisioning-tutorial/secrettoken1.png)

6.  Do pole **Adresa URL klienta** zadejte `https://api.ciscoweb.com/v1/scim/[OrgId]`hodnotu ve formě . Chcete-li získat, `[OrgId]`přihlaste se do [centra Cisco Webex Control Hub](https://admin.webex.com/login). Klikněte na název organizace vlevo dole a zkopírujte hodnotu z **ID organizace**. 

    * Chcete-li získat hodnotu **tajného tokenu**, přejděte na tuto [adresu URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). Na přihlašovací stránce webexu, která se zobrazí, se přihlaste pomocí úplného účtu správce Cisco Webex pro vaši organizaci. Zobrazí se chybová stránka, která říká, že web není dostupný, ale je to normální.

        ![Zřizování společnosti Cisco Webex](./media/cisco-webex-provisioning-tutorial/test.png)
 
    * Zkopírujte hodnotu generovaného nosného tokenu z adresy URL, jak je zvýrazněno níže. Tento token je platný po dobu 365 dnů.
        
        ![Zřizování společnosti Cisco Webex](./media/cisco-webex-provisioning-tutorial/test1.png)

7. Po vyplnění polí uvedených v kroku 5 klikněte na **test připojení** a ujistěte se, že Azure AD můžete připojit k Cisco Webex. Pokud se připojení nezdaří, ujistěte se, že váš účet Cisco Webex má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)
   
8. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s webexem Cisco Webex**.

    ![Zřizování společnosti Cisco Webex](./media/cisco-webex-provisioning-tutorial/usermapping.png)

11. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Cisco Webex v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Cisco Webex pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Zřizování společnosti Cisco Webex](./media/cisco-webex-provisioning-tutorial/usermappingattributes.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro Cisco Webex, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit společnosti Cisco Webex, výběrem požadovaných hodnot v **části Obor.** **Settings**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu zřizování aktivit, která popisuje všechny akce prováděné službou zřizování Azure AD na Cisco Webex.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Cisco Webex je v současné době ve fázi early field testing (EFT) společnosti Cisco. Další informace získáte od [týmu podpory společnosti Cisco](https://www.webex.co.in/support/support-overview.html). 
* Další informace o konfiguraci cisco webex naleznete v dokumentaci společnosti Cisco [zde](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
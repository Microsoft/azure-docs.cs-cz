---
title: 'Kurz: Konfigurace služby Symantec Web Security Service (WSS) pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a odpočámala uživatelské účty ve službě WSS (Symantec Web Security Service).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: fbd105ca1623512a3c16f3b609374f5c4055898b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063114"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Kurz: Konfigurace služby Symantec Web Security Service (WSS) pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Symantec Web Security Service (WSS) a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-provision uživatelů a/nebo skupin sYmantec Web Security Service (WSS).

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Klient služby Symantec Web Security Service (WSS)](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Uživatelský účet ve službě Symantec Web Security Service (WSS) s oprávněními správce.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Přiřazení uživatelů ke službě Symantec Web Security Service (WSS)

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup ke službě Symantec Web Security Service (WSS). Jakmile se rozhodnete, můžete těmto uživatelům a/nebo skupinám přiřadit službu Symantec Web Security Service (WSS) podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Důležité tipy pro přiřazení uživatelů ke službě Symantec Web Security Service (WSS)

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k Symantec Web Security Service (WSS) k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele ke službě Symantec Web Security Service (WSS) je nutné v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Instalace služby Symantec Web Security Service (WSS) pro zřizování

Před konfigurací služby WSS (Symantec Web Security Service) pro automatické zřizování uživatelů pomocí služby Azure AD budete muset povolit zřizování SCIM ve službě Symantec Web Security Service (WSS).

1. Přihlaste se ke [konzoli pro správu služby Symantec Web Security Service](https://portal.threatpulse.com/login.jsp). Přejděte na **službu Solutions** > **Service**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/service.png)

2. Přejděte na > **integraci údržby** >  **účtů****Nová integrace**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Vyberte **možnost Uživatelé jiných výrobců & synchronizaci skupin**. 

    ![Webová služba zabezpečení společnosti Symantec](media/symantec-web-security-service/third-party-users.png)

4.  Zkopírujte **adresu URL scim** a **token**. Tyto hodnoty se zanesou do pole **Adresa URL klienta** a **tajný token** na kartě Zřizování aplikace WSS (Symantec Web Security Service) na portálu Azure Portal.

    ![Webová služba zabezpečení společnosti Symantec](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Přidání služby Symantec Web Security Service (WSS) z galerie

Chcete-li nakonfigurovat službu WSS (Symantec Web Security Service) pro automatické zřizování uživatelů pomocí služby Azure AD, je třeba přidat službu WSS (Symantec Web Security Service) z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat službu Symantec Web Security Service (WSS) z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **službu Symantec Web Security Service**, vpanelu s výsledky vyberte **položku Symantec Web Security Service** a klepnutím na tlačítko **Přidat** aplikaci přidejte.

    ![Symantec Web Security Service (WSS) v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Konfigurace automatického zřizování uživatelů ve službě Symantec Web Security Service (WSS)

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin ve službě WSS (Symantec Web Security Service) na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování založené na protokolu SAML pro službu WSS (Symantec Web Security Service) podle pokynů uvedených v [kurzu jednotného přihlašování služby Symantec Web Security Service (WSS)](symantec-tutorial.md). Jednotné přihlašování lze konfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce se vzájemně doplňují.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro službu WSS (Symantec Web Security Service) ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **položku Symantec Web Security Service**.

    ![Odkaz WSS (Symantec Web Security Service) v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části Přihlašovací údaje správce zadejte hodnoty **adresy URL SCIM** a **tokenu** načtené dříve v **adrese URL klienta** a **tajném tokenu.** Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit ke službě Symantec Web Security Service. Pokud se připojení nezdaří, ujistěte se, že váš účet WSS (Symantec Web Security Service) má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **synchronizovat uživatele služby Azure Active Directory se službou WSS (Symantec Web Security Service).**

    ![Mapování uživatelů služby Symantec Web Security Service (WSS)](media/symantec-web-security-service/usermapping.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Symantec Web Security Service (WSS) v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů ve službě Symantec Web Security Service (WSS) pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Mapování uživatelů služby Symantec Web Security Service (WSS)](media/symantec-web-security-service/userattribute.png)

10. V části **Mapování** vyberte **synchronizovat skupiny Služby Azure Active Directory se službou Symantec Web Security Service**.

    ![Mapování uživatelů služby Symantec Web Security Service (WSS)](media/symantec-web-security-service/groupmapping.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do Symantec Web Security Service (WSS) v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám ve službě Symantec Web Security Service (WSS) pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Mapování uživatelů služby Symantec Web Security Service (WSS)](media/symantec-web-security-service/groupattribute.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro službu Symantec Web Security Service, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Pomocí výběru požadovaných hodnot v **části Obor** definujte uživatele nebo skupiny, které chcete zřídit službě **Settings** Symantec Web Security Service (WSS).

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace. Další informace o tom, jak dlouho bude trvat uživatelům nebo skupinám, naleznete v tématu [Jak dlouho bude trvat zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Část Aktuální **stav** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování služby Azure AD ve službě WSS (Symantec Web Security Service). Další informace naleznete [v tématu Kontrola stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Chcete-li číst protokoly zřizování Azure AD, najdete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

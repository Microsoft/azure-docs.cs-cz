---
title: 'Kurz: Konfigurace automatického přihlašování Soloinsight-CloudGate pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat Službu Azure Active Directory tak, aby automaticky zřašovala a odpočitatla uživatelské účty do automatického přihlašování Soloinsight-CloudGate.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 07558ceb-d406-40e7-90b8-1b40fdc829e7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 6ab90a6aea262d5c7067f9f41b9ddfc090b7371d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063190"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Kurz: Konfigurace automatického zřizování Soloinsight-CloudGate pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Soloinsight-CloudGate sso a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřizování a de-provision uživatelů a/nebo skupin soloinsight-CloudGate sso.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Tenant spři sondou Soloinsight-CloudGate](https://www.soloinsight.com/)
* Uživatelský účet v soloinsight-CloudGate s přihlašování s oprávněními správce.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Přiřazení uživatelů k sazí Soloinsight-CloudGate

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k správě při správě Soloinsight-CloudGate. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a/nebo skupiny Soloinsight-CloudGate SSO podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Důležité tipy pro přiřazení uživatelů k sazí Soloinsight-CloudGate

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k Soloinsight-CloudGate jednotného přihlašování k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k Soloinsight-CloudGate SSO, musíte vybrat všechny platné role specifické pro aplikaci (pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Nastavení sazí Soloinsight-CloudGate pro zřizování

1. Přihlaste se ke své [konzole pro správu SSO Soloinsight-CloudGate](https://soloinsight.sigateway.com/login). Přejděte do **části Nastavení systému pro správu >**.

    ![Konzola pro správu spřipoty SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Přejděte na **Obecné**.

    ![Soloinsight-CloudGate SSO Přidat SCIM](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Přejděte dolů na konec stránky a získejte **adresu URL klienta** a **tajný token**. Zkopírujte **tajný token**. Tato hodnota se zadá do pole Tajný token na kartě Zřizování aplikace s přihlašováním Soloinsight-CloudGate na webu Azure Portal.

    ![Vytvoření tokenu služby Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Přidání sazí Soloinsight-CloudGate z galerie

Před konfigurací Soloinsight-CloudGate SSO pro automatické zřizování uživatelů pomocí Azure AD, je třeba přidat Soloinsight-CloudGate sso z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat soloinsight-CloudGate SSO z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Soloinsight-CloudGate SSO**, v panelu výsledků vyberte **Soloinsight-CloudGate SSO** a pak kliknutím na tlačítko **Přidat** aplikaci přidejte.

    ![Soloinsight-CloudGate SSO v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Konfigurace automatického zřizování uživatelů na soloinsight-CloudGate sso 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v soloinsight-CloudGate sso na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování založené na SAML pro jednotné přihlašování Soloinsight-CloudGate , podle pokynů uvedených v [kurzu jednotného přihlašování Soloinsight-CloudGate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial). Jednotné přihlašování lze konfigurovat nezávisle na automatickézřivací službě uživatelů, i když tyto dvě funkce se vzájemně doplňují

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro soloinsight-CloudGate přihlašování ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Soloinsight-CloudGate SSO**.

    ![Odkaz SSO Soloinsight-CloudGate v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://sigateway.com/scim/v2/sync/serviceproviderconfig` zadejte adresu **URL klienta**. Zadejte hodnotu **ověřovacího tokenu SCIM** načtenou dříve v **tokenu tajného klíče**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k jednotnému přihlašovacímu centru Soloinsight-CloudGate. Pokud se připojení nezdaří, ujistěte se, že váš účet jednotného přihlašování Soloinsight-CloudGate má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **synchronizovat uživatele služby Azure Active Directory s přiřazovacím službou Soloinsight-CloudGate**.

    ![Mapování uživatelů služby SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Soloinsight-CloudGate sso v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v Soloinsight-CloudGate sso pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy uživatele s přihlašovat uživatele Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. V části **Mapování** vyberte **Synchronizovat skupiny Služby Active Directory Azure se službou SSO Soloinsight-CloudGate**.

    ![Mapování skupiny SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na Soloinsight-CloudGate SSO v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v Soloinsight-CloudGate SSO pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy skupiny SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro Soloinsight-CloudGate SSO, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit soloinsight-CloudGate sso výběrem požadované hodnoty v **oboru** v **nastavení** části.

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na soloinsight-CloudGate SSO.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)


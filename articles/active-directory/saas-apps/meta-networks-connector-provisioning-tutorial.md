---
title: 'Kurz: Konfigurace konektoru meta sítí pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty do konektoru Meta Networks.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6e34e540-7761-4bdf-a2f9-8c12becc119d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 03c2dc6253fba5c2c7d59f3aefc5c1c663ed8248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061352"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Kurz: Konfigurace konektoru meta sítí pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Meta Networks Connector a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-provision uživatelů a/nebo skupin pro meta sítě konektoru.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Klient konektoru meta sítí](https://www.metanetworks.com/)
* Uživatelský účet v konektoru Meta Networks s oprávněními správce.

## <a name="assigning-users-to-meta-networks-connector"></a>Přiřazení uživatelů ke konektoru Meta Networks

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup ke konektoru Meta Networks. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny Meta Networks Connector podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Důležité tipy pro přiřazení uživatelů ke konektoru Meta Networks

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a Meta Networks Connector otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele ke konektoru Meta Networks connector musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Nastavení konektoru meta sítí pro zřizování

1. Přihlaste se ke [konzoli Prosagze Administrátor](https://login.metanetworks.com/login/) u konektoru pomocí názvu organizace. Přejděte na **klíči rozhraní API pro správu >**.

    ![Konzola pro správu konektoru meta-networks](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Kliknutím na znaménko plus na pravé horní straně obrazovky vytvořte nový **klíč ROZHRANÍ API**.

    ![Ikona konektoru Meta Networks Connector plus](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Nastavte **název klíče rozhraní API** a **popis klíče rozhraní API**.

    ![Meta sítě konektor vytvořit token](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  Zapněte oprávnění **k zápisu** pro **skupiny** a **uživatele**.

    ![Oprávnění konektoru meta sítí](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Klikněte na **Přidat**. Zkopírujte **SECRET** a uložte jej, protože to bude jediný čas, kdy jej můžete zobrazit. Tato hodnota se zadá do pole Tajný token na kartě Zřizování aplikace Meta Networks Connector na webu Azure Portal.

    ![Meta sítě konektor vytvořit token](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  Přidejte idp přechodem na **nastavení > správy > idp > vytvořit nový**.

    ![Přidat idp konektoru Meta Networks](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  Na stránce **Konfigurace protokolu IdP** můžete **pojmenovat** konfiguraci protokolu IdP a zvolit **ikonu**.

    ![Název idp konektoru meta sítí](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Ikona idp konektoru meta-sítí](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  V **části Konfigurovat SCIM** vyberte název klíče rozhraní API vytvořený v předchozích krocích. Klikněte na **Uložit**.

    ![Konektor meta sítí konfigurace SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Přejděte na **kartu Nastavení > pro správu > .** Kliknutím na název konfigurace protokolu IdP vytvořené v předchozích krocích zobrazíte **ID protokolu IdP**. Toto **ID** se přidá na konec **adresy URL klienta** při zadávání hodnoty v poli **Adresa URL klienta** na kartě Zřizování aplikace Konektor meta sítí na webu Azure Portal.

    ![Id ID idi programu IdP konektoru meta sítí](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Přidání konektoru Meta Networks z galerie

Před konfigurací konektoru Meta Networks pro automatické zřizování uživatelů pomocí Azure AD je potřeba přidat konektor Meta Networks z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat konektor Meta Networks z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **konektor Meta Networks**, vpanelu s výsledky vyberte konektor meta **sítí** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![Meta Networks Connector v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Konfigurace automatického zřizování uživatelů na konektor meta sítí 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v konektoru Meta Networks na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování založené na SAML pro konektor Meta Networks , podle pokynů uvedených v [kurzu jednotného přihlášení konektoru Meta Networks](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial). Jednotné přihlašování lze konfigurovat nezávisle na automatickézřivací službě uživatelů, i když tyto dvě funkce se vzájemně doplňují

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro konektor meta sítí ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **meta networks konektor**.

    ![Propojení konektoru Meta-sítě v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://api.metanetworks.com/v1/scim/<IdP ID>` zadejte adresu **URL klienta**. Zadejte hodnotu **ověřovacího tokenu SCIM** načtenou dříve v **tokenu tajného klíče**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit ke konektoru Meta Networks. Pokud se připojení nezdaří, ujistěte se, že váš účet Meta Networks Connector má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **synchronizovat konektor Služby Azure Active Directory s konektorem meta sítí**.

    ![Mapování uživatelů konektoru meta sítí](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na konektor meta sítí v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v aplikaci Meta Networks Connector pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy konektoru meta sítí](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. V části **Mapování** vyberte **Synchronizovat skupiny Služby Azure Active Directory s konektorem Meta Networks .**

    ![Mapování skupin konektorů meta sítí](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD na konektor meta sítí v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v aplikaci Meta Networks Connector pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy skupiny konektorů meta sítí](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro konektor meta sítí, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit konektoru Meta Networks, výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na konektoru Meta Networks.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)


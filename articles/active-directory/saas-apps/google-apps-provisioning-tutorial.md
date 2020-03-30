---
title: 'Kurz: Konfigurace sady G Suite pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak automaticky zřídit a odpojit uživatelské účty z Azure AD do G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 969a2fb5444ae8ece2aa302c04a5bbb85dcca917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057698"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Kurz: Konfigurace sady G Suite pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny ve službách G Suite a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-zřizování uživatelů nebo skupin do G Suite.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> Konektor G Suite byl nedávno aktualizován v říjnu 2019. Změny provedené v konektoru G Suite zahrnují:
> - Byla přidána podpora pro další atributy uživatelů a skupin služby G Suite. 
> - Byly aktualizovány názvy cílových atributů služby G Suite tak, aby odpovídaly [tomu,](https://developers.google.com/admin-sdk/directory)co je zde definováno .
> - Bylo aktualizováno výchozí mapování atributů.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí služby G Suite, potřebujete následující položky:

- Klient Azure AD
- [Klient G Suite](https://gsuite.google.com/pricing.html)
- Uživatelský účet na G Suite s oprávněními správce.

## <a name="assign-users-to-g-suite"></a>Přiřazení uživatelů ke službě G Suite

Azure Active Directory používá koncept s názvem přiřazení k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup ke službě G Suite. Jakmile se rozhodnete, můžete těmto uživatelům a/nebo skupinám přiřadit službu G Suite podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Důležité tipy pro přiřazování uživatelů ke službám G Suite

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k G Suite k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele ke službě G Suite musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="setup-g-suite-for-provisioning"></a>Nastavení sady G Suite pro zřizování

Před konfigurací G Suite pro automatické zřizování uživatelů pomocí Azure AD, budete muset povolit zřizování SCIM na G Suite.

1. Přihlaste se ke [konzole G Suite Admin](https://admin.google.com/) pomocí svého účtu správce a pak vyberte **Zabezpečení**. Pokud odkaz nevidíte, může být skrytý v nabídce **Další ovládací prvky** v dolní části obrazovky.

    ![Vyberte zabezpečení.][10]

1. Na stránce **Zabezpečení** vyberte **odkaz na rozhraní API**.

    ![Vyberte odkaz na rozhraní API.][15]

1. Vyberte **Povolit přístup k rozhraní API**.

    ![Vyberte odkaz na rozhraní API.][16]

   > [!IMPORTANT]
   > Pro každého uživatele, který má v úmyslu zřídit G Suite, musí **být** jeho uživatelské jméno ve službě Azure AD svázané s vlastní doménou. Například uživatelská jména, bob@contoso.onmicrosoft.com která vypadají jako, nejsou službou G Suite přijata. Na druhou stranu, bob@contoso.com je přijat. Doménu existujícího uživatele můžete změnit podle pokynů [zde](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

1. Jakmile jste přidali a ověřili požadované vlastní domény s Azure AD, musíte je znovu ověřit pomocí Služby G Suite. Chcete-li ověřit domény ve g suite, postupujte takto:

    a. V [Konzole pro správu G Suite](https://admin.google.com/)vyberte **Domény**.

    ![Vybrat domény][20]

    b. Vyberte **Přidat doménu nebo alias domény**.

    ![Přidání nové domény][21]

    c. Vyberte **Přidat jinou doménu**a zadejte název domény, kterou chcete přidat.

    ![Zadejte název domény.][22]

    d. Vyberte **Pokračovat a ověřte vlastnictví domény**. Potom postupujte podle pokynů k ověření, že vlastníte název domény. Úplné pokyny k ověření domény u Googlu najdete v tématu [Ověření vlastnictví webu](https://support.google.com/webmasters/answer/35179).

    e. Opakujte předchozí kroky pro všechny další domény, které chcete přidat do služby G Suite.

1. Dále určete, který účet správce chcete použít ke správě zřizování uživatelů v G Suite. Přejděte na **Role správce**.

    ![Vyberte Službu Google Apps][26]

1. Pro **roli správce** tohoto účtu upravte **oprávnění** pro tuto roli. Ujistěte se, že povolit všechna **oprávnění rozhraní API správce** tak, aby tento účet lze použít pro zřizování.

    ![Vyberte Službu Google Apps][27]

## <a name="add-g-suite-from-the-gallery"></a>Přidání služby G Suite z galerie

Chcete-li nakonfigurovat Službu G Suite pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat G Suite z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS. 

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

1. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **G Suite**, vpanelu výsledků vyberte **G Suite** a pak klepnutím na tlačítko **Přidat** aplikaci přidejte.

    ![G Suite v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Konfigurace automatického zřizování uživatelů do služby G Suite 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin ve službě G Suite na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování ke službě Saml pro službu G Suite podle pokynů uvedených v [kurzu jednotného přihlašování ke službě G Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

> [!NOTE]
> Další informace o koncovém bodě rozhraní API adresáře služby G Suite naleznete v rozhraní [Directory API](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro G Suite ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **G Suite**.

    ![Odkaz G Suite v seznamu Aplikace](common/all-applications.png)

1. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

1. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

1. V části **Pověření správce** vyberte **Autorizovat**. Otevře dialogové okno autorizace Google v novém okně prohlížeče.

    ![Autorizace služby G Suite](media/google-apps-provisioning-tutorial/authorize.png)

1. Potvrďte, že chcete udělit oprávnění Azure AD k provádění změn v tenantovi G Suite. Vyberte **Přijmout**.

    ![Potvrďte oprávnění.][28]

1. Na webu Azure Portal vyberte **Testovat připojení,** abyste zajistili, že se Azure AD bude chtít připojit k vaší aplikaci. Pokud se připojení nezdaří, ujistěte se, že váš účet G Suite má oprávnění správce týmu. Potom opakujte krok **Autorizace.**

1. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

1. Klikněte na **Uložit**.

1. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s aplikací G Suite**.

    ![Mapování uživatelů služby G Suite](media/google-apps-provisioning-tutorial/usermappings.png)

1. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do G Suite v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v g suite pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy služby G Suite](media/google-apps-provisioning-tutorial/userattributes.png)

1. V části **Mapování** vyberte **Synchronizovat skupiny Služby Active Directory Azure se službou G Suite**.

    ![Mapování skupin g suite](media/google-apps-provisioning-tutorial/groupmappings.png)

1. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do G Suite v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v g suite pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.** UI zobrazí výchozí sadu mapování atributů mezi Azure AD a G Suite. Kliknutím na přidat nové mapování můžete přidat další atributy, například Organizační jednotku.

    ![Atributy skupiny G Suite](media/google-apps-provisioning-tutorial/groupattributes.png)

1. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Chcete-li povolit službu zřizování Azure AD pro G Suite, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

1. Definujte uživatele nebo skupiny, které chcete zřídit do služby G **Settings** Suite, výběrem požadovaných hodnot v **části Obor.**

    ![Obor zřizování](common/provisioning-scope.png)

1. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na G Suite.

> [!NOTE]
> Pokud uživatelé již mají existující osobní nebo spotřebitelský účet pomocí e-mailové adresy uživatele Azure AD, může to způsobit nějaký problém, který by mohl být vyřešen pomocí Nástroje pro přenos Google před provedením synchronizace adresáře.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="common-issues"></a>Běžné problémy
* K chybám autorizace může dojít, pokud účet použitý k navázání připojení není určen pro správce v gsuite. Ujistěte se, že účet používaný k autorizaci přístupu má oprávnění správce ve **všech doménách,** které uživatelé potřebují zřídit. 
* Azure AD podporuje zakázání uživatelů v GSuite tak, aby nemohli získat přístup k aplikaci, ale neodstraní uživatele v GSuite.

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png

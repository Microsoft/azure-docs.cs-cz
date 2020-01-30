---
title: 'Kurz: Konfigurace G Suite pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD do G Suite.
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
ms.openlocfilehash: f11258457e60ea3458b0ede1fe00f042041bcb1a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841246"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Kurz: Konfigurace G Suite pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v G Suite a Azure Active Directory (Azure AD), abyste mohli nakonfigurovat službu Azure AD tak, aby automaticky zřídila a zrušila zřizování uživatelů a skupin v G Suite.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).

> [!NOTE]
> Konektor G Suite se nedávno aktualizoval v říjnu 2019. Mezi změny provedené v konektoru G Suite patří:
> - Byla přidána podpora dalších atributů uživatelů a skupin G Suite. 
> - Aktualizované názvy atributů cíle G Suite tak, aby odpovídaly, co se [tady](https://developers.google.com/admin-sdk/directory)definuje.
> - Mapování výchozích atributů se aktualizovala.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s využitím G Suite potřebujete následující položky:

- Tenant Azure AD
- [Tenant G Suite](https://gsuite.google.com/pricing.html)
- Uživatelský účet v G Suite s oprávněními správce.

## <a name="assign-users-to-g-suite"></a>Přiřazení uživatelů ke G Suite

Azure Active Directory používá koncept nazvaný přiřazení k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k G Suite. Po rozhodnutí můžete přiřadit tyto uživatele a skupiny ke G Suite podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Důležité tipy pro přiřazení uživatelů ke G Suite

* Doporučuje se, aby jeden uživatel Azure AD byl přiřazený k G Suite, aby otestoval automatickou konfiguraci zřizování uživatelů. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k G Suite musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-g-suite-for-provisioning"></a>Nastavit G Suite pro zřizování

Před konfigurací G Suite pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit zřizování SCIM v G Suite.

1. Přihlaste se ke [konzole pro správu G Suite](https://admin.google.com/) pomocí účtu správce a pak vyberte **zabezpečení**. Pokud odkaz nevidíte, může být skrytý v nabídce **Další ovládací prvky** v dolní části obrazovky.

    ![Vyberte zabezpečení.][10]

1. Na stránce **zabezpečení** vyberte **odkaz rozhraní API**.

    ![Vyberte odkaz rozhraní API.][15]

1. Vyberte **Povolit přístup přes rozhraní API**.

    ![Vyberte odkaz rozhraní API.][16]

   > [!IMPORTANT]
   > Pro každého uživatele, kterého máte v úmyslu zřídit sadu G Suite, **musí** být jeho uživatelské jméno ve službě Azure AD vázané na vlastní doménu. Například uživatelská jména, která vypadají jako bob@contoso.onmicrosoft.com, nejsou přijata sadou G Suite. Na druhé straně se bob@contoso.com akceptuje. Stávající doménu uživatele můžete změnit podle pokynů uvedených [tady](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

1. Jakmile přidáte a ověříte vaše požadované vlastní domény pomocí Azure AD, musíte je znovu ověřit pomocí G Suite. Pokud chcete ověřit domény v G Suite, přečtěte si následující postup:

    a. V [konzole pro správu G Suite](https://admin.google.com/)vyberte **domény**.

    ![Vybrat domény][20]

    b. Vyberte **Přidat doménu nebo alias domény**.

    ![Přidat novou doménu][21]

    c. Vyberte **Přidat další doménu**a pak zadejte název domény, kterou chcete přidat.

    ![Zadejte název domény.][22]

    d. Vyberte **pokračovat a ověřte vlastnictví domény**. Pak postupujte podle pokynů a ověřte, že název domény vlastníte. Podrobné pokyny, jak ověřit vaši doménu pomocí Google, najdete v tématu [ověření vlastnictví webu](https://support.google.com/webmasters/answer/35179).

    e. Předchozí kroky opakujte pro všechny další domény, které máte v úmyslu přidat do G Suite.

1. Dále určete, který účet správce chcete použít ke správě zřizování uživatelů v G Suite. Přejděte na **role správce**.

    ![Vybrat Google Apps][26]

1. Pro **roli správce** tohoto účtu upravte **oprávnění** pro tuto roli. Ujistěte se, že jste povolili všechna **oprávnění rozhraní API pro správu** , aby se tento účet mohl použít k zřizování.

    ![Vybrat Google Apps][27]

## <a name="add-g-suite-from-the-gallery"></a>Přidání G Suite z Galerie

Pokud chcete nakonfigurovat G Suite pro Automatické zřizování uživatelů pomocí Azure AD, budete muset do seznamu spravovaných aplikací SaaS přidat G Suite z Galerie aplikací Azure AD. 

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

1. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **g Suite**, v panelu výsledků vyberte **g Suite** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![G Suite v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Konfigurace automatického zřizování uživatelů na G Suite 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v G Suite na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit pro G Suite jednotné přihlašování založené na SAML, a to podle pokynů uvedených v [kurzu jednotného přihlašování G Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

> [!NOTE]
> Další informace o koncovém bodu rozhraní API adresářových sad G Suite najdete v tématu věnovaném [rozhraní Directory API](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro G Suite ve službě Azure AD:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

1. V seznamu aplikace vyberte **G Suite**.

    ![Odkaz G Suite v seznamu aplikací](common/all-applications.png)

1. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

1. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

1. V části **přihlašovací údaje správce** vyberte **autorizovat**. Otevře se dialogové okno autorizace Google v novém okně prohlížeče.

    ![Autorizace G Suite](media/google-apps-provisioning-tutorial/authorize.png)

1. Potvrďte, že chcete udělit oprávnění služby Azure AD, aby bylo možné provádět změny v tenantovi G Suite. Vyberte **Přijmout**.

    ![Potvrďte oprávnění.][28]

1. V Azure Portal vyberte **Test připojení** , aby se služba Azure AD mohla připojit k vaší aplikaci. Pokud se připojení nepovede, ujistěte se, že váš účet G Suite má oprávnění správce týmu. Pak zkuste znovu spustit krok **autorizace** .

1. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

1. Klikněte na možnost **Uložit**.

1. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatelů do G Suite**.

    ![Mapování uživatelů G Suite](media/google-apps-provisioning-tutorial/usermappings.png)

1. Zkontrolujte atributy uživatele synchronizované z Azure AD do G Suite v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v G Suite pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Uživatelské atributy G Suite](media/google-apps-provisioning-tutorial/userattributes.png)

1. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do G Suite**.

    ![Mapování skupin G Suite](media/google-apps-provisioning-tutorial/groupmappings.png)

1. Zkontrolujte atributy skupiny synchronizované z Azure AD do G Suite v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v G Suite pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny. Uživatelské rozhraní zobrazuje výchozí sadu mapování atributů mezi Azure AD a G Suite. Kliknutím na Přidat nové mapování se můžete rozhodnout přidat další atributy, jako je například organizační jednotka.

    ![Atributy skupiny G Suite](media/google-apps-provisioning-tutorial/groupattributes.png)

1. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pokud chcete povolit službu Azure AD Provisioning pro G Suite, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

1. Definujte uživatele nebo skupiny, které chcete zřídit pro sadu G Suite výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

1. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Část **Podrobnosti o synchronizaci** můžete použít k monitorování průběhu a následnému sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v G Suite.

> [!NOTE]
> Pokud už uživatelé mají pomocí e-mailové adresy uživatele služby Azure AD existující osobní/uživatelský účet, může dojít k nějakému problému, který by se mohl vyřešit pomocí nástroje pro přenos Google před provedením synchronizace adresáře.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="common-issues"></a>Běžné problémy
* Pokud účet použitý k navázání připojení není pro správce v GSuite, může dojít k selháním autorizace. Ujistěte se, že účet, který se používá k autorizaci přístupu, má oprávnění správce pro **všechny domény** , se kterými se uživatelé musí zřídit. 
* Azure AD podporuje zakázání uživatelů v GSuite, aby k nim neměli přístup, ale neodstraňují uživatele v GSuite.

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)

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

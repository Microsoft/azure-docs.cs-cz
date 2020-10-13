---
title: 'Kurz: Konfigurace konzoly pro správu Netskope pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se, jak nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů do konzoly pro správu Netskope.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: b4ac2308eae3466dbb9d68895bca4a4de30fcebc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91304906"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Kurz: Konfigurace konzoly pro správu Netskope pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést v konzole pro správu Netskope a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů a skupin pro konzolu správce Netskope.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant konzoly správce Netskope](https://www.netskope.com/)
* Uživatelský účet v konzole pro správu Netskope s oprávněními správce.

## <a name="assigning-users-to-netskope-administrator-console"></a>Přiřazení uživatelů ke konzole správce Netskope

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup ke konzole pro správu Netskope. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny do konzoly správce Netskope podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Důležité tipy pro přiřazení uživatelů ke konzole správce Netskope

* Doporučujeme, aby se k otestování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD konzole pro správu Netskope. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele do konzoly pro správu Netskope musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>Nastavení konzoly pro správu Netskope pro zřizování

1. Přihlaste se ke [konzole správce konzoly pro správu Netskope](https://netskope.goskope.com/). Přejděte do **Nastavení domovská >**.

    ![Konzola správce konzoly pro správu Netskope](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Přejděte na **nástroje**. V nabídce **nástroje** přejděte na **nástroje adresáře > integrace SCIM**.

    ![Nástroje konzoly pro správu Netskope](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Konzola pro správu Netskope přidat SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Posuňte se dolů a klikněte na tlačítko **Přidat token** . V dialogovém okně **Přidat název klienta OAuth** zadejte **název klienta** a klikněte na tlačítko **Uložit** .

    ![Přidat token do konzoly pro správu Netskope](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Název klienta konzoly správce Netskope](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Zkopírujte **adresu URL serveru SCIM** a **token**. Tyto hodnoty se zadají do polí Adresa URL tenanta a tajného tokenu na kartě zřizování v aplikaci konzoly pro správu Netskope v Azure Portal.

    ![Vytvoření tokenu v konzole pro správu Netskope](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Přidání konzoly správce Netskope z Galerie

Před konfigurací konzoly pro správu Netskope pro Automatické zřizování uživatelů se službou Azure AD je nutné přidat konzolu správce Netskope z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat konzolu správce Netskope z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Netskope konzoly pro správu**, vyberte možnost **Konzola pro správu Netskope** na panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Konzola pro správu Netskope v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Konfigurace automatického zřizování uživatelů v konzole pro správu Netskope 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v konzole pro správu Netskope na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro konzolu pro správu Netskope podle pokynů uvedených v [kurzu pro jednotné přihlašování v konzole pro správu Netskope](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

> [!NOTE]
> Další informace o koncovém bodu SCIM konzoly správce Netskope najdete v [tomto](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)tématu.

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro konzolu pro správu Netskope v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **konzolu pro správu Netskope**.

    ![Odkaz na konzolu správce Netskope v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte na **adrese URL klienta**vstupní hodnotu **URL serveru SCIM** . Zadejte hodnotu **tokenu** získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** , aby se služba Azure AD mohla připojit ke konzole pro správu Netskope. Pokud se připojení nepovede, zajistěte, aby měl účet konzoly správce Netskope oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatelů a Netskope konzolu správce**.

    ![Mapování uživatelů konzoly správce Netskope](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do konzoly pro správu Netskope v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v konzole pro správu Netskope pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele konzoly správce Netskope](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do konzoly pro správu Netskope**.

    ![Mapování skupin konzoly správce Netskope](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do konzoly pro správu Netskope v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v konzole pro správu Netskope pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy skupiny konzoly správce Netskope](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro konzolu pro správu Netskope, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit v konzole pro správu Netskope, výběrem požadovaných hodnot v **oblasti** **Nastavení** v části.

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Část s **podrobnostmi o synchronizaci** můžete použít ke sledování průběhu a následného odkazu na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v konzole pro správu Netskope.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).


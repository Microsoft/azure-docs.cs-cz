---
title: 'Kurz: Konfigurace ověřování uživatelů Netskope pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se, jak nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Netskope ověřování uživatelů.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: 46766a7439185714648572f3f1b9d51ef96abba6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357466"
---
# <a name="tutorial-configure-netskope-user-authentication-for-automatic-user-provisioning"></a>Kurz: Konfigurace ověřování uživatelů Netskope pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Netskope ověřování uživatelů a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů a skupin pro Netskope ověřování uživatelů.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant ověřování uživatele Netskope](https://www.netskope.com/)
* Uživatelský účet v Netskope ověřování uživatele s oprávněními správce.

## <a name="assigning-users-to-netskope-user-authentication"></a>Přiřazení uživatelů k Netskope ověřování uživatelů

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k ověření uživatele Netskope. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny, abyste Netskope ověřování uživatelů podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-user-authentication"></a>Důležité tipy pro přiřazení uživatelů k Netskope ověřování uživatelů

* Doporučuje se, aby jeden uživatel Azure AD byl přiřazený k Netskope ověřování uživatelů, aby mohl otestovat automatickou konfiguraci zřizování uživatelů. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele k Netskope ověření uživatele, musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-netskope-user-authentication-for-provisioning"></a>Nastavení ověření uživatele Netskope pro zřizování

1. Přihlaste se ke [konzole Správce ověřování uživatelů Netskope](https://netskope.goskope.com/). Přejděte do **Nastavení domovská >**.

    ![Konzola pro správu ověřování uživatelů Netskope](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Přejděte na **nástroje**. V nabídce **nástroje** přejděte na **nástroje adresáře > integrace SCIM**.

    ![Nástroje pro ověřování uživatelů Netskope](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope ověřování uživatele přidat SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Posuňte se dolů a klikněte na tlačítko **Přidat token** . V dialogovém okně **Přidat název klienta OAuth** zadejte **název klienta** a klikněte na tlačítko **Uložit** .

    ![Přidat token pro ověření uživatele Netskope](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Název klienta ověřování uživatele Netskope](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Zkopírujte **adresu URL serveru SCIM** a **token**. Tyto hodnoty se zadají do polí Adresa URL tenanta a tajného tokenu na kartě zřizování vaší aplikace ověřování uživatelů Netskope v Azure Portal.

    ![Netskope vytvořit token pro ověření uživatele](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-user-authentication-from-the-gallery"></a>Přidání ověření uživatele Netskope z Galerie

Než nakonfigurujete ověřování uživatele Netskope pro Automatické zřizování uživatelů pomocí Azure AD, musíte do svého seznamu spravovaných aplikací pro SaaS přidat Netskope ověřování uživatelů z Galerie aplikací Azure AD.

**Pokud chcete přidat ověřování uživatelů Netskope z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Netskope ověření uživatele**, vyberte možnost **ověřování uživatelů Netskope** na panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Netskope ověřování uživatelů v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-user-authentication"></a>Konfigurace automatického zřizování uživatelů pro Netskope ověřování uživatelů 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Netskope ověřování uživatelů na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro ověřování uživatelů Netskope podle pokynů uvedených v [kurzu Netskope pro ověřování uživatelů pomocí jednotného přihlašování](./netskope-cloud-security-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

> [!NOTE]
> Další informace o koncovém bodu SCIM ověření uživatele Netskope najdete v [tomto](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)tématu.

### <a name="to-configure-automatic-user-provisioning-for-netskope-user-authentication-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro ověřování uživatelů Netskope ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Netskope ověřování uživatele**.

    ![Odkaz na ověření uživatele Netskope v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte na **adrese URL klienta** vstupní hodnotu **URL serveru SCIM** . Zadejte hodnotu **tokenu** získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k ověřování uživatelů Netskope. Pokud se připojení nepovede, ujistěte se, že váš účet pro ověřování uživatelů Netskope má oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatelů a Netskope ověřování uživatelů**.

    ![Mapování uživatelů ověřování uživatelů Netskope](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD a Netskope ověřování uživatelů v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Netskope ověřování uživatelů pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Uživatelské atributy ověřování uživatelů Netskope](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny a Netskope ověřování uživatelů**.

    ![Mapování skupin ověřování uživatelů Netskope](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny synchronizované z Azure AD a Netskope ověřování uživatelů v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v ověřování uživatelů Netskope pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy skupiny ověřování uživatelů Netskope](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro ověřování uživatelů Netskope, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Určete uživatele nebo skupiny, které chcete zřídit pro Netskope ověřování uživatelů, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Část **Podrobnosti o synchronizaci** můžete použít k monitorování průběhu a následnému sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD při ověřování uživatele Netskope.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
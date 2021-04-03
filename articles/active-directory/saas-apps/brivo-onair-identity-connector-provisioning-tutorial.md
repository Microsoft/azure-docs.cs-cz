---
title: 'Kurz: Konfigurace konektoru Brivo OnAir identity pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů k Brivo OnAir identity Connectoru.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 4f230a70ae87c7c0ec9db347e7cf975c0252bb18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998085"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Kurz: Konfigurace konektoru OnAir identity Brivo pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Brivo OnAir identity Connector a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD tak, aby automaticky zřídily a zrušily zřizování uživatelů a/nebo skupin pro Brivo OnAir identity Connector.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant Brivo OnAir identity Connector](https://www.brivo.com/lp/quote)
* Uživatelský účet ve službě Brivo OnAir identity Connector s oprávněními správce vyšších.

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Přiřazení uživatelů k Brivo OnAir identity Connector

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Brivo OnAir identity Connectoru. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Brivo OnAir identity Connector podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Důležité tipy pro přiřazení uživatelů ke konektoru Brivo OnAir identity

* Doporučuje se, aby se k otestování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD k Brivo OnAir identity Connectoru. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Brivo OnAir identity Connector musíte vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná) v dialogovém okně přiřazení. Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>Nastavení Brivo OnAir identity Connector pro zřizování

1. Přihlaste se ke [konzole pro správu konektoru Brivo OnAir identity](https://acs.brivo.com/login/). Přejděte na **účet > nastavení účtu**.

   ![Konzola správce Brivo OnAir identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2. Klikněte na kartu **Azure AD** . Na stránce s podrobnostmi **služby Azure AD** znovu zadejte heslo účtu správce vyšších účtů. Klikněte na **Odeslat**.

   ![Brivo OnAir identity Connector Azure](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3. Klikněte na tlačítko **Kopírovat token** a uložte **tajný token**. Tato hodnota se zadá do pole token tajného klíče na kartě zřizování aplikace Brivo OnAir identity Connector ve Azure Portal.

   ![Token konektoru Brivo OnAir identity](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Přidání konektoru Brivo OnAir identity z Galerie

Před konfigurací Brivo OnAir identity Connectoru pro Automatické zřizování uživatelů pomocí Azure AD je potřeba přidat konektor Brivo OnAir identity z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat konektor Brivo OnAir identity z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Brivo OnAir identity Connector**, na panelu výsledků vyberte **Brivo OnAir identity Connector** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Brivo OnAir identity Connector v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Konfigurace automatického zřizování uživatelů pro Brivo OnAir identity Connector 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Brivo OnAir identity Connector na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro konektor Brivo OnAir identity ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Brivo OnAir identity Connector**.

    ![Odkaz Brivo OnAir identity Connector v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://scim.brivo.com/ActiveDirectory/v2/` **adresu URL tenanta**. Zadejte hodnotu **SCIM tokenu ověřování** získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Brivo OnAir identity Connector. Pokud se připojení nepovede, ujistěte se, že váš účet Brivo OnAir identity Connector má oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatelů a Brivo OnAir identity Connector**.

    ![Mapování uživatele konektoru Brivo OnAir identity](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. Zkontrolujte atributy uživatele synchronizované z Azure AD až Brivo OnAir identity Connector v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v konektoru Brivo OnAir identity pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Uživatelské atributy Brivo OnAir identity Connectoru](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do Brivo OnAir identity Connector**.

    ![Mapování skupin Brivo OnAir identity Connectoru](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. Zkontrolujte atributy skupiny synchronizované z Azure AD až Brivo OnAir identity Connector v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v Brivo OnAir identity Connectoru pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy skupiny konektorů Brivo OnAir identity](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu zřizování Azure AD pro Brivo OnAir identity Connector, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro Brivo OnAir identity Connector, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Část s **podrobnostmi o synchronizaci** můžete použít ke sledování průběhu a následného odkazu na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Brivo OnAir identity Connector.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).


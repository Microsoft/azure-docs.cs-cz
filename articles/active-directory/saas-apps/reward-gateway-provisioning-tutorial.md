---
title: 'Kurz: Konfigurace brány odměna pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro odměnu brány.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 881be102a00badf1e03188b26514ad0eb1a9bd90
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520772"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>Kurz: Konfigurace brány pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést v části brána a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD tak, aby automaticky zřídily a zrušily zřizování uživatelů a skupin pro odměnu brány.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant brány pro odměnu](https://www.rewardgateway.com/)
* Uživatelský účet v Záměna brány s oprávněními správce.

## <a name="assigning-users-to-reward-gateway"></a>Přiřazování uživatelů k odměnu brány 

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k bráně pro odměnu. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k odměnu brány podle pokynů v tématu [přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md).


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>Důležité tipy pro přiřazování uživatelů k odměnu brány 

* Doporučuje se k tomu, aby se k otestování automatické konfigurace zřizování uživatelů přiřadil jeden účet Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k odměnu brány musíte vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná) v dialogovém okně přiřazení. Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-reward-gateway--for-provisioning"></a>Nastavení brány pro zajištění záměna pro zřizování
Před konfigurací brány pro odměnu pro Automatické zřizování uživatelů se službou Azure AD budete muset povolit zřizování SCIM pro bránu bez motivace.

1. Přihlaste se ke [konzole Správce brány pro odměnu](https://rewardgateway.photoshelter.com/login/). Klikněte na **integrace**.

    ![Snímek obrazovky konzoly Správce brány s možností integrace s názvem.](media/reward-gateway-provisioning-tutorial/image00.png)

2.  Vyberte **Moje integrace**.

    ![Snímek obrazovky se dvěma možnostmi integrace pomocí možnosti moje integrace s názvem.](media/reward-gateway-provisioning-tutorial/image001.png)

3.  Zkopírujte hodnoty **SCIM URL (v2)** a **tokenu OAuth Bearer**. Tyto hodnoty se zadají do pole Adresa URL klienta a tajný token na kartě zřizování aplikace brány pro odměnu v Azure Portal.

    ![Snímek obrazovky s panelem moje integrace s textovým polem s tokenem OAuth pro držitele](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>Přidání brány pro odměnu z Galerie

Pokud chcete nakonfigurovat bránu pro Automatické zřizování uživatelů pomocí Azure AD, musíte do svého seznamu spravovaných aplikací SaaS přidat bránu pro odměnu z Galerie aplikací Azure AD.

**Pokud chcete přidat nebránu brány z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **záměna brány**, vyberte možnost odznačit **bránu** na panelu výsledky a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Odměna brány v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Konfigurace automatického zřizování uživatelů pro odměnu brány  

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Záměna brány na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro bránu pro odměnu podle pokynů uvedených v [kurzu jednotného přihlašování brány pro odměnu](reward-gateway-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>Postup při konfiguraci automatického zřizování uživatelů pro bránu pro odměnu v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost odznačit **bránu**.

    ![Odkaz na bránu pro odměnu v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte hodnoty **SCIM URL (v2)** a **token nosiče OAuth** načtené dříve v **adrese URL tenanta** a **tajného tokenu** v uvedeném pořadí. Klikněte na **Test připojení** a ujistěte se, že se Azure AD může připojit k bráně pro odměnu. Pokud se připojení nepovede, zajistěte, aby měl účet brány pro odměnu oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelů a odměnu brány**.

    ![Snímek obrazovky s oddílem mapování s možností synchronizovat Azure Active Directory uživatele k odměnu brány s názvem.](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. Zkontrolujte atributy uživatelů synchronizované z Azure AD a odměna brány v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Záměna brány pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Snímek obrazovky s oddílem mapování atributů se zobrazenými šesti mapováními](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu zřizování Azure AD pro odměnu brány, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro odměnu brány, výběrem požadovaných hodnot v **rozsahu** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Část **Podrobnosti o synchronizaci** můžete použít ke sledování průběhu a následného odkazu na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování služby Azure AD v bráně pro odměnu.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

Brána pro odměnu v tuto chvíli nepodporuje zřizování skupin.

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

[Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
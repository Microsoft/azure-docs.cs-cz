---
title: 'Kurz: Konfigurace SpaceIQ pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro SpaceIQ.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 4e115de4f86baba9559730d94f45ab7b17cc4379
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002200"
---
# <a name="tutorial-configure-spaceiq-for-automatic-user-provisioning"></a>Kurz: Konfigurace SpaceIQ pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v SpaceIQ a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro SpaceIQ.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant SpaceIQ](https://spaceiq.com/)
* Uživatelský účet v SpaceIQ s oprávněními správce.

## <a name="assigning-users-to-spaceiq"></a>Přiřazování uživatelů k SpaceIQ

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k SpaceIQ. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k SpaceIQ podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-spaceiq"></a>Důležité tipy pro přiřazení uživatelů k SpaceIQ

* Doporučuje se, aby se k SpaceIQ k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k SpaceIQ musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-spaceiq-for-provisioning"></a>Nastavení SpaceIQ pro zřizování

1. Přihlaste se ke [konzole pro správu SpaceIQ](https://main.spaceiq.com/login/). Přejděte na **Nastavení** tím, že ho vyberete z rozevírací nabídky v pravém horním rohu obrazovky.

    ![Konzola pro správu SpaceIQ](media/spaceiq-provisioning-tutorial/admin.png)

2.  Na stránce **Nastavení** vyberte **Integrace třetích stran**.

    ![SpaceIQ přidat SCIM](media/spaceiq-provisioning-tutorial/thirdparty.png)

3.  Přejděte na kartu **zřizování a jednotné přihlašování** . Vyhledejte dlaždici **Azure** . Klikněte na **aktivovat**.

    ![Zřizování a jednotné přihlašování SpaceIQ](media/spaceiq-provisioning-tutorial/provisioning.png)

    ![SpaceIQ aktivovat Azure ](media/spaceiq-provisioning-tutorial/azure.png)

3.  Zkopírujte **token nosiče SCIM**. Tato hodnota se zadá do pole token tajného kódu na kartě zřizování vaší aplikace SpaceIQ ve Azure Portal. Klikněte na **aktivovat** .

    ![SpaceIQ vytvořit token](media/spaceiq-provisioning-tutorial/token.png)

## <a name="add-spaceiq-from-the-gallery"></a>Přidání SpaceIQ z Galerie

Před konfigurací SpaceIQ pro Automatické zřizování uživatelů se službou Azure AD je nutné přidat SpaceIQ z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat SpaceIQ z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SpaceIQ**, na panelu výsledků vyberte **SpaceIQ** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![SpaceIQ v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-spaceiq"></a>Konfigurace automatického zřizování uživatelů na SpaceIQ 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v SpaceIQ na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro SpaceIQ podle pokynů uvedených v [kurzu SpaceIQ jednotného přihlašování](./spaceiq-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce vzájemně přidávají.

### <a name="to-configure-automatic-user-provisioning-for-spaceiq-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro SpaceIQ ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **SpaceIQ**.

    ![Odkaz SpaceIQ v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://api.spaceiq.com/scim` **adresu URL tenanta**. Zadejte hodnotu **SCIM tokenu ověřování** získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k SpaceIQ. Pokud se připojení nepovede, ujistěte se, že má váš účet SpaceIQ oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé SpaceIQ**.

    ![Mapování uživatelů SpaceIQ](media/spaceiq-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do SpaceIQ v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v SpaceIQ pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele SpaceIQ](media/spaceiq-provisioning-tutorial/userattributes.png)

11. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Pokud chcete povolit službu Azure AD Provisioning pro SpaceIQ, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit pro SpaceIQ, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

14. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v SpaceIQ.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
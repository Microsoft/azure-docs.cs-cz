---
title: 'Kurz: Konfigurace přiblížení pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů k přiblížení.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: cd832a9dfec4680222d2c985f49aba499a56aaac
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062736"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Kurz: Konfigurace přiblížení pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést v rámci lupy a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD tak, aby automaticky zřídily a zrušily zřizování uživatelů a/nebo skupin, které se mají přiblížit.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant lupy](https://zoom.us/pricing)
* Uživatelský účet v přiblížení s oprávněními správce

## <a name="add-zoom-from-the-gallery"></a>Přidat přiblížení z Galerie

Než nakonfigurujete přiblížení pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat přiblížení z Galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat přiblížení z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **přiblížení**, v panelu výsledky vyberte **přiblížení** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Přiblížení v seznamu výsledků](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>Přiřadit uživatele k přiblížení

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k přiblížení. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny, abyste je mohli přiblížit podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>Důležité tipy pro přiřazení uživatelů k přiblížení

* Doporučuje se, aby byl k přiblížení přiřazený jeden uživatel Azure AD, aby bylo možné otestovat automatickou konfiguraci zřizování uživatelů. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k přiblížení musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configure-automatic-user-provisioning-to-zoom"></a>Konfigurace automatického zřizování uživatelů pro přiblížení 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v přiblížení na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit pro přiblížení jednotné přihlašování založené na SAML, a to podle pokynů uvedených v [kurzu přiblížení jednotného přihlašování](zoom-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro přiblížení ve službě Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Lupa**.

    ![Odkaz Lupa v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://api.zoom.us/scim` v **adrese URL tenanta**. Pokud chcete načíst **tajný token** účtu lupy, postupujte podle návodu popsaného v kroku 6.

6. Přihlaste se ke [konzole správce lupy](https://zoom.us/signin). Přejděte na **upřesnit > přiblížení pro vývojáře** v levém navigačním podokně.

    ![Integrace lupy](media/zoom-provisioning-tutorial/zoom01.png)

    V pravém horním rohu stránky přejděte ke **správě** . 

    ![Přiblížit instalaci](media/zoom-provisioning-tutorial/zoom02.png)

    Přejděte na vytvořenou aplikaci Azure AD. 
    
    ![Přiblížení aplikace](media/zoom-provisioning-tutorial/zoom03.png)

    V levém navigačním podokně vyberte možnost **přihlašovací údaje aplikace** .

    ![Přiblížení aplikace](media/zoom-provisioning-tutorial/zoom04.png)

    Načtěte níže uvedenou hodnotu tokenu JWT a zadejte ji do pole **tajný token** v Azure AD. Pokud potřebujete nový token bez vypršení platnosti, budete muset znovu nakonfigurovat čas vypršení platnosti, který bude automaticky generovat nový token. 

    ![Přiblížit instalaci](media/zoom-provisioning-tutorial/zoom05.png)

7. Po vyplnění polí zobrazených v kroku 5 klikněte na **Test připojení** . tím zajistíte, aby se služba Azure AD mohla připojit k přiblížení. Pokud se připojení nepovede, zajistěte, aby měl váš účet přiblížení oprávnění správce, a zkuste to znovu.

    ![Podpisový](common/provisioning-testconnection-tenanturltoken.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé pro přiblížení**.

    ![Přiblížení mapování uživatelů](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. Zkontrolujte atributy uživatelů synchronizované z Azure AD pro přiblížení oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v přiblížení pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.
    
     ![Přiblížení mapování uživatelů](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro přiblížení, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .
    
    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro přiblížení, výběrem požadovaných hodnot v části **Rozsah** **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Část **Podrobnosti o synchronizaci** můžete použít ke sledování průběhu a následnému zobrazení odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD při přiblížení.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Přiblížení nepodporuje zřizování skupin.

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

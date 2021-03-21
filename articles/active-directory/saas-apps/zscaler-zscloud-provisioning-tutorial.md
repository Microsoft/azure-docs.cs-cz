---
title: 'Kurz: Konfigurace Zscaler ZSCloud pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: V tomto kurzu se dozvíte, jak nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů na Zscaler ZSCloud.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: fa90cbf1e467416010ae0ba83e9344a84ce52e21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936494"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Kurz: Konfigurace Zscaler ZSCloud pro Automatické zřizování uživatelů

V tomto kurzu se dozvíte, jak nakonfigurovat Azure Active Directory (Azure AD) k automatickému zřízení a zrušení zřízení uživatelů nebo skupin pro Zscaler ZSCloud.

> [!NOTE]
> Tento kurz popisuje konektor, který je založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů při SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="prerequisites"></a>Předpoklady

K dokončení kroků popsaných v tomto kurzu budete potřebovat následující:

* Tenanta Azure AD.
* Tenant Zscaler ZSCloud.
* Uživatelský účet v Zscaler ZSCloud s oprávněními správce.

> [!NOTE]
> Integrace zřizování Azure AD spoléhá na rozhraní Zscaler ZSCloud SCIM API, které je k dispozici pro podnikové účty.

## <a name="add-zscaler-zscloud-from-the-gallery"></a>Přidání Zscaler ZSCloud z Galerie

Před konfigurací Zscaler ZSCloud pro Automatické zřizování uživatelů pomocí Azure AD je nutné přidat Zscaler ZSCloud z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

V [Azure Portal](https://portal.azure.com)v levém podokně vyberte **Azure Active Directory**:

![Vyberte Azure Active Directory.](common/select-azuread.png)

Přejít na **podnikové aplikace** a pak vyberte **všechny aplikace**:

![Podnikové aplikace](common/enterprise-applications.png)

Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace** :

![Vybrat novou aplikaci](common/add-new-app.png)

Do vyhledávacího pole zadejte **Zscaler ZSCloud**. Ve výsledcích vyberte **Zscaler ZSCloud** a pak vyberte **Přidat**.

![Seznam výsledků](common/search-new-app.png)

## <a name="assign-users-to-zscaler-zscloud"></a>Přiřazení uživatelů k Zscaler ZSCloud

Uživatelé Azure AD musí mít přiřazený přístup k vybraným aplikacím, aby je mohli používat. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé nebo skupiny, kteří jsou přiřazeni k aplikaci v Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, měli byste se rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Zscaler ZSCloud. Až se rozhodnete, že můžete přiřadit tyto uživatele a skupiny k Zscaler ZSCloud podle pokynů v tématu [přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Důležité tipy pro přiřazení uživatelů k Zscaler ZSCloud

* Doporučujeme, abyste nejdřív přiřadili jednoho uživatele Azure AD k Zscaler ZSCloud, abyste mohli otestovat automatickou konfiguraci zřizování uživatelů. Později můžete přiřadit více uživatelů a skupin.

* Když přiřadíte uživatele k Zscaler ZSCloud, musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-automatic-user-provisioning"></a>Nastavení automatického zřizování uživatelů

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů a skupin v Zscaler ZSCloud na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Pro Zscaler ZSCloud možná budete chtít povolit jednotné přihlašování založené na SAML. Pokud to uděláte, postupujte podle pokynů v [kurzu Zscaler ZSCloud jednotného přihlašování](zscaler-zsCloud-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, ale tyto dvě funkce spolu doplňují.

> [!NOTE]
> Pokud jsou uživatelé a skupiny zřízené nebo nezřízené, doporučujeme, abyste pravidelně restartovali zřizování, aby se zajistilo, že členství ve skupině bude správně aktualizováno. Při restartování dojde k vynucení, aby naše služba znovu vyhodnotila všechny skupiny a aktualizovala členství. 

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **podnikové aplikace**  >  **všechny aplikace**  >  **Zscaler ZSCloud**:

    ![Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Zscaler ZSCloud**:

    ![Seznam aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** :

    ![Zřizování Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. Nastavte **režim zřizování** na **automaticky**:

    ![Nastavení režimu zřizování](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta** a **tajný token** účtu Zscaler ZSCloud, jak je popsáno v dalším kroku.

6. Pokud chcete získat **adresu URL tenanta** a **tajný token**, klikněte na  >  portál Zscaler ZSCloud na **nastavení ověřování** pro správu a v části **typ ověřování** vyberte **SAML** :

    ![Nastavení ověřování Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    Vyberte **Konfigurovat SAML** pro otevření okna **Konfigurovat okno SAML** :

    ![Konfigurovat okno SAML](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)

    Vyberte **povolit SCIM-Based zřizování** a zkopírujte **základní adresu URL** a **nosný token** a pak nastavení uložte. V Azure Portal vložte **základní adresu URL** do pole **Adresa URL tenanta** a **token nosiče** do pole **tajný token** .

7. Až zadáte hodnoty do polí **Adresa URL tenanta** a **tajný token** , vyberte **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Zscaler ZSCloud. Pokud se připojení nepovede, ujistěte se, že váš účet Zscaler ZSCloud má oprávnění správce, a zkuste to znovu.

    ![Otestování připojení](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování. Vyberte **Odeslat e-mailové oznámení, když dojde k selhání**:

    ![Nastavení e-mailu s oznámením](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. Vyberte **Uložit**.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé ZscalerZSCloud**:

    ![Synchronizace uživatelů Azure AD](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. Zkontrolujte atributy uživatele synchronizované z Azure AD do Zscaler ZSCloud v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Zscaler ZSCloud pro operace aktualizace. Vyberte **Uložit** a potvrďte všechny změny.

    ![Snímek obrazovky s oddílem mapování atributů se zobrazenými sedmi mapováními](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do ZscalerZSCloud**:

    ![Synchronizace skupin Azure AD](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. Zkontrolujte atributy skupiny synchronizované z Azure AD do Zscaler ZSCloud v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v Zscaler ZSCloud pro operace aktualizace. Vyberte **Uložit** a potvrďte všechny změny.

    ![Snímek obrazovky oddílu mapování atributů se třemi zobrazenými mapováními](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. Pokud chcete nakonfigurovat filtry oborů, přečtěte si pokyny v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pokud chcete povolit službu Azure AD Provisioning pro Zscaler ZSCloud, změňte **stav zřizování** na **zapnuto** v části **Nastavení** :

    ![Stav zřizování](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. Určete uživatele nebo skupiny, které chcete zřídit pro Zscaler ZSCloud, a to výběrem požadovaných hodnot v části **obor** v části **Nastavení** :

    ![Hodnoty oboru](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. Až budete připraveni zřídit, vyberte **Uložit**:

    ![Vyberte možnost Uložit.](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a skupin definovaných v části **obor** v části **Nastavení** . Počáteční synchronizace trvá déle než následující synchronizace, ke kterým dochází každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Průběh můžete sledovat v části **Podrobnosti o synchronizaci** . Můžete také sledovat odkazy na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Zscaler ZSCloud.

Informace o tom, jak číst protokoly zřizování služby Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png
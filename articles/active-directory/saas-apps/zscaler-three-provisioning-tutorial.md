---
title: 'Kurz: Konfigurace Zscaler tří pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: V tomto kurzu se dozvíte, jak nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů, které Zscaler tři.
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
ms.openlocfilehash: c16c02a870edb1a777b63da6fea57fc02136d643
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936596"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>Kurz: Konfigurace Zscaler tří pro Automatické zřizování uživatelů

V tomto kurzu se dozvíte, jak nakonfigurovat Azure Active Directory (Azure AD) k automatickému zřízení a zrušení zřízení uživatelů nebo skupin, které Zscaler tři.

> [!NOTE]
> Tento kurz popisuje konektor, který je založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů při SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

K dokončení kroků popsaných v tomto kurzu budete potřebovat následující:

* Tenanta Azure AD.
* Zscaler třetí tenant.
* Uživatelský účet v Zscaler tři s oprávněními správce.

> [!NOTE]
> Integrace zřizování Azure AD spoléhá na rozhraní Zscaler ZSCloud SCIM API, které je k dispozici pro podnikové účty.

## <a name="adding-zscaler-three-from-the-gallery"></a>Přidání Zscaler tři z Galerie

Před konfigurací Zscaler tři pro Automatické zřizování uživatelů pomocí Azure AD je nutné přidat Zscaler tři z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

V [Azure Portal](https://portal.azure.com)v levém podokně vyberte **Azure Active Directory**:

![Vyberte Azure Active Directory.](common/select-azuread.png)

Přejít na **podnikové aplikace** a pak vyberte **všechny aplikace**:

![Podnikové aplikace](common/enterprise-applications.png)

Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace** :

![Vybrat novou aplikaci](common/add-new-app.png)

Do vyhledávacího pole zadejte **Zscaler tři**. Ve výsledcích vyberte **Zscaler tři** a pak vyberte **Přidat**.

![Seznam výsledků](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>Přiřadit uživatele k Zscaler třem

Uživatelé Azure AD musí mít přiřazený přístup k vybraným aplikacím, aby je mohli používat. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé nebo skupiny, kteří jsou přiřazeni k aplikaci v Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, měli byste se rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Zscaler třem. Až se rozhodnete, že tyto uživatele a skupiny můžete přiřadit k Zscaler třem podle pokynů v tématu [přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>Důležité tipy pro přiřazení uživatelů k Zscaler třem

* Doporučujeme, abyste nejdřív přiřadili jediného uživatele Azure AD, který Zscaler tři, aby bylo možné otestovat automatickou konfiguraci zřizování uživatelů. Později můžete přiřadit více uživatelů a skupin.

* Když přiřadíte uživatele Zscaler tři, musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-automatic-user-provisioning"></a>Nastavení automatického zřizování uživatelů

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů a skupin v Zscaler třech na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Pro Zscaler tři možná budete chtít povolit jednotné přihlašování založené na SAML. Pokud to uděláte, postupujte podle pokynů v tématu [Zscaler tři jednotné přihlašování](zscaler-three-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, ale tyto dvě funkce spolu doplňují.

> [!NOTE]
> Pokud jsou uživatelé a skupiny zřízené nebo nezřízené, doporučujeme, abyste pravidelně restartovali zřizování, aby se zajistilo, že členství ve skupině bude správně aktualizováno. Při restartování dojde k vynucení, aby naše služba znovu vyhodnotila všechny skupiny a aktualizovala členství. 

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **podnikové aplikace**  >  **všechny aplikace**  >  **Zscaler tři**:

    ![Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Zscaler tři**:

    ![Seznam aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** :

    ![Zscaler tři zřizování](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. Nastavte **režim zřizování** na **automaticky**:

    ![Nastavení režimu zřizování](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta** a **tajný token** vašeho Zscaler tři účtu, jak je popsáno v dalším kroku.

6. Pokud chcete získat **adresu URL tenanta** a **tajný token**, klikněte **na**  >  tlačítko **nastavení ověřování** na portálu Zscaler tři a v části **typ ověřování** vyberte **SAML** :

    ![Zscaler tři nastavení ověřování](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    Vyberte **Konfigurovat SAML** pro otevření okna **Konfigurovat okno SAML** :

    ![Konfigurovat okno SAML](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    Vyberte **povolit SCIM-Based zřizování** a zkopírujte **základní adresu URL** a **nosný token** a pak nastavení uložte. V Azure Portal vložte **základní adresu URL** do pole **Adresa URL tenanta** a **token nosiče** do pole **tajný token** .

7. Až zadáte hodnoty do polí **Adresa URL tenanta** a **tajný token** , vyberte **Test připojení** a ujistěte se, že se Azure AD může připojit k Zscaler třem. Pokud se připojení nepovede, zajistěte, aby měl účet Zscaler tři oprávnění správce, a zkuste to znovu.

    ![Otestování připojení](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování. Vyberte **Odeslat e-mailové oznámení, když dojde k selhání**:

    ![Nastavení e-mailu s oznámením](./media/zscaler-three-provisioning-tutorial/notification.png)

9. Vyberte **Uložit**.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé ZscalerThree**:

    ![Synchronizace uživatelů Azure AD](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. Zkontrolujte atributy uživatele synchronizované z Azure AD až Zscaler tři v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Zscaler tři pro operace aktualizace. Vyberte **Uložit** a potvrďte všechny změny.

    ![Snímek obrazovky s oddílem mapování atributů se zobrazenými sedmi mapováními](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do ZscalerThree**:

    ![Synchronizace skupin Azure AD](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. Zkontrolujte atributy skupiny synchronizované z Azure AD až Zscaler tři v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v Zscaler třech pro operace aktualizace. Vyberte **Uložit** a potvrďte všechny změny.

    ![Snímek obrazovky oddílu mapování atributů se třemi zobrazenými mapováními](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. Pokud chcete nakonfigurovat filtry oborů, přečtěte si pokyny v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pokud chcete povolit službu Azure AD Provisioning pro Zscaler tři, změňte **stav zřizování** na **zapnuto** v části **Nastavení** :

    ![Stav zřizování](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. Definujte uživatele nebo skupiny, které chcete zřídit pro Zscaler, výběrem požadovaných hodnot v části **Rozsah** **Nastavení** :

    ![Hodnoty oboru](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. Až budete připraveni zřídit, vyberte **Uložit**:

    ![Vyberte možnost Uložit.](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a skupin definovaných v části **obor** v části **Nastavení** . Počáteční synchronizace trvá déle než následující synchronizace, ke kterým dochází každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Průběh můžete sledovat v části **Podrobnosti o synchronizaci** . Můžete také sledovat odkazy na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Zscaler třech.

Informace o tom, jak číst protokoly zřizování služby Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png
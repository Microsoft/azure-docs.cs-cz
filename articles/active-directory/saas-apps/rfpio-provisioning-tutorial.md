---
title: 'Kurz: Konfigurace RFPIO pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro RFPIO.
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
ms.openlocfilehash: 98f92a6b0169c573b97788c7ffaf76255796cfa6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91278902"
---
# <a name="tutorial-configure-rfpio-for-automatic-user-provisioning"></a>Kurz: Konfigurace RFPIO pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v RFPIO a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro RFPIO.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [TENANT RFPIO](https://www.rfpio.com/product/)
* Uživatelský účet v RFPIO s oprávněními správce.

## <a name="assigning-users-to-rfpio"></a>Přiřazování uživatelů k RFPIO

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k RFPIO. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k RFPIO podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-rfpio"></a>Důležité tipy pro přiřazení uživatelů k RFPIO

* Doporučuje se, aby se k RFPIO k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k RFPIO musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-rfpio-for-provisioning"></a>Nastavení RFPIO pro zřizování

Před konfigurací RFPIO pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na RFPIO.

1.  Přihlaste se ke konzole pro správu RFPIO. V levém dolním rohu konzoly Správce klikněte na **klienta**.

    ![Konzola pro správu RFPIO](media/rfpio-provisioning-tutorial/aadtest0.png)

2.  Klikněte na **Nastavení organizace**.
    
    ![Správce RFPIO](media/rfpio-provisioning-tutorial/aadtest.png)

3.  Přejděte na SCIM zabezpečení **správy uživatelů**  >  **SECURITY**  >  **SCIM**.

    ![RFPIO přidat SCIM](media/rfpio-provisioning-tutorial/scim.png)

4.  Ujistěte se, že je povoleno **Automatické zřizování uživatelů** . Klikněte na **vygenerovat token rozhraní API SCIM**.

    ![Snímek obrazovky S oddílem C I M s parametrem GENERATE S C I M A TOKENem P, který se nazývá](media/rfpio-provisioning-tutorial/generate.png)

5.  Uložte **token rozhraní API SCIM** , protože tento token se pro účely zabezpečení nezobrazuje znovu. Tato hodnota se zadá do pole **token tajného** kódu na kartě zřizování vaší aplikace RFPIO ve Azure Portal.

    ![Snímek obrazovky S částí C I M s dialogovým oknem upozornění, které se zobrazí po výběru odeslat.](media/rfpio-provisioning-tutorial/auth.png)

## <a name="add-rfpio-from-the-gallery"></a>Přidání RFPIO z Galerie

Pokud chcete nakonfigurovat RFPIO pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat RFPIO z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat RFPIO z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **RFPIO**, na panelu výsledků vyberte **RFPIO** a potom kliknutím na tlačítko    **Přidat** přidejte aplikaci.

    ![RFPIO v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rfpio"></a>Konfigurace automatického zřizování uživatelů na RFPIO 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v RFPIO na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro RFPIO podle pokynů uvedených v [kurzu RFPIO jednotného přihlašování](rfpio-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-rfpio-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro RFPIO ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **RFPIO**.

    ![Odkaz RFPIO v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://<RFPIO tenant instance>.rfpio.com/rfpserver/scim/v2 ` **adresu URL tenanta**. Ukázková hodnota je `https://Azure-test1.rfpio.com/rfpserver/scim/v2` . Zadejte hodnotu **tokenu rozhraní API SCIM** získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k RFPIO. Pokud se připojení nepovede, ujistěte se, že má váš účet RFPIO oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé RFPIO**.

    ![Mapování uživatelů RFPIO](media/rfpio-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do RFPIO v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v RFPIO pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele RFPIO](media/rfpio-provisioning-tutorial/userattributes.png)

10. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro RFPIO, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro RFPIO, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v RFPIO.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* RFPIO aktuálně nepodporuje zřizování skupin.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

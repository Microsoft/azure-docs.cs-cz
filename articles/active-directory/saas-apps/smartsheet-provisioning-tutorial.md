---
title: 'Kurz: Konfigurace Smartsheet pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Smartsheet.
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
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 9fbdf8a1c4b1881fc6dfd9d7b95a4103761e9ce7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063182"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Kurz: Konfigurace Smartsheet pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Smartsheet a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro Smartsheet.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant Smartsheet](https://www.smartsheet.com/pricing)
* Uživatelský účet v plánu Smartsheet Enterprise nebo Enterprise Premier s oprávněními správce systému.

## <a name="assign-users-to-smartsheet"></a>Přiřazení uživatelů k Smartsheet

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Smartsheet. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Smartsheet podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Důležité tipy pro přiřazení uživatelů k Smartsheet

* Doporučuje se, aby se k Smartsheet k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Smartsheet musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

* Aby se zajistila parita v přiřazování rolí uživatelů mezi Smartsheet a Azure AD, doporučuje se využít stejné přiřazení rolí, které jsou naplněné v seznamu úplných uživatelů Smartsheet. Pokud chcete načíst tento seznam uživatelů z Smartsheet, přejděte na **správce účtu > Správa uživatelů > další akce > stáhnout seznam uživatelů (CSV)**.

* Pro přístup k určitým funkcím aplikace Smartsheet vyžaduje, aby uživatel měl více rolí. Další informace o uživatelských typech a oprávněních v Smartsheet najdete v [uživatelském typu a oprávnění](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Pokud má uživatel v Smartsheet přiřazených víc rolí, **musíte** zajistit, aby se tato přiřazení rolí replikoval ve službě Azure AD, aby nedocházelo k situaci, kdy uživatelé můžou trvale ztratit přístup k objektům Smartsheet. Každá jedinečná role v Smartsheet **musí** být přiřazena jiné skupině v Azure AD. Pak je **nutné** přidat uživatele do všech skupin odpovídajících rolím, které jsou požadovány. 

## <a name="set-up-smartsheet-for-provisioning"></a>Nastavení Smartsheet pro zřizování

Před konfigurací Smartsheet pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na Smartsheet.

1. Přihlaste se jako **správce** systému na **[portálu Smartsheet](https://app.smartsheet.com/b/home)** a přejděte na **správce účtu**.

    ![Správce účtu Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Přejít na **ovládací prvky zabezpečení > Automatické zřizování uživatelů > upravit**.

    ![Ovládací prvky zabezpečení Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Přidejte a ověřte e-mailové domény pro uživatele, které chcete zřídit z Azure AD do Smartsheet. Pokud chcete zajistit, aby všechny akce zřizování pocházely jenom z Azure AD, a taky zajistit, aby byl váš seznam uživatelů Smartsheet synchronizovaný s přiřazeními Azure AD, vyberte **Nepovoleno** .

    ![Zřizování uživatelů Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Po dokončení ověření bude nutné doménu aktivovat. 

    ![Smartsheet aktivovat doménu](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Vygenerujte **tajný token** potřebný ke konfiguraci automatického zřizování uživatelů pomocí Azure AD tak, že přejdete na **aplikace a integrace**.

    ![Instalace Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Vyberte možnost **přístup přes rozhraní API**. Klikněte na **vygenerovat nový přístupový token**.

    ![Instalace Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Zadejte název přístupového tokenu rozhraní API. Klikněte na tlačítko **OK**.

    ![Instalace Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Zkopírujte přístupový token rozhraní API a uložte ho tak, jak se to bude zobrazovat jenom v takovém případě. To se vyžaduje v poli **tajný token** v Azure AD.

    ![Token Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Přidání Smartsheet z Galerie

Pokud chcete nakonfigurovat Smartsheet pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat Smartsheet z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Smartsheet**, na panelu výsledků vyberte **Smartsheet** . 

    ![Smartsheet v seznamu výsledků](common/search-new-app.png)

5. Vyberte tlačítko **zaregistrovat se k Smartsheet** , které vás přesměruje na přihlašovací stránku Smartsheet. 

    ![Smartsheet OIDC přidat](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Smartsheet je aplikace OpenIDConnect, která se rozhodne přihlásit k Smartsheet pomocí pracovního účtu Microsoft.

    ![Smartsheet OIDC přihlášení](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Po úspěšném ověření Přijměte výzvu k zadání souhlasu pro stránku souhlasu. Aplikace se pak automaticky přidá do vašeho tenanta a budete přesměrováni na svůj účet Smartsheet.

    ![Smartsheet OIDc – souhlas](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Konfigurace automatického zřizování uživatelů na Smartsheet 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Smartsheet na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Smartsheet ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Smartsheet**.

    ![Odkaz Smartsheet v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://scim.smartsheet.com/v2/` **adresu URL tenanta**. Zadejte hodnotu, kterou jste načetli a uložili dříve ze Smartsheet v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Smartsheet. Pokud se připojení nepovede, ujistěte se, že váš účet Smartsheet má oprávnění správce systému, a zkuste to znovu.

    ![Podpisový](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Smartsheet**.

    ![Mapování uživatelů Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Smartsheet v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Smartsheet pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro Smartsheet, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro Smartsheet, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Smartsheet.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Smartsheet nepodporuje obnovitelné odstranění. Když je **aktivní** atribut uživatele nastaven na hodnotu false, Smartsheet odstraní uživatele trvale.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

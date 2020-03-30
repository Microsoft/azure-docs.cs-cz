---
title: 'Kurz: Konfigurace inteligentního listu pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty do inteligentního listu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063182"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Kurz: Konfigurace inteligentního listu pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Smartsheet a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-provision uživatelů nebo skupin na Smartsheet.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Tenant smartsheet](https://www.smartsheet.com/pricing)
* Uživatelský účet v plánu Smartsheet Enterprise nebo Enterprise Premier s oprávněními správce systému.

## <a name="assign-users-to-smartsheet"></a>Přiřazení uživatelů k inteligentnímu listu

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k inteligentnímu listu. Jakmile se rozhodnete, můžete tyto uživatele a/nebo skupiny přiřadit k inteligentnímu listu podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Důležité tipy pro přiřazení uživatelů k inteligentnímu listu

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k Smartsheet otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k inteligentnímu listu je nutné v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

* Chcete-li zajistit paritu přiřazení rolí uživatelů mezi smartsheet a Azure AD, doporučujeme využít stejné role přiřazení naplněných v úplném seznamu uživatelů Smartsheet. Chcete-li tento seznam uživatelů načíst z inteligentního listu, přejděte na **položku Správce účtu > Správa uživatelů > další akce > Stáhnout seznam uživatelů (csv).**

* Pro přístup k určitým funkcím aplikace vyžaduje Smartsheet, aby měl uživatel více rolí. Další informace o typech uživatelů a oprávněních v inteligentním listu najdete v části [Typy uživatelů a oprávnění](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Pokud má uživatel v inteligentním listu přiřazeno více rolí, **musíte** zajistit, aby se tato přiřazení rolí replikovala ve službě Azure AD, aby se zabránilo scénáři, kdy by uživatelé mohli trvale ztratit přístup k objektům Smartsheet. Každá jedinečná role v inteligentním listu **musí** být přiřazena k jiné skupině ve službě Azure AD. Uživatel **musí** být potom přidány do každé ze skupin odpovídajících požadované role. 

## <a name="set-up-smartsheet-for-provisioning"></a>Nastavení inteligentního listu pro zřizování

Před konfigurací Smartsheet pro automatické zřizování uživatelů s Azure AD, budete muset povolit zřizování SCIM na Smartsheet.

1. Přihlaste se jako **Správce SysAdmin** na **[portálu Smartsheet](https://app.smartsheet.com/b/home)** a přejděte na **Správce účtu**.

    ![Správce účtu smartsheetu](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Přejděte na **ovládací prvky zabezpečení > automatické zřizování uživatelů > úpravy**.

    ![Ovládací prvky zabezpečení inteligentních listů](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Přidejte a ověřte e-mailové domény pro uživatele, které plánujete zřídit z Azure AD do smartsheetu. Zvolte **Není povoleno,** abyste zajistili, že všechny akce zřizování pocházejí pouze z Azure AD a také ujistěte se, že váš seznam uživatelů Smartsheet je synchronizován s přiřazeními Azure AD.

    ![Zřizování uživatelů smartsheetu](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Po dokončení ověření budete muset aktivovat doménu. 

    ![Aktivovat doménu smartsheetu](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Vygenerujte **tajný token** potřebný ke konfiguraci automatického zřizování uživatelů pomocí **Služby**Azure AD přechodem na aplikace a integrace .

    ![Instalace inteligentního listu](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Zvolte **Přístup k rozhraní API**. Klepněte na **tlačítko Generovat nový přístupový token**.

    ![Instalace inteligentního listu](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Definujte název přístupového tokenu rozhraní API. Klikněte na tlačítko **OK**.

    ![Instalace inteligentního listu](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Zkopírujte přístupový token rozhraní API a uložte jej, protože to bude jediný čas, kdy jej můžete zobrazit. To je vyžadováno v poli **tajný token** ve službě Azure AD.

    ![Token inteligentního listu](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Přidání inteligentního listu z galerie

Chcete-li nakonfigurovat Smartsheet pro automatické zřizování uživatelů pomocí Azure AD, je třeba přidat Smartsheet z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Smartsheet**, vpanelu výsledky vyberte **Inteligentní list.** 

    ![Inteligentní list v seznamu výsledků](common/search-new-app.png)

5. Vyberte tlačítko **Registrace pro smartsheet,** které vás přesměruje na přihlašovací stránku Smartlistu. 

    ![Přidání oidc inteligentního listu](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Vzhledem k tomu, že Smartsheet je aplikace OpenIDConnect, zvolte přihlášení k Smartsheetu pomocí pracovního účtu Microsoft.

    ![Přihlášení oidc inteligentního listu](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Po úspěšném ověření přijměte výzvu k souhlasu pro stránku souhlasu. Aplikace bude poté automaticky přidána do vašeho tenanta a budete přesměrováni na váš účet Smartsheet.

    ![Souhlas inteligentního oidc](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Konfigurace automatického zřizování uživatelů na smartsheet 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v inteligentním listu na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro smartsheet ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Smartsheet**.

    ![Odkaz Smartsheet v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://scim.smartsheet.com/v2/` zadejte adresu **URL klienta**. Zadejte hodnotu, kterou jste načetli a uložili dříve z inteligentního listu v **tajném tokenu**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k Smartsheetu. Pokud se připojení nezdaří, ujistěte se, že váš účet Smartsheet má oprávnění SysAdmin a zkuste to znovu.

    ![Podpisový](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s inteligentním listem**.

    ![Mapování uživatelů smartsheetu](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do smartsheet v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v inteligentním listu pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy inteligentních listů](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Chcete-li povolit službu zřizování Azure AD pro smartsheet, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit do inteligentního listu, **Settings** výběrem požadovaných hodnot v **části Obor.**

    ![Obor zřizování](common/provisioning-scope.png)

13. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na smartsheetu.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Inteligentní list nepodporuje obnovitelné odstranění. Pokud je **aktivní** atribut uživatele nastaven na hodnotu False, inteligentní list uživatele trvale odstraní.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

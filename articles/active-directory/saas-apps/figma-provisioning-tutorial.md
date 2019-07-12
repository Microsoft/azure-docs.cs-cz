---
title: 'Kurz: Konfigurace Figma automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Figma.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: b71aa6709b1c93688ea3eece4ce3f4066f9a6b7a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673168"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Kurz: Konfigurace Figma pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v Figma a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se Figma.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných Microsoft Azure podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

* Tenanta Azure AD.
* [Figma tenant](https://www.figma.com/pricing/).
* Účet uživatele s oprávněními správce v Figma.

## <a name="assign-users-to-figma"></a>Přiřazení uživatelů k Figma.
Azure Active Directory používá koncept volá přiřazení k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze na uživatele a/nebo skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k Figma. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny Figma podle zde uvedených pokynů:
 
* [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Důležité tipy pro přiřazování uživatelů k Figma

 * Dále je doporučeno jednoho uživatele Azure AD, je přiřazená Figma otestovat automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazení uživatele k Figma, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s rolí výchozího přístupu k nezahrnou se zřizování.

## <a name="set-up-figma-for-provisioning"></a>Nastavení Figma pro zřizování

Před konfigurací Figma pro automatické zřizování uživatelů pomocí Azure AD, je potřeba načíst některé informace o zajištění z Figma.

1. Přihlaste se k vaší [konzoly pro správu Figma](https://www.Figma.com/). Klikněte na ikonu ozubeného kola vedle vašeho tenanta.

    ![FigmaFigma-employee-provision](media/Figma-provisioning-tutorial/image0.png)

2. Přejděte do **Obecné > aktualizovat přihlašovací nastavení**.

    ![FigmaFigma-employee-provision](media/Figma-provisioning-tutorial/figma03.png)

3. Kopírovat **ID Tenanta**. Tato hodnota se použije k vytvoření adresu URL koncového bodu SCIM se zapisují do **adresy URL Tenanta** pole na kartě zřizování Figma aplikace na webu Azure Portal.

    ![Figma vytvořit Token](media/Figma-provisioning-tutorial/figma-tenantid.png)

4. Posuňte se dolů a klikněte na **vygenerovat Token API**.

    ![Figma vytvořit Token](media/Figma-provisioning-tutorial/token.png)

5. Kopírovat **API Token** hodnotu. Tuto hodnotu zadá do **tajný klíč tokenu** pole na kartě zřizování Figma aplikace na webu Azure Portal. 

    ![Figma vytvořit Token](media/Figma-provisioning-tutorial/figma04.png)

## <a name="add-figma-from-the-gallery"></a>Přidání Figma z Galerie

Ke konfiguraci Figma pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Figma z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

1. V  **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Figma**vyberte **Figma** panel výsledků a pak klikněte na **přidat** tlačítko pro přidání aplikace.

    ![Figma v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Konfigurace automatické zřizování uživatelů pro Figma 

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v Figma podle přiřazení uživatele a/nebo skupiny ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro Figma, postupujte podle pokynů uvedených v [Figma Single sign-on kurzu](figma-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro Figma ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Figma**.

    ![Odkaz Figma v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování](common/provisioning-automatic.png)

5. V části **přihlašovacích údajů správce** části zadejte `https://www.figma.com/scim/v2/<TenantID>` v **adresy URL Tenanta** kde **TenantID** je hodnota, kterou jste získali dříve z Figma. Vstup **API Token** hodnota v **tajný klíč tokenu**. Klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k Figma. Pokud se nepovede, ujistěte se, že váš účet Figma má oprávnění správce a zkuste to znovu.

    ![Adresa URL tenanta + Token](common/provisioning-testconnection-tenanturltoken.png)

8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![E-mailové oznámení](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům Figma**.

    ![Mapování uživatelů Figma](media/Figma-provisioning-tutorial/figma05.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Figma v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Figma pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Atributy uživatele Figma](media/Figma-provisioning-tutorial/figma06.png)

12. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Služba pro Figma zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Stav zřizování zapnutém](common/provisioning-toggle-on.png)

14. Definovat uživatele a/nebo skupiny, které chcete k poskytování Figma výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Zřizování oboru](common/provisioning-scope.png)

15. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Ukládá se konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba na Figma zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)
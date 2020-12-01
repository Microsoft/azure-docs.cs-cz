---
title: 'Kurz: Konfigurace Soloinsight-CloudGate jednotného přihlašování pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Soloinsight-CloudGate jednotné přihlašování.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 5c42102c0704d7212411d6b86f4210ad8ecd885c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96347493"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Kurz: Konfigurace Soloinsight-CloudGate jednotného přihlašování pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést v Soloinsight-CloudGate SSO a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD tak, aby automaticky zřídily a zrušily zřizování uživatelů a/nebo skupin, aby Soloinsight-CloudGate jednotné přihlašování.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Soloinsight-CloudGate tenant jednotného přihlašování](https://www.soloinsight.com/)
* Uživatelský účet v Soloinsight-CloudGate jednotné přihlašování s oprávněními správce.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Přiřazování uživatelů k Soloinsight-CloudGate jednotného přihlašování

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Soloinsight-CloudGate jednotné přihlašování. Jakmile se rozhodnete, můžete těmto uživatelům nebo skupinám přiřadit Soloinsight-CloudGate jednotného přihlašování podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Důležité tipy pro přiřazování uživatelů k Soloinsight-CloudGate jednotného přihlašování

* Doporučujeme, aby se k Soloinsight-CloudGate jednotného přihlašování (SSO) pro testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele k Soloinsight-CloudGate jednotné přihlašování, musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Nastavení Soloinsight-CloudGate jednotného přihlašování pro zřizování

1. Přihlaste se ke [konzole správce jednotného přihlašování Soloinsight-CloudGate](https://soloinsight.sigateway.com/login). Přejděte na **Správa nastavení > systém**.

    ![Soloinsight-CloudGate konzolu správce jednotného přihlašování](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Přejděte na **Obecné**.

    ![Soloinsight-CloudGate přihlašování SSO Add SCIM](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Posuňte se dolů na konec stránky a získejte **adresu URL tenanta** a **tajný token**. Zkopírujte **tajný token**. Tato hodnota se zadá do pole token tajného klíče na kartě zřizování vaší aplikace Soloinsight-CloudGate jednotného přihlašování v Azure Portal.

    ![Token pro vytváření jednotného přihlašování Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Přidání Soloinsight-CloudGate SSO z Galerie

Než nakonfigurujete Soloinsight-CloudGate jednotné přihlašování pro Automatické zřizování uživatelů pomocí Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Soloinsight-CloudGate SSO z Galerie aplikací Azure AD.

**Pokud chcete přidat Soloinsight-CloudGate SSO z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Soloinsight-CLOUDGATE SSO**, v panelu výsledků vyberte **SOLOINSIGHT-CloudGate SSO** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Soloinsight-CloudGate jednotné přihlašování v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Konfigurace automatického zřizování uživatelů pro Soloinsight-CloudGate jednotného přihlašování 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Soloinsight-CloudGate jednotného přihlašování na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout, že povolíte jednotné přihlašování založené na SAML pro Soloinsight-CloudGate SSO, a to podle pokynů uvedených v [kurzu jednotného přihlašování Soloinsight-CLOUDGATE SSO](./soloinsight-cloudgate-sso-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce vzájemně přidávají.

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Soloinsight-CloudGate jednotného přihlašování v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Soloinsight-CLOUDGATE SSO**.

    ![Odkaz na Soloinsight-CloudGate jednotného přihlašování v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://sigateway.com/scim/v2/sync/serviceproviderconfig` **adresu URL tenanta**. Zadejte hodnotu **SCIM tokenu ověřování** získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Soloinsight-CloudGate jednotné přihlašování. Pokud se připojení nepovede, ujistěte se, že váš účet Soloinsight-CloudGate SSO má oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatelů a Soloinsight-CloudGate jednotné přihlašování**.

    ![Soloinsight-CloudGate mapování uživatelů jednotného přihlašování](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatelů synchronizované z Azure AD a Soloinsight-CloudGate jednotné přihlašování v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Soloinsight-CloudGate jednotné přihlašování pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Soloinsight-CloudGate atributy uživatele jednotného přihlašování](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny a Soloinsight-CloudGate jednotné přihlašování**.

    ![Soloinsight-CloudGate mapování skupin SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny synchronizované z Azure AD a Soloinsight-CloudGate jednotné přihlašování v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v Soloinsight-CloudGate jednotné přihlašování pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Soloinsight-CloudGate atributy skupiny SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro Soloinsight-CloudGate jednotné přihlašování, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Určete uživatele nebo skupiny, které chcete zřídit pro Soloinsight-CloudGate jednotné přihlašování, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD při Soloinsight-CloudGate jednotné přihlašování.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
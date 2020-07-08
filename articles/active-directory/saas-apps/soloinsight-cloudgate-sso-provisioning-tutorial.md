---
title: 'Kurz: Konfigurace jednotného přihlašování Soloinsight-CloudGate pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se, jak nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Soloinsight jednotné přihlašování CloudGate.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 07558ceb-d406-40e7-90b8-1b40fdc829e7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 6ab90a6aea262d5c7067f9f41b9ddfc090b7371d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77063190"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Kurz: Konfigurace jednotného přihlašování Soloinsight-CloudGate pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Soloinsight-CloudGate SSO a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD tak, aby automaticky zřídily a zrušily zřizování uživatelů a skupin pro Soloinsight-CloudGate SSO.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant Soloinsight-CloudGate SSO](https://www.soloinsight.com/)
* Uživatelský účet v rámci jednotného přihlašování Soloinsight-CloudGate s oprávněními správce.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Přiřazování uživatelů k Soloinsight jednotné přihlašování CloudGate

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k jednotnému přihlašování Soloinsight-CloudGate. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Soloinsight jednotné přihlašování podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Důležité tipy pro přiřazování uživatelů k Soloinsight jednotné přihlašování CloudGate

* Doporučuje se, aby jeden uživatel Azure AD byl přiřazený k Soloinsight jednotnému přihlašování (SSO) CloudGate pro testování konfigurace automatického zřizování uživatelů. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele k Soloinsight jednotné přihlašování, musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Nastavení jednotného přihlašování Soloinsight-CloudGate pro zřizování

1. Přihlaste se ke [konzole správce jednotného přihlašování Soloinsight-CloudGate](https://soloinsight.sigateway.com/login). Přejděte na **Správa nastavení > systém**.

    ![Soloinsight-CloudGate – Konzola pro správu jednotného přihlašování](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Přejděte na **Obecné**.

    ![Soloinsight-CloudGate SSO Add SCIM](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Posuňte se dolů na konec stránky a získejte **adresu URL tenanta** a **tajný token**. Zkopírujte **tajný token**. Tato hodnota se zadá do pole token tajného klíče na kartě zřizování vaší aplikace Soloinsight-CloudGate SSO ve Azure Portal.

    ![Soloinsight – vytvoření tokenu jednotného přihlašování CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Přidání jednotného přihlašování Soloinsight-CloudGate z Galerie

Před konfigurací jednotného přihlašování (Soloinsight-CloudGate SSO) pro Automatické zřizování uživatelů pomocí Azure AD je nutné přidat do svého seznamu spravovaných aplikací pro Soloinsight-CloudGate jednotné přihlašování z Galerie aplikací Azure AD.

**Pokud chcete přidat jednotné přihlašování Soloinsight-CloudGate z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Soloinsight-CLOUDGATE SSO**, v panelu výsledků vyberte **SOLOINSIGHT-CloudGate SSO** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Soloinsight-CloudGate jednotného přihlašování v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Konfigurace automatického zřizování uživatelů pro jednotné přihlašování Soloinsight-CloudGate 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v rámci Soloinsight jednotného přihlašování (SSO) na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro jednotné přihlašování Soloinsight-CloudGate, a to podle pokynů uvedených v [kurzu Soloinsight-CLOUDGATE jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce vzájemně přidávají.

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro jednotné přihlašování Soloinsight-CloudGate ve službě Azure AD:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Soloinsight-CLOUDGATE SSO**.

    ![Odkaz na jednotné přihlašování Soloinsight-CloudGate v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://sigateway.com/scim/v2/sync/serviceproviderconfig` **adresu URL tenanta**. Zadejte hodnotu **SCIM tokenu ověřování** získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Soloinsight-CloudGate jednotného přihlašování. Pokud se připojení nepovede, ujistěte se, že váš účet Soloinsight-CloudGate SSO má oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele do Soloinsight-CloudGate jednotného přihlašování**.

    ![Mapování uživatelů jednotného přihlašování Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD až Soloinsight SSO-CloudGate SSO v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v rámci jednotného přihlašování Soloinsight-CloudGate pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele jednotného přihlašování Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do Soloinsight-CloudGate jednotného přihlašování**.

    ![Mapování skupin SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny, které se synchronizují z Azure AD až do Soloinsight jednotného přihlašování v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v jednotném přihlašování Soloinsight-CloudGate pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Soloinsight-CloudGate atributy skupiny SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro jednotné přihlašování Soloinsight-CloudGate, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro jednotné přihlašování Soloinsight-CloudGate, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Soloinsight (SSO) CloudGate SSO.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)


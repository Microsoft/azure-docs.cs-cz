---
title: 'Kurz: Konfigurace automatického zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro Automatické zřizování a rušení uživatelských účtů, které se budou využívat.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 34d05d6392e00757bf1e5562ffd8341ad04cc9dc
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807755"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Kurz: Konfigurace funkce pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v systému a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů a skupin.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Klient přestroje](https://harness.io/pricing/)
* Uživatelský účet ve více kabelech s oprávněními správce.

## <a name="assigning-users-to-harness"></a>Přiřazování uživatelů ke kabelům

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup ke svým kabelům. Jakmile se rozhodnete, můžete tyto uživatele a skupiny přiřadit podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-harness"></a>Důležité tipy pro přiřazování uživatelů ke kabelům

* Doporučuje se, aby se k otestování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele ke svému používání, musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-harness-for-provisioning"></a>Nastavení rozpracovaného svazku pro zřizování

1. Přihlaste se ke [konzole správce](https://app.harness.io/#/login)aplikace. Přejděte k **průběžnému zabezpečení > správě přístupu**.

    ![Konzola správce ve více kabelech](media/harness-provisioning-tutorial/admin.png)

2.  Klikněte na **klíče rozhraní API**.

    ![Přidat SCIM](media/harness-provisioning-tutorial/apikeys.png)

3. Klikněte na **Přidat nový klíč**. V dialogovém okně **Přidat klíč rozhraní API** zadejte **název** a vyberte možnost z **oprávnění zděděných z** rozevírací nabídky. Klikněte na tlačítko **Odeslat** .

    ![Přidat nový klíč ze svazku](media/harness-provisioning-tutorial/addkey.png)

    ![Dialogové okno Přidat nový klíč](media/harness-provisioning-tutorial/title.png)

3.  Zkopírujte **klíč**. Tato hodnota se zadá do pole token tajného kódu na kartě zřizování vaší aplikace v Azure Portal.

    ![Vytvoření tokenu pro vytvoření svazku](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Přidání svazku z Galerie

Než začnete konfigurovat funkci pro Automatické zřizování uživatelů pomocí Azure AD, musíte do svého seznamu spravovaných aplikací pro SaaS přidat z Galerie aplikací službu Azure AD kabel.

**Pokud chcete přidat kabel z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **svazek** **, na panelu výsledků vyberte možnost** Geta potom kliknutím na tlačítko **Přidat** aplikaci přidejte.

    ![Využití svazku v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-harness"></a>Konfigurace automatického zřizování uživatelů pro využívání 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů a/nebo skupin na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML na základě pokynů uvedených v [kurzu jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

> [!NOTE]
> Další informace o "SCIM koncového bodu" najdete v [tomto](https://docs.harness.io/article/smloyragsm-api-keys) tématu.

### <a name="to-configure-automatic-user-provisioning-for-harness-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro funkci ve službě Azure AD:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **převyužívat**.

    ![Odkaz na svazek v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw` na **adrese URL tenanta**. Zadejte hodnotu **SCIM tokenu ověřování** získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k tomuto svazku. Pokud se připojení nepovede, zajistěte, aby měl váš účet správce oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele**.

    ![Mapování uživatelských mapování](media/harness-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizované z Azure AD, a využijte v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v nástroji pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Uživatelské atributy](media/harness-provisioning-tutorial/userattributes.png)

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny a využijte**.

    ![Mapování skupin zakabelů](media/harness-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD, a využijte v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin ve svazku pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy skupiny kabelů](media/harness-provisioning-tutorial/groupattributes.png)

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro svazek, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

14. Určete uživatele nebo skupiny, které chcete zřídit pro rozšíření, výběrem požadovaných hodnot v **rozsahu** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na platformě.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)

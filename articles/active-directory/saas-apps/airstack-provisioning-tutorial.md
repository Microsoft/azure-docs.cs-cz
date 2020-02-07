---
title: 'Kurz: Konfigurace služby Stack pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů do zásobníku.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 94738612-b7ab-48c5-a3a9-2c019281aba1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: Zhchia
ms.openlocfilehash: 17736f5215c4ed80a2140cfc664ef76b1a055f79
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060384"
---
# <a name="tutorial-configure-airstack-for-automatic-user-provisioning"></a>Kurz: Konfigurace služby Stack pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést v rámci služby Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD tak, aby automaticky zřídily a zrušily zřizování uživatelů a/nebo skupin do zásobníku.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant služby ve frontě](https://airstack.com/pricing/)
* Uživatelský účet ve službě Prostack s oprávněními správce.

## <a name="assigning-users-to-airstack"></a>Přiřazování uživatelů ke front-Stack

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup ke službě do zásobníku. Po rozhodnutí můžete tyto uživatele a skupiny přiřadit do zásobníku podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-airstack"></a>Důležité tipy pro přiřazení uživatelů ke front-Stack

* Doporučuje se, aby se k testování konfigurace automatického zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele ke službě průchozího zásobníku musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (je-li k dispozici). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-airstack-for-provisioning"></a>Nastavení zásobníku pro zřizování

1. Přihlaste se ke [konzole Správce služby Stack](https://airstack-qa.lenovosoftware.com/). Přejděte na **Nastavení**.

    ![Konzola správce front-Stack](media/airstack-provisioning-tutorial/airstackadminmenu.png)

2.  V nabídce, která se nachází na levé straně obrazovky, přejděte do **konfigurace Azure** .

    ![Přidání SCIM do zásobníku](media/airstack-provisioning-tutorial/azureconfig.png)

3.  Klikněte na tlačítko **Generovat** . Zkopírujte **tajný token pro Azure**. Tato hodnota se zadá do pole token tajného klíče na kartě zřizování vaší aplikace v rámci služby ve frontě Azure Portal.

    ![Token pro vytvoření zásobníku](media/airstack-provisioning-tutorial/generatetoken.png)

## <a name="add-airstack-from-the-gallery"></a>Přidání zásobníku z Galerie

Před konfigurací služby vzdálení pro Automatické zřizování uživatelů pomocí Azure AD je nutné přidat do svého seznamu spravovaných aplikací pro SaaS službu mikrozásobníků z Galerie aplikací Azure AD.

**Pokud chcete přidat do zásobníku z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **front-Stack**, v panelu výsledků vyberte možnost **kostack** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Zásobník v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-airstack"></a>Konfigurace automatického zřizování uživatelů do zásobníku 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin ve službě vzdálení na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro front-in podle pokynů uvedených v [kurzu jednotného přihlašování ve službě Stack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce vzájemně přidávají.

### <a name="to-configure-automatic-user-provisioning-for-airstack-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro front-Stack v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **kostack**.

    ![Odkaz na front-Stack v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://api-as.lenovosoftware.com/0/as/common/scim` na **adrese URL tenanta**. Zadejte hodnotu **SCIM tokenu ověřování** získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit ke službě Stack. Pokud se připojení nepovede, ujistěte se, že váš účet služby Stack má oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele do zásobníku**.

    ![Mapování uživatelů v zásobníku](media/airstack-provisioning-tutorial/mappings.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do služby Stack v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v rámci služby ve službě Stack pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Uživatelské atributy zásobníku](media/airstack-provisioning-tutorial/attributes.png)

10. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro front-Stack, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit ve službě Stack, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Část **Podrobnosti o synchronizaci** můžete použít ke sledování průběhu a následného odkazu na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování služby Azure AD v zásobníku.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)


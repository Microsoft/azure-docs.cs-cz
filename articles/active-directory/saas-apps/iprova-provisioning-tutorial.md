---
title: 'Kurz: Konfigurace iProva pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro iProva.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 4edba747-242d-4795-9539-649f33af4c13
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 320a3178448da93a04a332183ad5d34453f702ef
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897114"
---
# <a name="tutorial-configure-iprova-for-automatic-user-provisioning"></a>Kurz: Konfigurace iProva pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v iProva a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro iProva.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant iProva](https://www.iProva.com/)
* Uživatelský účet v iProva s oprávněními správce.

## <a name="assigning-users-to-iprova"></a>Přiřazování uživatelů k iProva

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k iProva. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k iProva podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-iprova"></a>Důležité tipy pro přiřazení uživatelů k iProva

* Doporučuje se, aby se k iProva k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k iProva musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-iprova-for-provisioning"></a>Nastavení iProva pro zřizování

1. Přihlaste se ke [konzole pro správu iProva](https://www.iProva.com/). Přejděte na **> Správa aplikací**.

    ![Konzola pro správu iProva](media/iprova-provisioning-tutorial/admin.png)

2.  Klikněte na **Správa externích uživatelů**.

    ![iProva přidat SCIM](media/iprova-provisioning-tutorial/external.png)

3. Chcete-li přidat nového poskytovatele, klikněte na ikonu se **symbolem plus** . V dialogovém okně Nový **poskytovatel přidat poskytovatele** zadejte **název**. Můžete přidat **omezení přístupu na základě IP adresy**. Klikněte na tlačítko **OK** .

    ![iProva přidat nový](media/iprova-provisioning-tutorial/add.png)

    ![iProva přidat poskytovatele](media/iprova-provisioning-tutorial/addprovider.png)

4.  Klikněte na tlačítko **trvalého tokenu** . Zkopírování **trvalého tokenu** a jeho uložení, jako by to byla jediná doba, kterou si můžete zobrazit. Tato hodnota se zadá do pole token tajného kódu na kartě zřizování vaší aplikace iProva ve Azure Portal.

    ![iProva vytvořit token](media/iprova-provisioning-tutorial/token.png)

## <a name="add-iprova-from-the-gallery"></a>Přidání iProva z Galerie

Před konfigurací iProva pro Automatické zřizování uživatelů se službou Azure AD je nutné přidat iProva z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat iProva z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **iProva**, na panelu výsledků vyberte **iProva** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![iProva v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-iprova"></a>Konfigurace automatického zřizování uživatelů na iProva 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v iProva na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro iProva podle pokynů uvedených v [kurzu IProva jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/iProva-tutorial). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

### <a name="to-configure-automatic-user-provisioning-for-iprova-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro iProva ve službě Azure AD:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **iProva**.

    ![Odkaz iProva v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://identitymanagement.services.iProva.nl/scim` na **adrese URL tenanta**. Zadejte hodnotu **trvalého tokenu** , která byla dříve načtena v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k iProva. Pokud se připojení nepovede, ujistěte se, že má váš účet iProva oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé iProva**.

    ![Mapování uživatelů iProva](media/iprova-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do iProva v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v iProva pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele iProva](media/iprova-provisioning-tutorial/userattributes.png)

10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do iProva**.

    ![Mapování skupin iProva](media/iprova-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do iProva v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v iProva pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![iProva – atributy skupiny](media/iprova-provisioning-tutorial/groupattributes.png)

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro iProva, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro iProva, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v iProva.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)


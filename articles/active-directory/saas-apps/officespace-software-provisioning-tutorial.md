---
title: 'Kurz: Konfigurace OfficeSpace softwaru pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů k OfficeSpace softwaru.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f832a0a6-ad0a-453f-a747-9cd717e11181
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: Zhchia
ms.openlocfilehash: 74135ccfe3bf8eb9625a8970a27b11c2a0e7b992
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134902"
---
# <a name="tutorial-configure-officespace-software-for-automatic-user-provisioning"></a>Kurz: Konfigurace OfficeSpace softwaru pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v OfficeSpace softwaru a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů a skupin pro OfficeSpace Software.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant OfficeSpace Software](https://www.officespacesoftware.com/)
* Uživatelský účet v softwaru OfficeSpace s oprávněními správce.

## <a name="assigning-users-to-officespace-software"></a>Přiřazení uživatelů k OfficeSpace softwaru

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k OfficeSpace softwaru. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k OfficeSpace softwaru podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-officespace-software"></a>Důležité tipy pro přiřazení uživatelů k OfficeSpace softwaru

* Doporučuje se, aby se k OfficeSpace softwaru, který testuje automatickou konfiguraci zřizování uživatelů, přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele k OfficeSpace softwaru, musíte vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná) v dialogovém okně přiřazení. Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-officespace-software-for-provisioning"></a>Nastavení OfficeSpace softwaru pro zřizování

1. Přihlaste se ke [konzole správce softwaru OfficeSpace](https://support.officespacesoftware.com/hc). Přejděte do **nastavení > konektory**.

    ![Konzola správce softwaru OfficeSpace](media/officespace-software-provisioning-tutorial/settings.png)

2.  Přejděte do **složky synchronizace adresářů > SCIM**.

    ![OfficeSpace Software Add SCIM](media/officespace-software-provisioning-tutorial/scim.png)

3.  Zkopírujte **token ověřování SCIM**. Tato hodnota se zadá do pole token tajného kódu na kartě zřizování OfficeSpace softwarové aplikace v Azure Portal.

    ![Token pro vytváření OfficeSpace softwaru](media/officespace-software-provisioning-tutorial/token.png)

## <a name="add-officespace-software-from-the-gallery"></a>Přidání OfficeSpace softwaru z Galerie

Před konfigurací OfficeSpace softwaru pro Automatické zřizování uživatelů se službou Azure AD je nutné přidat OfficeSpace Software z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat OfficeSpace Software z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **OfficeSpace Software**, na panelu výsledků vyberte **OfficeSpace Software** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![OfficeSpace Software v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-officespace-software"></a>Konfigurace automatického zřizování uživatelů pro OfficeSpace Software 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v OfficeSpace softwaru na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro software OfficeSpace podle pokynů uvedených v [kurzu OfficeSpace Software Single Signing](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-tutorial). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

### <a name="to-configure-automatic-user-provisioning-for-officespace-software-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro OfficeSpace Software v Azure AD:

1. Přihlásit se na [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **OfficeSpace Software**.

    ![Odkaz na software OfficeSpace v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte ve formátu **adresy url klienta**vstupní `https://<subdomain>.officespacesoftware.com/api/scim/v2/`. Například `https://contoso.officespacesoftware.com/api/scim/v2/`. Zadejte hodnotu **SCIM tokenu ověřování** získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k OfficeSpace softwaru. Pokud se připojení nepovede, ujistěte se, že váš účet OfficeSpace softwaru má oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na možnost **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé OfficeSpace softwaru**.

    ![Mapování uživatelů OfficeSpace softwaru](media/officespace-software-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD a OfficeSpace Software v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v OfficeSpace softwaru pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele OfficeSpace Software](media/officespace-software-provisioning-tutorial/userattributes.png)

11. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Pokud chcete povolit službu Azure AD Provisioning pro software OfficeSpace, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit pro OfficeSpace Software, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

    ![Rozsah zřizování](common/provisioning-scope.png)

14. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Část **Podrobnosti o synchronizaci** můžete použít ke sledování průběhu a následnému odkazu na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na OfficeSpace softwaru.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)


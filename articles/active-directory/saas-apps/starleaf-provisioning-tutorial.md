---
title: 'Kurz: Konfigurace StarLeaf pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro StarLeaf.
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
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 0676eb2830c2e0233eb182cfec0be3f39c6a39e9
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737742"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Kurz: Konfigurace StarLeaf pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v StarLeaf a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro StarLeaf.

> [!NOTE]
>  Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je momentálně ve verzi Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant StarLeaf](https://www.starleaf.com/solutions/)
* Uživatelský účet v StarLeaf s oprávněními správce.

## <a name="assign-users-to-starleaf"></a>Přiřazení uživatelů k StarLeaf
Azure Active Directory používá koncept nazvaný přiřazení k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, měli byste se rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k StarLeaf. Pak můžete přiřadit uživatele a skupiny k StarLeaf pomocí následujících [pokynů](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Důležité tipy pro přiřazení uživatelů k StarLeaf

* Doporučuje se, abyste k testování automatické konfigurace zřizování uživatelů přiřadili jednoho uživatele Azure AD StarLeaf. Další uživatele a skupiny se dají přiřadit později.

* Když přiřadíte uživatele k StarLeaf, musíte vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná) v dialogovém okně přiřazení. Uživatelé s výchozí rolí přístupu se z zřizování vylučují.

## <a name="set-up-starleaf-for-provisioning"></a>Nastavení StarLeaf pro zřizování

Před konfigurací StarLeaf pro Automatické zřizování uživatelů pomocí Azure AD budete muset nakonfigurovat zřizování SCIM v StarLeaf:

1. Přihlaste se ke [konzole pro správu StarLeaf](https://portal.starleaf.com/#page=login). Přejděte k **integraci** > **Přidat integraci**.

    ![StarLeaf přidat SCIM](media/starleaf-provisioning-tutorial/image00.png)

2. Vyberte **typ** , který se má Microsoft Azure Active Directory. Do **názvu**zadejte vhodný název. Klikněte na tlačítko **Použít**.

    ![StarLeaf přidat SCIM](media/starleaf-provisioning-tutorial/image01.png)

3.  Pak se zobrazí **základní adresa URL SCIM** a hodnoty **přístupového tokenu** . Tyto hodnoty se zadají do polí **Adresa URL tenanta** a **tajného tokenu** na kartě zřizování vaší aplikace StarLeaf ve Azure Portal. 

    ![StarLeaf vytvořit token](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>Přidání StarLeaf z Galerie

Pokud chcete nakonfigurovat StarLeaf pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat StarLeaf z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat StarLeaf z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **StarLeaf**, na panelu výsledků vyberte **StarLeaf** .
    ![StarLeaf v seznamu výsledků](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Konfigurace automatického zřizování uživatelů na StarLeaf

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v StarLeaf na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **StarLeaf**.

    ![Odkaz StarLeaf v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části přihlašovací údaje správce zadejte **základní adresu URL SCIM** a hodnoty **přístupového tokenu** , které jste dříve načetli v **adrese URL tenanta** a v uvedeném **tajném tokenu** . Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k StarLeaf. Pokud se připojení nepovede, ujistěte se, že má váš účet StarLeaf oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování a zaškrtněte pole **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé StarLeaf**.

    ![StarLeaf vytvořit token](media/starleaf-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do StarLeaf v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v StarLeaf pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![StarLeaf vytvořit token](media/starleaf-provisioning-tutorial/userattribute.png)


10. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).


11. Pokud chcete povolit službu Azure AD Provisioning pro StarLeaf, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro StarLeaf, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v StarLeaf.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md) .

## <a name="connector-limitations"></a>Omezení konektoru

* StarLeaf v současné době nepodporuje zřizování skupin. 
* StarLeaf vyžaduje, aby hodnoty **e-mailu** a **uživatelského jména** měly stejnou zdrojovou hodnotu.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [zkontrolovat protokoly a získat sestavy o aktivitě zřizování](../manage-apps/check-status-user-account-provisioning.md).

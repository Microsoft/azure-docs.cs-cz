---
title: 'Kurz: Konfigurace Insight4GRC pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Insight4GRC.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 34718201-4f0e-4260-9af0-b3b70a1e8265
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 7e8c6c2277f29fc114033aac24844e9e85816b78
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951033"
---
# <a name="tutorial-configure-insight4grc--for-automatic-user-provisioning"></a>Kurz: Konfigurace Insight4GRC pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Insight4GRC a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro Insight4GRC.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant Insight4GRC](https://www.rsmuk.com/)
* Uživatelský účet v Insight4GRC s oprávněními správce.

## <a name="assigning-users-to-insight4grc"></a>Přiřazování uživatelů k Insight4GRC 

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Insight4GRC. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Insight4GRC podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-insight4grc"></a>Důležité tipy pro přiřazení uživatelů k Insight4GRC 

* Doporučuje se, aby se k Insight4GRC k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Insight4GRC musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-insight4grc-for-provisioning"></a>Nastavení Insight4GRC pro zřizování

Před konfigurací Insight4GRC pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na Insight4GRC.

1. Pokud chcete získat nosný token, musí koncový zákazník kontaktovat [tým podpory](mailto:support.ss@rsmuk.com) a poskytnou mu token nosiče pro zákazníky.

2. Pokud chcete získat adresu URL koncového bodu SCIM, budete muset mít připravený název domény Insight4GRC, protože se bude používat k vytvoření adresy URL koncového bodu SCIM. Název domény Insight4GRC můžete načíst jako součást počátečního nákupu softwaru pomocí Insight4GRC.


## <a name="add-insight4grc--from-the-gallery"></a>Přidání Insight4GRC z Galerie

Pokud chcete nakonfigurovat Insight4GRC pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat Insight4GRC z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Insight4GRC z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Insight4GRC**, na panelu výsledků vyberte **Insight4GRC** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Insight4GRC v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-insight4grc"></a>Konfigurace automatického zřizování uživatelů na Insight4GRC  

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Insight4GRC na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Insight4GRC podle pokynů uvedených v [kurzu Insight4GRC – jednotné přihlašování](insight4grc-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-insight4grc--in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Insight4GRC ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Insight4GRC**.

    ![Odkaz Insight4GRC v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5.  V části přihlašovací údaje správce zadejte `https://{Insight4GRC Domain Name}.insight4grc.com/public/api/scim/v2` **adresu URL tenanta** pomocí hodnoty {Insight4GRC Domain Name}, která byla dříve načtena. Zadejte **hodnotu tokenu** načtenou dříve do **tajného tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Insight4GRC. Pokud se připojení nepovede, ujistěte se, že má váš účet Insight4GRC oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Insight4GRC**.

    ![Insight4GRC – mapování uživatelů](media/insight4grc-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Insight4GRC v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Insight4GRC pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Insight4GRC – mapování uživatelů](media/insight4grc-provisioning-tutorial/userattribute.png)

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupinu do Insight4GRC**

    ![Insight4GRC – mapování skupin](media/insight4grc-provisioning-tutorial/groupmapping.png)

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do Insight4GRC v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupinových účtů v Insight4GRC pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Insight4GRC – mapování skupin](media/insight4grc-provisioning-tutorial/groupattribute.png)

10. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro Insight4GRC, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro Insight4GRC, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Insight4GRC.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* Přečtěte si, [Jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování](../manage-apps/check-status-user-account-provisioning.md).
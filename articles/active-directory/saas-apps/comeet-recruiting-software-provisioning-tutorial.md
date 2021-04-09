---
title: 'Kurz: Konfigurace předvyhovujícího náborového softwaru pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se, jak nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro spoluuspokojení náborového softwaru.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 288d1e6cec8ddcf7d4afe5a35f28a022c2a7be10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006708"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Kurz: Konfigurace předvyhovujícího náborového softwaru pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v rámci předplatného náborového softwaru a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů a skupin, aby splnily náborový software.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Nevyhovující tenant náborového softwaru](https://www.comeet.co/)
* Uživatelský účet v rámci vyhovujícího náborového softwaru s oprávněními správce.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Přidání kovyhovujícího náborového softwaru z Galerie

Než nakonfigurujete vyhovující náborový software pro Automatické zřizování uživatelů se službou Azure AD, musíte do svého seznamu spravovaných aplikací pro SaaS přidat do svého seznamu spravovaných aplikací pro příjem softwaru z Galerie aplikací Azure AD.

**Pokud chcete přidat vyhovující náborový software z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte předaný **náborový software**, na panelu výsledků vyberte možnost **přeplnit náborový software** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Vyhovující náborový software v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Přiřazování uživatelů k předplatnému náborového softwaru

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, měli byste se rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k prohlášenému náborovému softwaru. Jakmile se rozhodnete, můžete těmto uživatelům nebo skupinám přiřadit tyto uživatele, aby splnili náborový software, a to podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Důležité tipy pro přiřazování uživatelů k předplatnému náborového softwaru

* Doporučuje se, aby jeden uživatel Azure AD byl přiřazený k tomu, aby splnil náborový software, aby otestoval automatickou konfiguraci zřizování uživatelů. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k souběžnému náborovém softwaru musíte vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná) v dialogovém okně přiřazení. Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Konfigurace automatického zřizování uživatelů pro vyhovující náborový software 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v rámci vyhovujícího náborového softwaru na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro dopředný náborový software, a to podle pokynů uvedených v [kurzu kovyhovujícího náborového softwaru pro jednotné přihlašování](comeetrecruitingsoftware-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro vyhovující náborový software v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **dodržet náborový software**.

    ![Odkaz na vyhovující náborový software v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta** a **tajný token** vašeho účtu náborového softwaru, jak je popsáno v kroku 6.

6. V konzole pro správu nástroje pro naplnění se [doplněním](https://app.comeet.co/)Nástroje přejděte do pole  **> nastavení > ověřování > Microsoft Azure** a zkopírujte **tajný token pro hodnotu vaší společnosti** do pole **tajného tokenu** v Azure AD.

    ![Vyhovující zřizování náborového softwaru](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. Po vyplnění polí zobrazených v kroku 5 klikněte na **Test připojení** . tím zajistíte, aby se služba Azure AD mohla připojit k prohlášenému náborovému softwaru. Pokud se připojení nepovede, zajistěte, aby váš náborový účet pro nábor softwaru měl oprávnění správce, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-token.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatelů, aby splňovaly** podmínky.

    ![Doplnění uživatelských mapování na nábor softwaru](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Zkontrolujte atributy uživatelů synchronizované z Azure AD a Projděte si náborový software v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v rámci vyhovujícího náborového softwaru pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Vyhovuje v sevýšení náborových atributů skupin softwaru](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro spolusplňujecí náborový software, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro provedený náborový software, a to tak, že v části **Nastavení** vyberete požadované hodnoty v poli **Rozsah** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na základě průběžného náborového softwaru.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Prohlášený náborový software v současné době nepodporuje skupiny.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).


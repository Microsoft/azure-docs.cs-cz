---
title: 'Kurz: Konfigurace Zscaler Private Access (ZPA) pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se, jak nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Zscaler privátního přístupu (ZPA).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 5ecb4d249c8c2167ec61740eb9515fd1937230b0
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519737"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Kurz: Konfigurace Zscaler Private Access (ZPA) pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Zscaler Private Access (ZPA) a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD tak, aby automaticky zřídily a zrušily zřizování uživatelů a/nebo skupin pro Zscaler privátní přístup (ZPA).

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant Zscaler Private Access (ZPA)](https://www.zscaler.com/pricing-and-plans#contact-us)
* Uživatelský účet v Zscaler Private Access (ZPA) s oprávněními správce.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Přiřazení uživatelů k privátnímu přístupu Zscaler (ZPA)

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k privátnímu přístupu Zscaler (ZPA). Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Zscaler privátního přístupu (ZPA) podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Důležité tipy pro přiřazení uživatelů k privátnímu přístupu Zscaler (ZPA)

* Doporučuje se, aby jeden uživatel Azure AD byl přiřazený k Zscaler privátnímu přístupu (ZPA), aby otestoval automatickou konfiguraci zřizování uživatelů. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele k Zscaler privátního přístupu (ZPA), musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Nastavení Zscaler Private Access (ZPA) pro zřizování

1. Přihlaste se ke [konzole pro správu Zscaler Private Access (ZPA)](https://admin.private.zscaler.com/). Přejděte na **správa > konfigurace IDP**.

    ![Konzola pro správu Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Ověřte, jestli je nakonfigurované IdP pro **jednotné přihlašování** . Pokud není nastavené žádné IdP, pak ho přidejte kliknutím na ikonu Plus v pravém horním rohu obrazovky.

    ![Zscaler Private Access (ZPA) přidat SCIM](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Pomocí průvodce **přidáním konfigurace IDP** přidejte IDP. Ponechejte pole **jednotného přihlašování** nastaveno na hodnotu **uživatel**. Zadejte **název** a vyberte **domény** z rozevíracího seznamu. Kliknutím na tlačítko **Další** přejdete k dalšímu oknu.

    ![Zscaler Private Access (ZPA) přidat IdP](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Stáhněte **certifikát poskytovatele služeb**. Kliknutím na tlačítko **Další** přejdete k dalšímu oknu.

    ![Certifikát Zscaler Private Access (ZPA) SP](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. V dalším okně nahrajte **certifikát poskytovatele služeb** , který jste předtím stáhli.

    ![Certifikát pro nahrávání Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Posuňte se dolů a zadejte **adresu URL jednotného přihlašování** a **ID entity IDP**.

    ![ID IdP Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Posuňte se dolů a **Povolte SCIM synchronizaci**. Klikněte na tlačítko **vygenerovat nový token** . Zkopírujte **token nosiče**. Tato hodnota se zadá do pole token tajného klíče na kartě zřizování aplikace Zscaler Private Access (ZPA) v Azure Portal.

    ![Vytvoření tokenu Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Pokud chcete najít **adresu URL tenanta** , přejděte do části **Správa > konfigurace IDP**. Klikněte na název nově přidané konfigurace IdP uvedené na stránce.

    ![Název IDP Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Posuňte se dolů a zobrazte **koncový bod poskytovatele služby SCIM** na konci stránky. Zkopírujte **koncový bod poskytovatele služby SCIM**. Tato hodnota se zadá do pole Adresa URL tenanta na kartě zřizování aplikace Zscaler Private Access (ZPA) v Azure Portal.

    ![Adresa URL SCIM Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Přidání Zscaler Private Access (ZPA) z Galerie

Než nakonfigurujete Zscaler Private Access (ZPA) pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat Zscaler Private Access (ZPA) z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Zscaler Private Access (ZPA) z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscaler Private Access (ZPA)**, vyberte **Zscaler Private Access (ZPA)** na panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Zscaler Private Access (ZPA) v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Konfigurace automatického zřizování uživatelů pro Zscaler Private Access (ZPA) 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Zscaler Private Access (ZPA) na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Zscaler Private Access (ZPA) podle pokynů uvedených v [kurzu pro jednotné přihlašování Zscaler Private Access (ZPA)](./zscalerprivateaccess-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

> [!NOTE]
> Další informace o koncovém bodu SCIM privátního přístupu Zscaler najdete v [tomto](https://www.zscaler.com/partners/microsoft)tématu.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Zscaler Private Access (ZPA) ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Zscaler Private Access (ZPA)**.

    ![Odkaz Zscaler Private Access (ZPA) v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte hodnotu **koncového bodu poskytovatele služby SCIM** dříve získanou v **adrese URL tenanta**. Zadejte hodnotu **tokenu nosiče** , která byla dříve načtena v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se Azure AD může připojit k Zscaler privátnímu přístupu (ZPA). Pokud se připojení nepovede, zajistěte, aby měl účet Zscaler Private Access (ZPA) oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele, aby se Zscaler soukromý přístup (ZPA)**.

    ![Mapování uživatelů Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD a Zscaler Private Access (ZPA) v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Zscaler Private Access (ZPA) pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Uživatelské atributy Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do Zscaler Private Access (ZPA)**.

    ![Mapování skupin Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny synchronizované z Azure AD a Zscaler Private Access (ZPA) v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v Zscaler Private Access (ZPA) pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy skupiny Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro Zscaler Private Access (ZPA), změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které byste chtěli zřídit pro Zscaler Private Access (ZPA), a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Zscaler Private Access (ZPA).

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
---
title: 'Kurz: Konfigurace federovaného adresáře pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů ke federovanému adresáři.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 336c1e78143c09e07b8f05c3dbd10ca647844242
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319941"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Kurz: Konfigurace federovaného adresáře pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v federovaném adresáři a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů a skupin pro federované adresáře.

> [!NOTE]
>  Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Federovaný adresář](https://www.federated.directory/pricing).
* Uživatelský účet ve federovaném adresáři s oprávněními správce.

## <a name="assign-users-to-federated-directory"></a>Přiřadit uživatele ke federovanému adresáři
Azure Active Directory používá koncept nazvaný přiřazení k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup ke federovanému adresáři. Po rozhodnutí můžete přiřadit tyto uživatele a skupiny ke federovanému adresáři podle pokynů uvedených tady:

 * [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Důležité tipy pro přiřazení uživatelů ke federovanému adresáři
 * Doporučujeme, aby se k federovanému adresáři přiřadil jeden uživatel Azure AD, který bude testovat automatickou konfiguraci zřizování uživatelů. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele ke federovanému adresáři musíte vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná) v dialogovém okně přiřazení. Uživatelé s výchozí rolí přístupu se z zřizování vylučují.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Nastavení federovaného adresáře pro zřizování

Před konfigurací federovaného adresáře pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit zřizování SCIM ve federovaném adresáři.

1. Přihlaste se ke [konzole správce federovaného adresáře](https://federated.directory/of) .

    ![Kurz federovaného adresáře](media/federated-directory-provisioning-tutorial/companyname.png)

2. Přejděte do **složky adresáře > uživatelských adresářů** a vyberte svého tenanta. 

    ![federovaný adresář](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Pokud chcete vygenerovat trvalý nosný token, přejděte k **klíčům adresáře > vytvořit nový klíč.** 

    ![federovaný adresář](media/federated-directory-provisioning-tutorial/federated01.png)

4. Vytvořte klíč adresáře. 

    ![federovaný adresář](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Zkopírujte hodnotu **přístupového tokenu** . Tato hodnota se zadá do pole **token tajného klíče** na kartě zřizování aplikace federovaného adresáře v Azure Portal. 

    ![federovaný adresář](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Přidat federovaný adresář z Galerie

Pokud chcete nakonfigurovat federované adresáře pro Automatické zřizování uživatelů pomocí Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat federovaný adresář z Galerie aplikací Azure AD.

**Pokud chcete přidat federovaný adresář z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **federovaný adresář**, na panelu výsledků vyberte **federovaný adresář** .

    ![Federovaný adresář v seznamu výsledků](common/search-new-app.png)

5. V samostatném prohlížeči přejděte na **adresu URL** zvýrazněnou níže. 

    ![federovaný adresář](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Klikněte na **Přihlásit se**.

    ![federovaný adresář](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Jako federovaný adresář je aplikace pro OpenIDConnect, která umožňuje přihlášení ke federovanému adresáři pomocí pracovního účtu Microsoft.
    
    ![federovaný adresář](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Po úspěšném ověření Přijměte výzvu k zadání souhlasu pro stránku souhlasu. Aplikace se pak automaticky přidá do vašeho tenanta a budete přesměrováni na svůj účet federovaného adresáře.

    ![federovaný adresář Add SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Konfigurace automatického zřizování uživatelů pro federovaný adresář 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v federovaném adresáři na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro federovaný adresář v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **federovaný adresář**.

    ![Odkaz federovaného adresáře v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://api.federated.directory/v2/` adresu URL tenanta. Zadejte hodnotu, kterou jste načetli a uložili dříve z federovaného adresáře v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k federovanému adresáři. Pokud se připojení nepovede, ujistěte se, že váš účet federovaného adresáře má oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele do federovaného adresáře**.

    ![Kurz federovaného adresáře](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Zkontrolujte atributy uživatele synchronizované z Azure AD do federovaného adresáře v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v federovaném adresáři pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Kurz federovaného adresáře](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro federované adresáře, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro federované adresáře výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Část **Podrobnosti o synchronizaci** můžete použít ke sledování průběhu a následného odkazu na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD ve federovaném adresáři.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md) .
## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

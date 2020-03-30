---
title: 'Kurz: Konfigurace federovaného adresáře pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřaštila a zřaštila uživatelské účty do federovaného adresáře.
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
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 910aaac84dacb75cd76772a0bc2960d9bfa8bb70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057890"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Kurz: Konfigurace federovaného adresáře pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny ve Federated Directory a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-zřizování uživatelů a/nebo skupin federovaného adresáře.

> [!NOTE]
>  Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Tenanta Azure AD.
* [Federovaný adresář](https://www.federated.directory/pricing).
* Uživatelský účet ve Federovaném adresáři s oprávněními správce.

## <a name="assign-users-to-federated-directory"></a>Přiřazení uživatelů do federovaného adresáře
Azure Active Directory používá koncept s názvem přiřazení k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k federovanému adresáři. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny federovaného adresáře podle pokynů zde:

 * [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Důležité tipy pro přiřazení uživatelů do federovaného adresáře
 * Doporučuje se, aby jeden uživatel Azure AD je přiřazen a Federated Directory otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele do adresáře Federated Directory musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí Výchozí přístup jsou z zřizování vyloučeni.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Nastavení federovaného adresáře pro zřizování

Před konfigurací federovaného adresáře pro automatické zřizování uživatelů pomocí Azure AD budete muset povolit zřizování SCIM ve federovaném adresáři.

1. Přihlaste se do [konzole federovaného adresáře adminconsole](https://federated.directory/of)

    ![Kurz federovaného adresáře](media/federated-directory-provisioning-tutorial/companyname.png)

2. Přejděte do **adresářů > adresáře uživatelů** a vyberte svého klienta. 

    ![federovaný adresář](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Chcete-li vygenerovat trvalý nosný token, přejděte na **klíče adresáře > vytvořit nový klíč.** 

    ![federovaný adresář](media/federated-directory-provisioning-tutorial/federated01.png)

4. Vytvořte klíč adresáře. 

    ![federovaný adresář](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Zkopírujte hodnotu **přístupového tokenu.** Tato hodnota se zadá do pole **Tajný token** na kartě Zřizování aplikace Federated Directory na webu Azure Portal. 

    ![federovaný adresář](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Přidání federovaného adresáře z galerie

Chcete-li nakonfigurovat federovaný adresář pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat federovaný adresář z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat federovaný adresář z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **federovaný adresář**, vpanelu výsledků vyberte **federovaný adresář.**

    ![Federovaný adresář v seznamu výsledků](common/search-new-app.png)

5. Přejděte na **adresu URL** zvýrazněnou níže v samostatném prohlížeči. 

    ![federovaný adresář](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Klepněte na tlačítko **PŘIHLÁSIT**.

    ![federovaný adresář](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Vzhledem k tomu, že Federated Directory je aplikace OpenIDConnect, zvolte přihlášení do federovaného adresáře pomocí pracovního účtu Microsoft.
    
    ![federovaný adresář](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Po úspěšném ověření přijměte výzvu k souhlasu pro stránku souhlasu. Aplikace pak bude automaticky přidána do vašeho tenanta a budete přesměrováni na váš účet Federated Directory.

    ![federovaný adresář Přidat SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Konfigurace automatického zřizování uživatelů do federovaného adresáře 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin ve Federovaném adresáři na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro federativní adresář ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **federovaný adresář**.

    ![Odkaz Federovaný adresář v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Přihlašovací údaje** `https://api.federated.directory/v2/` správce zadejte do adresy URL klienta. Zadejte hodnotu, kterou jste načetli a uložili dříve z federovaného adresáře v **tajném tokenu**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k federovanému adresáři. Pokud se připojení nezdaří, ujistěte se, že váš účet federovaného adresáře má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

8. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s federovaným adresářem**.

    ![Kurz federovaného adresáře](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do federovaného adresáře v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů ve federovaném adresáři pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Kurz federovaného adresáře](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit zřizovací službu Azure AD pro federovaný adresář, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit federovanému adresáři, výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD ve federovaném adresáři.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v [tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

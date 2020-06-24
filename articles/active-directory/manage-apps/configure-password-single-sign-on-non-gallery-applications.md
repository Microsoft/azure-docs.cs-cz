---
title: Jak nakonfigurovat jednotné přihlašování k heslům pro aplikace Azure AD | Microsoft Docs
description: Jak nakonfigurovat jednotné přihlašování (SSO) k podnikovým aplikacím Azure AD v platformě Microsoft Identity Platform (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/10/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 043adc309c3480865eb9aa7a7bff8d35e85bc78a
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763495"
---
# <a name="configure-password-single-sign-on"></a>Konfigurace jednotného přihlašování k heslům

Když [přidáte aplikaci Galerie](add-gallery-app.md) nebo [webovou aplikaci mimo galerii](add-non-gallery-app.md) do podnikových aplikací Azure AD, jedna z dostupných možností jednotného přihlašování je [jednotné přihlašování založené na heslech](what-is-single-sign-on.md#password-based-sso). Tato možnost je k dispozici pro všechny webové stránky s přihlašovací stránkou HTML. Jednotné přihlašování založené na heslech, označované taky jako trezor hesel, umožňuje spravovat přístup uživatelů a hesla k webovým aplikacím, které nepodporují federaci identit. Je to také užitečné ve scénářích, kdy je potřeba, aby několik uživatelů sdílelo jeden účet, například k účtům aplikací sociálních médií vaší organizace. 

Jednotné přihlašování založené na heslech je skvělým způsobem, jak rychle začít integrovat aplikace do služby Azure AD a umožní vám:

-   Povolení **jednotného přihlašování pro uživatele** díky bezpečnému ukládání a přehrávání uživatelských jmen a hesel pro aplikaci, kterou jste integrací se službou Azure AD.

-   **Podpora aplikací, které vyžadují více polí přihlašování** pro aplikace, které pro přihlášení vyžadují víc než jenom uživatelské jméno a heslo

-   **Přizpůsobení popisků** vstupních polí uživatelského jména a hesla, která se uživatelům zobrazí na [přístupovém panelu aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) při zadání přihlašovacích údajů

-   Umožněte **uživatelům** zadání vlastních uživatelských jmen a hesel pro všechny existující účty aplikací, které na [přístupovém panelu aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) zadávají ručně.

-   Umožňuje **členům obchodní skupiny** zadat uživatelská jména a hesla přiřazená uživateli pomocí funkce [Samoobslužný přístup k aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) .

-   Umožňuje **Správci** zadat uživatelské jméno a heslo, které mají jednotlivci nebo skupiny používat při přihlašování k aplikaci pomocí funkce aktualizovat přihlašovací údaje. 

## <a name="before-you-begin"></a>Než začnete

Pokud se aplikace nepřidala do tenanta Azure AD, přečtěte si téma [Přidání aplikace Galerie](add-gallery-app.md) nebo [Přidání aplikace mimo galerii](add-non-gallery-app.md).

## <a name="open-the-app-and-select-password-single-sign-on"></a>Otevřete aplikaci a vyberte možnost jednotné přihlašování pomocí hesla.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce cloudové aplikace nebo správce aplikace pro vašeho TENANTA Azure AD.

2. Přejděte na **Azure Active Directory**  >  **podnikové aplikace**. Zobrazí se náhodná ukázka aplikací v tenantovi Azure AD. 

3. V nabídce **Typ aplikace** vyberte **všechny aplikace**a pak vyberte **použít**.

4. Do vyhledávacího pole zadejte název aplikace a pak vyberte aplikaci z výsledků.

5. V části **Spravovat** vyberte **jednotné přihlašování**. 

6. Vyberte možnost **založené na heslech**.

7. Zadejte adresu URL stránky pro přihlášení na webu aplikace. Tento řetězec musí být stránka, která obsahuje pole pro zadání uživatelského jména.

   ![Jednotné přihlašování založené na heslech](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Vyberte **Uložit**. Služba Azure AD se pokusí analyzovat přihlašovací stránku pro zadání uživatelského jména a zadání hesla. Pokud je pokus úspěšný, jste hotovi. 
 
> [!NOTE]
> Dalším krokem je [přiřazení uživatelů nebo skupin k aplikaci](methods-for-assigning-users-and-groups.md). Po přiřazení uživatelů a skupin můžete zadat přihlašovací údaje, které se budou používat jménem uživatele při přihlášení k aplikaci. Vyberte **Uživatelé a skupiny**, zaškrtněte políčko pro řádek uživatele nebo skupiny a potom klikněte na **Aktualizovat přihlašovací údaje**. Pak zadejte uživatelské jméno a heslo, které chcete použít jménem uživatele nebo skupiny. V opačném případě budou uživatelé vyzváni k zadání přihlašovacích údajů sami při spuštění.
 

## <a name="manual-configuration"></a>Ruční konfigurace

Pokud se pokus o analýzu služby Azure AD nezdaří, můžete nakonfigurovat ruční přihlášení.

1. V části ** \<application name> Konfigurace**vyberte **Konfigurovat \<application name> nastavení jednotného přihlašování pro heslo** . zobrazí se stránka **Konfigurace přihlášení** . 

2. Vyberte možnost **ručně zjišťovat přihlašovací pole**. Zobrazí se další pokyny, které popisují ruční zjišťování polí přihlašování.

   ![Ruční konfigurace jednotného přihlašování založeného na heslech](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Vyberte možnost **zachytávání přihlašovacích polí**. Na nové kartě se otevře stránka stavu zachycení, která ukazuje, že **právě probíhá zachytávání metadat**zprávy.

4. Pokud se na nové kartě zobrazí pole **požadované rozšíření přístupového panelu** , vyberte **instalovat hned** a nainstalujte rozšíření prohlížeče **rozšíření pro zabezpečené přihlašování k aplikacím** . (Rozšíření prohlížeče vyžaduje Microsoft Edge, Chrome nebo Firefox.) Pak nainstalujte, spusťte a povolte rozšíření a aktualizujte stránku stavu zachycení.

   Rozšíření prohlížeče pak otevře jinou kartu, která zobrazí zadanou adresu URL.
5. Na kartě se zadanou adresou URL Projděte přihlašovací proces. Do polí uživatelské jméno a heslo zadejte a zkuste se přihlásit. (Nemusíte zadávat správné heslo.)

   Zobrazí se výzva k uložení zachycených přihlašovacích polí.
6. Vyberte **OK**. Rozšíření prohlížeče aktualizuje stránku stavu zachycení s metadaty zprávy, která byla **pro aplikaci aktualizována**. Karta prohlížeče se zavře.

7. Na **přihlašovací stránce konfigurace** Azure AD vyberte **OK, přihlášení k aplikaci bylo úspěšné**.

8. Vyberte **OK**.

Po zachytávání přihlašovací stránky můžete přiřadit uživatele a skupiny a zásady pro přihlašovací údaje můžete nastavit stejně jako běžné [aplikace jednotného hesla](what-is-single-sign-on.md).

> [!NOTE]
> Pro aplikaci můžete nahrát logo dlaždice pomocí tlačítka **nahrát logo** na kartě **Konfigurace** aplikace.

## <a name="next-steps"></a>Další kroky

- [Přiřazení uživatelů nebo skupin k aplikaci](methods-for-assigning-users-and-groups.md)
- [Konfigurace automatického zřizování uživatelských účtů](../app-provisioning/configure-automatic-user-provisioning-portal.md)

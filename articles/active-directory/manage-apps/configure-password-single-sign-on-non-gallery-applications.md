---
title: Jak nakonfigurovat jednotné přihlašování založené na heslech pro aplikace Azure AD
description: Jak nakonfigurovat jednotné přihlašování založené na heslech (SSO) pro vaše aplikace Azure AD v platformě Microsoft Identity Platform (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: kenwith
ms.openlocfilehash: c3f9f96c6429d4925c60a56cd450a9c2ee7dde24
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87419950"
---
# <a name="configure-password-based-single-sign-on"></a>Konfigurace jednotného přihlašování založeného na heslech

V [řadě rychlých startů](view-applications-portal.md) při správě aplikací jste zjistili, jak používat Azure AD jako zprostředkovatele identity (IDP) pro aplikaci. V průvodci rychlým startem můžete nastavit jednotné přihlašování založené na SAML. Kromě SAML existuje možnost jednotného přihlašování založeného na heslech. V tomto článku najdete další podrobnosti o možnosti založené na hesle pro jednotné přihlašování. 

Tato možnost je k dispozici pro všechny webové stránky s přihlašovací stránkou HTML. Jednotné přihlašování založené na heslech, označované taky jako trezor hesel, umožňuje spravovat přístup uživatelů a hesla k webovým aplikacím, které nepodporují federaci identit. Je to také užitečné ve scénářích, kdy je potřeba, aby několik uživatelů sdílelo jeden účet, například k účtům aplikací sociálních médií vaší organizace. 

Jednotné přihlašování založené na heslech je skvělým způsobem, jak rychle začít integrovat aplikace do služby Azure AD a umožní vám:

- Povolení jednotného přihlašování pro uživatele díky bezpečnému ukládání a přehrávání uživatelských jmen a hesel pro aplikaci, kterou jste integrací se službou Azure AD.

- Podpora aplikací, které vyžadují více polí přihlašování pro aplikace, které pro přihlášení vyžadují víc než jenom uživatelské jméno a heslo

- Přizpůsobení popisků vstupních polí uživatelského jména a hesla, která se uživatelům zobrazí na [přístupovém panelu aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) při zadání přihlašovacích údajů

- Umožněte uživatelům zadání vlastních uživatelských jmen a hesel pro všechny existující účty aplikací, které zadávají ručně.

- Umožňuje členům obchodní skupiny zadat uživatelská jména a hesla přiřazená uživateli pomocí funkce [Samoobslužný přístup k aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) .

-   Umožňuje správci zadat uživatelské jméno a heslo, které mají jednotlivci nebo skupiny používat při přihlašování k aplikaci pomocí funkce aktualizovat přihlašovací údaje. 

## <a name="before-you-begin"></a>Než začnete

Použití Azure AD jako zprostředkovatele identity a nastavení jednotného přihlašování (SSO) může být jednoduché nebo složité v závislosti na používané aplikaci. Některé aplikace je možné nastavit jenom pomocí několika akcí. Jiné vyžadují hloubkovou konfiguraci. K rychlému navýšení si Projděte příručku [rychlý Start](view-applications-portal.md) při správě aplikací. Pokud je aplikace, kterou přidáváte, jednoduchá, pak pravděpodobně nebudete muset číst tento článek. Pokud aplikace, kterou přidáváte, vyžaduje vlastní konfiguraci a potřebujete použít jednotné přihlašování založené na heslech, bude tento článek pro vás.

> [!IMPORTANT] 
> Existují některé scénáře, kdy možnost **jednotného přihlašování** nebude v navigaci pro aplikaci v **podnikových aplikacích**. 
>
> Pokud byla aplikace zaregistrovaná pomocí **Registrace aplikací** , je ve výchozím nastavení funkce jednotného přihlašování nastavená tak, aby ve výchozím nastavení používala protokol OAuth OIDC. V takovém případě se možnost **jednotného přihlašování** nezobrazí v části **podnikové aplikace**v navigaci. Když použijete **Registrace aplikací** k přidání vlastní aplikace, nakonfigurujete možnosti v souboru manifestu. Další informace o souboru manifestu naleznete v tématu [Azure Active Directory manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest). Další informace o standardech jednotného přihlašování najdete v tématu [ověřování a autorizace pomocí platformy Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform). 
>
> Další scénáře, kdy v navigaci chybí **jednotné přihlašování** , patří mezi ně, pokud je aplikace hostovaná v jiném tenantovi nebo pokud váš účet nemá požadovaná oprávnění (globální správce, správce cloudové aplikace, Správce aplikací nebo vlastník instančního objektu). Oprávnění mohou také způsobit situaci, kdy můžete otevřít **jednotné přihlašování** , ale nebudete je moci uložit. Další informace o rolích pro správu Azure AD najdete v tématu ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-configuration"></a>Základní konfigurace

V [řadě rychlý Start](view-applications-portal.md)jste zjistili, jak přidat aplikaci do svého tenanta, aby Azure AD věděl, že se používá jako zprostředkovatel identity (IDP) pro aplikaci. Některé aplikace jsou už předem nakonfigurované a zobrazují se v galerii Azure AD. Ostatní aplikace nejsou v galerii a Vy musíte vytvořit obecnou aplikaci a nakonfigurovat ji ručně. V závislosti na aplikaci nemusí být možnost jednotného přihlašování založená na heslech k dispozici. Pokud se seznam možností založených na heslech na stránce jednotného přihlašování pro aplikaci nezobrazuje, není k dispozici.

Konfigurační stránka jednotného přihlašování založená na heslech je jednoduchá. Obsahuje jenom adresu URL přihlašovací stránky, kterou používá aplikace. Tento řetězec musí být stránka, která obsahuje pole pro zadání uživatelského jména.

Po zadání adresy URL vyberte **Uložit**. Azure AD analyzuje kód HTML přihlašovací stránky pro pole pro zadání uživatelského jména a hesla. Pokud je pokus úspěšný, jste hotovi.
 
Dalším krokem je [přiřazení uživatelů nebo skupin k aplikaci](methods-for-assigning-users-and-groups.md). Po přiřazení uživatelů a skupin můžete zadat přihlašovací údaje, které se budou používat jménem uživatele při přihlášení k aplikaci. Vyberte **Uživatelé a skupiny**, zaškrtněte políčko pro řádek uživatele nebo skupiny a pak vyberte **Aktualizovat přihlašovací údaje**. Pak zadejte uživatelské jméno a heslo, které chcete použít jménem uživatele nebo skupiny. V opačném případě budou uživatelé vyzváni k zadání přihlašovacích údajů sami při spuštění.
 

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

## <a name="next-steps"></a>Další kroky

- [Přiřazení uživatelů nebo skupin k aplikaci](methods-for-assigning-users-and-groups.md)
- [Konfigurace automatického zřizování uživatelských účtů](../app-provisioning/configure-automatic-user-provisioning-portal.md)

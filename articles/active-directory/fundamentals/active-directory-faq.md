---
title: Nejčastější dotazy (FAQ) – Azure Active Directory | Dokumentace Microsoftu
description: Běžné otázky a odpovědi týkající se Azure a Azure Active Directory, správy hesel a přístupu k aplikacím.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.openlocfilehash: 2ff8f9764e61eb7f497e688c880b16529d1d6b8a
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093100"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Nejčastější dotazy k Azure Active Directory
Azure Active Directory (Azure AD) je komplexní řešení Identity jako služby (IDaaS), které pokrývá všechny prvky identity, řízení přístupu a zabezpečení.

Další informace najdete v tématu [Co je Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Přístup ke službě Azure a Azure Active Directory
**Otázka: Proč se zobrazí "žádné předplatné nenalezeno" při pokusu o přístup ke službě Azure AD na webu Azure Portal?**

**ODPOVĚĎ:** Pro přístup k webu Azure portal, musí každý uživatel oprávnění s předplatným Azure. Pokud máte placené předplatné Azure AD nebo Office 365, přejděte na [https://aka.ms/accessAAD](https://aka.ms/accessAAD), kde najdete jednorázovou aktivaci. Jinak si budete muset vytvořit bezplatný [účet Azure](https://azure.microsoft.com/pricing/free-trial/) nebo placené předplatné.

Další informace naleznete v tématu:

* [Jak je předplatné Azure propojeno se službou Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- - -
**Otázka: Jaký je vztah mezi službami Azure AD, Office 365 a Azure?**

**ODPOVĚĎ:** Azure AD poskytuje běžné funkce identity a přístupu do všech webových služeb. Ať používáte službu Office 365, Microsoft Azure, Intune nebo další, již používáte službu Azure AD, kterou se aktivuje správa přihlašování a přístupu pro všechny tyto služby.

Všichni uživatelé, u kterých je nastaveno používání webových služeb, jsou definováni jako uživatelské účty v jedné nebo více instancích služby Azure AD. Těmto účtům můžete nastavit přístup k bezplatným funkcím služby Azure AD, například ke cloudovým aplikacím.

Placené služby AD Azure, jako je Enterprise Mobility + Security, doplňují ostatní webové služby, např. Office 365 nebo Microsoft Azure o komplexní řešení správy a zabezpečení celého podniku.

- - -

**Otázka:  Jaké jsou rozdíly mezi vlastníka a globálním správcem?**

**ODPOVĚĎ:** Ve výchozím nastavení osoby, která uživatel zaregistruje do služby pro předplatné Azure je přiřazena role vlastníka pro prostředky Azure. Jako vlastníka můžete použít účet Microsoft nebo pracovní či školní účty z adresáře, který je přidružený k předplatnému Azure.  Tato role je oprávnění ke správě služeb na webu Azure Portal.

Pokud se ostatní potřebují k přihlášení a přístup ke službám pomocí stejného předplatného, můžete je přiřadit odpovídající [předdefinovaná role](../../role-based-access-control/built-in-roles.md). Další informace najdete v tématu [správě přístupu pomocí RBAC a webu Azure portal](../../role-based-access-control/role-assignments-portal.md).

Ve výchozím nastavení je osoba, která uživatel zaregistruje do služby pro předplatné Azure přiřadit roli globálního správce adresáře. Globální správce má přístup ke všem funkcím pro adresář Azure AD. Azure AD má jinou sadu rolí správce ke správě adresáře a funkcím souvisejícím s identitou. Tito správci budou mít přístup k různým funkcím na webu Azure Portal. Role správce určuje, co mohou provádět, jako je vytvoření nebo úprava uživatele, přiřazovat role správců jiným uživatelům, resetovat hesla uživatelů, spravovat uživatelské licence nebo spravovat domény.  Další informace o správci adresáře Azure AD a jejich rolích najdete v tématu [uživatele přiřadit do rolí správce ve službě Azure Active Directory](active-directory-users-assign-role-azure-portal.md) a [přiřazení rolí správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

Kromě toho placené služby AD Azure, jako je Enterprise Mobility + Security, doplňují ostatní webové služby, např. Office 365 nebo Microsoft Azure o komplexní řešení správy a zabezpečení celého podniku.

- - -
**Otázka: Je k dispozici sestavy, která ukazuje, kdy vyprší platnost mé uživatelské licence Azure AD?**

**ODPOVĚĎ:** Ne.  Aktuálně není k dispozici.

- - -

## <a name="get-started-with-hybrid-azure-ad"></a>Začínáme s hybridní službou Azure AD


**Otázka: Jak opustím tenanta, když jsem přidán jako spolupracovník?**

**ODPOVĚĎ:** Když se přidá jako spolupracovník do tenanta jiné organizace, můžete v pravém horním rohu "přepínače tenantů" mezi tenanty přepínat.  V současné době neexistuje způsob, jak opustit organizaci, která vás pozvala, ale společnost Microsoft na poskytnutí této funkce pracuje.  Dokud nebude tato funkce dostupná, můžete požádat organizaci, která vás pozvala, o odebrání z jejich tenanta.
- - -
**Otázka: Jak můžu připojit místní adresář ke službě Azure AD?**

**ODPOVĚĎ:** Místní adresář můžete připojit ke službě Azure AD pomocí služby Azure AD Connect.

Další informace najdete v článku [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

- - -
**Otázka: Jak mám nastavit jednotné přihlašování mezi místním adresářem a cloudovými aplikacemi?**

**ODPOVĚĎ:** Stačí nastavit jednotné přihlašování (SSO) mezi místním adresářem a službou Azure AD. Pokud cloudové aplikace používáte prostřednictvím služby Azure AD, služba automaticky povede uživatele, aby provedli správné ověření pomocí místních přihlašovacích údajů.

Implementaci jednotného přihlašování z místního prostředí lze snadno nastavit pomocí federačních řešení, jako je například služba Active Directory Federation Services (AD FS), nebo konfigurací synchronizace hodnot hash hesel. Obě možnosti můžete snadno nasadit pomocí průvodce konfigurací služby Azure AD Connect.

Další informace najdete v článku [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

- - -
**Otázka: Poskytuje Azure AD samoobslužný portál pro uživatele v mé organizaci?**

**ODPOVĚĎ:** Ano, Azure AD poskytuje [přístupový Panel Azure AD](https://myapps.microsoft.com) pro uživatelskou samoobsluhu a přístup k aplikacím. Pokud jste zákazníkem služeb Office 365, najdete mnoho stejných funkcí na portálu služeb Office 365.

Další informace najdete v tématu [Úvod do přístupového panelu](../user-help/active-directory-saas-access-panel-introduction.md).

- - -
**Otázka: Pomůže mi Azure AD spravovat místní infrastrukturu?**

**ODPOVĚĎ:** Ano. Edice Azure AD Premium nabízí službu Azure AD Connect Health. Služba Azure AD Connect Health pomáhá monitorovat místní infrastrukturu identity a synchronizační služby a lépe proniknout do jejich funkce.  

Další informace najdete v tématu [Sledování infrastruktury místních identit a synchronizačních služeb v cloudu](../hybrid/whatis-hybrid-identity-health.md).  

- - -
## <a name="password-management"></a>Správa hesel
**Otázka: Můžete použít zpětný zápis hesla služby Azure AD bez synchronizace hesla? (Je možné v takovém scénáři použít samoobslužné resetování hesla služby Azure AD se zpětným zápisem a neukládat hesla v cloudu?)**

**ODPOVĚĎ:** Není třeba synchronizovat hesla služby Active Directory do Azure AD a povolit zpětný zápis. Ve federovaném prostředí se jednotné přihlašování služby Azure AD spoléhá při ověřování uživatele na místní adresář. Tento scénář nevyžaduje, aby se ve službě Azure AD sledovalo místní heslo.

- - -
**Otázka: Jak dlouho trvá zadání hesla pro zapsání zpět do místní služby Active Directory?**

**ODPOVĚĎ:** Zpětný zápis hesla funguje v reálném čase.

Další informace najdete v tématu [Začínáme se správou hesel](../authentication/quickstart-sspr.md).

- - -
**Otázka: Můžete použít zpětný zápis hesel, která spravuje správce?**

**ODPOVĚĎ:** Ano, pokud budete mít povolený zpětný zápis hesla, operace s heslem provedené správcem se zapíšou zpátky do místního prostředí.  

Další odpovědi na dotazy související s hesly najdete v části [Správa hesel – nejčastější dotazy](../authentication/active-directory-passwords-faq.md).
- - -
**Otázka:  Co mám dělat, pokud při pokusu o změnu hesla nepamatuji stávající heslo Office 365 nebo Azure AD?**

**ODPOVĚĎ:** Pro takové situace existuje několik možností.  Pokud je k dispozici, použijte samoobslužné resetování hesla.  Fungování samoobslužného resetování hesla závisí na jeho konfiguraci.  Další informace najdete v tématu popisujícím [funkci portálu pro resetování hesla](../authentication/howto-sspr-deployment.md).

Pro uživatele Office 365 může váš správce resetovat heslo pomocí postupu uvedeného v článku [Resetování hesel uživatelů](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

Pro účty Azure AD můžou správci resetovat hesla pomocí jedné z následujících metod:

- [Resetování účtů na webu Azure Portal](active-directory-users-reset-password-azure-portal.md)
- [Pomocí PowerShellu](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


- - -
## <a name="security"></a>Zabezpečení
**Otázka: Jsou uzamčené účty po určitém počtu neúspěšných pokusů o přihlášení, nebo se používá složitější strategie?**

Používáme složitější strategii pro uzamčení účtů.  Je založená na adrese IP požadavku a zadaných heslech. Doba trvání uzamčení se taky zvyšuje podle pravděpodobnosti, že se jedná o útoku.  

**Otázka:  Určitá (běžná) hesla se zprávy odmítají "Toto heslo se použilo v mnoha případech", týká se to hesel použitých v aktuální službě active directory?**

To se vztahuje na hesla, která jsou častá globálně, například všechny varianty řetězců „Password“ a „123456“.

**Otázka: Bude požadavek přihlášení z podezřelých zdrojů (botnety, koncový bod tor) blokované v případě tenanta B2C, nebo to vyžaduje tenanta edice Basic nebo Premium?**

Máme bránu, která filtruje požadavky a nabízí určitou ochranu před botnety a která se používá pro všechny tenanty B2C.

## <a name="application-access"></a>Přístup k aplikaci

**Otázka: Kde najdu seznam aplikací, které jsou předem integrovány se službou Azure AD a jejími funkcemi?**

**ODPOVĚĎ:** Azure AD má více než 2 600 předem integrovaných aplikací od společnosti Microsoft, poskytovatelů služeb aplikací a partnerů. Všechny předem integrované aplikace podporují jednotné přihlašování. Jednotné přihlašování umožňuje používat k přístupu do aplikací firemní přihlašovací údaje. Některé aplikace také podporují automatické zřizování a jeho rušení.

Úplný seznam předem integrovaných aplikací najdete v části [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

- - -
**Otázka: Co když aplikace, kterou je třeba se nenachází na webu Azure AD Marketplace?**

**ODPOVĚĎ:** S Azure AD Premium můžete přidat a konfigurovat libovolné aplikace, které chcete. V závislosti na funkcích aplikace a předvolbách můžete nakonfigurovat jednotné přihlašování a automatické zřizování.  

Další informace naleznete v tématu:

* [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Zapnutí automatického zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací pomocí SCIM](../manage-apps/use-scim-to-provision-users-and-groups.md)

- - -
**Otázka: Jak se uživatelé registrují aplikací pomocí služby Azure AD?**

**ODPOVĚĎ:** Azure AD poskytuje několik způsobů, jak uživatelé si můžou zobrazit a přístupu k aplikacím, jako například:

* Přístupový panel služby Azure AD
* Spouštěč aplikace Office 365
* Přímé přihlášení k federovaným aplikacím
* Přímé odkazy na federované nebo existující aplikace či aplikace založené na hesle

Další informace najdete v tématu [prostředí koncového uživatele pro aplikace](../manage-apps/end-user-experiences.md).

- - -
**Otázka: Jaké jsou různé způsoby, jak Azure AD umožňuje ověřování a jednotné přihlašování k aplikacím?**

**ODPOVĚĎ:** Azure AD podporuje mnoho standardizovaných protokolů pro ověřování a autorizaci, například SAML 2.0, OpenID Connect, OAuth 2.0 a WS-Federation. Azure AD podporuje funkce ukládání hesel do trezoru a automatického přihlašování pro aplikace, které podporují pouze ověření na základě formuláře.   

Další informace naleznete v tématu:

* [Scénáře ověřování pro Azure AD](../develop/authentication-scenarios.md)
* [Protokoly ověřování pro Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Jednotné přihlašování pro aplikace ve službě Azure AD](../manage-apps/what-is-single-sign-on.md)

- - -
**Otázka: Můžete přidat aplikace v místním?**

**ODPOVĚĎ:** Proxy aplikací Azure AD poskytuje snadný a bezpečný přístup k místním webovým aplikacím, které zvolíte. Aplikace můžete používat stejným způsobem jako aplikace SaaS ve službě Azure AD. Není nutná síť VPN ani změna infrastruktury sítě.  

Další informace najdete v tématu [Jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](../manage-apps/application-proxy.md).

- - -
**Otázka: Jak můžu vyžadovat vícefaktorové ověřování pro uživatele, kteří používají určitou aplikaci?**

**ODPOVĚĎ:** Pomocí podmíněného přístupu Azure AD můžete přiřadit jedinečné zásady přístupu pro každou aplikaci. V zásadách můžete požadovat vícefaktorové ověřování pokaždé, nebo když uživatelé nejsou připojeni k místní síti.  

Další informace najdete v tématu [Zabezpečení přístupu k Office 365 a jiným aplikacím připojeným ke službě Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

- - -
**Otázka: Co je automatické zřizování uživatelů pro aplikace SaaS?**

**ODPOVĚĎ:** Pomocí služby Azure AD můžete automatizovat vytváření, údržbu a odebírání uživatelských identit v mnoha oblíbených cloudových aplikacích SaaS.

Další informace najdete v tématu [Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).

- - -
**Otázka:  Můžete nastavit zabezpečené připojení LDAP se službou Azure AD?**

**ODPOVĚĎ:**  Ne. Azure AD nepodporuje protokol LDAP. Můžete ale nakonfigurovat protokol secure LDAP s Azure AD Domain Services.

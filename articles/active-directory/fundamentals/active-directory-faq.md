---
title: Nejčastější dotazy (nejčastější dotazy) – Azure Active Directory | Dokumenty společnosti Microsoft
description: Běžné otázky a odpovědi týkající se Azure a Azure Active Directory, správy hesel a přístupu k aplikacím.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4a961e601949689db89f8819f0a1fe1c5a7b3a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875788"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Nejčastější dotazy týkající se služby Azure Active Directory
Azure Active Directory (Azure AD) je komplexní řešení Identity jako služby (IDaaS), které pokrývá všechny prvky identity, řízení přístupu a zabezpečení.

Další informace najdete v tématu [Co je Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Přístup ke službě Azure a Azure Active Directory
**Otázka: Proč se mi při pokusu o přístup k Azure AD na webu Azure Portal zobrazuje "Nebyla nalezena žádná předplatná"?**

**Odpověď:** Pro přístup k webu Azure Portal potřebuje každý uživatel oprávnění s předplatným Azure. Pokud nemáte placené předplatné Office 365 nebo Azure AD, budete muset aktivovat bezplatný [účet Azure](https://azure.microsoft.com/free/
) nebo placené předplatné.

Další informace naleznete v tématu:

* [Jak je předplatné Azure propojeno se službou Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

---
**Otázka: Jaký je vztah mezi Azure AD, Office 365 a Azure?**

**Odpověď:** Azure AD poskytuje běžné funkce identity a přístupu ke všem webovým službám. Ať používáte službu Office 365, Microsoft Azure, Intune nebo další, již používáte službu Azure AD, kterou se aktivuje správa přihlašování a přístupu pro všechny tyto služby.

Všichni uživatelé, u kterých je nastaveno používání webových služeb, jsou definováni jako uživatelské účty v jedné nebo více instancích služby Azure AD. Těmto účtům můžete nastavit přístup k bezplatným funkcím služby Azure AD, například ke cloudovým aplikacím.

Placené služby AD Azure, jako je Enterprise Mobility + Security, doplňují ostatní webové služby, např. Office 365 nebo Microsoft Azure o komplexní řešení správy a zabezpečení celého podniku.

---

**Otázka: Jaké jsou rozdíly mezi vlastníkem a globálním správcem?**

**A:** Ve výchozím nastavení je osobě, která se zaregistruje k předplatnému Azure, přiřazena role vlastníka pro prostředky Azure. Vlastník může použít účet Microsoft nebo pracovní nebo školní účet z adresáře, ke kterému je předplatné Azure přidruženo.  Tato role je oprávnění ke správě služeb na webu Azure Portal.

Pokud ostatní potřebují přihlásit a přistupovat ke službám pomocí stejného předplatného, můžete jim přiřadit příslušnou [předdefinovanou roli](../../role-based-access-control/built-in-roles.md). Další informace najdete [v tématu Správa přístupu pomocí RBAC a portálu Azure](../../role-based-access-control/role-assignments-portal.md).

Ve výchozím nastavení je osobě, která se zaregistruje k předplatnému Azure, přiřazena role globálního správce pro adresář. Globální správce má přístup ke všem funkcím adresáře Azure AD. Azure AD má jinou sadu rolí správce pro správu adresáře a funkce související s identitou. Tito správci budou mít přístup k různým funkcím na webu Azure Portal. Role správce určuje, co může dělat, například vytvářet nebo upravovat uživatele, přiřazovat role pro správu ostatním uživatelům, resetovat uživatelská hesla, spravovat uživatelské licence nebo spravovat domény.  Další informace o správcích adresářů Azure AD a jejich rolích najdete [v tématu Přiřazení uživatele k rolím správce ve službě Azure Active Directory](active-directory-users-assign-role-azure-portal.md) a přiřazení rolí správce ve službě Azure Active [Directory](../users-groups-roles/directory-assign-admin-roles.md).

Kromě toho placené služby AD Azure, jako je Enterprise Mobility + Security, doplňují ostatní webové služby, např. Office 365 nebo Microsoft Azure o komplexní řešení správy a zabezpečení celého podniku.

---
**Otázka: Existuje sestava, která ukazuje, kdy vyprší platnost mé uživatelské licence Azure AD?**

**Odpověď:** Ne.  Aktuálně není k dispozici.

---

## <a name="get-started-with-hybrid-azure-ad"></a>Začínáme s hybridní službou Azure AD


**Otázka: Jak opustím tenanta, když jsem přidán jako spolupracovník?**

**Odpověď:** Když jste přidáni jako spolupracovník do tenanta jiné organizace, můžete mezi tenanty přepínat pomocí „přepínače tenantů“ vlevo nahoře.  V současné době neexistuje způsob, jak opustit organizaci, která vás pozvala, ale společnost Microsoft na poskytnutí této funkce pracuje.  Dokud nebude tato funkce dostupná, můžete požádat organizaci, která vás pozvala, o odebrání z jejich tenanta.

---
**Otázka: Jak můžu připojit místní adresář ke službě Azure AD?**

**Odpověď:** Místní adresář můžete připojit ke službě Azure AD pomocí služby Azure AD Connect.

Další informace najdete v článku [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

---
**Otázka: Jak mám nastavit jednotné přihlašování mezi místním adresářem a cloudovými aplikacemi?**

**Odpověď:** Jednotné přihlašování (SSO) je třeba nastavit pouze mezi místním adresářem a službou Azure AD. Pokud cloudové aplikace používáte prostřednictvím služby Azure AD, služba automaticky povede uživatele, aby provedli správné ověření pomocí místních přihlašovacích údajů.

Implementace automatického přibližování z místního prostředí lze snadno dosáhnout pomocí federačních řešení, jako je služba AD FS (Active Directory Federation Services), nebo konfigurací synchronizace hash hesel. Obě možnosti můžete snadno nasadit pomocí Průvodce konfigurací služby Azure AD Connect.

Další informace najdete v článku [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

---
**Otázka: Poskytuje Azure AD samoobslužný portál pro uživatele v naší organizaci?**

**Odpověď:** Ano, Azure AD poskytuje pro uživatelskou samoobsluhu a přístup k aplikacím [Přístupový panel Azure AD](https://myapps.microsoft.com). Pokud jste zákazníkem Office 365, najdete mnoho stejných funkcí na [portálu Office 365](https://portal.office.com).

Další informace najdete v tématu [Úvod do přístupového panelu](../user-help/active-directory-saas-access-panel-introduction.md).

---
**Otázka: Pomůže mi Azure AD spravovat místní infrastrukturu?**

**Odpověď:** Ano. Edice Azure AD Premium nabízí službu Azure AD Connect Health. Služba Azure AD Connect Health pomáhá monitorovat místní infrastrukturu identity a synchronizační služby a lépe proniknout do jejich funkce.  

Další informace najdete v tématu [Sledování infrastruktury místních identit a synchronizačních služeb v cloudu](../hybrid/whatis-hybrid-identity-health.md).  

---
## <a name="password-management"></a>Správa hesel
**Otázka: Můžu použít zpětný zápis hesla Azure AD bez synchronizace hesel? (V tomto scénáři je možné použít samoobslužné resetování hesla Azure AD (SSPR) s zpětným zápisem hesla a neukládat hesla v cloudu?)**

**Odpověď:** Pokud chcete aktivovat zpětný zápis, není třeba synchronizovat hesla služby Active Directory do služby Azure AD. Ve federovaném prostředí se jednotné přihlašování služby Azure AD spoléhá při ověřování uživatele na místní adresář. Tento scénář nevyžaduje, aby se ve službě Azure AD sledovalo místní heslo.

---
**Otázka: Jak dlouho trvá zpětný zápis hesla do místní služby Active Directory?**

**Odpověď:** Zpětný zápis hesla funguje v reálném čase.

Další informace najdete v tématu [Začínáme se správou hesel](../authentication/quickstart-sspr.md).

---
**Otázka: Je možné použít zpětný zápis hesel, která spravuje správce?**

**Odpověď:** Ano, pokud máte aktivní zpětný zápis hesel, zapíšou se operace s heslem provedené správcem zpět do místního prostředí.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questions"></a>Další odpovědi na dotazy související s hesly najdete v části [Správa hesel – nejčastější dotazy](../authentication/active-directory-passwords-faq.md).
---
**Otázka: Co mám dělat, když si při pokusu o změnu hesla nepamatuji stávající heslo Office 365 nebo Azure AD?**

**Odpověď:** Pro takové situace existuje řada možností.  Pokud je k dispozici, použijte samoobslužné resetování hesla.  Fungování samoobslužného resetování hesla závisí na jeho konfiguraci.  Další informace najdete v tématu popisujícím [funkci portálu pro resetování hesla](../authentication/howto-sspr-deployment.md).

Pro uživatele Office 365 může váš správce resetovat heslo pomocí postupu uvedeného v článku [Resetování hesel uživatelů](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

Pro účty Azure AD můžou správci resetovat hesla pomocí jedné z následujících metod:

- [Resetování účtů na webu Azure Portal](active-directory-users-reset-password-azure-portal.md)
- [Pomocí prostředí PowerShell](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


---
## <a name="security"></a>Zabezpečení
**Otázka: Uzamknou se účty po určitém počtu neúspěšných pokusů o přihlášení, nebo se používá složitější strategie?**

Používáme složitější strategii pro uzamčení účtů.  Je založená na adrese IP požadavku a zadaných heslech. Doba trvání uzamčení se taky zvyšuje podle pravděpodobnosti, že se jedná o útoku.  

**Otázka: Některá (běžná) hesla jsou odmítnuta se zprávami "toto heslo bylo mnohokrát použito", týká se to hesel používaných v aktuálním aktivním adresáři?**

To se týká hesel, které jsou globálně běžné, například všechny varianty "Heslo" a "123456".

**Otázka: Budou všechny žádosti o přihlášení z podezřelých zdrojů (botnety, koncový bod tor) blokované v případě tenanta B2C, nebo to vyžaduje tenanta edice Basic nebo Premium?**

Máme bránu, která filtruje požadavky a nabízí určitou ochranu před botnety a která se používá pro všechny tenanty B2C.

## <a name="application-access"></a>Přístup k aplikaci

**Otázka: Kde můžu najít seznam aplikací, které jsou předem integrované se službou Azure AD a jejími funkcemi?**

**Odpověď:** Azure AD má více než 2 600 předem integrovaných aplikací od společnosti Microsoft, poskytovatelů služeb aplikací a partnerů. Všechny předem integrované aplikace podporují jednotné přihlašování. Jednotné přihlašování umožňuje používat k přístupu do aplikací firemní přihlašovací údaje. Některé aplikace také podporují automatické zřizování a jeho rušení.

Úplný seznam předem integrovaných aplikací najdete v části [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

---
**Otázka: Co mám dělat, když se aplikace, kterou potřebuji, nenachází na webu Azure AD Marketplace?**

**Odpověď:** Se službou Azure AD Premium můžete přidávat a konfigurovat libovolné aplikace. V závislosti na možnostech vaší aplikace a vašich předvolbách můžete nakonfigurovat sociální zabezpečení a automatizované zřizování.  

Další informace naleznete v tématu:

* [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Pomocí SCIM zapněte automatické zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací](../app-provisioning/use-scim-to-provision-users-and-groups.md)

---
**Otázka: Jak se uživatelé můžou přihlašovat do aplikací pomocí služby Azure AD?**

**Odpověď:** Azure AD uživatelům nabízí několik způsobů zobrazení a přístupu k aplikacím, jako například:

* Přístupový panel služby Azure AD
* Spouštěč aplikace Office 365
* Přímé přihlášení k federovaným aplikacím
* Přímé odkazy na federované nebo existující aplikace či aplikace založené na hesle

Další informace naleznete [v tématu End user experiences for applications](../manage-apps/end-user-experiences.md).

---
**Otázka: Jakými způsoby služba Azure AD umožňuje ověřování a jednotné přihlašování k aplikacím?**

**Odpověď:** Azure AD podporuje mnoho standardizovaných protokolů pro ověřování a autorizaci, například SAML 2.0, OpenID Connect, OAuth 2.0 a WS-Federation. Azure AD podporuje funkce ukládání hesel do trezoru a automatického přihlašování pro aplikace, které podporují pouze ověření na základě formuláře.   

Další informace naleznete v tématu:

* [Scénáře ověřování pro Azure AD](../develop/authentication-scenarios.md)
* [Ověřovací protokoly služby Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Jednotné přihlašování pro aplikace ve službě Azure AD](../manage-apps/what-is-single-sign-on.md)

---
**Otázka: Můžu přidat aplikace, které spouštěji místně?**

**Odpověď:** Proxy soubory aplikace služby Azure AD poskytují snadný a bezpečný přístup k místním webovým aplikacím podle vašeho výběru. Aplikace můžete používat stejným způsobem jako aplikace SaaS ve službě Azure AD. Není nutná síť VPN ani změna infrastruktury sítě.  

Další informace najdete v tématu [Jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](../manage-apps/application-proxy.md).

---
**Otázka: Jak můžu vyžadovat vícefaktorové ověřování pro uživatele, kteří používají určitou aplikaci?**

**A:** S Podmíněný přístup Azure AD můžete přiřadit jedinečné zásady přístupu pro každou aplikaci. V zásadách můžete požadovat vícefaktorové ověřování pokaždé, nebo když uživatelé nejsou připojeni k místní síti.  

Další informace najdete v tématu [Zabezpečení přístupu k Office 365 a jiným aplikacím připojeným ke službě Azure Active Directory](../conditional-access/overview.md).

---
**Otázka: Co je automatické zřizování uživatelů pro aplikace SaaS?**

**Odpověď:** Pomocí služby Azure AD můžete automatizovat vytváření, údržbu a odebírání uživatelských identit v mnoha oblíbených cloudových aplikacích SaaS.

Další informace najdete v tématu [Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).

---
**Otázka: Je možné vytvořit zabezpečené připojení LDAP se službou Azure Active Directory?**

**A:**  Ne. Azure AD nepodporuje protokol LDAP (LDAP) protokolu LDAP (LDAP) nebo secure LDAP přímo. Je však možné povolit instanci služby Azure AD Domain Services (Azure AD DS) v tenantovi Azure AD se správně nakonfigurovanými skupinami zabezpečení sítě prostřednictvím azure networkingu, aby bylo dosaženo připojení LDAP. Další informace naleznete v [tématu Konfigurace zabezpečeného protokolu LDAP pro spravovanou doménu služby Azure Active Directory Domain Services.](../../active-directory-domain-services/tutorial-configure-ldaps.md)

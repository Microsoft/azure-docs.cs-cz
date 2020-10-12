---
title: Nejčastější dotazy (FAQ) – Azure Active Directory | Microsoft Docs
description: Běžné otázky a odpovědi týkající se Azure a Azure Active Directory, správy hesel a přístupu k aplikacím.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: troubleshooting
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: afb6adc38fd44366b837119518622dd8931f9096
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89565561"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Nejčastější dotazy týkající se Azure Active Directory
Azure Active Directory (Azure AD) je komplexní řešení Identity jako služby (IDaaS), které pokrývá všechny prvky identity, řízení přístupu a zabezpečení.

Další informace najdete v tématu [Co je Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Přístup ke službě Azure a Azure Active Directory
**Otázka: Proč se při pokusu o přístup k Azure AD v Azure Portal zobrazí zpráva "žádné předplatné Nenalezeno"?**

**Odpověď:** Pro přístup k webu Azure Portal potřebuje každý uživatel oprávnění s předplatným Azure. Pokud nemáte placené Microsoft 365 nebo předplatné služby Azure AD, budete muset aktivovat bezplatný [účet Azure](https://azure.microsoft.com/free/
) nebo placené předplatné.

Další informace naleznete v tématech:

* [Jak je předplatné Azure propojeno se službou Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

---
**Otázka: Jaký je vztah mezi službami Azure AD, Microsoft 365 a Azure?**

**Odpověď:** Azure AD poskytuje běžné funkce identity a přístupu ke všem webovým službám. Bez ohledu na to, jestli používáte Microsoft 365, Microsoft Azure, Intune nebo jiné, už službu Azure AD používáte, abyste mohli zapnout správu přihlašování a přístupu pro všechny tyto služby.

Všichni uživatelé, u kterých je nastaveno používání webových služeb, jsou definováni jako uživatelské účty v jedné nebo více instancích služby Azure AD. Těmto účtům můžete nastavit přístup k bezplatným funkcím služby Azure AD, například ke cloudovým aplikacím.

Placené služby Azure AD, jako Enterprise Mobility + Security, doplňují další webové služby, jako je Microsoft 365 a Microsoft Azure s komplexními řešeními pro správu a zabezpečení na podnikové úrovni.

---

**Otázka: Jaké jsou rozdíly mezi vlastníkem a globálním správcem?**

**A:** Ve výchozím nastavení má osoba, která se zaregistruje k předplatnému Azure, přiřazenou roli vlastníka pro prostředky Azure. Vlastník může použít buď účet Microsoft, nebo pracovní nebo školní účet z adresáře, ke kterému je předplatné Azure přidružené.  Tato role je oprávnění ke správě služeb na webu Azure Portal.

Pokud se ostatní potřebují přihlašovat a přistupovat ke službám pomocí stejného předplatného, můžete jim přiřadit příslušnou [integrovanou roli](../../role-based-access-control/built-in-roles.md). Další informace najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md).

Ve výchozím nastavení se uživateli, který se zaregistruje do předplatného Azure, přiřadí role globálního správce pro tento adresář. Globální správce má přístup ke všem funkcím adresáře Azure AD. Azure AD má jinou sadu rolí správce pro správu adresáře a funkcí souvisejících s identitou. Tito správci budou mít přístup k různým funkcím v Azure Portal. Role správce určuje, co můžou dělat, jako je vytváření nebo úpravy uživatelů, přiřazování rolí pro správu ostatním, Resetování uživatelských hesel, Správa uživatelských licencí nebo Správa domén.  Další informace o správcích adresáře služby Azure AD a jejich rolích najdete v tématu [přiřazení uživatele k rolím správců v Azure Active Directory](active-directory-users-assign-role-azure-portal.md) a [přiřazování rolí správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

Kromě toho placené služby Azure AD, jako je Enterprise Mobility + Security, doplňují další webové služby, jako jsou Microsoft 365 a Microsoft Azure, s komplexními řešeními pro správu a zabezpečení v podnikovém měřítku.

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

Implementaci jednotného přihlašování z místního prostředí lze snadno dosáhnout pomocí federačních řešení, jako je Active Directory Federation Services (AD FS) (AD FS), nebo konfigurací synchronizace hodnot hash hesel. Obě možnosti můžete snadno nasadit pomocí Průvodce konfigurací Azure AD Connect.

Další informace najdete v článku [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

---
**Otázka: Poskytuje Azure AD samoobslužný portál pro uživatele v naší organizaci?**

**Odpověď:** Ano, Azure AD poskytuje pro uživatelskou samoobsluhu a přístup k aplikacím [Přístupový panel Azure AD](https://myapps.microsoft.com). Pokud jste zákazníkem Microsoft 365, můžete najít mnoho stejných funkcí na [portálu Office 365](https://portal.office.com).

Další informace najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

---
**Otázka: Pomůže mi Azure AD spravovat místní infrastrukturu?**

**Odpověď:** Ano. Edice Azure AD Premium nabízí službu Azure AD Connect Health. Služba Azure AD Connect Health pomáhá monitorovat místní infrastrukturu identity a synchronizační služby a lépe proniknout do jejich funkce.  

Další informace najdete v tématu [Sledování infrastruktury místních identit a synchronizačních služeb v cloudu](../hybrid/whatis-azure-ad-connect.md).  

---
## <a name="password-management"></a>Správa hesel
**Otázka: je možné použít zpětný zápis hesla služby Azure AD bez synchronizace hesla? (V tomto scénáři je možné použít Samoobslužné resetování hesla (SSPR) služby Azure AD se zpětným zápisem hesla a Neukládat hesla v cloudu?)**

**Odpověď:** Pokud chcete aktivovat zpětný zápis, není třeba synchronizovat hesla služby Active Directory do služby Azure AD. Ve federovaném prostředí se jednotné přihlašování služby Azure AD spoléhá při ověřování uživatele na místní adresář. Tento scénář nevyžaduje, aby se ve službě Azure AD sledovalo místní heslo.

---
**Otázka: Jak dlouho trvá zpětný zápis hesla do místní služby Active Directory?**

**Odpověď:** Zpětný zápis hesla funguje v reálném čase.

Další informace najdete v tématu [Začínáme se správou hesel](../authentication/tutorial-enable-sspr.md).

---
**Otázka: Je možné použít zpětný zápis hesel, která spravuje správce?**

**Odpověď:** Ano, pokud máte aktivní zpětný zápis hesel, zapíšou se operace s heslem provedené správcem zpět do místního prostředí.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questions"></a>Další odpovědi na dotazy související s hesly najdete v části [Správa hesel – nejčastější dotazy](../authentication/active-directory-passwords-faq.md).
---
**Otázka: co mohu udělat, když si při pokusu o změnu hesla nepamatuji existující Microsoft 365 nebo heslo Azure AD?**

**Odpověď:** Pro takové situace existuje řada možností.  Pokud je k dispozici, použijte samoobslužné resetování hesla.  Fungování samoobslužného resetování hesla závisí na jeho konfiguraci.  Další informace najdete v tématu popisujícím [funkci portálu pro resetování hesla](../authentication/howto-sspr-deployment.md).

U Microsoft 365 uživatelů může správce resetovat heslo pomocí postupu popsaného v části [resetování hesel uživatelů](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

Pro účty Azure AD můžou správci resetovat hesla pomocí jedné z následujících metod:

- [Resetování účtů na webu Azure Portal](active-directory-users-reset-password-azure-portal.md)
- [Použití PowerShellu](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


---
## <a name="security"></a>Zabezpečení
**Otázka: Uzamknou se účty po určitém počtu neúspěšných pokusů o přihlášení, nebo se používá složitější strategie?**

Používáme složitější strategii pro uzamčení účtů.  Je založená na adrese IP požadavku a zadaných heslech. Doba trvání uzamčení se taky zvyšuje podle pravděpodobnosti, že se jedná o útoku.  

**Otázka: určitá (společná) hesla se odmítnou se zprávou "Toto heslo bylo použito v mnoha časech", tento postup se vztahuje na hesla používaná v aktuální službě Active Directory?**

To odkazuje na globálně společná hesla, jako jsou třeba jakékoli varianty "Password" a "123456".

**Otázka: Budou všechny žádosti o přihlášení z podezřelých zdrojů (botnety, koncový bod tor) blokované v případě tenanta B2C, nebo to vyžaduje tenanta edice Basic nebo Premium?**

Máme bránu, která filtruje požadavky a nabízí určitou ochranu před botnety a která se používá pro všechny tenanty B2C.

## <a name="application-access"></a>Přístup k aplikaci

**Otázka: Kde můžu najít seznam aplikací, které jsou předem integrované se službou Azure AD a jejími funkcemi?**

**Odpověď:** Azure AD má více než 2 600 předem integrovaných aplikací od společnosti Microsoft, poskytovatelů služeb aplikací a partnerů. Všechny předem integrované aplikace podporují jednotné přihlašování. Jednotné přihlašování umožňuje používat k přístupu do aplikací firemní přihlašovací údaje. Některé aplikace také podporují automatické zřizování a jeho rušení.

Úplný seznam předem integrovaných aplikací najdete v části [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory).

---
**Otázka: Co mám dělat, když se aplikace, kterou potřebuji, nenachází na webu Azure AD Marketplace?**

**Odpověď:** Se službou Azure AD Premium můžete přidávat a konfigurovat libovolné aplikace. V závislosti na možnostech vaší aplikace a vašich předvolbách můžete nakonfigurovat jednotné přihlašování a automatizované zřizování.  

Další informace naleznete v tématech:

* [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Pomocí SCIM zapněte automatické zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací](../app-provisioning/use-scim-to-provision-users-and-groups.md)

---
**Otázka: Jak se uživatelé můžou přihlašovat do aplikací pomocí služby Azure AD?**

**Odpověď:** Azure AD uživatelům nabízí několik způsobů zobrazení a přístupu k aplikacím, jako například:

* Přístupový panel služby Azure AD
* Spouštěč aplikace Microsoft 365
* Přímé přihlášení k federovaným aplikacím
* Přímé odkazy na federované nebo existující aplikace či aplikace založené na hesle

Další informace najdete v tématu [prostředí koncových uživatelů pro aplikace](../manage-apps/end-user-experiences.md).

---
**Otázka: Jakými způsoby služba Azure AD umožňuje ověřování a jednotné přihlašování k aplikacím?**

**Odpověď:** Azure AD podporuje mnoho standardizovaných protokolů pro ověřování a autorizaci, například SAML 2.0, OpenID Connect, OAuth 2.0 a WS-Federation. Azure AD podporuje funkce ukládání hesel do trezoru a automatického přihlašování pro aplikace, které podporují pouze ověření na základě formuláře.   

Další informace naleznete v tématech:

* [Scénáře ověřování pro Azure AD](../develop/authentication-vs-authorization.md)
* [Protokoly pro ověřování služby Active Directory](/previous-versions/azure/dn151124(v=azure.100))
* [Jednotné přihlašování pro aplikace ve službě Azure AD](../manage-apps/what-is-single-sign-on.md)

---
**Otázka: mohu přidat aplikace, které jsem Spouštím místně?**

**Odpověď:** Proxy soubory aplikace služby Azure AD poskytují snadný a bezpečný přístup k místním webovým aplikacím podle vašeho výběru. Aplikace můžete používat stejným způsobem jako aplikace SaaS ve službě Azure AD. Není nutná síť VPN ani změna infrastruktury sítě.  

Další informace najdete v tématu [Jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](../manage-apps/application-proxy.md).

---
**Otázka: Jak můžu vyžadovat vícefaktorové ověřování pro uživatele, kteří používají určitou aplikaci?**

**A:** Pomocí podmíněného přístupu Azure AD můžete přiřadit jedinečné zásady přístupu pro každou aplikaci. V zásadách můžete požadovat vícefaktorové ověřování pokaždé, nebo když uživatelé nejsou připojeni k místní síti.  

Další informace najdete v tématu [zabezpečení přístupu k Microsoft 365 a dalším aplikacím připojeným k Azure Active Directory](../conditional-access/overview.md).

---
**Otázka: co je Automatické zřizování uživatelů pro aplikace SaaS?**

**Odpověď:** Pomocí služby Azure AD můžete automatizovat vytváření, údržbu a odebírání uživatelských identit v mnoha oblíbených cloudových aplikacích SaaS.

Další informace najdete v tématu [Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).

---
**Otázka: Je možné vytvořit zabezpečené připojení LDAP se službou Azure Active Directory?**

**A:**  Ne. Azure AD nepodporuje protokol LDAP (Lightweight Directory Access Protocol) ani protokol Secure LDAP přímo. Je ale možné povolit instanci služby Azure AD Domain Services (Azure služba AD DS) ve vašem tenantovi Azure AD s správně nakonfigurovanými skupinami zabezpečení sítě přes síť Azure, aby bylo možné připojení LDAP. Další informace najdete v tématu [Konfigurace protokolu Secure LDAP pro Azure Active Directory Domain Services spravovanou doménu](../../active-directory-domain-services/tutorial-configure-ldaps.md) .
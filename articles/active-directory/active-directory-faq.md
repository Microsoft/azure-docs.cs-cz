---
title: Nejčastější dotazy ke službě Azure Active Directory | Dokumentace Microsoftu
description: V nejčastějších dotazech ke službě Azure Active Directory získáte odpovědi na obvyklé otázky týkající se Azure a Azure Active Directory, správy hesel a přístupu k aplikacím.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: markvi
ms.openlocfilehash: 83586cff59d5beb7c48bc6bb8b72fd09e4d37bf8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="azure-active-directory-faq"></a>Nejčastější dotazy ke službě Azure Active Directory
Azure Active Directory (Azure AD) je komplexní řešení Identity jako služby (IDaaS), které pokrývá všechny prvky identity, řízení přístupu a zabezpečení.

Další informace najdete v tématu [Co je Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Přístup ke službě Azure a Azure Active Directory
**Otázka: Proč se při pokusu o přístup ke službě Azure AD na webu Azure Portal zobrazí zpráva „Žádné předplatné nenalezeno“?**

**Odpověď:** Pro přístup k webu Azure Portal potřebuje každý uživatel oprávnění s předplatným Azure. Pokud máte placené předplatné Azure AD nebo Office 365, přejděte na [https://aka.ms/accessAAD](https://aka.ms/accessAAD), kde najdete jednorázovou aktivaci. Jinak si budete muset vytvořit bezplatný [účet Azure](https://azure.microsoft.com/pricing/free-trial/) nebo placené předplatné.

Další informace naleznete v tématu:

* [Jak je předplatné Azure propojeno se službou Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Správa adresáře pro předplatné služeb Office 365 ve službě Azure](active-directory-manage-o365-subscription.md)

- - -
**Otázka: Jaký je vztah mezi službami Azure AD, Office 365 a Azure?**

**Odpověď:** Azure AD poskytuje běžné funkce identity a přístupu ke všem webovým službám. Ať používáte službu Office 365, Microsoft Azure, Intune nebo další, již používáte službu Azure AD, kterou se aktivuje správa přihlašování a přístupu pro všechny tyto služby.

Všichni uživatelé, u kterých je nastaveno používání webových služeb, jsou definováni jako uživatelské účty v jedné nebo více instancích služby Azure AD. Těmto účtům můžete nastavit přístup k bezplatným funkcím služby Azure AD, například ke cloudovým aplikacím.

Placené služby AD Azure, jako je Enterprise Mobility + Security, doplňují ostatní webové služby, např. Office 365 nebo Microsoft Azure o komplexní řešení správy a zabezpečení celého podniku.

- - -

**Otázka: Jaké jsou rozdíly mezi správcem předplatného a správcem adresáře?**

**Odpověď:** Ve výchozím nastavení máte po registraci v Azure přiřazenou roli správce předplatného. Správce předplatného může používat účty Microsoft nebo pracovní či školní účty z adresáře, který je přidružený k předplatnému Azure.  Tato role je oprávnění ke správě služeb na webu Azure Portal.

Pokud se ostatní potřebují přihlašovat ke službám pomocí stejného předplatného, můžete je přidat jako spolusprávce. Tato role má stejná přístupová oprávnění jako správce služeb, ale nemůže změnit přidružení předplatných k adresářům Azure.  Další informace o správcích předplatného najdete v tématech [Postup přidání nebo změna role správce služby Azure](../billing-add-change-azure-subscription-administrator.md) a [Jak je předplatné Azure propojeno se službou Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).


Azure AD má ke správě adresáře a funkcím souvisejícím s identitou k dispozici jinou sadu rolí správců.  Tito správci mají přístup k různým funkcím na webu Azure Portal. Role správce určuje, co může provádět – například vytvářet nebo upravovat uživatele, přiřazovat role správce ostatním uživatelům, resetovat hesla uživatelů, spravovat uživatelské licence nebo spravovat domény.  Další informace o správcích adresáře služby Azure AD a jejich rolích najdete v tématu [Přiřazení rolí správce ve službě Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

Kromě toho placené služby AD Azure, jako je Enterprise Mobility + Security, doplňují ostatní webové služby, např. Office 365 nebo Microsoft Azure o komplexní řešení správy a zabezpečení celého podniku.

- - -
**Otázka: Existuje sestava, která ukazuje, kdy vyprší platnost mé uživatelské licence Azure AD?**

**Odpověď:** Ne.  Aktuálně není k dispozici.

- - -

## <a name="get-started-with-hybrid-azure-ad"></a>Začínáme s hybridní službou Azure AD


**Otázka: Jak opustím tenanta, když jsem přidán jako spolupracovník?**

**Odpověď:** Když jste přidáni jako spolupracovník do tenanta jiné organizace, můžete mezi tenanty přepínat pomocí „přepínače tenantů“ vlevo nahoře.  V současné době neexistuje způsob, jak opustit organizaci, která vás pozvala, ale společnost Microsoft na poskytnutí této funkce pracuje.  Dokud nebude tato funkce dostupná, můžete požádat organizaci, která vás pozvala, o odebrání z jejich tenanta.
- - -
**Otázka: Jak můžu připojit místní adresář ke službě Azure AD?**

**Odpověď:** Místní adresář můžete připojit ke službě Azure AD pomocí služby Azure AD Connect.

Další informace najdete v článku [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).

- - -
**Otázka: Jak mám nastavit jednotné přihlašování mezi místním adresářem a cloudovými aplikacemi?**

**Odpověď:** Jednotné přihlašování (SSO) je třeba nastavit pouze mezi místním adresářem a službou Azure AD. Pokud cloudové aplikace používáte prostřednictvím služby Azure AD, služba automaticky povede uživatele, aby provedli správné ověření pomocí místních přihlašovacích údajů.

Implementaci jednotného přihlašování z místního prostředí lze snadno nastavit pomocí federačních řešení, jako je například služba Active Directory Federation Services (AD FS), nebo konfigurací synchronizace hodnot hash hesel. Obě možnosti můžete snadno nasadit pomocí průvodce konfigurací služby Azure AD Connect.

Další informace najdete v článku [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).

- - -
**Otázka: Poskytuje Azure AD samoobslužný portál pro uživatele v naší organizaci?**

**Odpověď:** Ano, Azure AD poskytuje pro uživatelskou samoobsluhu a přístup k aplikacím [Přístupový panel Azure AD](http://myapps.microsoft.com). Pokud jste zákazníkem služeb Office 365, najdete mnoho stejných funkcí na portálu služeb Office 365.

Další informace najdete v tématu [Úvod do přístupového panelu](active-directory-saas-access-panel-introduction.md).

- - -
**Otázka: Pomůže mi Azure AD spravovat místní infrastrukturu?**

**Odpověď:** Ano. Edice Azure AD Premium nabízí službu Azure AD Connect Health. Služba Azure AD Connect Health pomáhá monitorovat místní infrastrukturu identity a synchronizační služby a lépe proniknout do jejich funkce.  

Další informace najdete v tématu [Sledování infrastruktury místních identit a synchronizačních služeb v cloudu](active-directory-aadconnect-health.md).  

- - -
## <a name="password-management"></a>Správa hesel
**Otázka: Je možné použít zpětný zápis hesla služby Azure AD bez synchronizace hesla? (Je možné v takovém scénáři použít samoobslužné resetování hesla služby Azure AD se zpětným zápisem a neukládat hesla v cloudu?)**

**Odpověď:** Pokud chcete aktivovat zpětný zápis, není třeba synchronizovat hesla služby Active Directory do služby Azure AD. Ve federovaném prostředí se jednotné přihlašování služby Azure AD spoléhá při ověřování uživatele na místní adresář. Tento scénář nevyžaduje, aby se ve službě Azure AD sledovalo místní heslo.

- - -
**Otázka: Jak dlouho trvá zpětný zápis hesla do místní služby Active Directory?**

**Odpověď:** Zpětný zápis hesla funguje v reálném čase.

Další informace najdete v tématu [Začínáme se správou hesel](active-directory-passwords-getting-started.md).

- - -
**Otázka: Je možné použít zpětný zápis hesel, která spravuje správce?**

**Odpověď:** Ano, pokud máte aktivní zpětný zápis hesel, zapíšou se operace s heslem provedené správcem zpět do místního prostředí.  

Další odpovědi na dotazy související s hesly najdete v části [Správa hesel – nejčastější dotazy](authentication/active-directory-passwords-faq.md).
- - -
**Otázka: Co mám dělat, když si při pokusu o změnu hesla nepamatuji stávající heslo Office 365 nebo Azure AD?**

**Odpověď:** Pro takové situace existuje řada možností.  Pokud je k dispozici, použijte samoobslužné resetování hesla.  Fungování samoobslužného resetování hesla závisí na jeho konfiguraci.  Další informace najdete v tématu popisujícím [funkci portálu pro resetování hesla](authentication/howto-sspr-deployment.md).

Pro uživatele Office 365 může váš správce resetovat heslo pomocí postupu uvedeného v článku [Resetování hesel uživatelů](https://support.office.com/en-us/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

Pro účty Azure AD můžou správci resetovat hesla pomocí jedné z následujících metod:

- [Resetování účtů na webu Azure Portal](active-directory-users-reset-password-azure-portal.md)
- [Pomocí PowerShellu](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


- - -
## <a name="security"></a>Zabezpečení
**Otázka: Uzamknou se účty po určitém počtu neúspěšných pokusů o přihlášení, nebo se používá složitější strategie?**

Používáme složitější strategii pro uzamčení účtů.  Je založená na adrese IP požadavku a zadaných heslech. Doba trvání uzamčení se taky zvyšuje podle pravděpodobnosti, že se jedná o útoku.  

**Otázka: Určitá (běžná) hesla se odmítají se zprávou, že toto heslo se použilo už příliš mnohokrát. Týká se to hesel použitých v aktuální službě Active Directory?**

To se vztahuje na hesla, která jsou častá globálně, například všechny varianty řetězců „Password“ a „123456“.

**Otázka: Budou všechny žádosti o přihlášení z podezřelých zdrojů (botnety, koncový bod tor) blokované v případě tenanta B2C, nebo to vyžaduje tenanta edice Basic nebo Premium?**

Máme bránu, která filtruje požadavky a nabízí určitou ochranu před botnety a která se používá pro všechny tenanty B2C.

## <a name="application-access"></a>Přístup k aplikaci

**Otázka: Kde najdu seznam aplikací, které jsou předem integrovány se službou Azure AD a jejími funkcemi?**

**Odpověď:** Azure AD má více než 2 600 předem integrovaných aplikací od společnosti Microsoft, poskytovatelů služeb aplikací a partnerů. Všechny předem integrované aplikace podporují jednotné přihlašování. Jednotné přihlašování umožňuje používat k přístupu do aplikací firemní přihlašovací údaje. Některé aplikace také podporují automatické zřizování a jeho rušení.

Úplný seznam předem integrovaných aplikací najdete v části [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

- - -
**Otázka: Co mám dělat, když se požadovaná aplikace nenachází na webu Azure AD Marketplace?**

**Odpověď:** Se službou Azure AD Premium můžete přidávat a konfigurovat libovolné aplikace. V závislosti na funkcích aplikace a předvolbách můžete nakonfigurovat jednotné přihlašování a automatické zřizování.  

Další informace naleznete v tématu:

* [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Zapnutí automatického zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací pomocí SCIM](active-directory-scim-provisioning.md)

- - -
**Otázka: Jak se uživatelé můžou přihlašovat do aplikací pomocí služby Azure AD?**

**Odpověď:** Azure AD uživatelům nabízí několik způsobů zobrazení a přístupu k aplikacím, jako například:

* Přístupový panel služby Azure AD
* Spouštěč aplikace Office 365
* Přímé přihlášení k federovaným aplikacím
* Přímé odkazy na federované nebo existující aplikace či aplikace založené na hesle

Další informace naleznete v tématu [Nasazení integrovaných aplikací Azure AD pro uživatele](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

- - -
**Otázka: Jakými způsoby služba Azure AD umožňuje ověřování a jednotné přihlašování k aplikacím?**

**Odpověď:** Azure AD podporuje mnoho standardizovaných protokolů pro ověřování a autorizaci, například SAML 2.0, OpenID Connect, OAuth 2.0 a WS-Federation. Azure AD podporuje funkce ukládání hesel do trezoru a automatického přihlašování pro aplikace, které podporují pouze ověření na základě formuláře.   

Další informace naleznete v tématu:

* [Scénáře ověřování pro Azure AD](active-directory-authentication-scenarios.md)
* [Protokoly ověřování pro Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Jak funguje jednotné přihlašování pomocí služby Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

- - -
**Otázka: Je možné přidat místní aplikace?**

**Odpověď:** Proxy soubory aplikace služby Azure AD poskytují snadný a bezpečný přístup k místním webovým aplikacím podle vašeho výběru. Aplikace můžete používat stejným způsobem jako aplikace SaaS ve službě Azure AD. Není nutná síť VPN ani změna infrastruktury sítě.  

Další informace najdete v tématu [Jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](active-directory-application-proxy-get-started.md).

- - -
**Otázka: Jak můžu vyžadovat vícefaktorové ověřování pro uživatele, kteří používají určitou aplikaci?**

**Odpověď:** Díky podmíněnému přístupu ke službě Azure AD můžete ke každé aplikaci přiřadit jedinečné zásady přístupu. V zásadách můžete požadovat vícefaktorové ověřování pokaždé, nebo když uživatelé nejsou připojeni k místní síti.  

Další informace najdete v tématu [Zabezpečení přístupu k Office 365 a jiným aplikacím připojeným ke službě Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- - -
**Otázka: Co je automatické zřizování uživatelů pro aplikace SaaS?**

**Odpověď:** Pomocí služby Azure AD můžete automatizovat vytváření, údržbu a odebírání uživatelských identit v mnoha oblíbených cloudových aplikacích SaaS.

Další informace najdete v tématu [Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](active-directory-saas-app-provisioning.md).

- - -
**Otázka: Je možné vytvořit zabezpečené připojení LDAP se službou Azure Active Directory?**

**Odpověď:** Ne. Azure AD nepodporuje protokol LDAP.

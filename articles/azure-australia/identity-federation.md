---
title: Federace identit v Azure Austrálie
description: Pokyny týkající se konfigurace federace identit v rámci australských oblastí pro splnění konkrétních požadavků na zásady australské vlády, předpisy a právní předpisy.
author: galey801
ms.service: azure-australia
ms.topic: quickstart
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: e161e36ee7b403381b65f52a6f416be09025d0a2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570856"
---
# <a name="identity-federation-in-azure-australia"></a>Federace identit v Azure Austrálie

Správa identit a federace s nabídkami veřejných cloudů je jedním z nejdůležitějších prvních kroků pro používání cloudu. Služba Azure Active Directory Microsoftu ukládá informace o uživateli, aby umožnila přístup ke cloudovým službám a je předpokladem pro využívání jiných služeb Azure.

Tento článek popisuje klíčové body návrhu pro implementaci Azure Active Directory, zabezpečení uživatelů z Active Directory Domain Services domény a implementaci zabezpečeného ověřování. Konkrétní fokus je umístěný na doporučeních v australském Security Center internetovém a certifikaci Information Security Manual (ISM) a certifikace Azure.

Klasifikace informací uložených v rámci Azure Active Directory by měla informovat o rozhodnutích o tom, jak je navržena. Následující úryvek je k dispozici v [sestavě certifikace acsc – Microsoft Azure](https://aka.ms/au-irap):

>**Sestava certifikace acsc – Microsoft Azure** Azure Active Directory (Azure AD) je potřeba nakonfigurovat se službou AD FS (Active Directory Federation Services), když entity Společenství klasifikují použití a obsah dat jejich služby Active Directory v CHRÁNĚNÉm prostředí. I když data služby Active Directory s klasifikací UDLM (neklasifikované šíření) nevyžadují federaci, můžou entity Společenství stále implementovat federaci, aby se zmírnila rizika spojená se službou poskytovanými zvenčí. Austrálie.

V takovém případě jsou informace o tom, jaké informace se probíhají, a mechanismus, podle kterého se uživatelé ověřují, jsou zde uvedené dva důležité informace.

## <a name="key-design-considerations"></a>Klíčové faktory návrhu

### <a name="user-synchronisation"></a>Synchronizace uživatelů

Při nasazování Azure AD Connect existuje několik rozhodnutí, která je nutné provést v souvislosti s daty, která budou provedena do synchronizace. Azure AD Connect je založena na Microsoft Identity Manager a poskytuje robustní sadu funkcí pro transformaci [](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-best-practices-changing-default-configuration) dat mezi adresáři.

Služba konzultačních služeb společnosti Microsoft se může ADRAP vyhodnotit jako stávající služba Windows Server Active Directory. ADRAP pomáhá určit případné problémy, které může být potřeba opravit, než se bude synchronizace Azure Active Directory. Smlouvy Microsoft Premier Support budou obecně zahrnovat tuto službu.

[Nástroj IDFix](https://docs.microsoft.com/office365/enterprise/install-and-run-idfix) vyhledává problémy v místní doméně služby Active Directory před tím, než bude synchronizace s Azure AD. IDFix je klíčovým prvním krokem před implementací Azure AD Connect. I když může IDFix Scan identifikovat velký počet problémů, mnohé z těchto problémů se dají rychle vyřešit pomocí skriptů nebo s využitím transformačních dat v Azure AD Connect.

Azure AD vyžaduje, aby uživatelé měli externě směrovatelné domény na nejvyšší úrovni, aby umožnily ověřování. Pokud má vaše doména příponu hlavního názvu uživatele (UPN), která není externě směrovatelné, musíte nastavit [alternativní ID přihlášení](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname) ve službě AD Connect na atribut pošty uživatele. Uživatelé se pak přihlásí ke službám Azure pomocí své e-mailové adresy místo přihlášení k doméně.

Přípona hlavního názvu uživatele (UPN) u uživatelských účtů se dá také měnit pomocí nástrojů, jako je PowerShell, ale. může mít nepředvídatelné důsledky pro jiné připojené systémy a už se nepovažuje za osvědčené postupy.

Při rozhodování o tom, které atributy se mají Azure Active Directory, je bezpečnější předpokládat, že jsou vyžadovány všechny atributy. Adresář, který obsahuje skutečná CHRÁNĚNá data, je zřídka, ale doporučuje se provést audit. Pokud se v adresáři nacházejí CHRÁNĚNá data, vyhodnoťte dopad vynechání nebo transformace atributu. V této příručce najdete seznam atributů, které Microsoft Cloud služby [vyžadují](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized).

### <a name="authentication"></a>Ověřování

Je důležité porozumět možnostem, které jsou k dispozici, a jak je lze použít k zajištění zabezpečení koncových uživatelů.
Společnost Microsoft nabízí [tři nativní řešení](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) pro ověřování uživatelů proti Azure Active Directory:

* Synchronizace hodnot hash hesel – hesla s hashou z Active Directory Domain Services jsou Azure AD Connect do Azure Active Directory.
* [Předávací ověřování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) – hesla zůstávají v Active Directory Domain Services. Uživatelé se ověřují proti Active Directory Domain Services prostřednictvím agenta. V rámci služby Azure AD se neukládají žádná hesla.
* [FEDEROVANÉ jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-whatis) – Azure Active Directory je federované s Active Directory Federation Services (AD FS) během přihlašování Azure směruje uživatele na Active Directory Federation Services (AD FS) k ověření. V rámci služby Azure AD se neukládají žádná hesla.

Synchronizace hodnot hash hesel se dá použít ve scénářích, kde se v adresáři ukládají citlivé a níže uvedená data. Scénáře, ve kterých se ukládají CHRÁNĚNá data, budou vyžadovat jednu ze dvou zbývajících možností.

Všechny tři tyto možnosti podporují [zpětný zápis hesla](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback), který [acsc Consumer Guide](https://aka.ms/au-irap) doporučuje zakázat. Naopak organizace by měly vyhodnotit riziko zakázání zpětného zápisu hesla proti zvýšení produktivity a snížení úsilí podpory při používání samoobslužného resetování hesla.

#### <a name="pass-through-authentication-pta"></a>Předávací ověřování (PTA)

Předávací ověřování bylo vydáno po dokončení posouzení IRAP, a proto; měla by být individuálně vyhodnocena, aby bylo možné určit, jak řešení vyhovuje profilu rizika vaší organizace. Předávací ověřování je v rámci federace od společnosti Microsoft upřednostňováno kvůli vylepšení stav zabezpečení.

![Předávací ověřování](media/pta1.png)

Předávací ověřování představuje několik faktorů návrhu, které je potřeba vzít v úvahu:

* Agent předávacího ověřování musí být schopný navazovat odchozí připojení k Microsoft Cloud službám.
* Instalace více než jednoho agenta, aby bylo zajištěno, že bude služba vysoce dostupná. Osvědčeným postupem je nasadit aspoň tři agenty a maximálně 12 agentů.
* Osvědčeným postupem je zabránit instalaci agenta přímo do Doména služby Active Directory řadičů. Ve výchozím nastavení se při nasazení Azure AD Connect pomocí předávacího ověřování nainstaluje Agent na server služby AD Connect.
* Předávací ověřování je nižší možnost údržby než Active Directory Federation Services (AD FS), protože nevyžaduje vyhrazenou serverovou infrastrukturu, správu certifikátů nebo příchozí pravidla brány firewall.

#### <a name="active-directory-federation-services-adfs"></a>Active Directory Federation Services (AD FS) (ADFS)

Active Directory Federation Services (AD FS) bylo zahrnuto do posouzení IRAP a je schváleno pro použití v CHRÁNĚNých prostředích.

![Federace](media/federated-identity.png)

Active Directory Federation Services (AD FS) představuje několik faktorů návrhu, které je potřeba vzít v úvahu:

* Federační služby budou vyžadovat příchozí přenos dat HTTPS z Internetu nebo minimálních koncových bodů služby společnosti Microsoft.
* Federační služby využívají PKI a certifikáty, které vyžadují průběžnou správu a obnovování.
* Federační služby by se měly nasadit na vyhrazené servery a budou vyžadovat, aby příslušná síťová infrastruktura byla zabezpečeně přístupná externě.

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

Oddíl ISM na službě Multi-Factor Authentication doporučuje implementaci IT v následujících scénářích na základě vašeho profilu rizika:

* Ověřování standardních uživatelů
* Ověřování privilegovaných účtů
* Ověřování vzdáleného přístupu uživatelů
* Uživatelé s privilegovanými akcemi

Azure Active Directory poskytuje službu Multi-Factor Authentication, kterou je možné povolit buď pro všechny, nebo pro podmnožiny uživatelů (například jenom privilegované účty). Microsoft taky poskytuje řešení s názvem podmíněný přístup, které umožňuje podrobnější kontrolu nad tím, jak se používá vícefaktorové ověřování (například jenom v případě, že se uživatelé přihlásí ze vzdálených rozsahů IP adres).

Ověřování Azure Multi-Factor Authentication podporuje následující formy ISM přijatelné formy ověřování:

* Telefonní hovor
* Zpráva SMS
* Microsoft Authenticator aplikace
* Podporované hardwarové tokeny

Privileged Identity Management, součást Azure Active Directory, lze použít k vymáhání použití vícefaktorového ověřování, když uživatelé zvyšují oprávnění, aby splnili čtvrté doporučení.

## <a name="next-steps"></a>Další postup

Přečtěte si článek o [řízení přístupu na základě rolí a Privileged Identity Management](role-privileged.md).

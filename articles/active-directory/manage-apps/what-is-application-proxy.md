---
title: Publikování místních aplikací pomocí Proxy aplikace služby Azure AD
description: Zjistěte, proč použít Proxy aplikace publikovat místní webové aplikace externě na vzdálené uživatele. Další informace o Proxy aplikací architekturu, konektory, metody ověřování a výhody zabezpečení.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 05/31/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f23b20d460952ae582c292c8015851b9dc2ea98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108160"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Pomocí Azure AD Application Proxy publikovat místní aplikace pro koncové uživatele

Azure Active Directory (Azure AD) nabízí řadu funkcí pro ochranu uživatelů, aplikací a dat v cloudu i lokálně. Zejména funkci Proxy aplikací Azure AD může být implementována Odborníci v oblasti IT, kteří chtějí publikovat místní webové aplikace externě. Vzdálení uživatelé, kteří potřebují přístup k interním aplikacím můžete pak k nim přistupovat bezpečným způsobem.

Umožňuje bezpečný přístup k interním aplikacím z mimo vaši síť bude ještě důležitější součástí moderního pracoviště. Pomocí scénářů, jako je mobilní zařízení a modelu BYOD (přineste si vlastní zařízení) odborníky v oblasti ji nepotřebují splňují dva cíle:

* Umožněte koncoví uživatelé mohli pracovat kdykoli a kdekoli
* Chránit firemní prostředky po celou dobu

Mnoho organizací myslíte, že jsou v ovládacím prvku a chránit, když prostředky existovaly hranice jejich podnikové sítě. Ale v dnešních digitálních pracoviště, tuto hranici se rozšířila spravovaná mobilní zařízení a prostředků a služeb v cloudu. Teď budete muset spravovat složitost chrání identity vašich uživatelů a dat uložených na jejich zařízení a aplikací.

Možná už používáte Azure AD ke správě uživatelů v cloudu, kteří potřebují přístup k Office 365 a dalších aplikací SaaS, jakož i webové aplikace hostované v místním. Pokud už máte Azure AD, můžete je využít jako jeden ovládací prvek roviny umožňující bezproblémové a zabezpečený přístup k místním aplikacím. Nebo možná jste pořád zvažujete Přesun do cloudu. Pokud ano, můžete začít svou cestu do cloudu pomocí implementace Proxy aplikací a provádění první krok k vytváření základ využívá silné identity.

Přestože nejsou úplné, níže uvedeného seznamu znázorňuje některé věci, které můžete povolit pomocí implementace Proxy aplikace v hybridním scénáři koexistence:

* Externě publikovat místní webové aplikace v zjednodušený způsob bez DMZ
* Podpora jednotného přihlašování (SSO) v rámci zařízení, prostředky a aplikace v cloudu i lokálně
* Podpora vícefaktorového ověřování pro aplikace v cloudu i lokálně
* Rychle Využijte funkce cloudu se zabezpečením v cloudu společnosti Microsoft
* Centralizujte si správu účtu uživatele
* Centralizujte řízení identit a zabezpečení
* Automaticky přidat nebo odebrat přístup uživatelů k aplikacím na základě členství ve skupině

Tento článek vysvětluje, jak Azure AD a Proxy aplikací uživatelům vzdálené prostředí jednotného přihlašování (SSO). Uživatelé bezpečně připojovat k místním aplikacím bez sítě VPN nebo dvěma adresami, servery a pravidla brány firewall. Tento článek vám pomůže pochopit jak Proxy aplikací přináší funkce a výhody zabezpečení cloudu do místních webových aplikací. Také popisuje architekturu a topologie, které jsou možné.

## <a name="remote-access-in-the-past"></a>Vzdálený přístup v minulosti

Vaše rovina řízení pro ochranu interní prostředky z útočníci při současném usnadnění přístupu vzdálení uživatelé byl předtím všechno v hraniční síti nebo hraniční síti. Ale připojení VPN a nasazený v hraniční síti používají klienti externí přístup k firemním prostředkům řešení reverzního proxy serveru nejsou vhodné ve světě cloudu. Obvykle dochází z těchto nevýhod:

* Náklady na hardware
* Správa zabezpečení (použití dílčích oprav, sledování portů, atd.)
* Ověřování uživatelů na hraničních zařízeních
* Ověřování uživatelů do webových serverů v hraniční síti
* Správa přístup k síti VPN pro vzdálené uživatele s distribuce a konfigurací softwaru klienta VPN. Navíc údržby serverů připojených k doméně v hraniční síti, což může být zranitelný vůči zvenku.

V dnešním světě upřednostňujícím Azure AD je nejvhodnější pro řídit, kdo a co dostane do vaší sítě. Proxy aplikací Azure AD se integruje s moderním ověřováním a cloudové technologie, jako jsou aplikace SaaS a zprostředkovatele identity. Tato integrace umožňuje uživatelům přístup k aplikacím z libovolného místa. Nejen se Proxy aplikací více vhodných pro dnešních digitálních síti na pracovišti, je bezpečnější než řešení reverzního proxy serveru a sítě VPN a usnadnil. Vzdálení uživatelé můžou přístup k místním aplikacím stejným způsobem jako aplikace O365 a další aplikace SaaS integrované s Azure AD. Není potřeba změnit nebo aktualizovat vaše aplikace pro práci s Proxy aplikací. Kromě toho Proxy aplikací není nutné otevřít příchozí připojení přes bránu firewall. S App Proxy stačí ho nastavit a ho zapomenete.

## <a name="the-future-of-remote-access"></a>Budoucnost vzdáleného přístupu

V dnešních digitálních pracoviště uživatelé kdekoli pracovat s více zařízení a aplikací. Pouze konstantní je identita uživatele. Proto dnes je prvním krokem k zabezpečené sítě použití [správy identit Azure AD](https://docs.microsoft.com/azure/security/security-identity-management-overview) funkce jako vaše rovina řízení zabezpečení. Model, který se použije identitu jako vaše rovina řízení se obvykle skládá z následujících součástí:

* Ke sledování uživatelů a informace týkající se uživatelů zprostředkovatele identity.
* Adresář zařízení k údržbě seznamu zařízení, která mají přístup k firemním prostředkům. Tento adresář obsahuje odpovídající informace o zařízení (například typ zařízení, integritu atd.).
* Služba hodnocení zásady k určení, zda zařízení a uživatele odpovídá zásadám stanovených ve Správci zabezpečení.
* Možnost udělit nebo odepřít přístup k prostředkům organizace.

Pomocí Proxy aplikace Azure AD sleduje uživatelů, kteří potřebují přístup k webové aplikace publikované v místním prostředí i v cloudu. Pro tyto aplikace poskytuje bod centrální správy. Přestože se nevyžaduje, doporučujeme že také povolit podmíněný přístup Azure AD. Tak, že definujete podmínky pro jak ověřovat uživatele a získat přístup, dále zkontrolujte, že lidé mít přístup k aplikacím.

**Poznámka:** Je důležité pochopit, že Azure AD Application Proxy slouží jako síť VPN nebo nahrazení reverzního proxy serveru pro roaming (nebo vzdálené), kteří potřebují přístup k interním prostředkům. Není určena pro interní uživatele v podnikové síti. Interní uživatelé, kteří zbytečně pomocí Proxy aplikace může způsobovat problémy s výkonem neočekávané a nežádoucí.

![Azure Active Directory a všechny vaše aplikace](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Přehled toho, jak funguje Proxy aplikací

Proxy aplikací je služba Azure AD, které nakonfigurujete na portálu Azure portal. Umožňuje publikovat externí koncový bod veřejné adresy URL protokolu HTTP/HTTPS v cloudu Azure, který se připojuje k adresu URL serveru interní aplikace ve vaší organizaci. Tyto místní webové aplikace je možné integrovat s Azure AD pro podporu jednotného přihlašování. Koncoví uživatelé můžou potom přístup k místním webové aplikace stejným způsobem, jakým přistupují k Office 365 a dalším aplikacím SaaS.

Součástí této funkce zahrnují službu Proxy aplikací, která běží v cloudu, konektor Proxy aplikací, které je zjednodušené agent, který běží na místním serveru a Azure AD, která je zprostředkovatel identity. Všechny tři součásti společně poskytují uživatele s prostředím jednotné přihlašování pro přístup k místním webovým aplikacím.

Po přihlášení externí uživatelé mají přístup ke místních webových aplikací pomocí známých adresu URL nebo [MyApps přístupového panelu](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) ze svých zařízení stolní počítač nebo iOS/MAC. Například Proxy aplikací můžete poskytovat vzdálený přístup a jednotné přihlašování ve službě vzdálené klientské počítače, SharePoint servery, Tableau, Qlik, aplikace Outlook na webu a -obchodní aplikace (LOB).

![Architektura služby Azure AD Application Proxy](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Authentication

Existuje několik způsobů, jak nakonfigurovat aplikaci pro jednotné přihlašování a metoda, kterou vyberete, závisí na ověřování, které vaše aplikace používá. Proxy aplikací podporuje následující typy aplikací:

* Webové aplikace
* Webové rozhraní API, která chcete k tomu, aby bohaté aplikace na různých zařízeních
* Aplikací hostovaných za službou Brána vzdálené plochy
* Bohaté klientských aplikací, které jsou integrovány s Active Directory Authentication Library (ADAL)

Proxy aplikací funguje s aplikacemi, které používají následující nativního ověřovacího protokolu:

* **[Integrované ověřování Windows (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** Pro IWA konektory Proxy aplikací pomocí protokolu Kerberos omezené delegování (KCD) k ověřování uživatelů k aplikaci pomocí protokolu Kerberos.

Proxy aplikací také podporuje následující protokoly pro ověřování pomocí integrace třetích stran nebo v konkrétní scénáře konfigurace:

* [**Ověřování založené na hlavičkách**](application-proxy-configure-single-sign-on-with-ping-access.md). Tato metoda přihlašování využívá ověřování třetích stran služby zvané PingAccess a se používá, když aplikace používá hlavičky pro ověřování. V tomto scénáři ověřování zařizuje služba PingAccess.
* [**Ověřování pomocí formulářů nebo heslo**](application-proxy-configure-single-sign-on-password-vaulting.md). Pomocí této metody ověřování uživatelům přihlásit se k aplikaci pomocí uživatelského jména a hesla při prvním přístupu k jeho. Po první přihlašování Azure AD poskytuje uživatelské jméno a heslo k aplikaci. V tomto scénáři ověřování zařizuje služba Azure AD.
* [**Ověřování SAML**](application-proxy-configure-single-sign-on-on-premises-apps.md). Založené na SAML jednotného přihlašování se podporuje pro aplikace, které používají protokol SAML 2.0 nebo WS-Federation protokoly. Služba Azure AD se ověřuje pomocí SAML jednotného přihlašování, k aplikaci pomocí účtu uživatele Azure AD.

Další informace o podporovaných metod najdete v tématu [volba jedinou metodu přihlašování](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Výhody zabezpečení

Řešení vzdáleného přístupu, které nabízí Proxy aplikací a službou Azure AD podporují několik výhod zabezpečení, které zákazníci můžou využít výhod, včetně:

* **Ověřený přístup**. Je nejvhodnější pro publikování aplikací pomocí Proxy aplikací [předběžné ověření](application-proxy-security.md#authenticated-access) zajistit, že jenom ověřené připojení přístupů vaší sítě. Pro aplikace publikované pomocí předběžné ověření může předat prostřednictvím služby Proxy aplikace v místním prostředí, bez platného tokenu žádný provoz. Předběžné ověření, ze své podstaty blokuje velký počet cílenými útoky, jako pouze ověření identity můžou přistupovat k back-end aplikace.
* **Podmíněný přístup**. Ovládací prvky bohatší zásady je možné použít předtím, než se naváže připojení k síti. Pomocí podmíněného přístupu můžete definovat omezení při přenosu dat, díky kterým můžou volat back-end aplikace. Můžete vytvořit zásady, které omezují přihlášení podle umístění, síla ověření a profil rizika pro uživatele. Jak podmíněný přístup vyvíjí, se neustále přidávají další ovládací prvky pro zvýšení zabezpečení jako je například integrace s Cloud App Security (MCAS). MCAS integrace vám umožní nakonfigurovat místní aplikaci pro [monitorování v reálném čase](application-proxy-integrate-with-microsoft-cloud-application-security.md) díky využití podmíněného přístupu pro monitorování a řízení relace v reálném čase podle zásady podmíněného přístupu.
* **Ukončení provozu**. Veškerý provoz do back-end aplikace se ukončuje na službu Proxy aplikací v cloudu, zatímco relace se znovu naváže s back-end serveru. Tato strategie připojení znamená, že back-endu, který servery nejsou zveřejněné směrovat přenos dat protokolu HTTP. Jsou lépe chráněni před cílenými útoky DoS (denial-of-service) vzhledem k tomu, že brána firewall není terčem útoku.
* **Veškerý přístup probíhá odchozí**. Konektory Proxy aplikací pouze pomocí odchozí připojení ke službě Proxy aplikací v cloudu přes porty 80 a 443. Bez příchozí připojení není nutné otevřít porty brány firewall pro příchozí připojení nebo komponenty v hraniční síti. Všechna připojení jsou odchozí a přes zabezpečený kanál.
* **Zabezpečení analýzy a Machine Learning (ML) na základě intelligence**. Protože je součástí Azure Active Directory, můžou využívat Proxy aplikací [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview) (vyžaduje [licencování Premium P2](https://azure.microsoft.com/pricing/details/active-directory/)). Azure AD Identity Protection kombinuje inteligentního zabezpečení strojového učení s informačními kanály dat od společnosti Microsoft [Digital Crimes Unit](https://news.microsoft.com/stories/cybercrime/index.html) a [Microsoft Security Response Center](https://www.microsoft.com/msrc) k aktivnímu určení ohrožením účtů. Identity Protection nabízí ochranu v reálném čase od vysoce rizikových přihlášení. Bere v úvahu faktorů, jako je přístup z nakažených zařízení prostřednictvím anonymizace sítě, nebo z umístění atypické a pravděpodobně ke zvýšení profilu rizika v relaci. Toto riziko profil se používá pro ochranu v reálném čase. Mnohé z těchto sestav a události jsou již k dispozici prostřednictvím rozhraní API pro integraci se systémy SIEM.

* **Vzdáleného přístupu jako služba**. Nemusíte se starat o údržbu a opravy chyb a povolte vzdálený přístup na místních serverech. Proxy aplikací je škálování služby, který vlastní Microsoft, takže vždycky získáte nejnovější opravy a upgrady. Bez opravy zabezpečení softwaru stále účty pro velký počet útoků. Podle ministerstva vnitřní bezpečnosti USA, tolik [85 procent společností z žebříčku cílenými útoky jsou mohli](https://www.us-cert.gov/ncas/alerts/TA15-119A). V tomto modelu služby není nutné provádět náročné břemeno správy serverů edge už a promíchání o opravu je podle potřeby.

* **Integrace s Intune**. S Intune podnikové provoz směruje odděleně od osobních provozu. Proxy aplikací zajistí, že je ověřený podnikové provoz. [Proxy aplikací a Intune Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) funkce je také možné společně umožňuje vzdáleným uživatelům bezpečný přístup k interní weby s Iosem a androidem.

### <a name="roadmap-to-the-cloud"></a>Plán služby do cloudu

Další hlavní výhodou implementace Proxy aplikací je rozšíření Azure AD, aby v místním prostředí. Implementace Proxy aplikace se ve skutečnosti klíče krok ve vaší organizaci a aplikací přesouvá do cloudu. Díky přesunu do cloudu a od místního ověřování, snížit nároky na vaše místní a použít možnosti správy identit Azure AD jako vaše rovina řízení. S minimální nebo žádné aktualizace do stávající aplikace, máte přístup ke cloudovým funkcí, jako je jednotné přihlašování, vícefaktorové ověřování a centrální správy. Instalace nezbytné součásti na Proxy aplikací je jednoduchý proces pro vytvoření rozhraní vzdáleného přístupu. A díky přesunu do cloudu, mají přístup k nejnovějším funkcím služby Azure AD, aktualizace a funkce, jako je vysoká dostupnost a zotavení po havárii.

Další informace o migraci vašich aplikací do Azure AD, najdete v článku [migrace aplikací v Azure Active Directory](https://aka.ms/migrateapps/whitepaper) dokument white paper.

## <a name="architecture"></a>Architektura

Následující diagram ukazuje obecné služby ověřování Azure AD a work Proxy aplikací. společně k dispozici jednotné přihlašování k místním aplikacím koncovým uživatelům.

![Tok ověřování Azure AD Application Proxy](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Poté, co uživatel má aplikace prostřednictvím koncového bodu, bude uživatel přesměrován na přihlašovací stránce služby Azure AD. Pokud jste nakonfigurovali zásady podmíněného přístupu, určité podmínky jsou kontrolovány v tuto chvíli k zajištění, že budete jednat v souladu s požadavky na zabezpečení vaší organizace.
2. Azure AD poté, co úspěšném přihlášení se odešle token uživatele klientského zařízení.
3. Klient odešle token do služby Proxy aplikace, která načte hlavní název uživatele (UPN) a název objektu zabezpečení (SPN) z tokenu.
4. Proxy aplikace předá tento požadavek, který převezme Proxy aplikací [konektor](application-proxy-connectors.md).
5. Konektor provádí další ověřování vyžaduje jménem uživatele (*volitelně v závislosti na metodě ověřování*), vyžádá vnitřní koncový bod aplikačního serveru a odešle požadavek na místní aplikace.
6. Odpověď ze serveru aplikace se odesílá prostřednictvím konektoru Proxy aplikace služby.
7. Odpověď se odesílají ze služby Proxy aplikace pro uživatele.

|**Komponenta**|**Popis**|
|:-|:-|
|Koncový bod|Koncový bod je adresa URL nebo [portálu pro koncové uživatele](end-user-experiences.md). Uživatelé mohli spojit aplikace během mimo vaši síť díky přístupu do externí adresu URL. Uživatelé ve vaší síti přístup k aplikaci pomocí adresy URL nebo portálu pro koncové uživatele. Uživatelé přejít na jednu z těchto koncových bodů, ověřování ve službě Azure AD a pak se směrují prostřednictvím konektoru pro místní aplikace.|
|Azure AD|Azure AD provádí ověřování pomocí adresář tenanta uložená v cloudu.|
|Služba Proxy aplikace|Tato služba Proxy aplikace se spouští v cloudu jako součást služby Azure AD. Token přihlášení od uživatele předá do konektoru Proxy aplikace. Proxy aplikace předá jakékoli přístupné hlavičky v požadavku a nastaví záhlaví podle jeho protokol IP adresu klienta. Pokud je příchozí požadavek na server proxy už tuto hlavičku, IP adresa klienta se přidá na konec seznamu čárkami, který je hodnota hlavičky.|
|Konektor Proxy aplikace|Konektor je zjednodušené agent, který běží na Windows serveru ve vaší síti. Konektor skladuje komunikaci mezi službou Proxy aplikací v cloudu a místních aplikací. Konektor využívá jenom odchozí připojení, proto není nutné otevírat žádné příchozí porty ani umisťovat cokoliv hraniční sítě. Konektory jsou bezstavové a aktivního získávání informací z cloudu podle potřeby. Další informace o konektorech, jako je způsob jejich – nástroj pro vyrovnávání zatížení a ověření naleznete v tématu [pochopit Azure AD Application Proxy konektory](application-proxy-connectors.md).|
|Active Directory (AD)|Služba Active Directory spustí v místním provádět ověřování pro doménové účty. Když jednotného přihlašování je nakonfigurován, konektoru komunikuje se službou AD provádět žádné další ověření vyžaduje.|
|Místní aplikace|Uživatel je nakonec mít přístup k místní aplikaci.|

Proxy aplikací Azure AD se skládá z Proxy aplikací služby založené na cloudu a místního konektoru. Konektor naslouchá požadavkům Proxy aplikace služby a připojení k interní aplikace zpracovává. Je důležité si uvědomit, že veškerá komunikace probíhá přes SSL a vždy pocházejí z konektoru Proxy aplikace služby. To znamená, že komunikace je odchozí pouze. Konektor používá klientský certifikát k ověření služby Proxy aplikace pro všechna volání. K jediné výjimce zabezpečení připojení je krok počáteční nastavení, kde pokládáme stav klientského certifikátu. Proxy aplikací najdete v článku [pod pokličkou](application-proxy-security.md#under-the-hood) další podrobnosti.

### <a name="application-proxy-connectors"></a>Konektory Proxy aplikací

[Konektory Proxy aplikací](application-proxy-connectors.md) zjednodušené agentů nasazených v místním, které usnadňují odchozí připojení ke službě Proxy aplikací v cloudu. Konektory musí být nainstalována v systému Windows Server, který má přístup k back-end aplikace. Uživatelé připojit ke cloudové službě Proxy aplikace této trasy, provoz do aplikací pomocí konektorů, jako je znázorněno níže.

![Připojení k síti Azure AD Application Proxy](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Instalace a registrace až konektor Proxy aplikace služby se provádí následujícím způsobem:

1. Správce IT se otevře porty 80 a 443 pro odchozí provoz a umožňuje přístup k několika adresám URL, které jsou potřeba pro konektor, Proxy aplikace služby a služby Azure AD.
2. Správce přihlásí na web Azure Portal a spouští spustitelný soubor k instalaci konektoru na místní Windows server.
3. Konektor se začne "sledovat" ve službě Proxy aplikací.
4. Správce přidá místní aplikaci do služby Azure AD a konfiguruje nastavení, jako je adresy URL uživatelé potřebují k připojení do svých aplikací.

Další informace najdete v tématu [plánování nasazení služby Azure AD Application Proxy](application-proxy-deployment-plan.md).

Doporučujeme vždy nasadit více konektorů pro redundanci a škálování. Konektory ve spojení se službou, postará o jednotlivých úlohách vysoké dostupnosti a může být přidat či odebrat dynamicky. Pokaždé, když přijde nový požadavek přesměruje ho na jednu z konektorů, které je k dispozici. Konektor je spuštěný, zůstane aktivní jak připojí ke službě. Pokud konektor je dočasně nedostupný, nebude reagovat na tento provoz. Nepoužité konektory jsou označeni jako neaktivní a odebrat po 10 dnů nečinnosti.

Konektory také dotazování serveru a zjistěte, jestli je dostupná novější verze konektoru. Ačkoli je možné provést ruční aktualizaci, bude automaticky aktualizovat konektory, tak dlouho, dokud se službou Application Proxy Connector Updater. U klientů s více konektorů jako cíl automatické aktualizace jeden konektor v daný okamžik v každé skupině redukován ve vašem prostředí.

**Poznámka:** Proxy aplikace můžete monitorovat [stránky s historií verze](application-proxy-release-version-history.md) upozornění, až se přihlásíte k jeho informačním kanálu RSS odběru jsme vydali aktualizace.

Každý konektor Proxy aplikací je přiřazen [skupina konektorů](application-proxy-connector-groups.md). Konektory ve stejné skupině konektoru bude fungovat jako jednu jednotku pro zajištění vysoké dostupnosti a vyrovnávání zatížení. Můžete vytvářet nové skupiny přiřadit konektory k nim na webu Azure Portal a pak přiřadit konkrétní konektory k poskytování určité aplikace. Doporučuje se mít aspoň dva konektory v každé skupině konektoru pro vysokou dostupnost.

Skupiny konektorů jsou užitečné, když budete potřebovat pro podporu následujících scénářů:

* Publikování zeměpisné aplikací
* Segmentace/izolace aplikací
* Publikování webové aplikace běžící v cloudu nebo místně

Další informace o výběru místa pro instalaci konektoru a optimalizace sítě, naleznete v tématu [aspekty topologie sítě, při použití Azure Active Directory Application Proxy](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Ostatní případy použití

Do této chvíle jsme se zaměřovali na externě publikování místních aplikací při povolení jednotného přihlašování do všech vašich cloudových a místních aplikací pomocí Proxy aplikací. Existují však další případy použití pro Proxy aplikací, které stojí za zmínku. Mezi ně patří:

* **Bezpečně publikovat rozhraní API REST**. Když budete mít obchodní logikou nebo rozhraní API s místní nebo hostovaný na virtuálních počítačích v cloudu, Proxy aplikací poskytuje veřejný koncový bod pro přístup k rozhraní API. Přístup přes koncový bod rozhraní API umožňuje řízení ověřování a autorizace nevyžaduje příchozí porty. Poskytuje dodatečné zabezpečení prostřednictvím funkce Azure AD Premium, jako je vícefaktorové ověřování a podmíněného přístupu na základě zařízení pro stolní počítače, iOS, MAC a zařízení s Androidem v Intune. Další informace najdete v tématu [jak povolit nativní klientské aplikace pro interakci s proxy aplikace](application-proxy-configure-native-client-application.md) a [ochrana rozhraní API s použitím OAuth 2.0 s Azure Active Directory a API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad).
* **Vzdálená plocha** **(RDS)** . Standardní nasazení vzdálené plochy vyžaduje otevřené příchozí připojení. Ale [nasazení služby Vzdálená plocha s Proxy aplikací](application-proxy-integrate-with-remote-desktop-services.md) má trvalé odchozí připojení ze serveru se spuštěnou službou konektoru. Díky tomu můžou nabízet další aplikace koncovým uživatelům v publikování místních aplikací prostřednictvím služby Vzdálená plocha. Může také omezit prostor k napadení nasazení s omezenou sadu dvoustupňové ověřování a řízení podmíněného přístupu pro vzdálené plochy
* **Publikování aplikací, které se připojují, používá objekty Websocket**. Podpora s [Qlik Sense](application-proxy-qlik.md) je ve verzi Public Preview a bude v budoucnu rozšířena do ostatních aplikací.
* **Povolit nativní klientské aplikace pro interakci s proxy aplikace**. Azure AD Application Proxy můžete použít k publikování webových aplikací, ale je také lze použít k publikování [nativní klientské aplikace](application-proxy-configure-native-client-application.md) , které jsou nakonfigurované se Azure AD Authentication Library (ADAL). Nativní klientské aplikace se liší od webové aplikace, protože jejich instalaci na zařízení, zatímco webové aplikace jsou přístupné prostřednictvím prohlížeče.

## <a name="conclusion"></a>Závěr

Způsob, jakým způsobem pracujeme a nástroje, které používáme se rychle mění. Další přináší svá vlastní zařízení práci zaměstnanců a rozšířená použití Software-as-a-Service (SaaS) aplikací způsob, jak organizace přistupují ke správě a zabezpečení svých dat musí také vyvíjet. Společnosti nebude fungovat pouze v rámci své vlastní stěn chráněn moat, obklopující jejich ohraničení. Data se přenáší na více místech než kdy dřív – napříč místními a cloudovými prostředími. Tomuto vývoji pomohlo zvýšit produktivitu uživatelů a možnost spolupráce, ale také umožní ochranu citlivých dat ještě náročnější.

Ať už v tuto chvíli používáte Azure AD ke správě uživatelů v hybridním scénáři koexistence nebo mají zájem o spuštění vaší cestě ke cloudu, implementace Proxy aplikací Azure AD může pomoct snížit velikost vaší místní působnosti tím, že poskytuje vzdálený přístup k jako služba.

Organizace by měly začít využívat Proxy aplikací ještě dnes a využijte výhod následující výhody:

* Publikování aplikací v místním externě bez režie spojené s údržbu tradiční VPN nebo jiné místní webové publikování řešení a přístup k hraniční síti
* Jednotné přihlašování ke všem aplikacím, mohou to být Office 365 nebo dalšími aplikacemi SaaS a místních aplikací, včetně
* Škálování zabezpečení, kam Azure AD využívá telemetrická data Office 365 k zabránění neoprávněného přístupu v cloudu
* Ověření k zajištění provozu podnikové integrace s Intune
* Centralizace správy uživatelských účtů
* Automatické aktualizace zkontrolujte, že jste nainstalovány nejnovější opravy zabezpečení
* Nové funkce při jejich vydání; nejnovější se podpora pro jednotné přihlašování SAML a podrobnější řízení aplikace soubory cookie

## <a name="next-steps"></a>Další postup

* Informace o plánování, provoz a správu Azure AD Application Proxy, naleznete v tématu [plánování nasazení služby Azure AD Application Proxy](application-proxy-deployment-plan.md).
* Naplánovat na živou ukázku nebo získat bezplatné 90denní zkušební období pro hodnocení, naleznete v tématu [Začínáme s Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial).

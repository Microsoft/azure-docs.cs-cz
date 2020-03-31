---
title: Publikování místních aplikací pomocí proxy aplikací Azure AD
description: Zjistěte, proč používat proxy aplikace k externímu publikování místních webových aplikací vzdáleným uživatelům. Seznamte se s architekturou proxy aplikací, konektory, metodami ověřování a výhodami zabezpečení.
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
ms.openlocfilehash: 7047dfd0f02ffe95dcacfdf4ddc014047a338513
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79481190"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Použití proxy aplikací Azure AD k publikování místních aplikací pro vzdálené uživatele

Azure Active Directory (Azure AD) nabízí mnoho funkcí pro ochranu uživatelů, aplikací a dat v cloudu a místně. Zejména funkci Proxy aplikace Azure AD mohou implementovat odborníci v oblasti IT, kteří chtějí publikovat místní webové aplikace externě. Vzdálení uživatelé, kteří potřebují přístup k interním aplikacím, k nim pak mohou přistupovat bezpečným způsobem.

Možnost bezpečného přístupu k interním aplikacím mimo vaši síť se stává na moderním pracovišti ještě kritičtější. Se scénáři, jako je BYOD (Bring Your Own Device) a mobilnízařízení, it profesionálové jsou vyzváni ke splnění dvou cílů:

* Ušetřte koncovým uživatelům produktivitu kdykoli a kdekoli
* Ochrana firemních aktiv za všech okolností

Mnoho organizací věří, že jsou pod kontrolou a chráněny, pokud prostředky existují v rámci svých podnikových sítí. Na dnešním digitálním pracovišti se však tato hranice rozšířila o spravovaná mobilní zařízení a zdroje a služby v cloudu. Nyní musíte spravovat složitost ochrany identit a dat uživatelů uložených v jejich zařízeních a aplikacích.

Možná už používáte Azure AD ke správě uživatelů v cloudu, kteří potřebují přístup k Office 365 a dalším aplikacím SaaS, stejně jako webové aplikace hostované místně. Pokud už máte Azure AD, můžete ji využít jako jednu rovinu ovládacího prvku, která vám umožní bezproblémový a zabezpečený přístup k místním aplikacím. Nebo možná stále uvažujete o přesunu do cloudu. Pokud ano, můžete začít svou cestu do cloudu implementací proxy aplikace a provedením prvního kroku k vytvoření silného základu identity.

I když není komplexní, níže uvedený seznam ilustruje některé věci, které můžete povolit implementací proxy aplikace v hybridním scénáři koexistence:

* Publikování místních webových aplikací externě zjednodušeným způsobem bez DMZ
* Podpora jednotného přihlašování (SSO) napříč zařízeními, prostředky a aplikacemi v cloudu i v místním prostředí
* Podpora vícefaktorového ověřování pro aplikace v cloudu i v místním prostředí
* Rychlé využití cloudových funkcí se zabezpečením Microsoft Cloudu
* Centralizace správy uživatelských účtů
* Centralizovat kontrolu identity a zabezpečení
* Automatické přidávání nebo odebrání přístupu uživatelů k aplikacím na základě členství ve skupině

Tento článek vysvětluje, jak Azure AD a proxy aplikace poskytují vzdáleným uživatelům jednotné přihlašování (SSO) prostředí. Uživatelé se bezpečně připojují k místním aplikacím bez vpn nebo serverů s dvěma místy a pravidel brány firewall. Tento článek vám pomůže pochopit, jak proxy aplikace přináší možnosti a výhody zabezpečení cloudu do místních webových aplikací. Popisuje také architekturu a topologie, které jsou možné.

## <a name="remote-access-in-the-past"></a>Vzdálený přístup v minulosti

Dříve byla vaše řídicí rovina pro ochranu interních prostředků před útočníky a zároveň usnadnění přístupu vzdálenými uživateli v dmz nebo hraniční síti. Ale vpn a reverzní proxy řešení nasazená v DMZ používaná externími klienty pro přístup k podnikovým prostředkům nejsou vhodná pro cloudový svět. Obvykle trpí následujícími nevýhodami:

* Hardwarové náklady
* Udržování zabezpečení (opravy, monitorovací porty atd.)
* Ověřování uživatelů na hraničních zařízeních
* Ověřování uživatelů na webových serverech v hraniční síti
* Udržování přístupu vpn pro vzdálené uživatele s distribucí a konfigurací klientského softwaru VPN. Také udržování serverů spojených s doménou v DMZ, které mohou být zranitelné vůči vnějším útokům.

V dnešním světě prvního cloudu je Azure AD nejvhodnější pro řízení toho, kdo a co se dostane do vaší sítě. Azure AD Application Proxy se integruje s moderními ověřováníami a cloudovými technologiemi, jako jsou aplikace SaaS a poskytovatelé identit. Tato integrace umožňuje uživatelům přístup k aplikacím odkudkoli. Nejen, že je App Proxy vhodnější pro dnešní digitální pracoviště, je to bezpečnější než VPN a reverzní proxy řešení a snadněji implementovat. Vzdálení uživatelé mají přístup k místním aplikacím stejným způsobem jako při stupňující se k O365 a dalším aplikacím SaaS integrovaným ve službě Azure AD. Aby aplikace fungovaly s Proxy aplikací, nemusíte je měnit nebo aktualizovat. Kromě toho proxy aplikace nevyžaduje, abyste otevírali příchozí připojení přes bránu firewall. S App Proxy, stačí nastavit a zapomenout.

## <a name="the-future-of-remote-access"></a>Budoucnost vzdáleného přístupu

Na dnešním digitálním pracovišti uživatelé pracují kdekoli s více zařízeními a aplikacemi. Jedinou konstantou je identita uživatele. To je důvod, proč první krok k zabezpečené síti dnes je použití azure [ad je správa identit](https://docs.microsoft.com/azure/security/security-identity-management-overview) funkce jako vaše řízení zabezpečení roviny. Model, který používá identitu jako rovinu ovládacího prvku, se obvykle skládá z následujících součástí:

* Zprostředkovatel identity pro sledování uživatelů a informací souvisejících s uživatelem.
* Adresář zařízení pro údržbu seznamu zařízení, která mají přístup k podnikovým prostředkům. Tento adresář obsahuje odpovídající informace o zařízení (například typ zařízení, integrita atd.).
* Služba vyhodnocení zásad k určení, zda uživatel a zařízení odpovídá zásadám stanoveným správci zabezpečení.
* Možnost udělit nebo odepřít přístup k organizačním prostředkům.

S proxy aplikací Azure AD udržuje informace o uživatelích, kteří potřebují přístup k webovým aplikacím publikovaným místně a v cloudu. Poskytuje centrální bod správy pro tyto aplikace. I když to není povinné, doporučujeme také povolit podmíněný přístup Azure AD. Definováním podmínek pro ověřování uživatelů a získání přístupu dále zajistíte, aby k aplikacím měli přístup ti praví uživatelé.

**Poznámka:** Je důležité si uvědomit, že proxy aplikace Azure AD je určena jako VPN nebo reverzní proxy nahrazení pro roamingové (nebo vzdálené) uživatele, kteří potřebují přístup k interním prostředkům. Není určen pro interní uživatele v podnikové síti. Interní uživatelé, kteří zbytečně používají proxy aplikace, mohou způsobit neočekávané a nežádoucí problémy s výkonem.

![Azure Active Directory a všechny vaše aplikace](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Přehled fungování proxy aplikace

Proxy aplikace je služba Azure AD, kterou nakonfigurujete na webu Azure Portal. Umožňuje publikovat externí veřejný koncový bod HTTP/HTTPS URL v Azure Cloudu, který se připojuje k adrese URL interního aplikačního serveru ve vaší organizaci. Tyto místní webové aplikace lze integrovat s Azure AD pro podporu jednotného přihlášení. Koncoví uživatelé pak mohou přistupovat k místním webovým aplikacím stejným způsobem jako k Office 365 a dalším aplikacím SaaS.

Součásti této funkce zahrnují službu Proxy aplikace, která běží v cloudu, konektor proxy aplikace, což je lehký agent, který běží na místním serveru, a Azure AD, což je zprostředkovatel identity. Všechny tři součásti spolupracují, aby uživateli poskytly jednotné přihlašování pro přístup k místním webovým aplikacím.

Po přihlášení mohou externí uživatelé přistupovat k místním webovým aplikacím pomocí známé adresy URL nebo [přístupového panelu MyApps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) ze svých stolních nebo iOS/MAC zařízení. Proxy aplikace může například poskytovat vzdálený přístup a jednotné přihlašování ke vzdálené ploše, webům Služby SharePoint, Tableau, Qlik, Outlook u webu a obchodním (LOB) aplikacím.

![Architektura proxy aplikací Azure AD](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Ověřování

Existuje několik způsobů konfigurace aplikace pro jednotné přihlašování a vybraná metoda závisí na ověřování, které aplikace používá. Proxy aplikace podporuje následující typy aplikací:

* Webové aplikace
* Webová api, která chcete vystavit bohatým aplikacím na různých zařízeních
* Aplikace hostované za bránou vzdálené plochy
* Rozšířené klientské aplikace integrované s knihovnou ADAL (Active Directory Authentication Library)

Proxy aplikace funguje s aplikacemi, které používají následující nativní ověřovací protokol:

* **[Integrované ověřování systému Windows (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** Pro IWA konektory proxy aplikace používají kerberos omezené delegování (KCD) k ověření uživatelů do aplikace Kerberos.

Proxy aplikace také podporuje následující ověřovací protokoly s integrací třetích stran nebo v konkrétních scénářích konfigurace:

* [**Ověřování založené na záhlaví**](application-proxy-configure-single-sign-on-with-ping-access.md). Tato metoda přihlášení používá ověřovací službu jiného výrobce s názvem PingAccess a používá se, když aplikace používá hlavičky pro ověřování. V tomto scénáři ověřování zpracovává PingAccess.
* [**Ověřování pomocí formulářů nebo hesel**](application-proxy-configure-single-sign-on-password-vaulting.md). Pomocí této metody ověřování se uživatelé přihlašují k aplikaci pomocí uživatelského jména a hesla při prvním přístupu. Po prvním přihlášení Azure AD poskytuje uživatelské jméno a heslo do aplikace. V tomto scénáři ověřování zpracovává Azure AD.
* [**Saml ověřování**](application-proxy-configure-single-sign-on-on-premises-apps.md). Jednotné přihlašování založené na saml je podporováno pro aplikace, které používají protokoly SAML 2.0 nebo WS-Federation. S saml jednotné přihlašování Azure AD ověřuje k aplikaci pomocí účtu Azure AD uživatele.

Další informace o podporovaných metodách naleznete [v tématu Výběr metody jednotného přihlášení](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Výhody v oblasti zabezpečení

Řešení vzdáleného přístupu nabízené proxy aplikací a podporou Azure AD podporuje několik výhod zabezpečení, které mohou zákazníci využívat, včetně:

* **Ověřený přístup**. Proxy aplikace je nejvhodnější pro publikování aplikací s [předběžným ověřováním,](application-proxy-security.md#authenticated-access) aby bylo zajištěno, že do vaší sítě narazí pouze ověřená připojení. U aplikací publikovaných s předověřením není povoleno předávat službu Proxy aplikace do místního prostředí bez platného tokenu žádný provoz. Předběžné ověření ze své podstaty blokuje významný počet cílených útoků, protože k back-endové aplikaci mají přístup pouze ověřené identity.
* **Podmíněný přístup**. Před navázáním připojení k síti lze použít bohatší ovládací prvky zásad. Pomocí podmíněného přístupu můžete definovat omezení provozu, který povolíte přístupkovou aplikaci back-endu. Můžete vytvořit zásady, které omezují přihlášení na základě umístění, sílu ověřování a profil rizika uživatele. S vývojem podmíněného přístupu se přidávají další ovládací prvky, které poskytují další zabezpečení, jako je integrace s microsoftcloudovým zabezpečením aplikací (MCAS). Integrace MCAS umožňuje nakonfigurovat místní aplikaci pro [monitorování v reálném čase](application-proxy-integrate-with-microsoft-cloud-application-security.md) využitím podmíněného přístupu ke sledování a řízení relací v reálném čase na základě zásad podmíněného přístupu.
* **Ukončení provozu**. Veškerý provoz back-endové aplikace je ukončen službou Proxy aplikace v cloudu, zatímco relace je obnovena pomocí back-endového serveru. Tato strategie připojení znamená, že back-endové servery nejsou vystaveny přímému přenosu HTTP. Jsou lépe chráněny proti cíleným útokům DoS (denial-of-service), protože váš firewall není pod útokem.
* **Veškerý přístup je odchozí**. Konektory proxy aplikace používají pouze odchozí připojení ke službě Proxy aplikace v cloudu přes porty 80 a 443. Bez příchozích připojení není nutné otevírat porty brány firewall pro příchozí připojení nebo součásti v dmz. Všechna připojení jsou odchozí a přes zabezpečený kanál.
* **Bezpečnostní analýza a strojové učení (ML) založené inteligence**. Vzhledem k tomu, že je součástí služby Azure Active Directory, může proxy aplikace využít [azure ad identity protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview) (vyžaduje [licencování Premium P2).](https://azure.microsoft.com/pricing/details/active-directory/) Azure AD Identity Protection kombinuje inteligenci zabezpečení strojového učení s datovými kanály z [oddělení digitálních zločinů microsoftu](https://news.microsoft.com/stories/cybercrime/index.html) a [Centra reakce zabezpečení Microsoftu](https://www.microsoft.com/msrc) a aktivně identifikuje ohrožené účty. Ochrana identity nabízí ochranu v reálném čase před vysoce rizikovými přihlášeními. Bere v úvahu faktory, jako jsou přístupy z infikovaných zařízení, prostřednictvím anonymizačních sítí nebo z atypických a nepravděpodobných míst, aby se zvýšil rizikový profil relace. Tento rizikový profil se používá pro ochranu v reálném čase. Mnohé z těchto sestav a událostí jsou již k dispozici prostřednictvím rozhraní API pro integraci se systémy SIEM.

* **Vzdálený přístup jako služba**. Nemusíte se starat o údržbu a opravy místních serverů, abyste povolili vzdálený přístup. Aplikace Proxy je internetová škálovací služba, kterou společnost Microsoft vlastní, takže vždy získáte nejnovější opravy zabezpečení a upgrady. Neopravený software stále představuje velké množství útoků. Podle ministerstva vnitřní bezpečnosti, tolik jako [85 procent cílených útoků jsou předejít](https://www.us-cert.gov/ncas/alerts/TA15-119A). S tímto modelem služeb již nemusíte nést těžké břemeno správy hraničních serverů a vyškrábat se na jejich opravu podle potřeby.

* **Integrace Intune**. S Intune se firemní provoz směruje odděleně od osobního provozu. Proxy aplikace zajišťuje, že podnikový provoz je ověřen. Proxy aplikace a funkci [spravovaného prohlížeče Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) lze také použít společně k tomu, aby vzdáleným uživatelům umožnili bezpečný přístup k interním webům ze zařízení se systémem iOS a Android.

### <a name="roadmap-to-the-cloud"></a>Plán do cloudu

Další hlavní výhodou implementace proxy aplikace je rozšíření Azure AD do místního prostředí. Ve skutečnosti je implementace proxy aplikací klíčovým krokem při přesunu vaší organizace a aplikací do cloudu. Přechodem do cloudu a daleko od místního ověřování snížíte místní nároky na vaše místní nároky a použijete možnosti správy identit azure ad jako rovinu ovládacího prvku. S minimálními nebo žádnými aktualizacemi stávajících aplikací máte přístup ke cloudovým funkcím, jako je jednotné přihlašování, vícefaktorové ověřování a centrální správa. Instalace nezbytných součástí do proxy aplikace je jednoduchý proces pro vytvoření architektury vzdáleného přístupu. A přechodem do cloudu máte přístup k nejnovějším funkcím, aktualizacím a funkcím Azure AD, jako je vysoká dostupnost a zotavení po havárii.

Další informace o migraci aplikací do Azure AD najdete v tématu Migrace aplikací do bílého papíru [služby Azure Active Directory.](https://aka.ms/migrateapps/whitepaper)

## <a name="architecture"></a>Architektura

Následující diagram obecně znázorňuje, jak služby ověřování Azure AD a proxy aplikace spolupracují na poskytování jednotného přihlášení k místním aplikacím koncovým uživatelům.

![Tok ověřování proxy aplikace Azure AD](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Poté, co uživatel má přístup k aplikaci prostřednictvím koncového bodu, uživatel je přesměrován na přihlašovací stránku Azure AD. Pokud jste nakonfigurovali zásady podmíněného přístupu, jsou v tuto chvíli kontrolovány konkrétní podmínky, aby bylo zajištěno, že splňujete požadavky na zabezpečení vaší organizace.
2. Po úspěšném přihlášení Azure AD odešle token do klientského zařízení uživatele.
3. Klient odešle token službě Proxy aplikace, která z tokenu načte hlavní název uživatele (UPN) a hlavní název zabezpečení (SPN).
4. Proxy aplikace předá požadavek, který je vyzvednut [konektorem](application-proxy-connectors.md)proxy aplikace .
5. Konektor provádí jakékoli další ověřování požadované jménem uživatele *(Volitelné v závislosti na metodě ověřování*), požaduje interní koncový bod aplikačního serveru a odešle požadavek místní aplikaci.
6. Odpověď z aplikačního serveru je odeslána prostřednictvím konektoru do služby Proxy aplikace.
7. Odpověď je odeslána ze služby Proxy aplikace uživateli.

|**Komponenta**|**Popis**|
|:-|:-|
|Koncový bod|Koncový bod je adresa URL nebo [portál koncového uživatele](end-user-experiences.md). Uživatelé mohou přistupovat k aplikacím mimo vaši síť přístupem k externí adrese URL. Uživatelé v rámci vaší sítě mohou přistupovat k aplikaci prostřednictvím adresy URL nebo portálu koncového uživatele. Když uživatelé přejdou na jeden z těchto koncových bodů, ověří se ve službě Azure AD a pak jsou směrovány přes konektor do místní aplikace.|
|Azure AD|Azure AD provádí ověřování pomocí adresáře klienta uloženého v cloudu.|
|Služba Proxy aplikace|Tato služba Proxy aplikace běží v cloudu jako součást Azure AD. Předá přihlašovací token od uživatele konektoru proxy aplikace. Proxy aplikace předá všechny přístupné hlavičky na požadavek a nastaví záhlaví podle jeho protokolu, na IP adresu klienta. Pokud příchozí požadavek na proxy server již tuto hlavičku má, je adresa IP klienta přidána na konec seznamu odděleného čárkami, který je hodnotou hlavičky.|
|Konektor proxy aplikace|Konektor je lehký agent, který běží na windows serveru uvnitř sítě. Konektor spravuje komunikaci mezi službou Proxy aplikace v cloudu a místní aplikací. Konektor používá pouze odchozí připojení, takže není třeba otevírat žádné příchozí porty nebo dát nic v DMZ. Konektory jsou bezstavové a vyžádat informace z cloudu podle potřeby. Další informace o konektory, jako je například jejich vyrovnávání zatížení a ověřování, najdete [v tématu Principy konektorů proxy aplikací Azure AD](application-proxy-connectors.md).|
|Služba Active Directory (AD)|Služba Active Directory je spuštěna místně a provádí ověřování pro účty domény. Při konfiguraci jednotného přihlášení konektor komunikuje se službou AD a provádí další požadované ověřování.|
|Místní aplikace|Nakonec má uživatel přístup k místní aplikaci.|

Proxy aplikace Azure AD se skládá z cloudové služby proxy aplikací a místního konektoru. Konektor naslouchá požadavkům ze služby Proxy aplikace a zpracovává připojení k interním aplikacím. Je důležité si uvědomit, že veškerá komunikace probíhá přes TLS a vždy pocházejí z konektoru ke službě Proxy aplikace. To znamená, že komunikace jsou pouze odchozí. Konektor používá klientský certifikát k ověření služby Proxy aplikace pro všechna volání. Jedinou výjimkou ze zabezpečení připojení je počáteční krok nastavení, ve kterém je vytvořen klientský certifikát. Další podrobnosti najdete v části Proxy aplikace [pod kapotou.](application-proxy-security.md#under-the-hood)

### <a name="application-proxy-connectors"></a>Konektory proxy aplikací

[Konektory proxy aplikací](application-proxy-connectors.md) jsou zjednodušené agenty nasazené místně, které usnadňují odchozí připojení ke službě Proxy aplikace v cloudu. Konektory musí být nainstalovány na systému Windows Server, který má přístup k back-endové aplikaci. Uživatelé se připojují ke cloudové službě Proxy aplikace, která směruje jejich provoz do aplikací prostřednictvím konektorů, jak je znázorněno níže.

![Síťová připojení proxy aplikací Azure AD](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Nastavení a registrace mezi konektorem a službou Proxy aplikace se provádí následovně:

1. Správce IT otevře porty 80 a 443 odchozím přenosům a umožní přístup k několika adresám URL, které jsou potřeba konektorem, službou Proxy aplikace a službou Azure AD.
2. Správce se přihlásí na portál Azure a spustí spustitelný soubor pro instalaci konektoru na místní server Windows.
3. Konektor začne "naslouchat" službě Proxy aplikace.
4. Správce přidá místní aplikaci do Azure AD a nakonfiguruje nastavení, jako jsou adresy URL, které uživatelé potřebují k připojení ke svým aplikacím.

Další informace najdete [v tématu Plánování nasazení proxy aplikace Azure AD](application-proxy-deployment-plan.md).

Doporučujeme vždy nasadit více konektorů pro redundanci a škálování. Konektory ve spojení se službou se starají o všechny úkoly s vysokou dostupností a mohou být přidány nebo odstraněny dynamicky. Pokaždé, když přijde nový požadavek je směrován do jedné z konektorů, která je k dispozici. Když je konektor spuštěn, zůstane aktivní, protože se připojuje ke službě. Pokud je konektor dočasně nedostupný, nereaguje na tento provoz. Nepoužívané konektory jsou označeny jako neaktivní a odebrány po 10 dnech nečinnosti.

Konektory také dotazování serveru zjistit, pokud je novější verze konektoru. I když můžete provést ruční aktualizaci, konektory se automaticky aktualizují, pokud je spuštěna služba Aktualizace konektoru proxy aplikace. Pro klienty s více konektory, automatické aktualizace cíl jeden konektor najednou v každé skupině, aby se zabránilo prostojům ve vašem prostředí.

**Poznámka:** Stránku Historie [verzí](application-proxy-release-version-history.md) proxy aplikace můžete sledovat, aby byla upozorněna, když byly aktualizace vydány přihlášením se k odběru informačního kanálu RSS.

Každý konektor proxy aplikace je přiřazen ke [skupině konektorů](application-proxy-connector-groups.md). Konektory ve stejné skupině konektorů fungují jako jedna jednotka pro vysokou dostupnost a vyrovnávání zatížení. Můžete vytvořit nové skupiny, přiřadit konektory k nim na webu Azure portal a pak přiřadit konkrétní konektory pro poskytování konkrétních aplikací. Doporučuje se mít alespoň dva konektory v každé skupině konektorů pro vysokou dostupnost.

Skupiny konektorů jsou užitečné, když potřebujete podporovat následující scénáře:

* Geografické publikování aplikací
* Segmentace/izolace aplikací
* Publikování webových aplikací spuštěných v cloudu nebo místně

Další informace o výběru místa instalace konektorů a optimalizaci sítě najdete [v tématu Důležité informace o topologii sítě při používání proxy aplikace Azure Active Directory](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Jiné případy použití

Do tohoto okamžiku jsme se zaměřili na používání proxy aplikací k externímu publikování místních aplikací a současně povolujeme jednotné přihlašování ke všem cloudovým a místním aplikacím. Existují však další případy použití pro proxy aplikace, které stojí za zmínku. Patří mezi ně:

* **Bezpečně publikovat REST API**. Když máte obchodní logiku nebo rozhraní API spuštěná místně nebo hostovaná na virtuálních počítačích v cloudu, proxy aplikace poskytuje veřejný koncový bod pro přístup k rozhraní API. Přístup ke koncovému bodu rozhraní API umožňuje řídit ověřování a autorizaci bez nutnosti příchozích portů. Poskytuje další zabezpečení prostřednictvím funkcí Azure AD Premium, jako je vícefaktorové ověřování a podmíněný přístup založený na zařízení pro stolní počítače, iOS, MAC a zařízení s Androidem pomocí Intune. Další informace najdete v [tématu Jak povolit nativním klientským aplikacím interakci s proxy aplikacemi](application-proxy-configure-native-client-application.md) a [chránit rozhraní API pomocí OAuth 2.0 s Azure Active Directory a API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad).
* **Služba Vzdálená plocha** **(RDS).** Standardní nasazení RDS vyžadují otevřená příchozí připojení. [Nasazení rds s proxy aplikace](application-proxy-integrate-with-remote-desktop-services.md) má však trvalé odchozí připojení ze serveru se spuštěnou službou konektoru. Tímto způsobem můžete koncovým uživatelům nabídnout více aplikací publikováním místních aplikací prostřednictvím služby Vzdálená plocha. Můžete také snížit prostor pro útok na nasazení s omezenou sadou dvoustupňového ověřování a řízení podmíněného přístupu do rds.
* **Publikujte aplikace, které se připojují pomocí websockets**. Podpora s [Qlik Sense](application-proxy-qlik.md) je ve verzi Public Preview a v budoucnu bude rozšířena na další aplikace.
* **Povolte nativním klientským aplikacím interakci s proxy aplikacemi**. Proxy aplikace Azure AD můžete použít k publikování webových aplikací, ale také lze použít k publikování [nativní chod klientských aplikací,](application-proxy-configure-native-client-application.md) které jsou nakonfigurované s Azure AD Ověřování Library (ADAL). Nativní klientské aplikace se liší od webových aplikací, protože jsou nainstalované na zařízení, zatímco webové aplikace jsou přístupné prostřednictvím prohlížeče.

## <a name="conclusion"></a>Závěr

Způsob, jakým pracujeme, a nástroje, které používáme, se rychle mění. Vzhledem k tomu, že do práce pracuje více zaměstnanců a všudypřítomné používání aplikací Software-as-a-Service (SaaS) bude muset také vyvíjet způsob, jakým organizace spravují a zabezpečují svá data. Společnosti již nepůsobí pouze ve svých vlastních zdech, chráněné příkopem, který obklopuje jejich hranice. Data putují do více míst než kdy předtím – v místním i cloudovém prostředí. Tento vývoj pomohl zvýšit produktivitu a schopnost uživatelů spolupracovat, ale také ztěžuje ochranu citlivých dat.

Ať už aktuálně používáte Azure AD ke správě uživatelů v hybridním scénáři koexistence nebo máte zájem začít cestu do cloudu, implementace proxy aplikací Azure AD může pomoci zmenšit velikost vaší místní nároky tím, že poskytuje vzdálené přístup jako služba.

Organizace by měly začít využívat App Proxy dnes využít následující výhody:

* Publikování místních aplikací externě bez režie spojené s údržbou tradičních VPN nebo jiných místních řešení pro publikování webu a přístupu DMZ
* Jednotné přihlašování ke všem aplikacím, ať už se neděje Office 365 nebo jiným aplikacím SaaS a včetně místních aplikací
* Zabezpečení škálování v cloudu, kde Azure AD využívá telemetrická data Office 365 k zabránění neoprávněnému přístupu
* Integrace Intune pro ověření podnikového provozu
* Centralizace správy uživatelských účtů
* Automatické aktualizace, které zajistí nejnovější opravy zabezpečení
* Nové funkce při jejich vydání; poslední je podpora jednotného přihlášení SAML a podrobnější správa aplikačních souborů cookie

## <a name="next-steps"></a>Další kroky

* Informace o plánování, provozu a správě proxy aplikací Azure AD najdete v [tématu Plánování nasazení proxy aplikace Azure AD](application-proxy-deployment-plan.md).
* Pokud chcete naplánovat živou ukázku nebo získat bezplatnou 90denní zkušební verzi k vyhodnocení, přečtěte si informace [o tom, jak začít s podnikovou mobilitou a zabezpečením](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial).

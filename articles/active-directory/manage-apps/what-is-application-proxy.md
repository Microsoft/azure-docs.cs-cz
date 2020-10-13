---
title: Publikování místních aplikací pomocí Azure Proxy aplikací služby AD
description: Zjistěte, proč používat proxy aplikace k externímu publikování místních webových aplikací pro vzdálené uživatele. Přečtěte si o architektuře proxy aplikací, konektorech, metodách ověřování a výhodách zabezpečení.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/31/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: dbfe18c84217c741f8dd64481901747d068fcdd3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604134"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Použití Azure Proxy aplikací služby AD k publikování místních aplikací pro vzdálené uživatele

Azure Active Directory (Azure AD) nabízí mnoho možností pro ochranu uživatelů, aplikací a dat v cloudu i v místním prostředí. Konkrétně funkci Azure Proxy aplikací služby AD můžete implementovat odborníky v oblasti IT, kteří chtějí externě publikovat místní webové aplikace. Vzdálení uživatelé, kteří potřebují přístup k interním aplikacím, k nim můžou přistupovat zabezpečeným způsobem.

Možnost bezpečného přístupu k interním aplikacím mimo vaši síť se v moderním pracovišti bude ještě důležitější. Díky scénářům, jako je BYOD (Přineste si vlastní zařízení) a mobilním zařízením, jsou odborníci na IT vyzváni, aby splnili dva cíle:

* Umožněte koncovým uživatelům, aby byli produktivní kdykoli a kdekoli.
* Nepřetržitá ochrana podnikových prostředků

Řada organizací věří, že jsou pod kontrolou a chráněny v případě, že prostředky existují v hranicích jejich podnikových sítí. Ale v dnešním digitálním pracovišti se tato hranice rozšířila na spravovaná mobilní zařízení a prostředky a služby v cloudu. Teď musíte spravovat složitost ochrany identit uživatelů a dat uložených na svých zařízeních a aplikacích.

Možná už službu Azure AD používáte ke správě uživatelů v cloudu, kteří potřebují mít přístup k Microsoft 365 a dalším aplikacím SaaS i k místním hostovaným webovým aplikacím. Pokud už službu Azure AD máte, můžete ji využít jako jednu řídicí rovinu a zajistit tak bezproblémové a zabezpečené připojení k místním aplikacím. Nebo možná stále zvažujete přesun do cloudu. Pokud ano, můžete začít svoji cestu do cloudu tím, že implementujete proxy aplikace a vezmete první krok k vytvoření silného základu identity.

I když není vyčerpávající, níže uvedený seznam popisuje některé z akcí, které můžete povolit implementací proxy aplikací ve scénáři hybridní existence:

* Zjednodušení publikování místních webových aplikací s jednodušším způsobem bez DMZ
* Podpora jednotného přihlašování (SSO) v rámci zařízení, prostředků a aplikací v cloudu i v místním prostředí
* Podpora služby Multi-Factor Authentication pro aplikace v cloudu i v místním prostředí
* Rychlé využití cloudových funkcí s zabezpečením Microsoft Cloud
* Centralizace správy uživatelských účtů
* Centralizace řízení identity a zabezpečení
* Automaticky přidávat nebo odebírat přístup uživatelů k aplikacím na základě členství ve skupinách

Tento článek vysvětluje, jak Azure AD a proxy aplikací dávají vzdáleným uživatelům možnosti jednotného přihlašování (SSO). Uživatelé se bezpečně připojují k místním aplikacím bez serverů VPN nebo serverů s duálním domovem a pravidel brány firewall. Tento článek vám pomůže pochopit, jak proxy aplikací přináší možnosti a výhody zabezpečení cloudu pro vaše místní webové aplikace. Také popisuje architekturu a topologie, které lze použít.

## <a name="remote-access-in-the-past"></a>Vzdálený přístup v minulosti

Dříve byla vaše Řídicí rovina pro ochranu interních prostředků před útočníky a zároveň usnadňuje přístup vzdáleným uživatelům v DMZ nebo hraniční síti. Ale řešení VPN a reverzních proxy nasazená v DMZ, která používají externí klienti pro přístup k podnikovým prostředkům, nejsou vhodná pro cloudový svět. Obvykle mají následující nevýhody:

* Hardwarové náklady
* Údržba zabezpečení (opravy, porty monitorování atd.)
* Ověřování uživatelů na hraničních zařízeních
* Ověřování uživatelů na webové servery v hraniční síti
* Údržba přístupu VPN pro vzdálené uživatele s distribucí a konfigurací softwaru klienta VPN. Také Udržujte servery připojené k doméně v DMZ, což může být zranitelné vůči vnějším útokům.

V dnešním cloudovém světě se Azure AD nejlépe hodí k řízení toho, kdo a co se do vaší sítě dostane. Azure Proxy aplikací služby AD se integruje s moderním ověřováním a cloudovým technologií, jako jsou SaaS aplikace a zprostředkovatelé identity. Tato integrace umožňuje uživatelům přistoupit k aplikacím odkudkoli. Jenom proxy aplikace jsou pro dnešní digitální pracoviště lépe vhodné, je bezpečnější než řešení VPN a reverzní proxy a jednodušší implementace. Vzdálení uživatelé mají přístup k místním aplikacím stejným způsobem jako přístup k Microsoft a dalším SaaS aplikacím integrovaným s Azure AD. Aby aplikace fungovaly s Proxy aplikací, nemusíte je měnit nebo aktualizovat. Navíc proxy aplikací nevyžaduje, abyste otevřeli příchozí připojení přes bránu firewall. S proxy aplikací ho jednoduše nastavíte a zapomenete.

## <a name="the-future-of-remote-access"></a>Budoucnost vzdáleného přístupu

V dnešním digitálním pracovišti uživatelé pracují kdekoli s více zařízeními a aplikacemi. Jedinou konstantou je identita uživatele. To je proto, že první krok zabezpečené sítě dnes využívá možnosti [správy identit Azure AD](https://docs.microsoft.com/azure/security/security-identity-management-overview) jako plochu ovládacího prvku zabezpečení. Model, který používá identitu jako řídicí plochu, se obvykle skládá z následujících součástí:

* Zprostředkovatel identity, který uchovává přehled o uživatelích a informacích o uživatelích.
* Adresář zařízení pro udržování seznamu zařízení, která mají přístup k podnikovým prostředkům. Tento adresář obsahuje odpovídající informace o zařízení (například typ zařízení, integrita atd.).
* Služba vyhodnocení zásad k určení, jestli uživatel a zařízení odpovídají zásadám nastaveným správci zabezpečení.
* Schopnost udělit nebo odepřít přístup k prostředkům organizace.

V případě proxy aplikací uchovává služba Azure AD uživatele, kteří potřebují přístup k webovým aplikacím publikovaným místně a v cloudu. Poskytuje centrální bod správy pro tyto aplikace. I když se to nepožaduje, doporučuje se taky povolit podmíněný přístup Azure AD. Definováním podmínek, jak budou uživatelé ověřovat a získat přístup, budete dále zajišťovat, aby měli oprávnění k aplikacím přístup.

**Poznámka:** Je důležité si uvědomit, že Azure Proxy aplikací služby AD je určený jako náhrada sítě VPN nebo reverzního proxy serveru pro roaming (nebo vzdálené) uživatele, kteří potřebují přístup k interním prostředkům. Není určený pro interní uživatele v podnikové síti. Interní uživatelé, kteří zbytečně nepoužívají proxy aplikací, můžou způsobit neočekávané a nežádoucí problémy s výkonem.

![Azure Active Directory a všechny vaše aplikace](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Přehled fungování proxy aplikací

Proxy aplikací je služba Azure AD, kterou konfigurujete v Azure Portal. Umožňuje publikovat externí veřejný koncový bod adresy URL HTTP/HTTPS v cloudu Azure, který se připojí k interní adrese URL aplikačního serveru ve vaší organizaci. Tyto místní webové aplikace můžete integrovat se službou Azure AD pro podporu jednotného přihlašování. Koncoví uživatelé pak mohou přistupovat k místním webovým aplikacím stejným způsobem jako přístup k Microsoft 365 a dalším aplikacím SaaS.

Mezi součásti této funkce patří služba proxy aplikací, která běží v cloudu, konektor proxy aplikací, což je jednoduchý agent, který běží na místním serveru a Azure AD, což je poskytovatel identity. Všechny tři komponenty společně poskytují přístup k místním webovým aplikacím pro uživatele s jednotným přihlašováním.

Po přihlášení mohou externí uživatelé přistupovat k místním webovým aplikacím pomocí známé adresy URL nebo [mých aplikací](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) ze zařízení s platformou Desktop nebo iOS nebo Mac. Například proxy aplikace může poskytovat vzdálený přístup a jednotné přihlašování ke vzdálené ploše, webům SharePoint, Tableau, Qlik, Outlooku na webu a obchodním aplikacím (LOB).

![Architektura Azure Proxy aplikací služby AD](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Authentication

Existuje několik způsobů konfigurace aplikace pro jednotné přihlašování a vybraná metoda závisí na ověřování, které vaše aplikace používá. Proxy aplikace podporuje následující typy aplikací:

* Webové aplikace
* Webová rozhraní API, která chcete zpřístupnit pro rozšířené aplikace na různých zařízeních
* Aplikace hostované za Brána vzdálené plochy
* Bohatých klientských aplikací integrovaných do [knihovny Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)

App proxy funguje s aplikacemi, které používají následující nativní protokol ověřování:

* **[Integrované ověřování systému Windows (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** Konektory proxy aplikací pro IWA používají k ověřování uživatelů v aplikaci protokolu Kerberos omezené delegování (KCD).

Proxy aplikace také podporuje následující protokoly ověřování s integrací třetí strany nebo v konkrétních scénářích konfigurace:

* [**Ověřování na základě hlaviček**](application-proxy-configure-single-sign-on-with-ping-access.md). Tato metoda přihlašování používá ověřovací službu třetí strany nazvanou PingAccess a používá se v případě, že aplikace používá hlavičky pro ověřování. V tomto scénáři se ověřování zpracovává pomocí PingAccess.
* [**Ověřování pomocí formulářů nebo hesel**](application-proxy-configure-single-sign-on-password-vaulting.md). Pomocí této metody ověřování se uživatelé přihlásí k aplikaci pomocí uživatelského jména a hesla při prvním přístupu k ní. Po prvním přihlášení Azure AD zadá uživatelské jméno a heslo k aplikaci. V tomto scénáři služba Azure AD zpracovává ověřování.
* [**Ověřování SAML**](application-proxy-configure-single-sign-on-on-premises-apps.md). Jednotné přihlašování založené na SAML je podporované pro aplikace, které používají protokol SAML 2,0 nebo WS-Federation protokoly. Pomocí jednotného přihlašování SAML Azure AD ověřuje aplikace pomocí účtu Azure AD uživatele.

Další informace o podporovaných metodách najdete v tématu [Výběr metody jednotného přihlašování](sso-options.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Výhody zabezpečení

Řešení vzdáleného přístupu, které nabízí proxy aplikace a podpora Azure AD, může využít několik výhod, které zákazníci můžou využít, včetně těchto:

* **Ověřený přístup**. Proxy aplikací se nejlépe hodí pro publikování aplikací s [předběžným ověřením](application-proxy-security.md#authenticated-access) , aby se zajistilo, že vaše síť bude mít jenom ověřená připojení. U aplikací publikovaných s předběžným ověřením není povolený žádný provoz pro předávání služby App proxy do místního prostředí bez platného tokenu. Předběžné ověření, podle jeho velmi povahy, blokuje velký počet cílových útoků, protože k back-endu mají přístup jenom ověřené identity.
* **Podmíněný přístup**. Chcete-li vytvořit připojení k síti, můžete použít bohatší ovládací prvky zásad. Pomocí podmíněného přístupu můžete definovat omezení pro přenosy, které povolíte, aby se mohla zobrazit vaše back-end aplikace. Vytvoříte zásady, které omezují přihlášení na základě umístění, síly ověřování a profilu rizika uživatele. V případě vývoje podmíněného přístupu se přidávají další ovládací prvky, které poskytují další zabezpečení, jako je integrace s Microsoft Cloud App Security (MCAS). MCAS Integration umožňuje nakonfigurovat místní aplikaci pro [monitorování v reálném čase](application-proxy-integrate-with-microsoft-cloud-application-security.md) tím, že využije podmíněný přístup k monitorování a řízení relací v reálném čase na základě zásad podmíněného přístupu.
* **Ukončení provozu**. Veškerý provoz do back-endové aplikace se ukončí ve službě proxy aplikací v cloudu, zatímco se znovu naváže relace k back-end serveru. Tato strategie připojení znamená, že servery back-end nejsou vystaveny přímému přenosu HTTP. Jsou lépe chráněny proti cílovým útokům DoS (DOS), protože brána firewall není v útoku.
* **Veškerý přístup je odchozí**. Konektory proxy aplikací používají pouze odchozí připojení ke službě proxy aplikací v cloudu přes porty 80 a 443. Bez příchozích připojení není nutné otevírat porty brány firewall pro příchozí připojení nebo komponenty v DMZ. Všechna připojení jsou odchozí a přes zabezpečený kanál.
* **Analýza zabezpečení a Machine Learning (ml) – inteligentní**informace Vzhledem k tomu, že je součástí Azure Active Directory, může proxy aplikace využít [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview) (vyžaduje [licencování Premium P2](https://azure.microsoft.com/pricing/details/active-directory/)). Azure AD Identity Protection kombinuje informace o zabezpečení strojového učení s datovými kanály z [digitálních činů](https://news.microsoft.com/stories/cybercrime/index.html) od společnosti Microsoft a z [centra Microsoft Security Response Center](https://www.microsoft.com/msrc) k proaktivní identifikaci ohrožených účtů. Identity Protection nabízí ochranu v reálném čase z přihlašování s vysokým rizikem. Bere v úvahu faktory, jako je přístup z nakažených zařízení, prostřednictvím sítí anonymizace nebo z netypických a nepravděpodobných umístění pro zvýšení rizikového profilu relace. Tento rizikový profil se používá pro ochranu v reálném čase. Mnohé z těchto sestav a událostí už jsou k dispozici prostřednictvím rozhraní API pro integraci se systémy SIEM.

* **Vzdálený přístup jako služba**. Nemusíte si dělat starosti s údržbou a opravou místních serverů, abyste mohli povolit vzdálený přístup. Proxy aplikací je služba internetového škálování, kterou Microsoft vlastní, takže vždycky získáte nejnovější opravy a upgrady zabezpečení. Neopravný software je stále pro velký počet útoků. V souladu s Homeland zabezpečením je možné [zabránit tomu, aby se zabránilo 85% cílených útoků](https://www.us-cert.gov/ncas/alerts/TA15-119A). S tímto modelem služeb nemusíte mít k dispozici velkou zátěž pro správu hraničních serverů a jejich případné opravy podle potřeby.

* **Integrace Intune**. V Intune se podnikový provoz směruje odděleně od osobního provozu. Proxy aplikace zajišťuje ověření podnikového provozu. [Proxy aplikací a funkce Intune Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) lze také použít společně k tomu, aby vzdálení uživatelé měli bezpečný přístup k interním webům ze zařízení s iOS a Androidem.

### <a name="roadmap-to-the-cloud"></a>Plán do cloudu

Další hlavní výhodou implementace proxy aplikací je rozšíření Azure AD na vaše místní prostředí. Implementace proxy aplikací je ve skutečnosti klíčový krok při přesunu vaší organizace a aplikací do cloudu. Přesunutím do cloudu a nezávisle na místním ověřování snížíte své místní nároky a jako svou řídicí plochu použijete možnosti správy identit Azure AD. S minimálními nebo žádnými aktualizacemi stávajících aplikací máte přístup ke cloudovým funkcím, jako je jednotné přihlašování, vícefaktorové ověřování a centrální Správa. Instalace nezbytných komponent do proxy aplikací je jednoduchý proces pro vytvoření architektury vzdáleného přístupu. A přechodem do cloudu máte přístup k nejnovějším funkcím, aktualizacím a funkcím služby Azure AD, jako je vysoká dostupnost a zotavení po havárii.

Další informace o migraci vašich aplikací do Azure AD najdete v článku [migrace vašich aplikací na Azure Active Directory](https://aka.ms/migrateapps/whitepaper) dokument White Paper.

## <a name="architecture"></a>Architektura

Následující diagram znázorňuje obecný postup, jak služby ověřování Azure AD a proxy aplikací spolupracují za účelem poskytování jednotného přihlašování k místním aplikacím koncovým uživatelům.

![Tok ověřování Azure Proxy aplikací služby AD](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Po tom, co uživatel k aplikaci přistoupí prostřednictvím koncového bodu, se uživatel přesměruje na přihlašovací stránku služby Azure AD. Pokud jste nakonfigurovali zásady podmíněného přístupu, v tuto chvíli se zkontrolují konkrétní podmínky, abyste měli jistotu, že budete dodržovat požadavky na zabezpečení vaší organizace.
2. Po úspěšném přihlášení pošle služba Azure AD token do klientského zařízení uživatele.
3. Klient odešle token službě proxy aplikací, která načte hlavní název uživatele (UPN) a hlavní název zabezpečení (SPN) z tokenu.
4. Proxy aplikace přepošle požadavek, který vybral [konektor](application-proxy-connectors.md)proxy aplikace.
5. Konektor provádí jakékoli další ověřování vyžadované jménem uživatele (*volitelné v závislosti na metodě ověřování*), požaduje interní koncový bod aplikačního serveru a odešle požadavek do místní aplikace.
6. Odpověď z aplikačního serveru se odesílá prostřednictvím konektoru službě proxy aplikace.
7. Odpověď je odeslána od služby proxy aplikace uživateli.

|**Komponenta**|**Popis**|
|:-|:-|
|Koncový bod|Koncový bod je adresa URL nebo [portál pro koncové uživatele](end-user-experiences.md). Uživatelé můžou kontaktovat aplikace i mimo vaši síť přístupem k externí adrese URL. Uživatelé v rámci vaší sítě mají přístup k aplikaci prostřednictvím adresy URL nebo portálu pro koncové uživatele. Když uživatelé přejdou do některého z těchto koncových bodů, ověřují se ve službě Azure AD a potom jsou směrováni přes konektor do místní aplikace.|
|Azure AD|Azure AD provádí ověřování pomocí adresáře tenanta, který je uložený v cloudu.|
|Služba proxy aplikací|Tato služba proxy aplikací běží v cloudu jako součást služby Azure AD. Předá přihlašovací token od uživatele k konektoru proxy aplikací. Proxy aplikace přepošle všechny dostupné hlavičky na žádosti a nastaví hlavičky podle jejího protokolu na IP adresu klienta. Pokud příchozí požadavek na proxy již má tuto hlavičku, adresa IP klienta se přidá na konec seznamu odděleného čárkami, který je hodnotou záhlaví.|
|Konektor proxy aplikací|Konektor je jednoduchý agent, který běží na Windows serveru ve vaší síti. Konektor spravuje komunikaci mezi službou proxy aplikací v cloudu a místní aplikací. Konektor používá pouze odchozí připojení, takže nemusíte otevírat žádné příchozí porty ani nic vkládat do DMZ. V případě potřeby jsou konektory bezstavové a nabízené informace z cloudu. Další informace o konektorech, jako je například vyrovnávání zatížení a ověřování, najdete v tématu [vysvětlení konektorů Azure proxy aplikací služby AD](application-proxy-connectors.md).|
|Služba Active Directory (AD)|Služba Active Directory pracuje místně a provádí ověřování pro doménové účty. Pokud je nakonfigurováno jednotné přihlašování, konektor komunikuje se službou AD za účelem provedení dalšího vyžadovaného ověřování.|
|Místní aplikace|Nakonec může uživatel získat přístup k místní aplikaci.|

Služba Azure Proxy aplikací služby AD se skládá z cloudové služby proxy aplikací a konektoru On-Premises Connector. Konektor naslouchá požadavkům od služby proxy aplikací a zpracovává připojení k interním aplikacím. Je důležité si uvědomit, že veškerá komunikace probíhá přes protokol TLS, a vždy pochází z konektoru na službu proxy aplikací. To znamená, že komunikace je pouze odchozí. Konektor používá klientský certifikát k ověření pro službu proxy aplikací pro všechna volání. Jedinou výjimkou zabezpečení připojení je úvodní krok nastavení, ve kterém je klientský certifikát vytvořen. Další podrobnosti najdete v [části proxy aplikace v digestoři](application-proxy-security.md#under-the-hood) .

### <a name="application-proxy-connectors"></a>Konektory proxy aplikací

[Konektory proxy aplikací](application-proxy-connectors.md) jsou nezjednodušené agenty nasazené místně, které usnadňují odchozí připojení ke službě proxy aplikací v cloudu. Konektory musí být nainstalované na Windows serveru, který má přístup k back-endové aplikaci. Uživatelé se připojují ke cloudové službě proxy aplikací, která směruje provoz do aplikací přes konektory, jak je znázorněno níže.

![Připojení k síti Azure Proxy aplikací služby AD](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Nastavení a registrace mezi konektorem a službou proxy aplikací se dosahují takto:

1. Správce IT otevře porty 80 a 443 odchozího provozu a umožní přístup k několika adresám URL, které vyžaduje konektor, proxy služby aplikací a Azure AD.
2. Správce se přihlásí do Azure Portal a spustí spustitelný soubor pro instalaci konektoru na místní Windows Server.
3. Konektor začne naslouchat službě App proxy.
4. Správce přidá místní aplikaci do služby Azure AD a nakonfiguruje nastavení, jako jsou adresy URL, které uživatelé potřebují k připojení ke svým aplikacím.

Další informace najdete v tématu [Plánování nasazení služby Azure proxy aplikací služby AD](application-proxy-deployment-plan.md).

Doporučuje se vždycky nasadit více konektorů pro redundanci a škálování. Konektory společně se službou se postarou o všechny úlohy s vysokou dostupností a dají se dynamicky přidat nebo odebrat. Pokaždé, když přijde nový požadavek, se směruje na jeden z dostupných konektorů. Když je konektor spuštěný, zůstane aktivní, protože se ke službě připojí. Pokud je konektor dočasně nedostupný, nereaguje na tento provoz. Nepoužívané konektory jsou označené jako neaktivní a odebrané po 10 dnech nečinnosti.

Konektory se také dotazují na server a zjistí, zda je k dispozici novější verze konektoru. I když můžete provést ruční aktualizaci, konektory se automaticky aktualizují, pokud je spuštěná služba proxy aplikace Aktualizátor konektorů. Pro klienty s více konektory se automaticky aktualizují jednotlivé konektory v každé skupině, aby nedocházelo k výpadkům ve vašem prostředí.

**Poznámka**: [stránku Historie verzí](application-proxy-release-version-history.md) proxy aplikací můžete monitorovat, abyste byli informováni o vydání aktualizací pomocí přihlášení k odběru informačního kanálu RSS.

Každý konektor proxy aplikace je přiřazený ke [skupině konektorů](application-proxy-connector-groups.md). Konektory ve stejné skupině konektoru fungují jako jedna jednotka pro zajištění vysoké dostupnosti a vyrovnávání zatížení. Můžete vytvořit nové skupiny, přiřadit k nim konektory v Azure Portal a pak přiřadit konkrétní konektory, které budou obsluhovat konkrétní aplikace. Doporučuje se mít v každé skupině konektorů aspoň dva konektory pro zajištění vysoké dostupnosti.

Skupiny konektorů jsou užitečné v případě, že potřebujete podporovat následující scénáře:

* Publikování geografické aplikace
* Segmentace a izolace aplikace
* Publikování webových aplikací běžících v cloudu nebo místně

Další informace o výběru umístění pro instalaci konektorů a optimalizaci sítě najdete v tématu věnovaném [hlediskům síťové topologie při použití proxy aplikací služby Azure Active Directory](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Jiné případy použití

V tomto okamžiku se zaměřujeme na použití proxy aplikací k externímu publikování místních aplikací při povolování jednotného přihlašování ke všem cloudovým a místním aplikacím. Existují však i další případy použití pro proxy aplikace, které představují zmínku. Mezi ně patří:

* **Bezpečně zveřejňujte rozhraní REST API**. Pokud máte obchodní logiku nebo rozhraní API spuštěná místně nebo hostovaná na virtuálních počítačích v cloudu, proxy aplikací poskytuje veřejný koncový bod pro přístup k rozhraní API. Přístup ke koncovému bodu rozhraní API umožňuje řídit ověřování a autorizaci bez vyžadování příchozích portů. Poskytuje další zabezpečení prostřednictvím Azure AD Premiumch funkcí, jako jsou vícefaktorové ověřování a podmíněný přístup na základě zařízení pro stolní počítače, iOS, MAC a zařízení s Androidem pomocí Intune. Další informace najdete v tématu [Jak povolit nativním klientským aplikacím pracovat s proxy aplikacemi](application-proxy-configure-native-client-application.md) a [chránit rozhraní API pomocí protokolu OAuth 2,0 s Azure Active Directory a API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad).
* **Služba Vzdálená plocha** **(RDS)**. Standardní nasazení služby RDS vyžaduje otevřená příchozí připojení. [Nasazení RDS s proxy aplikací](application-proxy-integrate-with-remote-desktop-services.md) má ale trvalé odchozí připojení ze serveru, na kterém je spuštěná služba konektoru. Tímto způsobem můžete koncovým uživatelům nabídnout další aplikace publikováním místních aplikací prostřednictvím služby Vzdálená plocha. Můžete také omezit plochu pro útok na nasazení s omezeným počtem dvou kroků ověřování a řízení podmíněného přístupu na RDS.
* **Publikujte aplikace, které se připojují pomocí WebSockets**. Podpora s [Qlik senseem](application-proxy-qlik.md) je v Public Preview a v budoucnu se rozšíří na jiné aplikace.
* **Povolit nativním klientským aplikacím interakci s proxy aplikacemi**. Pomocí služby Azure Proxy aplikací služby AD můžete publikovat webové aplikace, ale také je lze použít k publikování [nativních klientských aplikací](application-proxy-configure-native-client-application.md) , které jsou nakonfigurovány pomocí knihovny ADAL (Azure AD Authentication Library). Nativní klientské aplikace se liší od webových aplikací, protože jsou nainstalované v zařízení, zatímco webové aplikace jsou dostupné přes prohlížeč.

## <a name="conclusion"></a>Závěr

Způsob, jakým budeme pracovat, a nástroje, které používáme, se rychle mění. Díky většímu počtu zaměstnanců, kteří přinášejí svá vlastní zařízení a Pervasive používání aplikací SaaS (software jako služba), se musí také vyvíjet organizace, které spravují a zabezpečují svá data. Společnosti již nefungují výhradně v rámci svých vlastních stěn chráněných Moat, která obklopuje jejich hranici. Data se v místních i cloudových prostředích přenáší do více umístění než kdykoli dřív. Tento vývoj pomáhá zvýšit produktivitu uživatelů a schopnost spolupracovat, ale zároveň zajišťuje náročnější ochranu citlivých dat.

Bez ohledu na to, jestli aktuálně používáte Azure AD ke správě uživatelů v hybridním scénáři existence nebo zajímání vaší cesty do cloudu, implementace služby Azure Proxy aplikací služby AD může pomoct snížit velikost vašich místních přístupů tím, že poskytuje vzdálený přístup jako službu.

Organizace by si měli začít využívat výhod proxy aplikací ještě dnes, abyste mohli využít výhod následujících výhod:

* Publikujte místní aplikace externě bez režie spojené s údržbou tradiční sítě VPN nebo jiných řešení pro publikování webů a DMZ přístupu.
* Jednotné přihlašování pro všechny aplikace, Microsoft 365 nebo jiné aplikace SaaS a včetně místních aplikací
* Cloudové škálování, kde Azure AD využívá Microsoft 365 telemetrii k tomu, aby se zabránilo neoprávněnému přístupu
* Integrace Intune, aby se zajistilo ověřování podnikového provozu
* Centralizované řízení správy uživatelských účtů
* Automatické aktualizace, aby se zajistilo, že máte nejnovější opravy zabezpečení
* Nové funkce při jejich vydání; Nejnovější podpora jednotného přihlašování SAML a podrobnější správy souborů cookie aplikace

## <a name="next-steps"></a>Další kroky

* Informace o plánování, provozování a správě Proxy aplikací služby AD Azure najdete v tématu [Plánování nasazení azure proxy aplikací služby AD](application-proxy-deployment-plan.md).
* Pokud chcete naplánovat živou ukázku nebo získat bezplatnou zkušební verzi 90 pro zkušební verzi, přečtěte si téma [Začínáme s Enterprise mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial).

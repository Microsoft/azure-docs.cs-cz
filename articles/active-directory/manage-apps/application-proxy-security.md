---
title: Požadavky na zabezpečení pro Azure Proxy aplikací služby AD | Microsoft Docs
description: Zahrnuje požadavky na zabezpečení při používání Azure Proxy aplikací služby AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1c0e91b08fff80c0308c0ead5fabce903421adc
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090116"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Otázky zabezpečení při vzdáleném přístupu k aplikacím pomocí Azure Proxy aplikací služby AD

Tento článek vysvětluje komponenty, které pracují na zabezpečení uživatelů a aplikací při použití Proxy aplikací služby Azure Active Directory.

Následující diagram znázorňuje, jak Azure AD umožňuje zabezpečený vzdálený přístup k místním aplikacím.

 ![Diagram zabezpečeného vzdáleného přístupu prostřednictvím Azure Proxy aplikací služby AD](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Výhody zabezpečení

Azure Proxy aplikací služby AD nabízí následující výhody zabezpečení:

### <a name="authenticated-access"></a>Ověřený přístup 

Pokud se rozhodnete použít předběžné ověření Azure Active Directory, budou mít přístup k vaší síti jenom ověření připojení.

Služba Azure Proxy aplikací služby AD spoléhá na všechna ověření prostřednictvím služby tokenů zabezpečení Azure AD (STS).  Předběžné ověření, podle jeho velmi povahy, blokuje velký počet anonymních útoků, protože přístup k back-endové aplikaci mají jenom ověřené identity.

Pokud jako metodu předběžného ověření zvolíte možnost Passthrough, tato výhoda se nezobrazuje. 

### <a name="conditional-access"></a>Podmíněný přístup

Než se naváže připojení k vaší síti, použijte rozšířené ovládací prvky zásad.

Pomocí [podmíněného přístupu](../conditional-access/concept-conditional-access-cloud-apps.md)můžete definovat omezení způsobu, jakým uživatelé mají povolený přístup k vašim aplikacím. Můžete vytvářet zásady, které omezují přihlášení na základě umístění, síly ověřování a profilu rizika uživatele.

Podmíněný přístup můžete také použít ke konfiguraci zásad Multi-Factor Authentication a přidání další vrstvy zabezpečení do ověřování uživatelů. Kromě toho je možné vaše aplikace směrovat také na Microsoft Cloud App Security prostřednictvím podmíněného přístupu Azure AD k poskytování monitorování a řízení v reálném čase prostřednictvím zásad [přístupu](/cloud-app-security/access-policy-aad) a [relací](/cloud-app-security/session-policy-aad) .

### <a name="traffic-termination"></a>Ukončení provozu

Veškerý provoz se v cloudu ukončí.

Vzhledem k tomu, že Azure Proxy aplikací služby AD je reverzní proxy, veškerý provoz do back-endové aplikace se ve službě ukončí. Relaci lze znovu vytvořit pouze s back-end serverem, což znamená, že back-endové servery nejsou vystaveny přímým přenosům HTTP. Tato konfigurace znamená, že je lepší chránit před cílenými útoky.

### <a name="all-access-is-outbound"></a>Veškerý přístup je odchozí. 

Nemusíte otevírat příchozí připojení k podnikové síti.

Konektory proxy aplikací používají pouze odchozí připojení ke službě Azure Proxy aplikací služby AD, což znamená, že pro příchozí připojení není nutné otevírat porty brány firewall. Tradiční proxy servery vyžadovaly hraniční síť (označovanou také jako *DMZ*, *demilitarizovaná zóna* nebo *monitorovaná podsíť*) a povolený přístup k neověřeným připojením v hraniční síti. Tento scénář vyžadoval investice do produktů firewallu webových aplikací za účelem analýzy provozu a ochrany prostředí. U proxy aplikací nepotřebujete hraniční síť, protože všechna připojení jsou odchozí a probíhají přes zabezpečený kanál.

Další informace o konektorech najdete v tématu [vysvětlení konektorů Azure proxy aplikací služby AD](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Analytické a strojové učení v cloudovém měřítku 

Získejte ochranu před špičkovým zabezpečením.

Vzhledem k tomu, že se jedná o součást Azure Active Directory, může proxy aplikace využívat [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)s daty z centra Microsoft Security Response Center a z jednotky digitálních zločinů. Společně provedeme proaktivní identifikaci ohrožených účtů a nabízíme ochranu před vysoce rizikovými přihlášeními. Bereme v úvahu mnoho faktorů, abyste zjistili, které pokusy o přihlášení jsou vysoké riziko. Mezi tyto faktory patří označení nakažených zařízení, anonymizace sítí a neobvyklých nebo nepravděpodobných umístění.

Mnohé z těchto sestav a událostí už jsou k dispozici prostřednictvím rozhraní API pro integraci se systémy SIEM (Security Information and Event Management).

### <a name="remote-access-as-a-service"></a>Vzdálený přístup jako služba

Nemusíte si dělat starosti s údržbou a opravou místních serverů.

Neopravný software je stále pro velký počet útoků. Azure Proxy aplikací služby AD je služba internetového škálování, kterou Microsoft vlastní, takže vždycky získáte nejnovější opravy a upgrady zabezpečení.

Abychom vylepšili zabezpečení aplikací publikovaných v Azure Proxy aplikací služby AD, zablokovali jsme roboty webového prohledávacího modulu z indexování a archivace vašich aplikací. Pokaždé, když se robot webového prohledávacího programu pokusí načíst nastavení robota pro publikovanou aplikaci, proxy aplikací aplikace odpoví souborem robots.txt, který obsahuje `User-agent: * Disallow: /` .

#### <a name="azure-ddos-protection-service"></a>Služba Azure DDoS Protection

Aplikace publikované prostřednictvím proxy aplikací jsou chráněné před distribuovanými útoky s cílem odepření služeb (DDoS). Tato ochrana je spravovaná Microsoftem a je automaticky povolená ve všech našich datacentrech. Služba Azure DDoS Protection zajišťuje nepřetržité monitorování provozu a zmírnění běžných útoků na úrovni sítě v reálném čase. 

## <a name="under-the-hood"></a>Pod pokličkou

Azure Proxy aplikací služby AD se skládá ze dvou částí:

* Cloudová služba: Tato služba běží v Azure a je tam, kde se nastavili externí připojení klienta nebo uživatele.
* [On-Premises Connector](application-proxy-connectors.md): místní komponenta konektor naslouchá žádostem ze služby Azure proxy aplikací služby AD a zpracovává připojení k interním aplikacím. 

Tok mezi konektorem a službou proxy aplikací je vytvořen v těchto případech:

* Konektor se nastavil jako první.
* Konektor vyžádá informace o konfiguraci ze služby proxy aplikací.
* Uživatel přistupuje k publikované aplikaci.

>[!NOTE]
>Veškerá komunikace probíhá přes protokol TLS a vždy pochází z konektoru na službu proxy aplikací. Služba je pouze odchozí.

Konektor používá klientský certifikát k ověřování pro službu proxy aplikací pro téměř všechna volání. Jedinou výjimkou z tohoto procesu je úvodní krok nastavení, ve kterém je vytvořen klientský certifikát.

### <a name="installing-the-connector"></a>Instalace konektoru

Při prvním nastavení konektoru dochází k následujícím událostem toku:

1. K registraci konektoru služby dochází jako součást instalace konektoru. Uživatelům se zobrazí výzva k zadání přihlašovacích údajů správce Azure AD. Token získaný z tohoto ověřování se pak zobrazí službě Azure Proxy aplikací služby AD.
2. Služba proxy aplikací vyhodnocuje token. Kontroluje, zda je uživatel globálním správcem v tenantovi. Pokud uživatel není správcem, proces se ukončí.
3. Konektor vygeneruje žádost o certifikát klienta a předá ho spolu s tokenem do služby proxy aplikací. Služba zase ověří token a podepíše žádost o certifikát klienta.
4. Konektor používá klientský certifikát pro budoucí komunikaci s proxy službou aplikace.
5. Konektor provádí počáteční načtení dat konfigurace systému ze služby pomocí jejího klientského certifikátu a je nyní připraven k přijetí požadavků.

### <a name="updating-the-configuration-settings"></a>Aktualizace nastavení konfigurace

Pokaždé, když služba proxy aplikace aktualizuje konfigurační nastavení, dojde k následujícím událostem toku:

1. Konektor se připojí ke koncovému bodu konfigurace v rámci služby proxy aplikací pomocí jeho klientského certifikátu.
2. Po ověření certifikátu klienta vrátí služba proxy aplikace do konektoru konfigurační data (například skupinu konektorů, jejíž konektor by měl být součástí).
3. Pokud je aktuální certifikát starší než 180 dní, konektor vygeneruje novou žádost o certifikát, která efektivně aktualizuje klientský certifikát každých 180 dnů.

### <a name="accessing-published-applications"></a>Přístup k publikovaným aplikacím

Když uživatelé přistupují k publikované aplikaci, probíhají mezi službou proxy aplikací a konektorem proxy aplikací tyto události:

1. Služba ověřuje uživatele pro aplikaci
2. Služba umístí požadavek ve frontě konektoru.
3. Konektor zpracuje požadavek z fronty.
4. Konektor čeká na odpověď.
5. Služba streamuje data uživateli.

Další informace o tom, co se v každém z těchto kroků provádí, najdete v tématu zachování.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. služba ověřuje uživatele pro aplikaci.

Pokud jste aplikaci nakonfigurovali tak, aby jako metodu předběžného ověření používala Passthrough, kroky v této části se přeskočí.

Pokud jste aplikaci nakonfigurovali k předověřování pomocí Azure AD, budou se uživatelé přesměrováni na službu Azure AD STS, aby ověřili a provedli tyto kroky:

1. Proxy aplikace kontroluje všechny požadavky zásad podmíněného přístupu pro konkrétní aplikaci. Tento krok zajistí, že uživatel byl přiřazen k aplikaci. Pokud je vyžadováno dvoustupňové ověřování, pořadí ověřování vyzve uživatele k zadání druhé metody ověřování.

2. Po úspěšném dokončení všech kontrol služba tokenů zabezpečení Azure AD vystaví podepsaný token pro aplikaci a přesměruje uživatele zpět na službu proxy aplikací.

3. Proxy aplikace ověří, že token byl vydán pro správnou aplikaci. Provádí také další kontroly, například zajištění, že token byl podepsán službou Azure AD a že je stále v platném okně.

4. Proxy aplikace nastaví šifrovaný ověřovací soubor cookie, který označuje, že došlo k ověřování aplikace. Tento soubor cookie obsahuje časové razítko vypršení platnosti, které je založené na tokenu z Azure AD a dalších dat, jako je například uživatelské jméno, na kterém je ověřování založené. Soubor cookie je zašifrovaný s privátním klíčem, který je známý jenom pro službu proxy aplikace.

5. Proxy aplikace přesměruje uživatele zpět na původně požadovanou adresu URL.

Pokud některá část kroků předběžného ověření neproběhne úspěšně, je žádost uživatele odepřena a uživatel se zobrazí zpráva s informacemi o zdroji problému.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. služba umístí požadavek ve frontě konektoru.

Konektory udržují odchozí připojení otevřené službě proxy aplikací. Když je požadavek dostupný v, služba zařadí požadavek na jedno z otevřených připojení, aby se konektor vybral.

Požadavek zahrnuje položky z aplikace, jako jsou například hlavičky žádosti, data z šifrovaného souboru cookie, uživatel, který požadavek odeslal, a ID žádosti. I když se k žádosti odesílají data z šifrovaného souboru cookie, samotný ověřovací soubor cookie není.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. konektor zpracuje požadavek z fronty. 

Na základě požadavku proxy aplikace provádí jednu z následujících akcí:

* Pokud se jedná o jednoduchou operaci (například v rámci těla žádosti RESTful *Get* nejsou žádná data), konektor vytvoří připojení k cílovému internímu prostředku a pak počká na odpověď.

* Pokud žádost obsahuje data, která jsou k němu přidružená v těle (například operace *post* RESTful), konektor vytvoří odchozí připojení pomocí klientského certifikátu k instanci proxy aplikace. Vytvoří toto připojení, aby vyžádalo data a otevřelo připojení k internímu prostředku. Po přijetí požadavku z konektoru služba proxy aplikací zahájí přijímání obsahu od uživatele a přesměruje data do konektoru. Konektor zase přepošle data do interního prostředku.

#### <a name="4-the-connector-waits-for-a-response"></a>4. konektor čeká na odpověď.

Po dokončení žádosti a přenosu veškerého obsahu do back-endu konektor počká na odpověď.

Až obdrží odpověď, konektor vytvoří odchozí připojení ke službě proxy aplikací, aby vrátil podrobnosti hlavičky a zahájil streamování vrácených dat.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. služba streamuje data uživateli. 

V takovém případě může dojít k určitému zpracování aplikace. Pokud jste nakonfigurovali proxy aplikace tak, aby přeložila hlavičky nebo adresy URL ve vaší aplikaci, může během tohoto kroku proběhnout zpracování podle potřeby.


## <a name="next-steps"></a>Další kroky

[Důvody síťové topologie při použití Azure Proxy aplikací služby AD](application-proxy-network-topology.md)

[Vysvětlení konektorů Azure Proxy aplikací služby AD](application-proxy-connectors.md)
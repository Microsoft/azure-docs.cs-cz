---
title: Důležité informace o zabezpečení pro proxy aplikace Azure AD | Dokumenty společnosti Microsoft
description: Popisuje důležité informace o zabezpečení pro použití proxy aplikace Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fd6794bafc3c209032f32626e8c46b51769d05e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481224"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Důležité informace o zabezpečení pro vzdálený přístup k aplikacím pomocí proxy aplikací Azure AD

Tento článek vysvětluje součásti, které pracují, aby vaši uživatelé a aplikace v bezpečí při použití proxy aplikace služby Azure Active Directory.

Následující diagram znázorňuje, jak Azure AD umožňuje zabezpečený vzdálený přístup k místním aplikacím.

 ![Diagram zabezpečeného vzdáleného přístupu prostřednictvím proxy aplikace Azure AD](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Výhody v oblasti zabezpečení

Azure AD Application Proxy nabízí následující výhody zabezpečení:

### <a name="authenticated-access"></a>Ověřený přístup 

Pokud se rozhodnete používat předběžné ověřování služby Azure Active Directory, budou mít přístup k vaší síti jenom ověřená připojení.

Azure AD Aplikační proxy spoléhá na službu tokenu zabezpečení Azure AD (STS) pro všechna ověřování.  Předběžné ověřování ze své podstaty blokuje významný počet anonymních útoků, protože pouze ověřené identity mají přístup k back-endové aplikaci.

Pokud zvolíte Passthrough jako metodu předběžného ověřování, nezískáte tuto výhodu. 

### <a name="conditional-access"></a>Podmíněný přístup

Před navázáním připojení k síti použijte bohatší ovládací prvky zásad.

Pomocí [podmíněného přístupu](../conditional-access/overview.md)můžete definovat omezení týkající se toho, jaký provoz je povolen pro přístup k aplikacím back-end. Můžete vytvořit zásady, které omezují přihlášení na základě umístění, síly ověřování a profilu rizika uživatele.

Podmíněný přístup můžete také použít ke konfiguraci zásad vícefaktorového ověřování a přidat další vrstvu zabezpečení k ověřování uživatelů. Kromě toho mohou být vaše aplikace směrovány do Microsoft Cloud App Security prostřednictvím podmíněného přístupu Azure AD a poskytovat monitorování a řízení v reálném čase prostřednictvím zásad [přístupu](https://docs.microsoft.com/cloud-app-security/access-policy-aad) a [relací.](https://docs.microsoft.com/cloud-app-security/session-policy-aad)

### <a name="traffic-termination"></a>Ukončení provozu

Veškerý provoz je ukončen v cloudu.

Vzhledem k tomu, že proxy aplikace Azure AD je reverzní proxy server, veškerý provoz na back-end aplikace je ukončena ve službě. Relace může získat znovu pouze s back-end server, což znamená, že vaše back-end servery nejsou vystaveny přímému přenosu HTTP. Tato konfigurace znamená, že jste lépe chráněni před cílenými útoky.

### <a name="all-access-is-outbound"></a>Veškerý přístup je odchozí. 

Není nutné otevírat příchozí připojení k podnikové síti.

Konektory proxy aplikací používají jenom odchozí připojení ke službě Proxy aplikací Azure AD, což znamená, že není nutné otevírat porty brány firewall pro příchozí připojení. Tradiční proxy servery vyžadovaly hraniční síť (označovanou také jako *DMZ*, *demilitarizovozónu*nebo *promítnutou podsíť)* a umožnily přístup k neověřeným připojením na okraji sítě. Tento scénář vyžadoval investice do produktů brány firewall webových aplikací k analýze provozu a ochraně životního prostředí. S proxy aplikací nepotřebujete hraniční síť, protože všechna připojení jsou odchozí a probíhají přes zabezpečený kanál.

Další informace o konektory naleznete [v tématu Principy konektorů proxy aplikací Azure AD](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Cloudová analytika a strojové učení 

Získejte špičkovou ochranu zabezpečení.

Vzhledem k tomu, že je součástí služby Azure Active Directory, může proxy aplikace využívat [Azure AD Identity Protection](../active-directory-identityprotection.md)s daty z Centra pro odezvu zabezpečení Microsoftu a oddělení digitálních zločinů. Společně proaktivně identifikujeme ohrožené účty a nabízíme ochranu před vysoce rizikovými přihlášeními. Bereme v úvahu řadu faktorů, které určují, které pokusy o přihlášení jsou vysoce rizikové. Mezi tyto faktory patří označování infikovaných zařízení, anonymizace sítí a atypická nebo nepravděpodobná umístění.

Mnohé z těchto sestav a událostí jsou již k dispozici prostřednictvím rozhraní API pro integraci s systémy správy informací o zabezpečení a událostí (SIEM).

### <a name="remote-access-as-a-service"></a>Vzdálený přístup jako služba

Nemusíte se starat o údržbu a opravy místních serverů.

Neopravený software stále představuje velké množství útoků. Azure AD Aplikační proxy je internetová služba, kterou vlastní Microsoft, takže vždy získáte nejnovější opravy zabezpečení a upgrady.

Abychom zlepšili zabezpečení aplikací publikovaných serverem Proxy aplikací Azure AD, blokujeme webové prohledávací roboty před indexováním a archivací vašich aplikací. Pokaždé, když se webový prohledávač robot pokusí načíst nastavení robota pro publikovanou aplikaci, app proxy odpoví souborem robots.txt, který obsahuje `User-agent: * Disallow: /`.

#### <a name="azure-ddos-protection-service"></a>Služba ochrany Před DoS Azure

Aplikace publikované prostřednictvím proxy aplikace jsou chráněny proti útokům DDoS (Distributed Denial of Service). **Azure DDoS protection** je služba nabízená s platformou Azure k ochraně prostředků Azure před útoky odmítnutí služby. Úroveň **základní** služby je automaticky povolena, poskytuje nepřetržité monitorování provozu a zmírňování běžných útoků na úrovni sítě v reálném čase. K dispozici je také úroveň **Standard,** která nabízí další možnosti zmírnění, které jsou vyladěné speciálně pro prostředky virtuální sítě Azure. Podrobnosti najdete [v tématu Přehled Azure DDoS Protection Standard](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

## <a name="under-the-hood"></a>Pod pokličkou

Proxy aplikace Azure AD se skládá ze dvou částí:

* Cloudová služba: Tato služba běží v Azure a je místo, kde jsou navazována připojení externích klientů a uživatelů.
* [Místní konektor](application-proxy-connectors.md): Místní součást, konektor naslouchá požadavkům ze služby Proxy aplikací Azure AD a zpracovává připojení k interním aplikacím. 

Tok mezi konektorem a službou Proxy aplikace je vytvořen, když:

* Konektor je nejprve nastaven.
* Konektor získává informace o konfiguraci ze služby Proxy aplikace.
* Uživatel přistupuje k publikované aplikaci.

>[!NOTE]
>Veškerá komunikace probíhá přes TLS a vždy pocházejí z konektoru ke službě Proxy aplikace. Služba je pouze odchozí.

Konektor používá klientský certifikát k ověření služby Proxy aplikace pro téměř všechna volání. Jedinou výjimkou z tohoto procesu je počáteční krok nastavení, kde je vytvořen klientský certifikát.

### <a name="installing-the-connector"></a>Instalace konektoru

Při prvním nastavení konektoru dojde k následujícím událostem toku:

1. Registrace konektoru do služby se stane jako součást instalace konektoru. Uživatelé se zobrazí výzva k zadání jejich přihlašovacích údajů správce Azure AD.Token získaný z tohoto ověřování se pak zobrazí službě Proxy aplikace Azure AD.
2. Služba Proxy aplikace vyhodnotí token. Zkontroluje, zda je uživatel správcem společnosti v tenantovi.Pokud uživatel není správcem, proces je ukončen.
3. Konektor vygeneruje žádost o certifikát klienta a předá ji spolu s tokenem službě Proxy aplikace. Služba zase ověří token a podepíše žádost o certifikát klienta.
4. Konektor používá klientský certifikát pro budoucí komunikaci se službou Proxy aplikace.
5. Konektor provádí počáteční vyžádat konfigurační data systému ze služby pomocí svého klientského certifikátu a je nyní připraven přijímat požadavky.

### <a name="updating-the-configuration-settings"></a>Aktualizace nastavení konfigurace

Kdykoli služba Proxy aplikace aktualizuje nastavení konfigurace, dojde k následujícím událostem toku:

1. Konektor se připojuje ke koncovému bodu konfigurace v rámci služby Proxy aplikace pomocí klientského certifikátu.
2. Po ověření klientského certifikátu vrátí služba Proxy aplikace konfigurační data konektoru (například skupinu konektorů, jejíž součástí by měl být konektor).
3. Pokud je aktuální certifikát starší než 180 dní, konektor vygeneruje novou žádost o certifikát, která efektivně aktualizuje klientský certifikát každých 180 dní.

### <a name="accessing-published-applications"></a>Přístup k publikovaným aplikacím

Když uživatelé přistupují k publikované aplikaci, mezi službou Proxy aplikace a konektorem proxy aplikace dochází k následujícím událostem:

1. Služba ověřuje uživatele aplikace
2. Služba umístí požadavek do fronty konektorů.
3. Spojnice zpracuje požadavek z fronty.
4. Konektor čeká na odpověď
5. Služba streamuje data uživateli

Chcete-li se dozvědět více o tom, co se děje v každém z těchto kroků, pokračujte ve čtení.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. Služba ověřuje uživatele aplikace

Pokud jste nakonfigurovali aplikaci tak, aby používala passthrough jako metodu předběžného ověřování, kroky v této části jsou přeskočeny.

Pokud jste aplikaci nakonfigurovali tak, aby se předem ověřovala pomocí Azure AD, uživatelé budou přesměrováni na službu Azure AD STS k ověření a provedou se následující kroky:

1. Proxy aplikace kontroluje všechny požadavky zásad podmíněného přístupu pro konkrétní aplikaci. Tento krok zajišťuje, že uživatel byl přiřazen k aplikaci. Pokud je vyžadováno dvoustupňové ověření, pořadí ověřování vyzve uživatele k použití druhé metody ověřování.

2. Po schválení všech kontrol vydá služba Azure AD STS podepsaný token pro aplikaci a přesměruje uživatele zpět na službu Proxy aplikace.

3. Proxy aplikace ověří, zda byl token vydán správné aplikaci. Provádí také další kontroly, jako je například zajištění, že token byl podepsán službou Azure AD a že je stále v platném okně.

4. Proxy aplikace nastaví šifrovaný ověřovací soubor cookie označující, že došlo k ověření aplikace. Soubor cookie obsahuje časové razítko vypršení platnosti, které je založeno na tokenu z Azure AD a dalších dat, jako je například uživatelské jméno, na které je založeno ověřování. Soubor cookie je zašifrován soukromým klíčem známým pouze službě Proxy aplikace.

5. Proxy aplikace přesměruje uživatele zpět na původně požadovanou adresu URL.

Pokud se nezdaří některá část kroků předběžného ověřování, je požadavek uživatele odmítnut a uživateli se zobrazí zpráva označující zdroj problému.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. Služba umístí požadavek do fronty konektorů

Konektory udržují odchozí připojení otevřené ke službě Proxy aplikace. Když přijde požadavek, služba fronty požadavek na jednom z otevřených připojení pro konektor vyzvednout.

Požadavek obsahuje položky z aplikace, jako jsou hlavičky požadavku, data z šifrovaného souboru cookie, uživatel, který žádost a ID žádosti. Přestože jsou data z šifrovaného souboru cookie odeslána spolu s požadavkem, ověřovací soubor cookie sám o sobě není.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. Konektor zpracuje požadavek z fronty. 

Na základě požadavku proxy aplikace provede jednu z následujících akcí:

* Pokud požadavek je jednoduchá operace (například není žádná data v těle, jako je s POŽADAVEK RESTful *GET),* spojnice vytvoří připojení k cílové interní prostředek a pak čeká na odpověď.

* Pokud má požadavek data přidružená k němu v těle (například operace RESTful *POST),* konektor provede odchozí připojení pomocí klientského certifikátu k instanci proxy aplikace. Vytvoří toto připojení k vyžádání dat a otevření připojení k internímu prostředku. Poté, co obdrží požadavek z konektoru, služba Proxy aplikace začne přijímat obsah od uživatele a předává data do konektoru. Konektor zase předá data do interního prostředku.

#### <a name="4-the-connector-waits-for-a-response"></a>4. Konektor čeká na odpověď.

Po dokončení požadavku a přenosu veškerého obsahu do back-endu konektor čeká na odpověď.

Poté, co obdrží odpověď, konektor vytvoří odchozí připojení ke službě Proxy aplikace, vrátit podrobnosti záhlaví a začít streamování zpětných dat.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. Služba streamuje data uživateli. 

Zde může dojít k určitému zpracování aplikace. Pokud jste nakonfigurovali proxy aplikace pro překlad záhlaví nebo adres URL ve vaší aplikaci, toto zpracování probíhá podle potřeby během tohoto kroku.


## <a name="next-steps"></a>Další kroky

[Důležité informace o topologii sítě při použití proxy aplikací Azure AD](application-proxy-network-topology.md)

[Principy konektorů proxy aplikací Azure AD](application-proxy-connectors.md)

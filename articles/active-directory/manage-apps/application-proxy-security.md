---
title: Informace o zabezpečení pro Proxy aplikací Azure AD | Dokumentace Microsoftu
description: Popisuje aspekty zabezpečení pro používání Azure AD Application Proxy
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 985ea1f16cff010041d61d808280cb47f2b77aa9
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618355"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Informace o zabezpečení pro přístup k aplikacím s využitím Azure AD Application Proxy

Tento článek popisuje komponenty, které pracují na bezpečnost vašich uživatelů a aplikací při použití Azure Active Directory Application Proxy.

Následující diagram znázorňuje, jak Azure AD umožňuje zabezpečený vzdálený přístup k místním aplikacím.

 ![Diagram zabezpečený vzdálený přístup prostřednictvím Proxy aplikací Azure AD](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Výhody zabezpečení

Proxy aplikací Azure AD nabízí následující výhody:

### <a name="authenticated-access"></a>Ověřený přístup 

Pokud se rozhodnete použít předběžné ověřování Azure Active Directory, můžete přístup jenom ověřené připojení k síti.

Proxy aplikací Azure AD spoléhá na Azure AD služby tokenů zabezpečení (STS) k veškerému ověřování.  Předběžné ověření, ze své podstaty blokuje velký počet anonymních útoky, protože pouze ověření identity můžou přistupovat k back endové aplikace.

Pokud se rozhodnete průchozí jako vaše metoda předběžného ověření, nebudete mít tuto výhodu. 

### <a name="conditional-access"></a>Podmíněný přístup

Použijte bohatší ovládací prvky zásad, předtím, než se naváže připojení k síti.

S [podmíněného přístupu](../conditional-access/overview.md), na jaký provoz je povolený přístup k back endovým aplikacím můžete definovat omezení. Můžete vytvořit zásady, které omezují přihlášení podle umístění, síla ověření a profil rizika pro uživatele.

Můžete také použít podmíněný přístup ke konfiguraci zásad ověřování službou Multi-Factor Authentication, přidává další vrstvu zabezpečení k ověřování vašich uživatelů. 

### <a name="traffic-termination"></a>Ukončení provozu

Veškerý provoz se ukončí v cloudu.

Protože Azure AD Application Proxy reverzních proxy serverů, veškerý provoz do back endovým aplikacím se ukončuje na službu. Relace můžete získat navázán pouze s back endového serveru, což znamená, že nejsou zveřejněné směrovat přenos dat protokolu HTTP back-end serverů. Tato konfigurace znamená, že je lépe chráněna před cílenými útoky.

### <a name="all-access-is-outbound"></a>Veškerý přístup probíhá odchozí 

Není nutné otevřít příchozí připojení k podnikové síti.

Konektory Proxy aplikací použít jenom odchozí připojení ke službě Azure AD Application Proxy, což znamená, že není třeba otevřít porty brány firewall pro příchozí připojení. Tradiční proxy vyžaduje hraniční síti (označované také jako *DMZ*, *demilitarizovaná zóna nebo*, nebo *monitorována podsíť*) a povolí se přístup k připojení bez ověření na hranici sítě. Tento scénář vyžaduje investice do brány firewall webových aplikací k analýze provozu a ochraně prostředí. Pomocí Proxy aplikace není nutné do hraniční sítě vzhledem k tomu, že všechna připojení jsou odchozí a probíhat přes zabezpečený kanál.

Další informace o konektorech najdete v tématu [pochopit Azure AD Application Proxy konektory](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Cloudových analýz a strojového učení 

Získáte špičkové zabezpečení ochrany.

Protože je součástí Azure Active Directory, můžou využívat Proxy aplikací [Azure AD Identity Protection](../active-directory-identityprotection.md), s daty z Microsoft Security Response Center a Digital Crimes Unit. Současně jsme aktivně identifikovat ohrožením účtů a nabízí ochranu od vysoce rizikových přihlášení. Můžeme vzít v úvahu řadu faktorů k určení, které po konci přihlášení jsou vysoce rizikové. Tyto faktory zahrnují označování nakažené zařízení, anonymizace sítě a neobvyklé nebo pravděpodobné umístění.

Mnohé z těchto sestav a události jsou již k dispozici prostřednictvím rozhraní API pro integraci se systémy pro správu (SIEM) událostí a informací o zabezpečení.

### <a name="remote-access-as-a-service"></a>Vzdáleného přístupu jako služba

Nemusíte se starat o údržbu a opravy na místních serverech.

Bez opravy zabezpečení softwaru stále účty pro velký počet útoků. Proxy aplikací Azure AD je služba internetovém měřítku, který vlastní Microsoft, takže vždycky získáte nejnovější opravami zabezpečení a inovace.

Tím se zvyšuje zabezpečení aplikací publikovaných serverem Azure AD Application Proxy, jsme blokovat webové prohledávacího modulu roboty, které z indexování a archivace vašich aplikací. Pokaždé, když robot prohledávacího modulu web, pokusí se načíst nastavení robot pro publikovanou aplikaci Proxy aplikací odpoví odpovědí s, která obsahuje soubor robots.txt `User-agent: * Disallow: /`.

### <a name="ddos-prevention"></a>Před útoky DDOS ochrany před únikem informací

Aplikace publikované prostřednictvím Proxy aplikací jsou chráněné proti útokům distribuován s cílem odepření služeb (DDOS).

Proxy aplikací služby monitoruje objem provozu, které chcete připojit svoje aplikace a síť. Pokud počet zařízení vyžadující vzdáleného přístupu pro vaše aplikace špičky, Microsoft omezuje přístup k vaší síti. 

Microsoft sleduje vzory provozu pro jednotlivé aplikace a pro vaše předplatné jako celek. Pokud vyšší než normální požadavky obdrží jednu aplikaci, požadavky na přístup k této aplikaci budou odepřeny na krátkou dobu. Pokud se zobrazí vyšší než normální požadavky napříč celé předplatné, je odepřen požadavky na přístup k některé z vašich aplikací. Preventivní opatření zachová aplikačních serverů přetížení žádostmi o vzdáleného přístupu, takže místních uživatelů můžete zachovat přístup k jejich aplikacím. 

## <a name="under-the-hood"></a>Pohled pod kapotu

Proxy aplikací Azure AD se skládá ze dvou částí:

* Cloudové služby: Tato služba běží v Azure a je provedena připojení externí klient/uživatel.
* [Software on-premises connector](application-proxy-connectors.md): jako součást místní konektor čeká na požadavky od připojení služby a obslužné rutiny Proxy aplikací Azure AD na interní aplikace. 

Vytvořit tok mezi konektoru a službou Proxy aplikací při:

* Konektor je nejprve nastavit.
* Konektor vrátí informace o konfiguraci ze služby Proxy aplikace.
* Uživatel má přístup k publikované aplikaci.

>[!NOTE]
>Veškerá komunikace probíhá přes SSL a vždy vycházet v konektoru Proxy aplikace služby. Tato služba je pouze odchozí.

Konektor používá klientský certifikát k ověření Proxy aplikací služby pro téměř všechna volání. Jedinou výjimkou z tohoto procesu je krok počáteční nastavení, ve kterém se naváže klientský certifikát.

### <a name="installing-the-connector"></a>Instalace konektoru

Pokud je nejprve konektor nastavit, provedou se následující události toku:

1. Jako součást instalace konektoru se stane registrace konektoru ke službě. Uživatelům se výzva k zadání přihlašovacích údajů správce Azure AD. Token získaných z tohoto ověřování se pak předává do služby Azure AD Application Proxy.
2. Proxy aplikací služby vyhodnotí token. Zkontroluje, jestli je uživatel správce společnosti v tenantovi. Pokud uživatel není správcem, proces se ukončí.
3. Konektor vygeneruje žádost o certifikát klienta a předává je, spolu s token pro službu Proxy aplikací. Služba pak ověří token a podepíše žádost o certifikát klienta.
4. Konektor používá certifikát klienta pro budoucí komunikaci se službou Proxy aplikací.
5. Konektor provede počáteční o přijetí změn systému konfiguračních dat ze služby pomocí jeho klientský certifikát a je teď jste připraveni udělat požadavky.

### <a name="updating-the-configuration-settings"></a>Aktualizuje se nastavení konfigurace

Pokaždé, když se Proxy aplikací služby aktualizuje nastavení konfigurace, provedou se následující události toku:

1. Konektoru připojí ke koncovému bodu konfigurace v rámci služby Proxy aplikace s použitím jeho klientského certifikátu.
2. Po ověření klientského certifikátu služby Proxy aplikace vrátí konfigurační data konektor (například skupina konektorů, která konektor musí být součástí).
3. Pokud aktuální certifikát je starší než 180 dní, konektor vygeneruje nové žádosti o certifikát, který efektivně aktualizuje certifikát klienta za 180 dní.

### <a name="accessing-published-applications"></a>Přístup k publikovaným aplikacím

Při přístupu uživatelů k publikované aplikaci, tyto události probíhat až konektor Proxy aplikací služby Proxy aplikace:

1. [Služba ověřuje uživatele pro aplikaci](#the-service-checks-the-configuration-settings-for-the-app)
2. [Službu umístí požadavek ve frontě connector](#The-service-places-a-request-in-the-connector-queue)
3. [Konektor zpracuje žádosti z fronty](#the-connector-receives-the-request-from-the-queue)
4. [Konektor čeká na odpověď](#the-connector-waits-for-a-response)
5. [Služby streamování dat pro uživatele](#the-service-streams-data-to-the-user)

Další informace o co se provádí v každém z těchto kroků, pokračujte ve čtení.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. Služba ověřuje uživatele pro aplikaci

Pokud jste nakonfigurovali aplikaci, aby používala průchozí jako její metoda předběžného ověření, kroky v této části se přeskočí.

Pokud jste nakonfigurovali aplikaci preauthenticate s Azure AD, uživatelé jsou přesměrováni do tokenů zabezpečení Azure AD k ověřování a provést následující kroky:

1. Proxy aplikací kontroluje všechny požadavky zásad podmíněného přístupu pro konkrétní aplikaci. Tento krok zajistí, že uživateli se přiřadila k aplikaci. Pokud je potřeba dvoustupňové ověření, pořadí ověřování vyzve uživatele k druhé metody ověřování.

2. Poté, co prošly všechny kontroly, služba tokenů zabezpečení služby Azure AD vydá token podepsaný držitelem pro aplikaci a přesměruje uživatele zpět na Proxy aplikace služby.

3. Proxy aplikace ověřuje, zda byl token vydán Chcete-li aplikace. Provede další kontroly také třeba zajistit, že byl token podepsán pomocí Azure AD a že je stále platný okna.

4. Nastavuje Proxy aplikace šifrované ověřovací soubor cookie k označení, že ověřování do aplikace došlo k chybě. Soubor cookie zahrnuje razítkem vypršení platnosti, která je založena na token ze služby Azure AD a dalších dat, jako je například uživatelské jméno ověřování založené na. Soubor cookie se šifruje s privátním klíčem, který znáte jenom službu Proxy aplikací.

5. Proxy aplikace přesměruje uživatele zpět na původně požadovanou URL adresu.

Pokud některá část kroky předběžného ověření selže, žádost uživatele se odepře a uživateli se zobrazí zprávu s oznámením příčiny problému.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. Službu umístí požadavek ve frontě connector

Konektory nechte odchozí připojení otevřené ve službě Proxy aplikací. Když přijde žádost, služba fronty požadavek na jednom z otevřená připojení pro konektor ke sbírání.

Požadavek obsahuje položky z aplikace, jako je například záhlaví požadavku, dat z šifrovaného souboru cookie uživatele, který vytváří žádosti a ID požadavku. I když se požadavek neposílají data ze šifrovaného souboru cookie, je samotný soubor cookie ověřování.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. Konektor zpracuje žádosti z fronty. 

Na základě daného požadavku, provede Proxy aplikací jednu z následujících akcí:

* Pokud požadavek je jednoduchá operace (například nejsou žádná data v rámci textu, jako je s RESTful *získat* žádosti), konektor vytvoří připojení k interní cílový prostředek a potom čeká na odpověď.

* Pokud požadavek má dat s ním spojená v textu (například RESTful *příspěvek* operace), konektor vytvoří odchozí připojení pomocí certifikátu klienta pro instanci Proxy aplikací. Je toto připojení k vyžádání dat a otevřít připojení k vnitřních prostředků. Po přijetí požadavku z konektoru nástroje, služby Proxy aplikace začne přijímat obsah od uživatele a předá data do konektoru. Konektoru, pak předá data, která mají vnitřních prostředků.

#### <a name="4-the-connector-waits-for-a-response"></a>4. Konektor čeká na odpověď.

Po dokončení požadavku a přenos veškerý obsah do back-endu konektoru čeká na odpověď.

Po obdržení odpovědi, konektor umožňuje odchozí připojení ke službě Proxy aplikací, a vrátí podrobnosti o hlavičky a začít Streamovat návratová data.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. Služby streamování dat pro uživatele. 

Může dojít k nějaké zpracování žádosti. Pokud jste nakonfigurovali Proxy aplikací přeložit záhlaví nebo adresy URL ve vaší aplikaci, že zpracování se stane, podle potřeby během tohoto kroku.


## <a name="next-steps"></a>Další postup

[Aspekty topologie sítě při použití Azure AD Application Proxy](application-proxy-network-topology.md)

[Principy konektorů Proxy aplikací Azure AD](application-proxy-connectors.md)

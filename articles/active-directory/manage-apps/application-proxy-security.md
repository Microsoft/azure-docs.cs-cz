---
title: Důležité informace o zabezpečení pro proxy aplikace služby Azure AD | Microsoft Docs
description: Popisuje aspekty zabezpečení pomocí proxy aplikace služby Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: aaec5febaa8d697ceb9fd32d3bcdd6c37c399e84
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34162033"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Důležité informace o zabezpečení pro přístup k aplikacím vzdáleně pomocí proxy aplikace služby Azure AD

Tento článek vysvětluje komponenty, které fungují a zabezpečit uživatelů a aplikací při použití aplikace Proxy Azure Active Directory.

Následující diagram ukazuje, jak Azure AD umožňuje zabezpečený vzdálený přístup k místním aplikacím.

 ![Diagram zabezpečený vzdálený přístup prostřednictvím proxy aplikace služby Azure AD](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Výhody zabezpečení

Azure AD Application Proxy nabízí následující výhody zabezpečení:

### <a name="authenticated-access"></a>Ověřený přístup 

Pokud se rozhodnete použít předběžné ověřování Azure Active Directory, můžete přístup pouze ověřené připojení k síti.

Azure AD Application Proxy spoléhá na Azure AD služby tokenů zabezpečení (STS) pro všechny ověřování.  Předběžné ověření, ze své podstaty blokuje velký počet anonymní útoky, protože pouze ověřených identit přístup k aplikaci back-end.

Pokud si zvolíte průchozí jako způsob předběžného ověření, neobdržíte této výhody. 

### <a name="conditional-access"></a>Podmíněný přístup

Použijete širší zásad řízení předtím, než se vytvoří připojení k síti.

S [podmíněného přístupu](../active-directory-conditional-access-azure-portal-get-started.md), můžete definovat omezení na jaký provoz je povolený přístup k aplikacím vaší back-end. Můžete vytvořit zásady, které omezují přihlášení v závislosti na umístění, sílu ověřování a riziko profilu uživatele.

Můžete taky podmíněného přístupu můžete nakonfigurovat zásady služby Multi-Factor Authentication, přidání další úroveň zabezpečení do vaší ověřování uživatele. 

### <a name="traffic-termination"></a>Ukončení provozu

Veškerý provoz, je ukončen v cloudu.

Proxy aplikace služby Azure AD je reverznímu proxy serveru, a proto je veškerý provoz do back-end aplikace ukončena na službu. Relaci můžete získat obnovila pouze s back-end serverů, což znamená, že nejsou pro přímý přenos HTTP zveřejněné back-end serverů. Tato konfigurace znamená, že je líp chráněný před cílenými útoky.

### <a name="all-access-is-outbound"></a>Je odchozí veškerý přístup. 

Nemusíte otevřete příchozí připojení k podnikové síti.

Konektory Proxy aplikace použít pouze odchozí připojení ke službě Azure AD Application Proxy, což znamená, že je potřeba otevřít porty brány firewall pro příchozí spojení. Tradiční proxy vyžaduje hraniční síti (označované také jako *DMZ*, *demilitarizovaná zóna*, nebo *monitorována podsíť*) a v hraniční sítě může být přístup k připojení bez ověření. Tento scénář vyžaduje investice do webové aplikace firewally analyzovat provoz a chránit prostředí. Pomocí Proxy aplikace nepotřebujete hraniční síti, protože všechna připojení jsou odchozí a provést přes zabezpečený kanál.

Další informace o konektory najdete v tématu [pochopit Azure AD Application Proxy konektory](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Analýza cloudové škálování a machine learningu 

Získáte ochranu nejmodernější zabezpečení.

Protože je součástí služby Azure Active Directory, můžete využít Proxy aplikace [Azure AD Identity Protection](../active-directory-identityprotection.md), s daty z Microsoft Security Response Center a jejichž šíření tým Dcu. Společně jsme aktivně identifikovat ohroženými účty a nabízejí ochranu proti s vysokým rizikem přihlášení. Jsme vzít v úvahu mnoho faktory určit, které přihlášení docházelo jsou vysoce rizikové. Tyto faktory zahrnují nastavením příznaku nakažených zařízení, zavedení anonymity sítí a netypických nebo pravděpodobně umístění.

Mnoho tyto sestavy a události je již k dispozici prostřednictvím rozhraní API pro integraci s informace o zabezpečení a událostí systémy správy (SIEM).

### <a name="remote-access-as-a-service"></a>Vzdálený přístup jako služby

Nemusíte si dělat starosti o údržbu a opravy na místní servery.

Neopravené softwaru stále účty pro velký počet útoků. Proxy aplikace služby Azure AD je internetových služba, která Microsoft vlastní, takže neustále stahovat nejnovější opravy zabezpečení a upgrady.

Chcete-li zlepšit zabezpečení aplikací publikovaných serverem proxy aplikace služby Azure AD, jsme zablokovat webové prohledávacího modulu robotů ze indexování a archivaci aplikace. Pokaždé, když robot webový prohledávací modul, pokusí se načíst nastavení robot pro publikované aplikace Proxy aplikace odpoví odpovědí s robots.txt soubor, který zahrnuje `User-agent: * Disallow: /`.

### <a name="ddos-prevention"></a>Prevence DDOS

Aplikacích publikovaných prostřednictvím Proxy aplikace jsou chráněny před útoky distribuované útok na dostupnost služby (DDOS).

Služba Proxy aplikace monitoruje objem provozu, které chcete připojit vaše aplikace a síť. Pokud počet zařízení, které žádají o vzdálený přístup do vaší aplikace špičky, Microsoft omezením přístupu k síti. 

Microsoft sleduje vzory přenosů dat pro jednotlivé aplikace a pro vaše předplatné jako celek. Pokud jedna aplikace obdrží vyšší než normální požadavky, požadavky na přístup k této aplikaci budou odepřeny na krátkou dobu. Pokud se zobrazí vyšší než normální požadavky napříč celou předplatné, je odepřen požadavky na přístup k libovolnému vaší aplikace. Toto preventivní opatření zachová aplikačních serverů z přetížen žádostmi o vzdáleného přístupu, takže místních uživatelů můžete zachovat přístup k jejich aplikace. 

## <a name="under-the-hood"></a>Pod pokličkou

Proxy aplikace služby Azure AD se skládá ze dvou částí:

* Cloudové služby: Tato služba běží v Azure a je, kde jsou vytvářeny připojení externí klient/uživatel.
* [Místní konektor](application-proxy-connectors.md): komponentu místní konektor naslouchá požadavkům z Azure AD Application Proxy služby a obslužné rutiny připojení k interní aplikace. 

Tok mezi konektor a služba Proxy aplikace je vytvořeno při:

* Konektor je nejdřív nastavit.
* Konektor vrátí informace o konfiguraci ze služby Proxy aplikace.
* Přístupu uživatele k publikované aplikaci.

>[!NOTE]
>Veškerá komunikace probíhá přes protokol SSL a vždy vycházet v konektoru Proxy aplikace služby. Služba je pouze odchozí.

Tento konektor využívá certifikát klienta k ověření služby Proxy aplikace u skoro všech volání. Jedinou výjimkou tohoto procesu je počáteční nastavení krok, kde je navázat certifikát klienta.

### <a name="installing-the-connector"></a>Instalace konektoru

Pokud konektor je nejdřív nastavený, následující události toku proběhnout:

1. Jako součást instalace konektoru se stane registrace konektoru ke službě. Uživatelé se výzva k zadání přihlašovacích údajů správce Azure AD. Pro službu Azure AD Application Proxy pak zobrazí token získali z tohoto ověřování.
2. Služba Proxy aplikace vyhodnotí token. Zkontroluje, zda uživatel je správce společnosti v klientovi. Pokud uživatel není správcem, ukončení procesu.
3. Konektor generuje žádost o certifikát klienta a předává je, společně s token, služba Proxy aplikace. Služba zase ověří token a podepíše žádost o certifikát klienta.
4. Tento konektor využívá certifikát klienta pro budoucí komunikaci se službou Proxy aplikace.
5. Konektor provádí počáteční přijetí změn systému konfiguračních dat ze služby pomocí jeho klientský certifikát a je nyní připravena převzít požadavky.

### <a name="updating-the-configuration-settings"></a>Aktualizuje se nastavení konfigurace

Vždy, když služba Proxy aplikace aktualizuje nastavení konfigurace, následující události toku proběhnout:

1. Konektor připojuje ke koncovému bodu konfigurace v rámci služby Proxy aplikace pomocí jeho klientský certifikát.
2. Po ověření certifikátu klienta se vrátí služba Proxy aplikace konfigurační data do konektor (například konektor skupině, která konektor musí být součástí).
3. Pokud je aktuální certifikát víc než 180 dní, konektor generuje novou žádost o certifikát, který efektivně aktualizace certifikát klienta za 180 dní.

### <a name="accessing-published-applications"></a>Přístup k publikovaným aplikacím

Při přístupu uživatelů k publikované aplikaci, následující události proběhnout mezi Proxy aplikace služby a konektor Proxy aplikace:

1. [Služba ověřuje uživatele pro aplikaci](#the-service-checks-the-configuration-settings-for-the-app)
2. [Služba umístí požadavek ve frontě konektoru](#The-service-places-a-request-in-the-connector-queue)
3. [Konektor zpracuje požadavek z fronty](#the-connector-receives-the-request-from-the-queue)
4. [Konektor čeká na odpověď](#the-connector-waits-for-a-response)
5. [Služba datové proudy dat uživatele](#the-service-streams-data-to-the-user)

Chcete-li další informace o co probíhá v každé z těchto kroků, uchovávejte čtení.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. Služba ověřuje uživatele pro aplikaci

Pokud jste nakonfigurovali aplikaci, aby používala průchozí jako jeho metoda předběžného ověření, kroky v této části se přeskočí.

Pokud jste nakonfigurovali aplikaci preauthenticate s Azure AD, uživatelé přesměrovaní na tokenů zabezpečení Azure AD k ověření a provést následující kroky:

1. Proxy aplikací kontroluje všechny požadavky zásad podmíněného přístupu pro konkrétní aplikaci. Tento krok zajistí, že uživatel přiřazený k aplikaci. Pokud dvoustupňové ověření je potřeba, pořadí ověřování vyzve uživatele k druhé metody ověřování.

2. Po všechny kontroly, služby tokenů zabezpečení služby Azure AD vydá token podepsaný držitelem pro aplikaci a přesměruje uživatele zpět na Proxy aplikace služby.

3. Proxy aplikací ověřuje, zda byl token vydán opravit aplikace. K dalším kontrolám vykonává také třeba zajistit, aby byl podepsán token službou Azure AD a že je stále v okně platný.

4. Nastaví Proxy aplikace šifrovaný soubor cookie označíte, že ověřování do aplikace došlo k chybě. Soubor cookie zahrnuje vypršení platnosti časového razítka, která je založená na tokenu z Azure AD a další data, jako je například uživatelské jméno, které je na základě ověření. Soubor cookie je šifrován privátní klíč, který zná pouze služba Proxy aplikace.

5. Proxy aplikace přesměruje uživatele zpět na původně požadovanou URL adresu.

Pokud se libovolná součást kroky předběžného ověření nezdaří, je odepřena žádost uživatele a uživateli se zobrazí zprávu s upozorněním zdroj problému.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. Služba umístí požadavek ve frontě konektoru

Konektory nechte odchozí připojení otevřené Proxy aplikace služby. Když přijde žádost, služba fronty požadavek na jedno z připojení k otevřete vyzvedne, až konektor.

Požadavek obsahuje položky z aplikace, třeba hlavičky žádosti, dat z šifrovaného souboru cookie uživatele, který vytvořil požadavek a ID požadavku. I když se pošle dat z šifrovaného souboru cookie s požadavkem samotném souboru cookie ověřování není.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. Konektor zpracuje požadavek z fronty. 

Na základě daného požadavku, provádí aplikace Proxy jednu z následujících akcí:

* Pokud požadavek je jednoduchá operace (například nejsou žádná data v textu, jako je se dosáhl standardu RESTful *získat* požadavek), konektor vytvoří připojení k interní cílový prostředek a potom čeká na odpověď.

* Pokud požadavek má data přidružená v těle (například dosáhl standardu RESTful *POST* operaci), konektor vytvoří odchozí připojení pomocí certifikátu klienta do instance Proxy aplikace. Umožňuje toto připojení k vyžádání dat a otevřít připojení k interní prostředek. Po přijetí žádosti z konektoru nástroje, služba Proxy aplikace začne přijímat obsah od uživatele a předává data ke konektoru. Tento konektor, pak předá data do interní prostředku.

#### <a name="4-the-connector-waits-for-a-response"></a>4. Konektor čeká na odpověď.

Po dokončení požadavku a přenosu veškerý obsah na back-end konektor čeká na odpověď.

Po obdržení odpovědi, konektor vytvoří odchozí připojení ke službě Proxy aplikace vrátit podrobnosti záhlaví a začněte streamování vracená data.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. Služba datové proudy dat pro uživatele. 

Některé zpracování aplikace může dojít, zde. Pokud jste nakonfigurovali Proxy aplikace přeložit hlavičky nebo adresy URL v aplikaci, se stane toto zpracování podle potřeby v tomto kroku.


## <a name="next-steps"></a>Další postup

[Aspekty topologie sítě při použití proxy aplikace služby Azure AD](application-proxy-network-topology.md)

[Pochopení konektory proxy aplikace služby Azure AD](application-proxy-connectors.md)

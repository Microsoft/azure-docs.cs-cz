---
title: Monitorování metrik a protokolů v dopředných dveřích Azure | Microsoft Docs
description: Tento článek popisuje různé metriky a protokoly přístupu, které podporuje přední dvířka Azure.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: 58db217ca173acbe0356596de916216c4ab7f241
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715543"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Monitorování metrik a protokolů v frontách Azure na předních dveřích

Pomocí front-dveří Azure můžete monitorovat prostředky následujícími způsoby:

- **Metriky**. Přední dveře Azure mají v současnosti osm metrik pro zobrazení čítačů výkonu.
- **Protokoly**. Protokoly aktivit a diagnostiky umožňují uložit nebo spotřebovat data z prostředku pro účely monitorování.

##  <a name="metrics"></a><a name="metrics"></a>Metriky

Metriky jsou funkce pro určité prostředky Azure, které umožňují zobrazit čítače výkonu na portálu. K dispozici jsou následující metriky front dveří:

| Metric | Zobrazovaný název metriky | Jednotka | Dimenze | Popis |
| --- | --- | --- | --- | --- |
| RequestCount | Počet požadavků | Počet | Stavu protokolu http</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet požadavků klientů poskytovaných předními dveřmi.  |
| RequestSize | Velikost požadavku | Bajty | Stavu protokolu http</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet bajtů odeslaných jako požadavek od klientů do předních dveří. |
| ResponseSize | Velikost odpovědi | Bajty | Stavu protokolu http</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Počet bajtů odeslaných jako odpověď z front-dveří klientům. |
| TotalLatency | Celková latence | Milisekund | Stavu protokolu http</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Celková doba od žádosti klienta přijatá předními dveřmi, dokud poslední bajt odpovědi odeslaný z AFD na klienta. |
| BackendRequestCount | Počet požadavků back-endu | Počet | Stavu protokolu http</br>HttpStatusGroup</br>Back-end | Počet požadavků odeslaných z předních dveří do back-endu. |
| BackendRequestLatency | Latence žádosti back-endu | Milisekund | Back-end | Čas vypočítaný z doby, kdy byla žádost odeslána přes dvířka do back-endu, dokud přední dveře nedostaly poslední bajt odezvy z back-endu. |
| BackendHealthPercentage | Procento stavu back-endu | Procento | Back-end</br>Problémových | Procento úspěšných sond stavu z předních dveří do back-endu. |
| WebApplicationFirewallRequestCount | Počet požadavků firewallu webových aplikací | Počet | PolicyName</br>RuleName</br>Akce | Počet požadavků klientů zpracovaných zabezpečením aplikační vrstvy předních dveří. |

## <a name="activity-logs"></a><a name="activity-log"></a>Protokoly aktivit

Protokoly aktivit obsahují informace o operacích provedených na Frontních dveřích. Určují taky to, kdo a kdy se při operacích zápisu (Put, post nebo Delete) převezme na přední dveře.

>[!NOTE]
>Protokoly aktivit nezahrnují operace čtení (Get). Nezahrnují také operace, které provádíte pomocí Azure Portal nebo původního rozhraní API pro správu.

Přístup k protokolům aktivit ve front-Vratích nebo ve všech protokolech prostředků Azure v Azure Monitor. Zobrazení protokolů aktivit:

1. Vyberte instanci front-dveří.
2. Vyberte **Protokol aktivit**.

    :::image type="content" source="./media/front-door-diagnostics/activity-log.png" alt-text="Protokol aktivit":::

3. Zvolte rozsah filtrování a pak vyberte **použít**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Diagnostické protokoly
Diagnostické protokoly poskytují obsáhlé informace o operacích a chybách, které jsou důležité pro auditování a řešení potíží. Diagnostické protokoly se liší od protokolů aktivit.

Protokoly aktivit poskytují přehled o operacích provedených v prostředcích Azure. Diagnostické protokoly poskytují přehled o operacích, které váš prostředek dokončil. Další informace najdete v tématu [Azure monitor diagnostické protokoly](../azure-monitor/essentials/platform-logs-overview.md).

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="Diagnostické protokoly":::

Konfigurace diagnostických protokolů pro vaše přední dveře:

1. Vyberte si přední dvířka Azure.

2. Vyberte **nastavení diagnostiky**.

3. Vyberte **zapnout diagnostiku**. Archivujte diagnostické protokoly spolu se metrikami do účtu úložiště, streamujte je do centra událostí nebo je odešlete do protokolů Azure Monitor.

Přední dveře aktuálně poskytují protokoly diagnostiky (v dávce po hodinách). Diagnostické protokoly poskytují jednotlivé požadavky rozhraní API s každou položkou, která má následující schéma:

| Vlastnost  | Popis |
| ------------- | ------------- |
| BackendHostname | Pokud byl požadavek předán do back-endu, toto pole představuje název hostitele back-endu. Pokud se požadavek přesměruje nebo přesměruje do místní mezipaměti (Pokud je u pravidla směrování povolený ukládání do mezipaměti), bude toto pole prázdné. |
| CacheStatus | V případě scénářů pro ukládání do mezipaměti toto pole definuje počet přístupů do mezipaměti nebo neúspěšný přístup na adresu POP. |
| ClientIp | IP adresa klienta, který odeslal požadavek. Pokud v žádosti existovala hlavička X předaná-pro, pak je IP adresa klienta převzata ze stejné. |
| ClientPort | Port IP klienta, který odeslal požadavek. |
| HttpMethod | Metoda HTTP, kterou požadavek používá |
| HttpStatusCode | Stavový kód HTTP vrácený z proxy serveru. |
| HttpStatusDetails | Výsledný stav žádosti. Význam této řetězcové hodnoty lze nalézt v tabulce odkazů na stav. |
| HttpVersion | Typ požadavku nebo připojení |
| POP | Krátký název okraje, ve kterém se vyložila žádost |
| RequestBytes | Velikost zprávy požadavku HTTP v bajtech, včetně hlaviček požadavků a textu žádosti. |
| RequestUri | Identifikátor URI přijatého požadavku |
| ResponseBytes | Bajty odeslané back-end serverem jako odpověď  |
| RoutingRuleName | Název pravidla směrování, na které se požadavek shodoval. |
| RulesEngineMatchNames | Názvy pravidel, která požadavek odpovídá. |
| Tato SecurityProtocol | Verze protokolu TLS/SSL používaná požadavkem nebo hodnotou null, pokud není šifrování. |
| SentToOriginShield </br> (nepoužívané) * v **následující části najdete poznámky k** vyřazení.| Pokud má hodnotu true, znamená to, že žádost byla zodpovězena z počáteční mezipaměti ochrany před hraničním rozhraním pop. Počáteční ochrana je nadřazená mezipaměť, která se používá ke zvýšení poměru přístupů do mezipaměti. |
| isReceivedFromClient | Pokud má hodnotu true, znamená to, že požadavek pochází z klienta. Pokud je hodnota false, je požadavek na hranu (podřízený bod POP) neúspěšný a reaguje na počátek ochrany (nadřazený bod POP). |
| TimeTaken | Doba od prvního bajtu žádosti do posledního bajtu odpovědi v sekundách. |
| TrackingReference | Jedinečný referenční řetězec, který identifikuje požadavek, který je obsluhován předními dvířky, je také odeslán jako hlavička X-Azure-ref na klienta. Vyžaduje se pro hledání podrobností v protokolech přístupu pro konkrétní požadavek. |
| UserAgent | Typ prohlížeče, který klient použil. |
| Nastavena | Toto pole obsahuje konkrétní typ chyby pro další řešení potíží. </br> Mezi možné hodnoty patří: </br> **Chyba**: indikuje, že se nenašla žádná chyba. </br> **CertificateError**: Chyba obecného certifikátu SSL.</br> **CertificateNameCheckFailed**: název hostitele v certifikátu SSL je neplatný nebo neodpovídá. </br> **ClientDisconnected**: požadavek se nezdařil z důvodu připojení k síti klienta. </br> **UnspecifiedClientError**: došlo k obecné chybě klienta. </br> **InvalidRequest**: neplatný požadavek. Může to být způsobeno chybnou hlavičkou, textem a adresou URL. </br> **DNSFailure**: selhání serveru DNS. </br> **DNSNameNotResolved**: nepovedlo se přeložit název nebo adresu serveru. </br> **OriginConnectionAborted**: spojení s počátkem bylo náhle zastaveno. </br> **OriginConnectionError**: Chyba obecného připojení k původnímu zdroji. </br> **OriginConnectionRefused**: připojení k původnímu zdroji nebylo možné navázat. </br> **OriginError**: Chyba obecného původce </br> **OriginInvalidResponse**: počátek vrátil neplatnou nebo nerozpoznanou odpověď. </br> **OriginTimeout**: vypršel časový limit pro vypršení platnosti žádosti o zdroj. </br> **ResponseHeaderTooBig**: původ vrátil příliš velkou velikost hlavičky odpovědi. </br> **RestrictedIP**: požadavek byl zablokován kvůli omezené IP adrese. </br> **SSLHandshakeError**: nepovedlo se navázat spojení se zdrojem z důvodu selhání protřepení SSL. </br> **UnspecifiedError**: došlo k chybě, která se nevešla do žádné chyby v tabulce. |

### <a name="sent-to-origin-shield-deprecation"></a>Bylo odesláno do původního základu ochrany.
Nezpracovaná vlastnost protokolu **isSentToOriginShield** byla zastaralá a nahrazena novým polem **isReceivedFromClient**. Pokud už používáte zastaralé pole, použijte nové pole. 

Nezpracované protokoly obsahují protokoly vygenerované z hraniční sítě CDN (podřízeného bodu POP) i z počátečního štítku. Počátek štítku odkazuje na nadřazené uzly, které jsou strategicky umístěné na celém světě. Tyto uzly komunikují se zdrojovými servery a omezují zatížení provozu na počátku. 

Pro každý požadavek, který směřuje ke zdroji štítku, existují 2 – položky protokolu:

* Jeden pro hraniční uzly
* Jednu pro zdroj štítku. 

Chcete-li odlišit výstupy nebo odpovědi z hraničních uzlů vs. od počátku, můžete k získání správných dat použít pole **isReceivedFromClient** . 

Pokud je hodnota false, znamená to, že požadavek reaguje od počátku ochrany do hraničních uzlů. Tento přístup je platný pro porovnání nezpracovaných protokolů s fakturačními daty. Neúčtují se poplatky za výstup od počátku ochrany od počátku do hraničních uzlů. Poplatky za výstup z hraničních uzlů do klientů se účtují. 

**Ukázka dotazu Kusto pro vyloučení protokolů vygenerovaných z počátečního štítku v Log Analytics.**

`AzureDiagnostics 
| where Category == "FrontdoorAccessLog" and isReceivedFromClient_b == true`

> [!NOTE]
> U různých konfigurací směrování a chování provozu můžou některá pole jako backendHostname, cacheStatus, isReceivedFromClient a POP reagovat pomocí různých hodnot. Následující tabulka vysvětluje různé hodnoty, které tato pole budou mít pro různé scénáře:

| Scénáře | Počet položek protokolu | POP | BackendHostname | isReceivedFromClient | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Pravidlo směrování bez povoleného ukládání do mezipaměti | 1 | Hraniční kód POP | Back-end, kde byl požadavek předán | Ano | CONFIG_NOCACHE |
| Pravidlo směrování s povoleným ukládáním do mezipaměti. Přístup do mezipaměti na hraničním okně Edge | 1 | Hraniční kód POP | Obsahovat | Ano | KLEPNUTÍ |
| Pravidlo směrování s povoleným ukládáním do mezipaměti. Neúspěšné přístupy do mezipaměti na hraničním místě POP, ale přístupů do mezipaměti v nadřazené mezipaměti | 2 | 1. hraniční kód POP</br>2. kód POP nadřazené mezipaměti | 1. název hostitele POP nadřazené mezipaměti</br>2. prázdné | 1. true</br>2. false | 1. NEÚSPĚŠNÉ</br>2. PŘÍSTUPŮ |
| Pravidlo směrování s povoleným ukládáním do mezipaměti. Při ukládání do mezipaměti došlo na hraničním bodu POP, ale při ČÁSTEČNÉm přístupu do mezipaměti v nadřazené mezipaměti POP | 2 | 1. hraniční kód POP</br>2. kód POP nadřazené mezipaměti | 1. název hostitele POP nadřazené mezipaměti</br>2. back-end, který pomáhá naplnit mezipaměť | 1. true</br>2. false | 1. NEÚSPĚŠNÉ</br>2. PARTIAL_HIT |
| Pravidlo směrování s povoleným ukládáním do mezipaměti. Ukládat PARTIAL_HIT mezipaměti na hraničním bodu POP, ale v mezipaměti se nachází v nadřazené mezipaměti | 2 | 1. hraniční kód POP</br>2. kód POP nadřazené mezipaměti | 1. hraniční kód POP</br>2. kód POP nadřazené mezipaměti | 1. true</br>2. false | 1. PARTIAL_HIT</br>2. PŘÍSTUPŮ |
| Pravidlo směrování s povoleným ukládáním do mezipaměti. Neúspěšné přístupy do mezipaměti na hraničních i nadřazených položkách mezipaměti | 2 | 1. hraniční kód POP</br>2. kód POP nadřazené mezipaměti | 1. hraniční kód POP</br>2. kód POP nadřazené mezipaměti | 1. true</br>2. false | 1. NEÚSPĚŠNÉ</br>2. NEÚSPĚŠNÉ |

> [!NOTE]
> V případě scénářů pro ukládání do mezipaměti bude hodnota pro stav mezipaměti partial_hit, když se některé bajty pro požadavek dostanou z hraničního zařízení na okraji předních dveří nebo z mezipaměti ochrany počáteční paměti, zatímco některé z bajtů se dodávají od počátku pro velké objekty.

Přední dvířka používají techniku nazvanou bloků objektů. Když je požadován velký soubor, načte přední dvířka menší části souboru od počátku. Poté, co server POP pro front-in obdrží plný nebo v bajtech požadovaný soubor, požádá server Edge z domu na soubor od počátku v blocích po 8 MB.

Po obdržení bloku dat na přední straně dveře je tato část ukládána do mezipaměti a okamžitě se jí dodrží uživateli. Přední dveře potom předem načítají další blok dat paralelně. Tento předběžný postup zajistí, že obsah zůstane jeden blok před uživatelem, což snižuje latenci. Tento proces pokračuje, dokud se celý soubor nestáhne (Pokud je to požadováno), jsou dostupné všechny rozsahy bajtů (Pokud se požaduje), nebo klient připojení ukončí. Další informace o požadavku na rozsah bajtů najdete v dokumentu RFC 7233. Přední dveře ukládá do mezipaměti všechny bloky, jak jsou přijaty. Celý soubor není nutné ukládat do mezipaměti v mezipaměti front-dveří. Požadavky na rozsah souborů nebo bajtů jsou obsluhovány z mezipaměti front-dveří. Pokud se všechny bloky dat v mezipaměti neukládají do mezipaměti, použije se k vypsání bloků od počátku předběžné načtení. Tato optimalizace spoléhá na to, jestli má zdrojový server podporovat požadavky na rozsah bajtů. Pokud zdrojový server nepodporuje požadavky na rozsah bajtů, tato optimalizace se neprojeví.

## <a name="next-steps"></a>Další kroky

- [Vytvoření profilu front-dveří](quickstart-create-front-door.md)
- [Jak fungují přední dveře](front-door-routing-architecture.md)

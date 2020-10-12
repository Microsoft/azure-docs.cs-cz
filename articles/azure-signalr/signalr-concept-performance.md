---
title: Průvodce výkonem pro službu Azure SignalR Service
description: Přehled výkonu a srovnávacích testů služby Azure Signal Service. Klíčové metriky, které je potřeba vzít v úvahu při plánování kapacity.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74157666"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Průvodce výkonem pro službu Azure SignalR Service

Jednou z klíčových výhod používání služby signalizace Azure je snadné škálování aplikací signalizace. Ve velkém scénáři je výkon důležitým faktorem. 

V této příručce zavedeme faktory, které ovlivňují výkon aplikace Signal. Popisujeme typický výkon v různých scénářích použití. Na konci zavádíme prostředí a nástroje, které můžete použít k vygenerování sestavy výkonu.

## <a name="term-definitions"></a>Definice termínů

*Příchozí*: příchozí zpráva do služby Azure Signal Service.

*Odchozí*: odchozí zpráva ze služby signalizace Azure.

*Šířka pásma*: Celková velikost všech zpráv v 1 sekundách.

*Výchozí režim*: výchozí pracovní režim po vytvoření instance služby Azure signaler. Služba signalizace Azure očekává, že aplikační server naváže spojení s ním, než přijme všechna připojení klientů.

*Režim bez serveru*: režim, ve kterém služba přijímače Azure přijímá jenom připojení klientů. Není povoleno žádné připojení k serveru.

## <a name="overview"></a>Přehled

Služba signalizace Azure definuje sedm standardních úrovní pro různé kapacity výkonu. Tato příručka obsahuje odpovědi na následující otázky:

-   Jaký je typický výkon služby signalizace Azure pro každou úroveň?

-   Splňuje služba Azure Signal Service požadavky na propustnost zpráv (například odesílání 100 000 zpráv za sekundu)?

-   Pro můj konkrétní scénář, která úroveň je vhodná pro mě? Nebo jak můžu vybrat správnou úroveň?

-   Jaký druh aplikačního serveru (velikost virtuálního počítače) je vhodný pro mě? Kolik z nich mám nasadit?

Pro zodpovězení těchto otázek obsahuje tato příručka nejdůležitější vysvětlení faktorů, které mají vliv na výkon. Pak ukazuje maximální příchozí a odchozí zprávy pro každou vrstvu pro typické případy použití: **echo**, **všesměrové**, **odesílací ke skupině**a **odeslání do připojení** (konverzace peer-to-peer).

Tato příručka se nezabývá všemi scénáři (a různými případy použití, velikostmi zpráv, schématy odesílání zpráv atd.). Nabízí vám ale několik metod, které vám pomůžou:

- Vyhodnoťte přibližný požadavek pro příchozí nebo odchozí zprávy.
- Správné úrovně najdete v tabulce výkonu.

## <a name="performance-insight"></a>Přehled výkonu

Tato část popisuje metodologie hodnocení výkonu a pak uvádí všechny faktory, které mají vliv na výkon. Na konci poskytuje metody, které vám pomůžou vyhodnotit požadavky na výkon.

### <a name="methodology"></a>Metodologie

*Propustnost* a *latence* jsou dva typické aspekty kontroly výkonu. U služby signalizace Azure má každá úroveň SKU vlastní zásady omezování propustnosti. Tato zásada definuje *maximální povolenou propustnost (příchozí a odchozí šířka pásma)* jako maximální dosaženou propustnosti, když 99 procento zpráv má latenci menší než 1 sekunda.

Latence je časové období z připojení, které odesílá zprávu pro příjem zprávy s odpovědí ze služby Azure Signal. Pojďme si jako příklad vykonat **ozvěnu** . Každé připojení klienta přidá do zprávy časové razítko. Centrum aplikačního serveru pošle původní zprávu zpátky do klienta. Proto je zpoždění šíření snadno vypočítáno při každém připojení klienta. Časové razítko je připojeno ke každé zprávě ve **vysílání**, **Odeslat do skupiny**a **poslat připojení**.

K simulaci tisíců souběžných připojení klientů se ve virtuální privátní síti v Azure vytvoří víc virtuálních počítačů. Všechny tyto virtuální počítače se připojují ke stejné instanci služby signalizace Azure.

Ve výchozím režimu služby signalizace Azure jsou virtuální počítače aplikačního serveru nasazené ve stejné virtuální privátní síti jako klientské virtuální počítače. Všechny virtuální počítače klienta a virtuální počítače aplikačního serveru jsou nasazené ve stejné síti, aby nedocházelo ke latenci mezi oblastmi.

### <a name="performance-factors"></a>Faktory výkonu

Teoretická kapacita služby signálů Azure je omezená výpočetními prostředky: procesor, paměť a síť. Například další připojení ke službě Azure Signal Service způsobí, že služba bude používat více paměti. U větších přenosů zpráv (například každá zpráva je větší než 2 048 bajtů) potřebuje služba Azure Signal Service více cyklů procesoru pro zpracování provozu. Mezitím šířka pásma Azure taky omezuje maximální provoz.

Typ přenosu má jiný faktor, který má vliv na výkon. Tři typy jsou [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [Server-Event-Event](https://en.wikipedia.org/wiki/Server-sent_events)a [Long-cyklické dotazování](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket je obousměrný a plně duplexní komunikační protokol přes jedno připojení TCP. Událost odeslaná serverem je jednosměrný protokol pro zasílání zpráv ze serveru klientovi. Long-Polling vyžaduje, aby klienti pravidelně procházeli informace ze serveru prostřednictvím požadavku HTTP. Pro stejné rozhraní API za stejných podmínek má WebSocket nejlepší výkon a událost odeslanou serverem a je pomalejší, Long-Polling je nejpomalejší. Služba signalizace Azure doporučuje ve výchozím nastavení WebSocket.

Náklady na směrování zpráv také omezují výkon. Služba signalizace Azure hraje roli jako směrovač zpráv, který směruje zprávu ze sady klientů nebo serverů na jiné klienty nebo servery. Jiný scénář nebo rozhraní API vyžaduje jinou zásadu směrování. 

Pro **echo**klient pošle zprávu sám sobě a cíl směrování také sám o sobě. Tento model má nejnižší náklady na směrování. Pro **vysílání**, **odesílání do skupin**a **odesílání připojení**ale služba signálu Azure potřebuje vyhledat cílová připojení prostřednictvím interní distribuované struktury dat. Toto dodatečné zpracování využívá větší kapacitu procesoru, paměti a šířky pásma sítě. Výsledkem je, že výkon je pomalejší.

Ve výchozím režimu se může v některých scénářích taky stát, že se aplikační server stane kritickým bodem. Sada SDK pro službu Azure Signal musí vyvolat centrum, zatímco udržuje živé připojení ke každému klientovi prostřednictvím signálů prezenčního signálu.

V režimu bez serveru pošle klient zprávu prostřednictvím HTTP POST, což není tak efektivní jako WebSocket.

Dalším faktorem je protokol: JSON a [MessagePack](https://msgpack.org/index.html). MessagePack má menší velikost a doručí rychleji než JSON. MessagePack nemusí zvýšit výkon, ale. Výkon služby Azure Signaler není citlivý na protokoly, protože nekóduje datovou část zprávy během předávání zpráv od klientů po servery nebo naopak.

V souhrnu mají tyto faktory vliv na příchozí a odchozí kapacitu:

-   Úroveň SKU (CPU/paměť)

-   Počet připojení

-   Velikost zpráv

-   Rychlost odeslání zprávy

-   Typ přenosu (WebSocket, server – událost-události nebo dlouhé cyklické dotazování)

-   Scénář případu použití (náklady na směrování)

-   Aplikační server a připojení služby (v režimu serveru)


### <a name="finding-a-proper-sku"></a>Hledání správné SKU

Jak můžete vyhodnotit příchozí/odchozí kapacitu nebo najít, která úroveň je vhodná pro konkrétní případ použití?

Předpokládejme, že je server aplikace dostatečně výkonný a nejedná se o problém s výkonem. Pak zkontrolujte maximální příchozí a odchozí šířku pásma pro každou vrstvu.

#### <a name="quick-evaluation"></a>Rychlé hodnocení

Pojďme nejprve zjednodušit vyhodnocení za předpokladu, že jsou k diskaždé výchozí nastavení: 

- Typ přenosu je WebSocket.
- Velikost zprávy je 2 048 bajtů.
- Zpráva se pošle každou 1 sekundu.
- Služba signalizace Azure je ve výchozím režimu.

Každá úroveň má svou vlastní maximální příchozí a odchozí šířku pásma. Možnost hladkého uživatelského prostředí není zaručena, pokud příchozí nebo odchozí připojení překročí limit.

**Echo** poskytuje maximální příchozí šířku pásma, protože má nejnižší náklady na směrování. **Všesměrové vysílání** definuje maximální šířku pásma odchozích zpráv.

Nepřekračuje zvýrazněné hodnoty v následujících dvou tabulkách. *not*

|       Zvuk                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení                       | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50,000 | 100 000 |
| **Příchozí šířka pásma** | **2 Mb/s**    | **4 Mb/s**    | **10 Mb/s**   | **20 MB/s**    | **40 MB/s**    | **100 MB/s**   | **200 MB/s**    |
| Odchozí šířka pásma | 2 Mb/s   | 4 Mb/s   | 10 Mb/s  | 20 MB/s   | 40 MB/s   | 100 MB/s  | 200 MB/s   |


|     To             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50,000  | 100 000 |
| Příchozí šířka pásma  | 4 KB/s   | 4 KB/s   | 4 KB/s    | 4 KB/s    | 4 KB/s    | 4 KB/s     | 4 KB/s    |
| **Odchozí šířka pásma** | **4 Mb/s**    | **8 Mb/s**    | **20 MB/s**    | **40 MB/s**    | **80 MB/s**    | **200 MB/s**    | **400 MB/s**   |

*Příchozí šířka pásma* a *odchozí šířka pásma* jsou celková velikost zpráv za sekundu.  Tady jsou vzorce:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: počet připojení, která odesílají zprávu.

- *outboundConnections*: počet připojení, která přijímají zprávu.

- *messageSize*: velikost jedné zprávy (průměrná hodnota). Malá zpráva, která je menší než 1 024 bajtů, má dopad na výkon, který je podobný zprávě 1 024-Byte.

- *sendInterval*: čas odeslání jedné zprávy. Obvykle je 1 sekunda na zprávu, což znamená odeslání jedné zprávy každou sekundu. Menší interval znamená odeslání více zpráv za časové období. Například 0,5 sekund na zprávu znamená, že každá sekunda posílá dvě zprávy.

- *Připojení*: potvrzená maximální prahová hodnota pro službu Azure Signal Service pro každou úroveň. Pokud je číslo připojení vyšší, ztratí se omezení připojení.

#### <a name="evaluation-for-complex-use-cases"></a>Vyhodnocování pro složité případy použití

##### <a name="bigger-message-size-or-different-sending-rate"></a>Větší velikost zprávy nebo jiná rychlost odesílání

Skutečný případ použití je složitější. Může poslat zprávu větší než 2 048 bajtů, nebo počet zpráv o odeslání není jedna zpráva za sekundu. Pojďme Unit100's vysílání jako příklad, abychom zjistili, jak vyhodnotit jeho výkon.

V následující tabulce jsou uvedeny reálné případy použití **všesměrového vysílání**. Velikost zprávy, počet připojení a rychlost odesílání zpráv se ale liší od toho, co jsme předpokládali v předchozí části. Otázka je způsob, jak můžeme odvodit libovolnou z těchto položek (velikost zprávy, počet připojení nebo rychlost odeslání zprávy), pokud ví jenom dva z nich.

| To  | Velikost zpráv | Příchozí zprávy za sekundu | Připojení | Intervaly odesílání |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100 000     | 5 sekund                      |
| 2 | 256 kB               | 1                        | 8 000       | 5 sekund                      |

Následující vzorec se dá snadno odvodit v závislosti na předchozím vzorci:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

V případě Unit100 je maximální odchozí šířka pásma 400 MB z předchozí tabulky. Pro velikost zprávy o velikosti 20 KB by měla být maximální počet odchozích připojení 400 MB \* 5/20 KB = 100 000, což odpovídá skutečné hodnotě.

##### <a name="mixed-use-cases"></a>Smíšené případy použití

Skutečný případ použití typicky kombinuje čtyři základní případy použití dohromady: **echo**, **Broadcast**, **Send to Group**a **Send to Connection**. Metodologie, kterou použijete k vyhodnocení kapacity, je:

1. Jednotlivé kombinované případy použití můžete rozdělit do čtyř základních případů použití.
1. Vypočítá maximální šířku pásma příchozích a odchozích zpráv pomocí předchozích vzorců samostatně.
1. Součet výpočtů šířky pásma pro získání celkové maximální příchozí nebo odchozí šířky pásma. 

Pak z maximálního počtu příchozích a odchozích tabulek šířky pásma vyberte správnou vrstvu.

> [!NOTE]
> Pro odeslání zprávy na stovky nebo tisíce malých skupin nebo pro tisíce klientů, kteří odesílají zprávy navzájem, se náklady na směrování stanou dominantní. Vzít tento dopad na účet.

Pro případ použití odeslání zprávy *klientům se ujistěte* , že aplikační server není kritickým bodem. Následující část "Případová studie" obsahuje pokyny k tomu, kolik aplikačních serverů potřebujete a kolik připojení k serveru byste měli nakonfigurovat.

## <a name="case-study"></a>Případová studie

V následujících částech najdete čtyři typické případy použití pro přenos protokolu WebSocket: **echo**, **všesměrové**, **odesílací ke skupině**a **odesílání na připojení**. V jednotlivých scénářích obsahuje oddíl aktuální příchozí a odchozí kapacitu pro službu Azure Signal Service. Vysvětluje také hlavní faktory ovlivňující výkon.

Ve výchozím režimu vytvoří aplikační server pro službu Azure Signal Service pět připojení k serveru. Aplikační server používá službu Azure Signal Service SDK ve výchozím nastavení. V následujících výsledcích testu výkonu se připojení k serveru zvyšují na 15 (nebo více pro vysílání a odesílání zprávy do velké skupiny).

Různé případy použití mají různé požadavky na aplikační servery. **Všesměrové vysílání** potřebuje malý počet aplikačních serverů. **Ozvěna** nebo **odesílání k připojení** vyžaduje mnoho aplikačních serverů.

Ve všech případech použití je výchozí velikost zprávy 2 048 bajtů a interval odesílání zprávy je 1 sekunda.

### <a name="default-mode"></a>Výchozí režim

Do výchozího režimu patří klienti, servery webových aplikací a služba signalizace Azure. Každý klient představuje jedno připojení.

#### <a name="echo"></a>Zvuk

Nejdřív se webová aplikace připojí ke službě Azure Signal. Za druhé se mnoho klientů připojuje k webové aplikaci, která přesměruje klienty na službu Azure Signal Service pomocí přístupového tokenu a koncového bodu. Pak klienti navázali připojení pomocí protokolu WebSocket ke službě Azure Signal.

Po navázání připojení všemi klienty začne odeslání zprávy, která obsahuje časové razítko konkrétního centra, každou sekundu. Centrum vrátí zprávu zpátky původnímu klientovi. Každý klient vypočítá latenci při přijetí zprávy echo zpět.

V následujícím diagramu jsou ve smyčce smyčka 5 až 8 (červený zvýrazněný provoz). Smyčka se spustí pro výchozí dobu trvání (5 minut) a získá statistiku pro veškerou latenci zprávy.

![Přenos pro případ použití ozvěny](./media/signalr-concept-performance/echo.png)

Chování funkce **echo** určuje, zda je maximální příchozí šířka pásma rovna maximální odchozí šířce pásma. Podrobnosti najdete v následující tabulce.

|       Zvuk                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení                       | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50,000 | 100 000 |
| Příchozí/odchozí zprávy za sekundu | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50,000 | 100 000 |
| Příchozí/odchozí šířka pásma | 2 Mb/s   | 4 Mb/s   | 10 Mb/s  | 20 MB/s   | 40 MB/s   | 100 MB/s  | 200 MB/s   |

V tomto případu použití každý klient vyvolá rozbočovač definovaný na aplikačním serveru. Centrum pouze volá metodu definovanou v původní straně klienta. Toto centrum je nejjednodušším centrem pro **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

I u tohoto jednoduchého centra je přenos dat na aplikačním serveru výrazný, jako se zvyšuje zatížení příchozí zprávy **echo** . Tento přenosový tlak vyžaduje mnoho aplikačních serverů pro velké úrovně SKU. Následující tabulka uvádí počet aplikačních serverů pro každou úroveň.


|    Zvuk          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50,000 | 100 000 |
| Počet aplikačních serverů | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Celkový výkon **ozvěny**má vliv na číslo klientského připojení, velikost zprávy, rychlost odesílání zpráv, úroveň SKU a procesor/paměť aplikačního serveru.

#### <a name="broadcast"></a>To

V případě **všesměrového vysílání**, když webová aplikace obdrží zprávu, vysílá všem klientům. Víc klientů, které se mají vysílat, je další přenos zpráv, ke kterým mají všichni klienti. Podívejte se na následující diagram.

![Provoz pro případ použití všesměrového vysílání](./media/signalr-concept-performance/broadcast.png)

Vysílá se malý počet klientů. Šířka pásma příchozí zprávy je malá, ale odchozí šířka pásma je velmi velká. Šířka pásma odchozí zprávy se zvyšuje při zvýšení připojení klienta nebo rychlosti vysílání.

Následující tabulka shrnuje maximální počet připojení klientů, počet příchozích a odchozích zpráv a šířku pásma.

|     To             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50,000  | 100 000 |
| Příchozí zprávy za sekundu  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Odchozí zprávy za sekundu | 2 000 | 4 000 | 10 000 | 20 000 | 40,000 | 100 000 | 200 000 |
| Příchozí šířka pásma  | 4 KB/s   | 4 KB/s   | 4 KB/s    | 4 KB/s    | 4 KB/s    | 4 KB/s     | 4 KB/s     |
| Odchozí šířka pásma | 4 Mb/s   | 8 Mb/s   | 20 MB/s   | 40 MB/s   | 80 MB/s   | 200 MB/s   | 400 MB/s   |

Vysílající klienti, kteří odesílají zprávy, nejsou delší než čtyři. Budou potřebovat méně aplikačních serverů v porovnání s **odezvou** , protože velikost příchozí zprávy je malá. Dva aplikační servery jsou dostatečné pro požadavky smlouvy SLA i na výkon. Měli byste ale zvýšit výchozí připojení k serveru, aby nedocházelo k nerovnováze, zejména pro Unit50 a Unit100.

|   To      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50,000 | 100 000 |
| Počet aplikačních serverů | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Zvyšte výchozí připojení serveru z 5 na 40 na každém serveru aplikace, abyste zabránili možným nevyváženým připojením k serveru ke službě Azure Signal.
>
> Celkový výkon **všesměrového vysílání**má vliv na číslo klientského připojení, velikost zprávy, rychlost odeslání zprávy a úroveň SKU.

#### <a name="send-to-group"></a>Odeslat do skupiny

Případ použití **skupiny Odeslat do skupiny** má podobný vzor provozu, který se má **vysílat**. Rozdílem je to, že jakmile klienti navážou připojení pomocí protokolu WebSocket ke službě Azure Signal, musí se před odesláním zprávy do určité skupiny spojit se skupinami. Tok přenosů znázorňuje následující diagram.

![Provoz pro případ použití odeslání na skupinu](./media/signalr-concept-performance/sendtogroup.png)

Počet členů skupiny a skupin je dva faktory, které mají vliv na výkon. Pro zjednodušení analýzy definujeme dva druhy skupin:

- **Malá skupina**: každá skupina má 10 připojení. Číslo skupiny je rovno (maximální počet připojení)/10. Například pro Unit1, pokud existuje 1 000 počtu připojení, pak máme 1000/10 = 100 skupin.

- **Velká skupina**: číslo skupiny je vždycky 10. Počet členů skupiny se rovná (maximální počet připojení)/10. Například pro Unit1, pokud existuje 1 000 počtu připojení, pak každá skupina obsahuje 1000/10 = 100 členů.

**Odeslání do skupiny** přináší náklady na směrování do služby Azure Signal, protože musí najít cílová připojení prostřednictvím distribuované struktury dat. Při zvýšení počtu odesílání se náklady zvyšují.

##### <a name="small-group"></a>Malá skupina

Náklady na směrování jsou významné pro posílání zpráv do mnoha malých skupin. V současné době implementace služby signalizace Azure narazí na limit nákladů na směrování na adrese Unit50. Přidání dalších PROCESORů a paměti nepomáhá, takže Unit100 nebude moci lépe zlepšit návrh. Pokud potřebujete větší příchozí šířku pásma, obraťte se na zákaznickou podporu.

|   Odeslat do malé skupiny     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50,000 | 100 000
| Počet členů skupiny        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Počet skupin               | 100   | 200   | 500    | 1 000  | 2 000  | 5 000  | 10 000 
| Příchozí zprávy za sekundu  | 200   | 400   | 1 000  | 2,500  | 4 000  | 7 000  | 7 000   |
| Příchozí šířka pásma  | 400 KB/s  | 800 KB/s  | 2 Mb/s     | 5 Mb/s     | 8 Mb/s     | 14 MB/s    | 14 MB/s     |
| Odchozí zprávy za sekundu | 2 000 | 4 000 | 10 000 | 25 000 | 40,000 | 70 000 | 70 000  |
| Odchozí šířka pásma | 4 Mb/s    | 8 Mb/s    | 20 MB/s    | 50 MB/s     | 80 MB/s    | 140 MB/s   | 140 MB/s    |

Mnoho připojení klientů volá centrum, takže je pro výkon také důležité toto číslo aplikačního serveru. V následující tabulce jsou uvedené doporučené počty aplikačních serverů.

|  Odeslat do malé skupiny   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50,000 | 100 000 |
| Počet aplikačních serverů | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Celkový výkon **pro odesílání do malých skupin**má vliv na číslo klientského připojení, velikost zprávy, rychlost odesílání zpráv, náklady na směrování, úroveň SKU a procesor/paměť aplikačního serveru.

##### <a name="big-group"></a>Velká skupina

U pole **Odeslat do velké skupiny**se odchozí šířka pásma bude kritickým bodem, než se zasáhne limit nákladů na směrování. Následující tabulka uvádí maximální šířku pásma, která je téměř stejná jako u **všesměrového vysílání**.

|    Odeslat do velké skupiny      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50,000  | 100 000
| Počet členů skupiny        | 100   | 200   | 500    | 1 000  | 2 000  | 5 000   | 10 000 
| Počet skupin               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Příchozí zprávy za sekundu  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Příchozí šířka pásma  | 80 kb/s   | 40 kB/s   | 40 kB/s    | 20 kB/s    | 40 kB/s    | 40 kB/s     | 40 kB/s     |
| Odchozí zprávy za sekundu | 2 000 | 4 000 | 10 000 | 20 000 | 40,000 | 100 000 | 200 000 |
| Odchozí šířka pásma | 8 Mb/s    | 8 Mb/s    | 20 MB/s    | 40 MB/s    | 80 MB/s    | 200 MB/s    | 400 MB/s    |

Počet odesílajících připojení není vyšší než 40. Zatížení aplikačního serveru je malé, takže navrhovaný počet webových aplikací je malý.

|  Odeslat do velké skupiny  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50,000 | 100 000 |
| Počet aplikačních serverů | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Zvyšte výchozí připojení serveru z 5 na 40 na každém serveru aplikace, abyste zabránili možným nevyváženým připojením k serveru ke službě Azure Signal.
> 
> Číslo klientského připojení, velikost zprávy, rychlost odesílání zpráv, náklady na směrování a úroveň SKU ovlivňují celkový výkon **pro odesílání do velké skupiny**.

#### <a name="send-to-connection"></a>Odeslat do připojení

Když klienti navázali připojení ke službě Azure Signal, v případě použití **připojení poslat do připojení** , každý klient zavolá speciální rozbočovač, aby získal své vlastní ID připojení. Výkonnostní test shromáždí všechna ID připojení, rozdělí je a znovu přiřadí všem klientům jako cíl odeslání. Klienti budou posílat zprávy do cílového připojení, dokud se nedokončí test výkonnosti.

![Přenos pro případ použití odeslání na klienta](./media/signalr-concept-performance/sendtoclient.png)

Náklady na směrování pro **připojení pro odesílání na** jsou podobné nákladům na **odesílání do malých skupin**.

Po zvýšení počtu připojení náklady na směrování omezí celkový výkon. Unit50 dosáhl limitu. V důsledku toho Unit100 nemůže lépe zlepšit.

Následující tabulka představuje statistické Shrnutí po mnoha směrech spuštění srovnávacího testu **pro odeslání do připojení** .

|   Odeslat do připojení   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Připojení                        | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50,000          | 100 000         |
| Příchozí/odchozí zprávy za sekundu | 1 000 | 2 000 | 5 000 | 8 000  | 9 000  | 20 000 | 20 000 |
| Příchozí/odchozí šířka pásma | 2 Mb/s    | 4 Mb/s    | 10 Mb/s   | 16 MB/s    | 18 MB/s    | 40 MB/s       | 40 MB/s       |

Tento případ použití vyžaduje vysoké zatížení na straně aplikačního serveru. Podívejte se na téma navrhovaný počet aplikačních serverů v následující tabulce.

|  Odeslat do připojení  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50,000 | 100 000 |
| Počet aplikačních serverů | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Počet klientů, velikost zprávy, rychlost odesílání zpráv, náklady na směrování, úroveň SKU a procesor/paměť pro aplikační server ovlivňují celkový výkon **odesílání do připojení**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET signalizace, vysílání a odeslání do malé skupiny

Služba signalizace Azure poskytuje stejnou kapacitu výkonu pro ASP.NET signál. 

Test výkonu používá Azure Web Apps ze [služby Standard Service Plan S3](https://azure.microsoft.com/pricing/details/app-service/windows/) pro nástroj ASP.NET Signal.

Následující tabulka obsahuje navrhovaný počet webových aplikací pro ASP.NET signalizace **.**

|   Zvuk           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50,000 | 100 000 |
| Počet aplikačních serverů | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

Následující tabulka poskytuje navrhovaný počet webových aplikací pro **vysílání**signálem ASP.NET.

|  To       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50,000 | 100 000 |
| Počet aplikačních serverů | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

V následující tabulce je uveden navrhovaný počet navrhovaných webových aplikací pro ASP.NET signalizace **odeslání do malé skupiny**.

|  Odeslat do malé skupiny     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50,000 | 100 000 |
| Počet aplikačních serverů | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Režim bez serveru

Klienti a služba Azure Signal Service jsou součástí režimu bez serveru. Každý klient představuje jedno připojení. Klient odesílá zprávy prostřednictvím REST API do jiného klienta nebo zprávy všesměrového vysílání do všech.

Posílání zpráv s vysokou hustotou prostřednictvím REST API není tak efektivní jako použití protokolu WebSocket. Vyžaduje vám, abyste si pokaždé vytvořili nové připojení HTTP a v režimu bez serveru se za další cenu.

#### <a name="broadcast-through-rest-api"></a>Všesměrové vysílání prostřednictvím REST API
Všichni klienti navážou připojení pomocí protokolu WebSocket ke službě Azure Signal. Pak někteří klienti začnou vysílat přes REST API. Odesílá se (příchozí) zpráva prostřednictvím HTTP POST, což není v porovnání s WebSocket efektivní.

|   Všesměrové vysílání prostřednictvím REST API     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50,000  | 100 000 |
| Příchozí zprávy za sekundu  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Odchozí zprávy za sekundu | 2 000 | 4 000 | 10 000 | 20 000 | 40,000 | 100 000 | 200 000 |
| Příchozí šířka pásma  | 4 KB/s    | 4 KB/s    | 4 KB/s     | 4 KB/s     | 4 KB/s     | 4 KB/s      | 4 KB/s      |
| Odchozí šířka pásma | 4 Mb/s    | 8 Mb/s    | 20 MB/s    | 40 MB/s    | 80 MB/s    | 200 MB/s    | 400 MB/s    |

#### <a name="send-to-user-through-rest-api"></a>Odeslat uživateli prostřednictvím REST API
Srovnávací test přiřazuje uživatelská jména všem klientům předtím, než se začne připojovat ke službě Azure Signal. Poté, co klienti navážou připojení pomocí protokolu WebSocket ke službě Azure Signal, začnou posílat zprávy ostatním prostřednictvím HTTP POST.

|   Odeslat uživateli prostřednictvím REST API | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50,000  | 100 000 |
| Příchozí zprávy za sekundu  | 300   | 600   | 900    | 1 300  | 2 000  | 10 000  | 18 000  |
| Odchozí zprávy za sekundu | 300   | 600   | 900    | 1 300  | 2 000  | 10 000  | 18 000 |
| Příchozí šířka pásma  | 600 KB/s  | 1,2 MB/s  | 1,8 MB/s   | 2,6 MB/s   | 4 Mb/s     | 10 Mb/s     | 36 MB/s    |
| Odchozí šířka pásma | 600 KB/s  | 1,2 MB/s  | 1,8 MB/s   | 2,6 MB/s   | 4 Mb/s     | 10 Mb/s     | 36 MB/s    |

## <a name="performance-test-environments"></a>Prostředí testování výkonu

Pro všechny případy použití uvedené výše jsme provedli testy výkonu v prostředí Azure. Ve většině případů jsme použili 50 klientských virtuálních počítačů a 20 virtuálních počítačů aplikačních serverů. Tady jsou některé podrobnosti:

- Velikost virtuálního počítače klienta: StandardDS2V2 (2 vCPU, 7G paměť)

- Velikost virtuálního počítače aplikačního serveru: StandardF4sV2 (4 vCPU, 8G paměť)

- Připojení k serveru sady SDK pro službu Azure Signal: 15

## <a name="performance-tools"></a>Nástroje pro měření výkonu

Nástroje pro sledování výkonu pro službu Azure Signal Service najdete na [GitHubu](https://github.com/Azure/azure-signalr-bench/).

## <a name="next-steps"></a>Další kroky

V tomto článku získáte přehled výkonu služby signalizace Azure v typických scénářích použití.

Pokud chcete získat podrobné informace o vnitřních verzích služby a jejímu škálování, přečtěte si následující příručky:

* [Interní informace služby Azure SignalR Service](signalr-concept-internals.md)
* [Škálování služby signálu Azure](signalr-howto-scale-multi-instances.md)

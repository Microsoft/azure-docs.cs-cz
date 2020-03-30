---
title: Průvodce výkonem pro službu Azure SignalR Service
description: Přehled výkonu a srovnávacího testu služby Azure SignalR. Klíčové metriky, které je třeba zvážit při plánování kapacity.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74157666"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Průvodce výkonem pro službu Azure SignalR Service

Jednou z klíčových výhod používání služby Azure SignalR je snadné škálování aplikací SignalR. Ve velkém měřítku scénáře výkon je důležitým faktorem. 

V této příručce představíme faktory, které ovlivňují výkon aplikace SignalR. Typický výkon popíšeme v různých scénářích použití. Nakonec představíme prostředí a nástroje, které můžete použít ke generování sestavy výkonu.

## <a name="term-definitions"></a>Definice termínů

*Příchozí*: Příchozí zpráva do služby Azure SignalR.

*Odchozí*: Odchozí zpráva ze služby Azure SignalR.

*Šířka pásma*: Celková velikost všech zpráv za 1 sekundu.

*Výchozí režim*: Výchozí pracovní režim při vytvoření instance služby Azure SignalR. Služba Azure SignalR očekává, že aplikační server s ním naváže připojení, než přijme všechna připojení klientů.

*Režim bez serveru*: Režim, ve kterém služba Azure SignalR přijímá pouze připojení klientů. Není povoleno žádné připojení k serveru.

## <a name="overview"></a>Přehled

Služba Azure SignalR definuje sedm úrovní Standard pro různé kapacity výkonu. Tato příručka odpovídá na následující otázky:

-   Jaký je typický výkon služby Azure SignalR pro každou úroveň?

-   Splňuje služba Azure SignalR moje požadavky na propustnost zpráv (například odesílání 100 000 zpráv za sekundu)?

-   Pro můj konkrétní scénář, která úroveň je pro mě vhodná? Nebo jak si mohu vybrat správnou úroveň?

-   Jaký typ aplikačního serveru (velikost virtuálního počítače) je pro mě vhodný? Kolik z nich bych měl nasadit?

Chcete-li odpovědět na tyto otázky, tato příručka nejprve poskytuje vysoké úrovni vysvětlení faktorů, které ovlivňují výkon. Potom ilustruje maximální příchozí a odchozí zprávy pro každou úroveň pro typické případy použití: **echo**, **broadcast**, **send to group**a send to **connection** (peer-to-peer chatování).

Tato příručka nemůže pokrýt všechny scénáře (a různé případy použití, velikosti zpráv, vzorky odesílání zpráv a tak dále). Ale poskytuje některé metody, které vám pomohou:

- Vyhodnoťte přibližný požadavek na příchozí nebo odchozí zprávy.
- Najděte správné úrovně kontrolou tabulky výkonu.

## <a name="performance-insight"></a>Přehled výkonu

Tato část popisuje metodiky hodnocení výkonu a potom uvádí všechny faktory, které ovlivňují výkon. Nakonec poskytuje metody, které vám pomohou vyhodnotit požadavky na výkon.

### <a name="methodology"></a>Metodologie

*Propustnost* a *latence* jsou dva typické aspekty kontroly výkonu. Pro službu Azure SignalR má každá úroveň Skladové položky vlastní zásadu omezení propustností. Zásada definuje *maximální povolenou propustnost (příchozí a odchozí šířku pásma)* jako maximální dosaženou propustnost, pokud 99 procent zpráv má latenci menší než 1 sekundu.

Latence je časové rozpětí od připojení odesílající zprávu k přijetí zprávy odpovědi ze služby Azure SignalR. Vezměme si **echo** jako příklad. Každé připojení klienta přidá časové razítko ve zprávě. Rozbočovač aplikačního serveru odešle původní zprávu zpět klientovi. Takže zpoždění šíření lze snadno vypočítat podle každého připojení klienta. Časové razítko je připojeno ke každé zprávě ve **vysílání**, **odeslat do skupiny**a **odeslat do spojení**.

Chcete-li simulovat tisíce souběžných klientských připojení, více virtuálních počítačů se vytvoří ve virtuální privátní síti v Azure. Všechny tyto virtuální počítače se připojují ke stejné instanci služby Azure SignalR.

Ve výchozím režimu služby Azure SignalR se virtuální počítače aplikačního serveru nasazují ve stejné virtuální privátní síti jako klientské virtuální počítače. Všechny virtuální servery klientů a virtuální servery aplikace se nasazují ve stejné síti stejné oblasti, aby se zabránilo latenci mezi oblastmi.

### <a name="performance-factors"></a>Výkonnostní faktory

Teoreticky je kapacita služby Azure SignalR omezena výpočetními prostředky: procesorem, pamětí a sítí. Například další připojení ke službě Azure SignalR způsobit služby používat více paměti. Pro větší přenos zpráv (například každá zpráva je větší než 2 048 bajtů), Azure SignalR Service musí strávit více cyklů procesoru ke zpracování provozu. Šířka pásma sítě Azure mezitím také ukládá omezení pro maximální provoz.

Typ přenosu je dalším faktorem, který ovlivňuje výkon. Tři typy jsou [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [Server-Sent-Event](https://en.wikipedia.org/wiki/Server-sent_events)a [Long-Polling](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket je obousměrný a plně duplexní komunikační protokol přes jedno připojení TCP. Server-Sent-Event je jednosměrný protokol pro nabízení zpráv ze serveru do klienta. Dlouhé dotazování vyžaduje, aby klienti pravidelně dotazování informací ze serveru prostřednictvím požadavku HTTP. Pro stejné rozhraní API za stejných podmínek WebSocket má nejlepší výkon, Server-Sent-Event je pomalejší a Long-Polling je nejpomalejší. Služba Azure SignalR doporučuje websocket ve výchozím nastavení.

Náklady na směrování zpráv také omezují výkon. Služba Azure SignalR hraje roli jako směrovač zpráv, který směruje zprávu ze sady klientů nebo serverů na jiné klienty nebo servery. Jiný scénář nebo rozhraní API vyžaduje různé zásady směrování. 

Pro **echo**klient odešle zprávu sám sobě a cíl směrování je také sám. Tento vzor má nejnižší náklady na směrování. Ale pro **vysílání**, **odeslat do skupiny**a **odeslat připojení**, Azure SignalR Služba musí vyhledat cílová připojení prostřednictvím vnitřní distribuované datové struktury. Toto další zpracování využívá větší šířku pásma procesoru, paměti a sítě. V důsledku toho je výkon pomalejší.

Ve výchozím režimu se aplikační server může také stát kritickým bodem pro určité scénáře. Sada Azure SignalR SDK má vyvolat rozbočovač, zatímco udržuje živé připojení s každým klientem prostřednictvím signálů prezenčního signálu.

V bezserverovém režimu klient odešle zprávu pomocí příspěvku HTTP, který není tak efektivní jako WebSocket.

Dalším faktorem je protokol: JSON a [MessagePack](https://msgpack.org/index.html). MessagePack je menší velikost a dodává se rychleji než JSON. MessagePack nemusí zlepšit výkon, ačkoli. Výkon služby Azure SignalR není citlivý na protokoly, protože nedekóduje datovou část zprávy během předávání zpráv z klientů na servery nebo naopak.

Stručně řečeno, následující faktory ovlivňují příchozí a odchozí kapacitu:

-   Úroveň SKU (procesor/paměť)

-   Počet připojení

-   Velikost zpráv

-   Rychlost odeslání zprávy

-   Typ přenosu (WebSocket, Server-Sent-Event nebo Long-Polling)

-   Případ použití (náklady na směrování)

-   Připojení aplikačního serveru a služeb (v režimu serveru)


### <a name="finding-a-proper-sku"></a>Nalezení správné skladové položky

Jak můžete vyhodnotit příchozí/odchozí kapacitu nebo zjistit, která úroveň je vhodná pro konkrétní případ použití?

Předpokládejme, že aplikační server je dostatečně výkonný a není problémovým místem výkonu. Potom zkontrolujte maximální příchozí a odchozí šířku pásma pro každou vrstvu.

#### <a name="quick-evaluation"></a>Rychlé vyhodnocení

Nejprve zjednodušme vyhodnocení za předpokladu, že se naněkterých výchozích nastavení: 

- Typ přenosu je WebSocket.
- Velikost zprávy je 2 048 bajtů.
- Zpráva je odeslána každých 1 sekunda.
- Služba Azure SignalR je ve výchozím režimu.

Každá úroveň má svou vlastní maximální příchozí šířku pásma a odchozí šířku pásma. Hladké uživatelské prostředí není zaručeno poté, co příchozí nebo odchozí připojení překročí limit.

**Echo** poskytuje maximální příchozí šířku pásma, protože má nejnižší náklady na směrování. **Vysílání** definuje maximální šířku pásma odchozí zprávy.

Nepřekračujte zvýrazněné hodnoty v následujících dvou tabulkách. *not*

|       Echo                        | Jednotka1 | Jednotka2 | Jednotka5 | Jednotka10 | Jednotka20 | Jednotka50 | Jednotka100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení                       | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| **Příchozí šířka pásma** | **2 Mb/s**    | **4 Mb/s**    | **10 Mb/s**   | **20 mb/s**    | **40 mb/s**    | **100 mb/s**   | **200 mb/s**    |
| Odchozí šířka pásma | 2 Mb/s   | 4 Mb/s   | 10 Mb/s  | 20 mb/s   | 40 mb/s   | 100 mb/s  | 200 mb/s   |


|     Vysílání             | Jednotka1 | Jednotka2 | Jednotka5  | Jednotka10 | Jednotka20 | Jednotka50  | Jednotka100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Příchozí šířka pásma  | 4 kb/s   | 4 kb/s   | 4 kb/s    | 4 kb/s    | 4 kb/s    | 4 kb/s     | 4 kb/s    |
| **Odchozí šířka pásma** | **4 Mb/s**    | **8 Mb/s**    | **20 mb/s**    | **40 mb/s**    | **80 mb/s**    | **200 mb/s**    | **400 mb/s**   |

*Příchozí šířka pásma* a *odchozí šířka pásma* jsou celková velikost zprávy za sekundu.  Zde jsou vzorce pro ně:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: Počet připojení odesílajících zprávu.

- *odchozí připojení*: Počet připojení přijímajících zprávu.

- *messageSize*: Velikost jedné zprávy (průměrná hodnota). Malá zpráva, která je menší než 1 024 bajtů, má vliv na výkon, který je podobný zprávě o 1 024 bajtů.

- *sendInterval*: Čas odeslání jedné zprávy. Obvykle je to 1 sekunda na zprávu, což znamená odeslání jedné zprávy každou sekundu. Menší interval znamená odeslání více zpráv v časovém období. Například 0,5 sekundy na zprávu znamená odesílání dvou zpráv každou sekundu.

- *Připojení*: Potvrzená maximální prahová hodnota pro službu Azure SignalR pro každou úroveň. Pokud je číslo připojení dále zvyšováno, bude trpět omezením připojení.

#### <a name="evaluation-for-complex-use-cases"></a>Vyhodnocení složitých případů použití

##### <a name="bigger-message-size-or-different-sending-rate"></a>Větší velikost zprávy nebo různá rychlost odesílání

Případ skutečného použití je složitější. Může odeslat zprávu větší než 2 048 bajtů nebo rychlost odesílání zpráv není jedna zpráva za sekundu. Vezměme unit100 vysílání jako příklad najít, jak vyhodnotit jeho výkon.

V následující tabulce je uveden případ skutečného použití **vysílání**. Ale velikost zprávy, počet připojení a rychlost odesílání zpráv se liší od toho, co jsme předpokládali v předchozí části. Otázkou je, jak můžeme odvodit některou z těchto položek (velikost zprávy, počet připojení nebo rychlost odesílání zpráv), pokud známe pouze dvě z nich.

| Vysílání  | Velikost zpráv | Příchozí zprávy za sekundu | Připojení | Posílat intervaly |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100 000     | 5 s                      |
| 2 | 256 kB               | 1                        | 8 000       | 5 s                      |

Následující vzorec lze snadno odvodit na základě předchozího vzorce:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Pro jednotku Unit100 je maximální odchozí šířka pásma 400 MB oproti předchozí tabulce. Pro velikost zprávy 20 KB maximální odchozí připojení by měla \* být 400 MB 5 / 20 KB = 100 000, což odpovídá skutečné hodnotě.

##### <a name="mixed-use-cases"></a>Případy smíšeného použití

Případ skutečného použití obvykle kombinuje čtyři případy základního použití: **echo**, **broadcast**, **send to group**a send to **connection**. Metodika, kterou používáte k vyhodnocení kapacity, je:

1. Rozdělte případy smíšeného použití do čtyř základních případů použití.
1. Vypočítejte maximální šířku pásma příchozích a odchozích zpráv pomocí předchozích vzorců samostatně.
1. Součet výpočtů šířky pásma získáte celkovou maximální příchozí/odchozí šířku pásma. 

Pak vyzvednout správnou úroveň z tabulky maximální příchozí nebo odchozí šířky pásma.

> [!NOTE]
> Pro odeslání zprávy stovkám nebo tisícům malých skupin nebo pro tisíce klientů, kteří si navzájem posílají zprávu, se náklady na směrování stanou dominantními. Vezměte tento dopad v úvahu.

V případě použití odeslání zprávy klientům, ujistěte se, že aplikační server *není* kritickým bodem. V následující části "Případová studie" pokyny o tom, kolik serverů aplikací, které potřebujete a kolik připojení k serveru byste měli nakonfigurovat.

## <a name="case-study"></a>Případová studie

Následující části procházejí čtyřmi typickými případy použití přenosu WebSocket: **echo**, **broadcast**, **send to group**a send to **connection**. Pro každý scénář v části uvádí aktuální příchozí a odchozí kapacitu pro službu Azure SignalR. Vysvětluje také hlavní faktory, které ovlivňují výkon.

Ve výchozím režimu app server vytvoří pět připojení serveru se službou Azure SignalR. Aplikační server používá ve výchozím nastavení sdk služby Azure SignalR. V následujících výsledcích testu výkonu se připojení k serveru zvýší na 15 (nebo více pro vysílání a odeslání zprávy velké skupině).

Různé případy použití mají různé požadavky na aplikační servery. **Vysílání** vyžaduje malý počet serverů aplikací. **Echo** nebo **odeslat do připojení** potřebuje mnoho serverů aplikací.

Ve všech případech použití je výchozí velikost zprávy 2 048 bajtů a interval odeslání zprávy je 1 sekunda.

### <a name="default-mode"></a>Výchozí režim

Klienti, servery webových aplikací a služba Azure SignalR jsou součástí výchozího režimu. Každý klient znamená jediné připojení.

#### <a name="echo"></a>Echo

Nejprve se webová aplikace připojí ke službě Azure SignalR. Za druhé, mnoho klientů připojit k webové aplikaci, která přesměruje klienty na službu Azure SignalR s přístupový token a koncový bod. Potom klienti navázat websocket připojení se službou Azure SignalR.

Poté, co všichni klienti navázat připojení, začnou odesílat zprávu, která obsahuje časové razítko do konkrétního rozbočovače každou sekundu. Rozbočovač odráží zprávu zpět do původního klienta. Každý klient vypočítá latenci, když obdrží zprávu echo zpět.

V následujícím diagramu jsou 5 až 8 (červený zvýrazněný provoz) ve smyčce. Smyčka běží po výchozí dobu trvání (5 minut) a získá statistiku všech latence zpráv.

![Provoz pro případ použití ozvěny](./media/signalr-concept-performance/echo.png)

Chování **echo** určuje, že maximální příchozí šířka pásma se rovná maximální odchozí šířku pásma. Podrobnosti najdete v následující tabulce.

|       Echo                        | Jednotka1 | Jednotka2 | Jednotka5 | Jednotka10 | Jednotka20 | Jednotka50 | Jednotka100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení                       | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Příchozí a odchozí zprávy za sekundu | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Šířka pásma příchozích a odchozích | 2 Mb/s   | 4 Mb/s   | 10 Mb/s  | 20 mb/s   | 40 mb/s   | 100 mb/s  | 200 mb/s   |

V tomto případě použití každý klient vyvolá rozbočovač definovaný na aplikačním serveru. Centrum pouze volá metodu definovanou na původní straně klienta. Tento rozbočovač je nejlehčí rozbočovač pro **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

I pro tento jednoduchý rozbočovač je dopravní tlak na aplikačním serveru prominentní, protože se zvyšuje zatížení příchozízprávy **echo.** Tento dopravní tlak vyžaduje mnoho serverů aplikací pro velké úrovně Skladové položky. V následující tabulce je uveden počet aplikačních serverů pro každou vrstvu.


|    Echo          | Jednotka1 | Jednotka2 | Jednotka5 | Jednotka10 | Jednotka20 | Jednotka50 | Jednotka100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Číslo připojení klienta, velikost zprávy, rychlost odesílání zpráv, vrstva sku a procesor/paměť aplikačního serveru ovlivňují celkový výkon **echo**.

#### <a name="broadcast"></a>Vysílání

Pro **vysílání**, když webová aplikace obdrží zprávu, vysílá všem klientům. Čím více klientů je k vysílání, tím větší je přenos zpráv pro všechny klienty. Podívejte se na následující diagram.

![Provoz pro případ použití vysílání](./media/signalr-concept-performance/broadcast.png)

Vysílá malý počet klientů. Šířka pásma příchozí zprávy je malá, ale odchozí šířka pásma je obrovská. Šířka pásma odchozí zprávy se zvyšuje s tím, jak se zvyšuje rychlost připojení klienta nebo vysílání.

Následující tabulka shrnuje maximální počet klientských připojení, počet příchozích a odchozích zpráv a šířku pásma.

|     Vysílání             | Jednotka1 | Jednotka2 | Jednotka5  | Jednotka10 | Jednotka20 | Jednotka50  | Jednotka100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Příchozí zprávy za sekundu  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Odchozí zprávy za sekundu | 2 000 | 4 000 | 10 000 | 20 000 | 40,000 | 100 000 | 200 000 |
| Příchozí šířka pásma  | 4 kb/s   | 4 kb/s   | 4 kb/s    | 4 kb/s    | 4 kb/s    | 4 kb/s     | 4 kb/s     |
| Odchozí šířka pásma | 4 Mb/s   | 8 Mb/s   | 20 mb/s   | 40 mb/s   | 80 mb/s   | 200 mb/s   | 400 mb/s   |

Klienti vysílání, kteří zveřejňují zprávy, nejsou větší než čtyři. Potřebují méně serverů aplikací ve srovnání s **echo,** protože množství příchozí zprávy je malé. Dva servery aplikací jsou dost pro sla a výkon aspekty. Ale měli byste zvýšit výchozí připojení serveru, aby se zabránilo nerovnováze, zejména pro Unit50 a Unit100.

|   Vysílání      | Jednotka1 | Jednotka2 | Jednotka5 | Jednotka10 | Jednotka20 | Jednotka50 | Jednotka100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Zvyšte výchozí připojení serveru z 5 na 40 na každém aplikačním serveru, abyste se vyhnuli možným nevyváženým serverovým připojením ke službě Azure SignalR.
>
> Číslo připojení klienta, velikost zprávy, rychlost odesílání zpráv a úroveň skladové položky ovlivňují celkový výkon **vysílání**.

#### <a name="send-to-group"></a>Odeslat do skupiny

Případ **použití odeslat do skupiny** má podobný vzor přenosu jako **vysílání**. Rozdíl je, že poté, co klienti navázat připojení WebSocket se službou Azure SignalR, musí připojit skupiny před odesláním zprávy do určité skupiny. Následující diagram znázorňuje tok provozu.

![Provoz pro případ použití odeslat skupině](./media/signalr-concept-performance/sendtogroup.png)

Počet členů a skupin skupiny jsou dva faktory, které ovlivňují výkon. Pro zjednodušení analýzy definujeme dva druhy skupin:

- **Malá skupina**: Každá skupina má 10 připojení. Číslo skupiny je rovno (maximální počet připojení) / 10. Například pro Unit1, pokud existuje 1 000 počty připojení, pak máme 1000 / 10 = 100 skupin.

- **Velká skupina**: Číslo skupiny je vždy 10. Počet členů skupiny se rovná (maximální počet připojení) / 10. Například pro Unit1, pokud existuje 1 000 počty připojení, pak každá skupina má 1000 / 10 = 100 členů.

**Odeslat do skupiny** přináší náklady na směrování do služby Azure SignalR, protože má najít cílová připojení prostřednictvím struktury distribuovaných dat. Jak se zvyšují odesílající připojení, náklady se zvyšují.

##### <a name="small-group"></a>Malá skupina

Náklady na směrování jsou významné pro odesílání zprávy do mnoha malých skupin. V současné době implementace služby Azure SignalR narazí na limit nákladů na směrování na Unit50. Přidání další procesora a paměti nepomůže, takže Unit100 nemůže zlepšit dále podle návrhu. Pokud potřebujete větší šířku pásma, obraťte se na zákaznickou podporu.

|   Odeslat do malé skupiny     | Jednotka1 | Jednotka2 | Jednotka5  | Jednotka10 | Jednotka20 | Jednotka50 | Jednotka100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000 | 100 000
| Počet členů skupiny        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Počet skupin               | 100   | 200   | 500    | 1 000  | 2 000  | 5 000  | 10 000 
| Příchozí zprávy za sekundu  | 200   | 400   | 1 000  | 2,500  | 4 000  | 7,000  | 7,000   |
| Příchozí šířka pásma  | 400 kb/s  | 800 kb/s  | 2 Mb/s     | 5 Mb/s     | 8 Mb/s     | 14 mb/s    | 14 mb/s     |
| Odchozí zprávy za sekundu | 2 000 | 4 000 | 10 000 | 25 000 | 40,000 | 70,000 | 70,000  |
| Odchozí šířka pásma | 4 Mb/s    | 8 Mb/s    | 20 mb/s    | 50 mb/s     | 80 mb/s    | 140 mb/s   | 140 mb/s    |

Mnoho klientských připojení volá rozbočovač, takže číslo aplikačního serveru je také důležité pro výkon. V následující tabulce jsou uvedeny počty navržených aplikačních serverů.

|  Odeslat do malé skupiny   | Jednotka1 | Jednotka2 | Jednotka5 | Jednotka10 | Jednotka20 | Jednotka50 | Jednotka100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Číslo připojení klienta, velikost zprávy, rychlost odesílání zpráv, náklady na směrování, úroveň souku a procesor/paměť aplikačního serveru ovlivňují celkový výkon **odesílání do malé skupiny**.

##### <a name="big-group"></a>Velká skupina

Pro **odeslání do velké skupiny**se odchozí šířka pásma stane kritickým bodem před dosažením limitu nákladů na směrování. V následující tabulce je uvedena maximální odchozí šířka pásma, která je téměř stejná jako u **vysílání**.

|    Odeslat velké skupině      | Jednotka1 | Jednotka2 | Jednotka5  | Jednotka10 | Jednotka20 | Jednotka50  | Jednotka100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000
| Počet členů skupiny        | 100   | 200   | 500    | 1 000  | 2 000  | 5 000   | 10 000 
| Počet skupin               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Příchozí zprávy za sekundu  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Příchozí šířka pásma  | 80 kb/s   | 40 kb/s   | 40 kb/s    | 20 kb/s    | 40 kb/s    | 40 kb/s     | 40 kb/s     |
| Odchozí zprávy za sekundu | 2 000 | 4 000 | 10 000 | 20 000 | 40,000 | 100 000 | 200 000 |
| Odchozí šířka pásma | 8 Mb/s    | 8 Mb/s    | 20 mb/s    | 40 mb/s    | 80 mb/s    | 200 mb/s    | 400 mb/s    |

Počet odesílajícího připojení není větší než 40. Zatížení aplikačního serveru je malé, takže doporučený počet webových aplikací je malý.

|  Odeslat velké skupině  | Jednotka1 | Jednotka2 | Jednotka5 | Jednotka10 | Jednotka20 | Jednotka50 | Jednotka100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Zvyšte výchozí připojení serveru z 5 na 40 na každém aplikačním serveru, abyste se vyhnuli možným nevyváženým serverovým připojením ke službě Azure SignalR.
> 
> Číslo připojení klienta, velikost zprávy, rychlost odesílání zpráv, náklady na směrování a úroveň skladové položky ovlivňují celkový výkon **odesílání do velké skupiny**.

#### <a name="send-to-connection"></a>Odeslat k připojení

V případě **použití odeslat připojení,** když klienti navázat připojení ke službě Azure SignalR, každý klient volá speciální rozbočovač získat vlastní ID připojení. Srovnávací test výkonu shromažďuje všechna ID připojení, zamíchá je a znovu je přiřadí všem klientům jako cíl odesílání. Klienti ponechat odesílání zprávy do cílového připojení až do dokončení testu výkonu.

![Provoz pro případ použití odesílatele klientovi](./media/signalr-concept-performance/sendtoclient.png)

Náklady na směrování pro **odesílání do připojení** jsou podobné nákladům na odeslání do malé **skupiny**.

S nárůstem počtu připojení náklady na směrování omezuje celkový výkon. Jednotka 50 dosáhla limitu. V důsledku toho se Unit100 nemůže dále zlepšovat.

Následující tabulka je statistický přehled po mnoha kolech spuštění **odkazpro odesílání připojení.**

|   Odeslat k připojení   | Jednotka1 | Jednotka2 | Jednotka5 | Jednotka10 | Jednotka20 | Jednotka50          | Jednotka100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Připojení                        | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000          | 100 000         |
| Příchozí a odchozí zprávy za sekundu | 1 000 | 2 000 | 5 000 | 8 000  | 9 000  | 20 000 | 20 000 |
| Šířka pásma příchozích a odchozích | 2 Mb/s    | 4 Mb/s    | 10 Mb/s   | 16 mb/s    | 18 mb/s    | 40 mb/s       | 40 mb/s       |

Tento případ použití vyžaduje vysoké zatížení na straně aplikačního serveru. Podívejte se na navrhovaný počet aplikačních serverů v následující tabulce.

|  Odeslat k připojení  | Jednotka1 | Jednotka2 | Jednotka5 | Jednotka10 | Jednotka20 | Jednotka50 | Jednotka100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Číslo připojení klienta, velikost zprávy, rychlost odesílání zpráv, náklady na směrování, úroveň souku a procesor/paměť pro aplikační server ovlivňují celkový výkon **odesílání připojení**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET SignalR echo, vysílání a odeslání do malé skupiny

Služba Azure SignalR poskytuje stejnou kapacitu výkonu pro ASP.NET SignalR. 

Test výkonu používá Azure Web Apps ze [standardního plánu služeb S3](https://azure.microsoft.com/pricing/details/app-service/windows/) pro ASP.NET SignalR.

V následující tabulce je uveden doporučený počet webových aplikací pro ASP.NET **odezvu**SignalR .

|   Echo           | Jednotka1 | Jednotka2 | Jednotka5 | Jednotka10 | Jednotka20 | Jednotka50 | Jednotka100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

V následující tabulce je uveden doporučený počet webových aplikací pro **vysílání**ASP.NET SignalR .

|  Vysílání       | Jednotka1 | Jednotka2 | Jednotka5 | Jednotka10 | Jednotka20 | Jednotka50 | Jednotka100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

V následující tabulce je uveden doporučený počet webových aplikací pro ASP.NET signalr **odeslat malé skupině**.

|  Odeslat do malé skupiny     | Jednotka1 | Jednotka2 | Jednotka5 | Jednotka10 | Jednotka20 | Jednotka50 | Jednotka100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Bezserverový režim

Klienti a služba Azure SignalR jsou zapojeni do bezserverového režimu. Každý klient znamená jediné připojení. Klient odesílá zprávy prostřednictvím rozhraní REST API jinému klientovi nebo vysílá zprávy všem.

Odesílání zpráv s vysokou hustotou prostřednictvím rozhraní REST API není tak efektivní jako použití websocketu. Vyžaduje, abyste pokaždé vytvořili nové připojení HTTP, a to je příplatek v režimu bez serveru.

#### <a name="broadcast-through-rest-api"></a>Vysílání prostřednictvím rozhraní REST API
Všichni klienti navazují připojení WebSocket se službou Azure SignalR. Pak někteří klienti začít vysílat prostřednictvím rozhraní REST API. Zpráva odesílání (příchozí) je vše prostřednictvím HTTP Post, což není efektivní ve srovnání s WebSocket.

|   Vysílání prostřednictvím rozhraní REST API     | Jednotka1 | Jednotka2 | Jednotka5  | Jednotka10 | Jednotka20 | Jednotka50  | Jednotka100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Příchozí zprávy za sekundu  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Odchozí zprávy za sekundu | 2 000 | 4 000 | 10 000 | 20 000 | 40,000 | 100 000 | 200 000 |
| Příchozí šířka pásma  | 4 kb/s    | 4 kb/s    | 4 kb/s     | 4 kb/s     | 4 kb/s     | 4 kb/s      | 4 kb/s      |
| Odchozí šířka pásma | 4 Mb/s    | 8 Mb/s    | 20 mb/s    | 40 mb/s    | 80 mb/s    | 200 mb/s    | 400 mb/s    |

#### <a name="send-to-user-through-rest-api"></a>Odeslat uživateli prostřednictvím rozhraní REST API
Srovnávací test přiřadí uživatelská jména všem klientům před tím, než se začnou připojovat ke službě Azure SignalR. Poté, co klienti navázat websocket připojení se službou Azure SignalR, začnou odesílat zprávy ostatním prostřednictvím HTTP Post.

|   Odeslat uživateli prostřednictvím rozhraní REST API | Jednotka1 | Jednotka2 | Jednotka5  | Jednotka10 | Jednotka20 | Jednotka50  | Jednotka100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Příchozí zprávy za sekundu  | 300   | 600   | 900    | 1,300  | 2 000  | 10 000  | 18 000  |
| Odchozí zprávy za sekundu | 300   | 600   | 900    | 1,300  | 2 000  | 10 000  | 18 000 |
| Příchozí šířka pásma  | 600 kb/s  | 1,2 mb/s  | 1,8 mb/s   | 2,6 mb/s   | 4 Mb/s     | 10 Mb/s     | 36 mb/s    |
| Odchozí šířka pásma | 600 kb/s  | 1,2 mb/s  | 1,8 mb/s   | 2,6 mb/s   | 4 Mb/s     | 10 Mb/s     | 36 mb/s    |

## <a name="performance-test-environments"></a>Testovací prostředí výkonu

Pro všechny případy použití uvedené dříve jsme provedli testy výkonu v prostředí Azure. Maximálně jsme použili 50 klientských virtuálních aplikací a 20 virtuálních aplikací serverů. Zde jsou některé podrobnosti:

- Velikost virtuálního počítače klienta: StandardDS2V2 (2 virtuální procesory, 7G paměť)

- Velikost virtuálního počítače aplikačního serveru: StandardF4sV2 (4 virtuální procesory, 8G paměť)

- Připojení k serveru Azure SignalR SDK: 15

## <a name="performance-tools"></a>Nástroje pro měření výkonu

Nástroje pro výkon služby Azure SignalR najdete na [GitHubu](https://github.com/Azure/azure-signalr-bench/).

## <a name="next-steps"></a>Další kroky

V tomto článku získáte přehled výkonu služby Azure SignalR v typické scénáře použití.

Chcete-li získat podrobnosti o vnitřní části služby a škálování pro něj, přečtěte si následující příručky:

* [Interní informace služby Azure SignalR Service](signalr-concept-internals.md)
* [Škálování služby Azure SignalR](signalr-howto-scale-multi-instances.md)

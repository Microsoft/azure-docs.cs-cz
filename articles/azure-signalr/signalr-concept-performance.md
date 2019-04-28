---
title: Průvodce výkonem pro služby Azure SignalR
description: Přehled o výkonu služby Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: f7cc05c8c2a299d809c4386d119fef58fa2548d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269398"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Průvodce výkonem pro služby Azure SignalR

Jednou z klíčových výhod používání služby Azure SignalR je snadné škálování aplikací SignalR. V případě rozsáhlých výkonu je důležitým faktorem. 

V této příručce zavedeme faktorů ovlivňujících výkon aplikace SignalR. Popíšeme si typické výkonu v různých scénářích případů použití. Nakonec Připravujeme prostředí a nástroje, které můžete použít k vytvoření sestavy výkonu.

## <a name="term-definitions"></a>Definice termínu

*Příchozí*: Příchozí zprávy do služby Azure SignalR.

*Odchozí*: Odchozí zprávy ze služby Azure SignalR.

*Šířka pásma*: Celková velikost všech zpráv do 1 sekundy.

*Výchozí režim*: Výchozí pracovní režim při vytvoření instance služby Azure SignalR. Službě Azure SignalR očekává, že server aplikace k navázání připojení s ním před přijímá všechna připojení klientů.

*Bez serveru režimu*: Režim, ve kterém služby Azure SignalR akceptuje pouze připojení klientů. Nepovoluje se žádné připojení serveru.

## <a name="overview"></a>Přehled

Službě Azure SignalR definuje sedm úrovně Standard pro jiné výkonové kapacity. Tato příručka poskytuje odpovědi na následující otázky:

-   Co je typické výkonu služby Azure SignalR pro každou vrstvu?

-   Splňuje služby Azure SignalR Moje požadavky na propustnost zpráv (například odesílání 100 000 zpráv za sekundu)?

-   Pro tento konkrétní scénář, jaké úroveň je vhodný pro mě nejlepší? Nebo jak můžete vybrat na správné úrovni?

-   Jaký druh aplikačního serveru (velikost virtuálního počítače) je vhodný pro mě nejlepší? Kolik z nich měli nasadit?

Odpovědi na tyto otázky, nejprve tato příručka obsahuje podrobný popis faktory, které ovlivňují výkon. Pak znázorňuje maximální počet příchozích a odchozích zpráv pro každou vrstvu pro typické případy použití: **echo**, **vysílání**, **odeslat do skupiny**, a **odeslat připojení** (peer-to-peer chatovat).

Tento průvodce nemůže měli pokryté všechny scénáře (a různé případy použití, velikost zpráv, zpráv odesílání vzorků a tak dále). Ale poskytuje některé metody umožňují:

- Vyhodnoťte vaše přibližné požadavek pro příchozí nebo odchozí zprávy.
- Najdete správné úrovně tak, že zkontrolujete tabulku výkonu.

## <a name="performance-insight"></a>Přehled o výkonu

Tato část popisuje metody vyhodnocení výkonu a pak zobrazí seznam všech faktorů, které ovlivňují výkon. Nakonec poskytuje metody, které vám pomůžou vyhodnotit požadavky na výkon.

### <a name="methodology"></a>Metodologie

*Propustnost* a *latence* dva aspekty typické kontroly výkonu. Každá úroveň SKU pro služby Azure SignalR, má vlastní propustnost omezení zásad. Definuje zásady *maximální povolenou propustnost (příchozí a odchozí šířka pásma)* jako maximální dosažená propustnost, když 99 procent společností z žebříčku zprávy mají latenci, která je menší než 1 sekundu.

Latence je časový rozsah od připojení posílání zprávy na příjem zprávy s odpovědí ze služby Azure SignalR. Pojďme se na to **echo** jako příklad. Každé připojení klienta se přidá časové razítko ve zprávě. App server hub odesílá na původní zprávu o zpět do klienta. Snadno se tak zpoždění šíření vypočte tak, všechna připojení klienta. Časové razítko je připojen pro každou zprávu v **vysílání**, **odeslat do skupiny**, a **odeslat připojení**.

Umožňuje simulovat tisíce souběžných připojení, se vytvoří víc virtuálních počítačů v Azure virtuální privátní síť. Všechny tyto virtuální počítače připojit na stejnou instanci služby Azure SignalR.

Ve výchozím režimu služby Azure SignalR aplikačního serveru virtuálních počítačů nasazených ve stejné virtuální privátní síti jako virtuální počítače klienta. Všechny virtuální počítače klienta a aplikačního serveru virtuálních počítačů nasazených ve stejné síti stejné oblasti, aby se zabránilo latenci mezi oblastmi.

### <a name="performance-factors"></a>Faktory výkonu

Teoreticky je omezena služby Azure SignalR kapacitu výpočetních prostředků, které: Využití procesoru, paměti a sítě. Například další připojení ke službě Azure SignalR způsobit, že má služba používat více paměti. Pro větší přenos zpráv (například všechny zprávy je větší než 2 048 bajtů), služby Azure SignalR je potřeba věnovat víc cyklů procesoru pro zpracování provozu. Mezitím šířky pásma sítě Azure má také omezení pro maximální provoz.

Typ přenosu je dalším faktorem, který má vliv na výkon. Jsou tři typy [objektu websocket na straně](https://en.wikipedia.org/wiki/WebSocket), [Server odeslané události](https://en.wikipedia.org/wiki/Server-sent_events), a [s dlouhým intervalem dotazování](https://en.wikipedia.org/wiki/Push_technology). 

Objekt WebSocket je obousměrný a protokol plně duplexní komunikace přes samostatné připojení TCP. Server odeslané události je jednosměrná protokol předávat zprávy ze serveru do klienta. S dlouhým intervalem dotazování vyžaduje, aby klienti pravidelně posílat informace ze serveru prostřednictvím požadavku HTTP do služby. Pro stejného rozhraní API za stejných podmínek objektu websocket na straně má nejlepší výkon, Server odeslané události je pomalejší a s dlouhým intervalem dotazování se načítají nejpomaleji. Službě Azure SignalR doporučuje ve výchozím nastavení protokolu WebSocket.

Náklady směrování zpráv také omezení výkonu. Službě Azure SignalR hraje roli jako směrovač zprávu, která směruje zprávy ze skupiny klientů nebo serverů do jiných klientů nebo serverů. Jiný scénář nebo rozhraní API vyžaduje jiné zásady směrování. 

Pro **echo**, klient odešle zprávu do samotného a cíl směrování je také samotný. Tento model má nejnižší směrování náklady. Ale pro **vysílání**, **odeslat do skupiny**, a **odeslat připojení**, musí vyhledat cíl připojení prostřednictvím interní distribuovaných dat služby Azure SignalR Struktura. Tato další zpracování používá další procesoru, paměti a šířky pásma sítě. V důsledku toho je pomalejší výkon.

Ve výchozím režimu aplikačním serverem může být také stát úzkým místem pro určité scénáře. Sada SDK Azure SignalR musí vyvolat rozbočovače, zatímco udržuje živého připojení se každý klient prostřednictvím prezenční signály.

V režimu bez serveru klient odešle zprávu pomocí protokolu HTTP post, který není tak účinné jako protokolu WebSocket.

Dalším faktorem je protokol: JSON a [MessagePack](https://msgpack.org/index.html). MessagePack je menší a poskytují rychlejší než JSON. MessagePack nemusí ale zlepšit výkon. Výkon služby Azure SignalR je citlivé na protokoly, protože ho nebude během předávání zpráv od klientů na servery nebo naopak dekódování datovou část zprávy.

Stručně řečeno ovlivňují následující faktory kapacitu příchozích a odchozích:

-   Úroveň skladové položky (procesoru nebo paměti)

-   Počet připojení

-   Velikost zpráv

-   míra odesílání zpráv

-   Typ přenosu (pomocí protokolu WebSocket, Server odeslané události nebo s dlouhým intervalem dotazování)

-   Scénář použití (náklady na směrování)

-   aplikace serveru a služby připojení (v režimu serveru)


### <a name="finding-a-proper-sku"></a>Vyhledání správné SKU

Jak můžete vyhodnotit vstupní/výstupní kapacity nebo najít jaké úroveň je vhodný pro případ použití konkrétního?

Předpokládají, že server aplikace je dostatečně výkonná a není snížení výkonu. Zkontrolujte maximální příchozí a odchozí šířka pásma pro každou vrstvu.

#### <a name="quick-evaluation"></a>Rychlé vyhodnocení

Můžeme zjednodušit hodnocení nejprve za předpokladu, že některé výchozí nastavení: 

- Typ přenosu je pomocí protokolu WebSocket.
- Velikost zprávy je 2 048 bajtů.
- Každou 1 sekundu je odeslána zpráva.
- Službě Azure SignalR je ve výchozím režimu.

Každá vrstva zahrnuje vlastní maximální příchozí a odchozí šířka pásma. Usnadnili uživateli práci není zaručeno, že po příchozí nebo odchozí připojení překračuje limit.

**Echo** poskytuje maximální šířky příchozího pásma, protože má nejnižší směrování náklady. **Vysílání** definuje maximální odchozí zprávy šířky pásma.

Proveďte *není* překročit zvýrazněné hodnoty v následujících dvou tabulkách.

|       echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení                       | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| **Příchozí šířky pásma** | **2 MB/s**    | **4 MB/s**    | **10 MB/s**   | **20 MB/s**    | **40 MB/s**    | **100 MB/s**   | **200 MB/s**    |
| Odchozí šířka pásma | 2 Mb/s   | 4 Mb/s   | 10 Mb/s  | 20 MB/s   | 40 MBps   | 100 MB/s  | 200 MB/s   |


|     Vysílání             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Příchozí šířky pásma  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps    |
| **Odchozí šířka pásma** | **4 MB/s**    | **8 MB/s**    | **20 MB/s**    | **40 MB/s**    | **80 MB/s**    | **200 MB/s**    | **400 MB/s**   |

*Příchozí šířky pásma* a *šířku odchozího pásma* jsou celková velikost zpráv za sekundu.  Tady jsou vzorce pro ně:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: Počet připojení odeslání zprávy.

- *outboundConnections*: Počet připojení příjem zprávy.

- *messageSize*: Velikost (průměrná hodnota) do jedné zprávy. Krátké zprávy kratší než 1024 bajtů, která má dopad na výkon, který je podobný 1 024 bajty zprávy.

- *sendInterval*: Čas odeslání zpráv. Obvykle je 1 sekunda na zprávu, což znamená, že odesílání zpráv za sekundu. Menší interval znamená, že odesílání další zprávu v časovém období. Například 0,5 sekund za zpráva znamená, že odesílání dvě zprávy za sekundu.

- *Připojení*: Potvrdit maximální prahová hodnota pro služby Azure SignalR pro každou vrstvu. Pokud číslo připojení se dál zvýší, sníží se z omezení šířky pásma připojení.

#### <a name="evaluation-for-complex-use-cases"></a>Vyhodnocení pro případy použití komplexní

##### <a name="bigger-message-size-or-different-sending-rate"></a>Větší velikost zprávy nebo jinou míru odesílání

Případu skutečném použití je složitější. To může odesílat zprávy větší než 2 048 bajtů nebo rychlost odesílání zprávy není jedna zpráva za sekundu. Pojďme se na to vysílání Unit100 jako příklad najít jak vyhodnotit výkon.

V následující tabulce jsou uvedeny případu skutečném použití z **vysílání**. Ale velikost, počet připojení a rychlost odesílání zprávy se liší od co jsme předpokládá, že v předchozí části. Dotaz je, jak můžeme odvodit kterékoli z těchto položek (velikost zprávy, počet připojení nebo rychlost odesílání zpráv) Pokud víme pouze dva z nich.

| Vysílání  | Velikost zpráv | Příchozí zprávy za sekundu | Připojení | Odeslat intervaly |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100 000     | 5 s                      |
| 2 | 256 kB               | 1                        | 8 000       | 5 s                      |

Tento vzorec je snadné odvodit na základě předchozí vzorce:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Maximální šířku odchozího pásma pro Unit100, je 400 MB z předchozí tabulky. Zpráva o velikosti 20 KB velikost, maximální počet odchozích připojení by měl být 400 MB \* 5 / 100 000 = 20 KB, který odpovídá skutečné hodnoty.

##### <a name="mixed-use-cases"></a>Smíšené použití

Případu skutečném použití společně obvykle používá čtyři základní svědectví: **echo**, **vysílání**, **odeslat do skupiny**, a **odeslat připojení**. Metoda, která slouží k vyhodnocení, kapacitu je:

1. Smíšené použití rozdělte do čtyř případy základní použití.
1. Vypočítejte šířku pásma maximální příchozí a odchozí zprávy pomocí předchozích vzorců samostatně.
1. Součet výpočet šířky pásma a získat celkový počet maximální příchozí/odchozí šířka pásma. 

Potom si vyberte si správnou vrstvu z tabulek maximální příchozí/odchozí šířka pásma.

> [!NOTE]
> K odeslání zprávy na stovky nebo tisíce malé skupiny, nebo tisíců klientů posílání zprávy k sobě navzájem se stane dominantní směrování náklady. Využijte tento dopad v úvahu.

Pro případ použití odeslat zprávu na klienty, ujistěte se, že je server aplikace *není* problémové místo. Následující část "Případovou studii –" poskytuje pokyny o tom, kolik serverů aplikace budete potřebovat a kolik připojení k serveru byste měli nakonfigurovat.

## <a name="case-study"></a>Případová studie

V dalších částech projít čtyři typické případy použití pro přenos pomocí protokolu WebSocket: **echo**, **vysílání**, **odeslat do skupiny**, a **odeslat připojení**. Pro každý scénář v části jsou uvedené aktuální vstupní a výstupní kapacity pro služby Azure SignalR. Také vysvětluje hlavní faktory, které ovlivňují výkon.

Ve výchozím režimu aplikačního serveru vytvoří pět připojení k serveru pomocí služby Azure SignalR. Ve výchozím nastavení používá server aplikace sadu SDK služby Azure SignalR. V následujících výsledků testu výkonnosti se připojení k serveru zvyšují do 15 (nebo více pro všesměrové vysílání a posílání zprávy k velké skupině).

Použití v odlišných situacích mají různé požadavky na serverech aplikace. **Vysílání** potřebuje malý počet serverů aplikací. **Echo** nebo **odeslat připojení** potřebuje mnoho serverů aplikací.

Ve všech případy použití, výchozí velikost zprávy je 2 048 bajtů a intervalu odeslání zprávy je 1 sekunda.

### <a name="default-mode"></a>Výchozí režim

Ve výchozím režimu se podílejí klientů, serverů webových aplikací a služby Azure SignalR. Každý klient je zkratka pro jedno připojení.

#### <a name="echo"></a>echo

Webovou aplikaci poprvé, připojí ke službě Azure SignalR. Za druhé počtu klientů připojení k webové aplikace, který přesměruje klienty do služby Azure SignalR s přístupovým tokenem a koncového bodu. Potom klienti musí vytvořit připojení pomocí protokolu WebSocket pomocí služby Azure SignalR.

Po připojení všech klientů, začnou odesílá zprávu, která obsahuje časové razítko pro konkrétní rozbočovače za sekundu. Centrum vypisuje zprávy zpět do její původní klienta. Každý klient vypočítá latence při přijetí zprávy echo zpět.

V následujícím diagramu 5 až 8 (červený zvýrazněný provozu) se ve smyčce. Smyčky spouští pro výchozí dobu trvání (5 minut) a získá statistiku všechny zprávy latence.

![Provoz pro případ použití programu echo](./media/signalr-concept-performance/echo.png)

Chování **echo** shledá, že maximální šířky příchozího pásma je rovna hodnotě maximální šířky odchozího pásma. Podrobnosti najdete v následující tabulce.

|       echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení                       | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Příchozí/odchozí zprávy za sekundu | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Příchozí nebo odchozí šířka pásma | 2 Mb/s   | 4 Mb/s   | 10 Mb/s  | 20 MB/s   | 40 MBps   | 100 MB/s  | 200 MB/s   |

V tomto případě se vyvolá každý klient rozbočovači definovanému v aplikačním serverem. Centrum jen volá metodu definované v původní na straně klienta. Je nejvíce zjednodušené rozbočovače pro toto centrum **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

I pro toto centrum jednoduché je provoz tlak na serveru aplikace jako viditelného **echo** příchozí zprávy zvyšuje zatížení. Toto přetížení provoz vyžaduje počtu serverů aplikace pro velké SKU úrovně. V následující tabulce jsou uvedeny počtu serverů aplikace pro každou vrstvu.


|    echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Klienta připojení číslo, velikost zprávy, zprávu odesílání rychlost, úroveň skladové položky a procesoru nebo paměti aplikačního serveru ovlivnit celkový výkon **echo**.

#### <a name="broadcast"></a>Vysílání

Pro **vysílání**, když webová aplikace obdrží zprávu, vysílá pro všechny klienty. Existuje více klientů se všesměrové vysílání, další přenos zpráv je pro všechny klienty. Podívejte se na následující diagram.

![Provoz pro případ použití všesměrového vysílání](./media/signalr-concept-performance/broadcast.png)

Malý počet klientů jsou všesměrové vysílání. Je malá šířka pásma příchozích zpráv, ale je velkou šířku odchozího pásma. Šířka pásma pro odchozí zprávy se zvyšuje s rostoucím připojení klienta nebo míry vysílání zvyšuje.

Následující tabulka shrnuje maximální počet klientských připojení, počet příchozích nebo odchozích zpráv a šířky pásma.

|     Vysílání             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Příchozí zprávy za sekundu  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Odchozí zprávy za sekundu | 2 000 | 4,000 | 10 000 | 20,000 | 40,000 | 100 000 | 200 000 |
| Příchozí šířky pásma  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     |
| Odchozí šířka pásma | 4 Mb/s   | 8 Mb/s   | 20 MB/s   | 40 MBps   | 80 MBps   | 200 MB/s   | 400 MB/s   |

Všesměrové vysílání klienty, kteří odesílají zprávy jsou více než čtyři. Ve srovnání s menším počtem serverů aplikace potřebují **echo** vzhledem k tomu, že je malá velikost příchozí zprávy. Dva servery aplikace jsou dostatečné pro smlouvy SLA a výkonové požadavky. Ale měli byste zvýšit výchozí serverová připojení, aby se zabránilo nerovnováze, zejména u Unit50 a Unit100.

|   Vysílání      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Zvýšení výchozí připojení serveru z 5 na 40 na každém serveru aplikace, aby se zabránilo možné nevyváženou serveru připojení do služby Azure SignalR.
>
> Počet připojení klienta, velikost zprávy, rychlost odesílání zpráv a úroveň skladové položky vliv na celkový výkon pro **vysílání**.

#### <a name="send-to-group"></a>Odeslat do skupiny

**Odeslat do skupiny** případem použití je podobný vzorec provoz do **vysílání**. Rozdíl je, že po klientů připojení protokolu WebSocket pomocí služby Azure SignalR, se musíte připojit skupiny před odesláním zprávy pro konkrétní skupinu. Následující diagram znázorňuje tok provozu.

![Provoz pro případ použití odeslat do skupiny](./media/signalr-concept-performance/sendtogroup.png)

Člen skupiny a počet skupin jsou dva faktory, které ovlivňují výkon. Pro zjednodušení analýzy, nadefinujeme dva typy skupin:

- **Malá skupina**: Každá skupina má 10 připojení. Číslo skupiny je rovno (počet maximální počet připojení) / 10. Například Unit1, pokud jsou počty 1 000 připojení, potom máme 1 000 / 10 = 100 skupiny.

- **Velká skupina**: Číslo skupiny je vždy 10. Počet členů skupiny je rovno (počet maximální počet připojení) / 10. Například pro Unit1, při 1 000 připojení počty, pak každá skupina má 1 000 / 10 = 100 členy.

**Odeslat do skupiny** přináší směrování náklady na služby Azure SignalR, protože má se najít cíl připojení prostřednictvím distribuované datová struktura. Odesílání připojení zvýšit, zvyšuje náklady.

##### <a name="small-group"></a>Malá skupina

Náklady směrování je důležité pro odesílání zpráv do více skupin malé. V současné době implementaci služby Azure SignalR narazí směrování omezení nákladů na Unit50. Přidání další procesoru a paměti nepomůže, proto nemůže zlepšit Unit100 další záměrné. Pokud potřebujete více příchozí šířky pásma, obraťte se na zákaznickou podporu.

|   Odeslat do malé skupiny     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20,000 | 50,000 | 100 000
| Počet členů skupiny        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Počet skupin               | 100   | 200   | 500    | 1 000  | 2 000  | 5 000  | 10 000 
| Příchozí zprávy za sekundu  | 200   | 400   | 1 000  | 2,500  | 4,000  | 7,000  | 7,000   |
| Příchozí šířky pásma  | 400 KBps  | 800 KBps  | 2 Mb/s     | 5 Mb/s     | 8 Mb/s     | 14 MBps    | 14 MBps     |
| Odchozí zprávy za sekundu | 2 000 | 4,000 | 10 000 | 25,000 | 40,000 | 70,000 | 70,000  |
| Odchozí šířka pásma | 4 Mb/s    | 8 Mb/s    | 20 MB/s    | 50 MBps     | 80 MBps    | 140 MBps   | 140 MBps    |

Počet připojení klientů jsou volání rozbočovače, tak server číslo aplikace je také důležité pro výkon. V následující tabulce jsou uvedeny počty server navrhované aplikace.

|  Odeslat do malé skupiny   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Klienta připojení číslo, velikost zprávy, zprávu odesílání rychlost, směrování náklady, úroveň skladové položky a procesoru nebo paměti aplikačního serveru ovlivnit celkový výkon **odeslat do malé skupiny**.

##### <a name="big-group"></a>Velká skupina

Pro **odeslat do velké skupiny**, odchozí šířka pásma se stane problémové místo před tím, směrování nákladů limit. V následující tabulce jsou uvedeny maximální šířku odchozího pásma, která je téměř stejný jako u **vysílání**.

|    Odeslat do velké skupiny      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000
| Počet členů skupiny        | 100   | 200   | 500    | 1 000  | 2 000  | 5 000   | 10 000 
| Počet skupin               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Příchozí zprávy za sekundu  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Příchozí šířky pásma  | 80 KBps   | 40 KBps   | 40 KBps    | 20 KBps    | 40 KBps    | 40 KBps     | 40 KBps     |
| Odchozí zprávy za sekundu | 2 000 | 4,000 | 10 000 | 20,000 | 40,000 | 100 000 | 200 000 |
| Odchozí šířka pásma | 8 Mb/s    | 8 Mb/s    | 20 MB/s    | 40 MBps    | 80 MBps    | 200 MB/s    | 400 MB/s    |

Odesílání počet připojení je více než 40. Zatížení na serveru aplikace je malé, takže doporučený počet webových aplikací je malý.

|  Odeslat do velké skupiny  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Zvýšení výchozí připojení serveru z 5 na 40 na každém serveru aplikace, aby se zabránilo možné nevyváženou serveru připojení do služby Azure SignalR.
> 
> Počet připojení klientů, velikost, rychlost odesílání zpráv, směrování náklady a úroveň skladové položky ovlivnit celkový výkon **odeslat do velké skupiny**.

#### <a name="send-to-connection"></a>Odeslat do připojení

V **odeslat připojení** případ použití, když klienti navázat připojení ke službě Azure SignalR, každý klient volá speciální hub získat vlastní ID připojení. Srovnávací test výkonu shromažďuje všechna ID připojení, je uspořádá a znovu jim přiřadí všem klientům jako odesílající cíl. Klienti posílat zprávy do cílové připojení až do dokončení testu výkonnosti.

![Provoz pro případ použití odeslat klienta](./media/signalr-concept-performance/sendtoclient.png)

Směrování nákladů pro **odeslat připojení** je podobný náklady pro **odeslat do malé skupiny**.

Jak se zvyšuje počet připojení, omezuje směrování náklady na celkový výkon. Unit50 dosáhl limitu. V důsledku toho nelze ještě více zlepšit Unit100.

V následující tabulce je uveden statistické po mnoho zaokrouhlí spuštění **odeslat připojení** srovnávacího testu.

|   Odeslat do připojení   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Připojení                        | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000          | 100 000         |
| Příchozí/odchozí zprávy za sekundu | 1 000 | 2 000 | 5 000 | 8 000  | 9,000  | 20,000 | 20,000 |
| Příchozí nebo odchozí šířka pásma | 2 Mb/s    | 4 Mb/s    | 10 Mb/s   | 16 MB/s    | 18 MB/s    | 40 MBps       | 40 MBps       |

Tento případ použití vyžaduje vysokého zatížení v aplikaci na straně serveru. Zobrazit server navrhované aplikace počet v následující tabulce.

|  Odeslat do připojení  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Klienta připojení číslo, velikost zprávy, zprávu odesílání rychlost, směrování náklady, úroveň skladové položky a procesoru nebo paměti pro aplikační server ovlivnit celkový výkon **odeslat připojení**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>Funkce SignalR technologie ASP.NET odezvu, všesměrového vysílání a odeslat do malé skupiny

Službě Azure SignalR poskytuje kapacitu výkonu pro funkci SignalR technologie ASP.NET. 

Test výkonu používá Azure Web Apps z [Standard S3 plánování služby](https://azure.microsoft.com/pricing/details/app-service/windows/) pro funkci SignalR technologie ASP.NET.

Následující tabulka uvádí počet navrhované web app pro funkci SignalR technologie ASP.NET **echo**.

|   echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

Následující tabulka uvádí počet navrhované web app pro funkci SignalR technologie ASP.NET **vysílání**.

|  Vysílání       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

Následující tabulka uvádí počet navrhované web app pro funkci SignalR technologie ASP.NET **odeslat do malé skupiny**.

|  Odeslat do malé skupiny     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Režim bez serveru

Klienti a služby Azure SignalR jsou zahrnuty v režimu bez serveru. Každý klient je zkratka pro jedno připojení. Klient odešle do jiného klienta nebo všesměrové vysílání zpráv do všech zpráv prostřednictvím rozhraní REST API.

Odesílání zpráv s vysokou hustotou přes rozhraní REST API není tak účinné jako pomocí protokolu WebSocket. Můžete vytvořit nové připojení HTTP pokaždé, když se vyžaduje a, který je speciální poplatky v režimu bez serveru.

#### <a name="broadcast-through-rest-api"></a>Vysílání přes rozhraní REST API
Všichni klienti připojení pomocí protokolu WebSocket pomocí služby Azure SignalR. Někteří klienti pak spusťte vysílání přes rozhraní REST API. Zpráva odesílání (příchozí) je to vše prostřednictvím HTTP Post, který není efektivní ve srovnání s pomocí protokolu WebSocket.

|   Vysílání přes rozhraní REST API     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Příchozí zprávy za sekundu  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Odchozí zprávy za sekundu | 2 000 | 4,000 | 10 000 | 20,000 | 40,000 | 100 000 | 200 000 |
| Příchozí šířky pásma  | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     | 4 KBps     | 4 KBps      | 4 KBps      |
| Odchozí šířka pásma | 4 Mb/s    | 8 Mb/s    | 20 MB/s    | 40 MBps    | 80 MBps    | 200 MB/s    | 400 MB/s    |

#### <a name="send-to-user-through-rest-api"></a>Poslat uživateli prostřednictvím rozhraní REST API
Test výkonnosti přiřadí uživatelských jmen pro všechny klienty dříve, než začnou připojení ke službě Azure SignalR. Jakmile klienti připojení pomocí protokolu WebSocket pomocí služby Azure SignalR, začnou odesílání zpráv přes HTTP Post.

|   Poslat uživateli prostřednictvím rozhraní REST API | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Příchozí zprávy za sekundu  | 300   | 600   | 900    | 1,300  | 2 000  | 10 000  | 18,000  |
| Odchozí zprávy za sekundu | 300   | 600   | 900    | 1,300  | 2 000  | 10 000  | 18,000 |
| Příchozí šířky pásma  | 600 KBps  | 1.2 MB/s  | 1.8 MB/s   | 2.6 MB/s   | 4 Mb/s     | 10 Mb/s     | 36 MB/s    |
| Odchozí šířka pásma | 600 KBps  | 1.2 MB/s  | 1.8 MB/s   | 2.6 MB/s   | 4 Mb/s     | 10 Mb/s     | 36 MB/s    |

## <a name="performance-test-environments"></a>Prostředí pro testování výkonu

Pro všechny případy výše uvedených použití, můžeme regulovat testy výkonu v prostředí Azure. Maximálně jsme použili 50 virtuální počítače klienta a aplikačního serveru 20 virtuálních počítačů. Tady jsou některé podrobnosti:

- Velikost virtuálního počítače klienta: StandardDS2V2 (2 virtuální procesory a paměť 7G)

- Aplikační server velikost virtuálního počítače: StandardF4sV2 (4 vCPU, 8G memory)

- Azure SignalR SDK připojení serverů: 15

## <a name="performance-tools"></a>Nástroje pro měření výkonu

Nástroje pro měření výkonu pro služby Azure SignalR můžete najít na [Githubu](https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin).

## <a name="next-steps"></a>Další postup

V tomto článku jste získali přehled o výkonu služby Azure SignalR v typické scénáře použití.

Získat podrobné informace o interní informace služby a škálování, přečtěte si následující příručky:

* [Interní informace o Azure služby SignalR](signalr-concept-internals.md)
* [Škálování služby Azure SignalR](signalr-howto-scale-multi-instances.md)

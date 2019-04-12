---
title: Průvodce výkonem pro služby Azure SignalR
description: Přehled o výkonu služby Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: 53139dd253c491ea6578fd0b9cbada4e7b331c7d
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502035"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Průvodce výkonem pro služby Azure SignalR

Jednou z klíčových výhod používání služby Azure SignalR je snadné škálování aplikací SignalR. V případě rozsáhlých stane výkonu důležitým faktorem. V této příručce uvedeme faktory, které mají dopad na výkon aplikace SignalR a v části různé scénáře použití, co je typické výkonu? Nakonec jsme také zavádí prostředí a nástroje sloužící ke generování sestav výkonu.

## <a name="terms-definition"></a>Definice podmínek

*ASRS*: Služba Azure SignalR

*Příchozí*: příchozí zprávy do služby Azure SignalR

*Odchozí*: odchozí zprávy ze služby Azure SignalR

*Šířka pásma*: Celková velikost všech zpráv do 1 sekundy

*Výchozí režim*: ASRS očekává, že server aplikace k navázání připojení s ním před přijetím všechna připojení klientů. Při vytváření ASRS je výchozí pracovní režim.

*Bez serveru režimu*: ASRS akceptuje pouze připojení klientů. Nepovoluje se žádné připojení serveru.

## <a name="overview"></a>Přehled

ASRS definuje sedm úrovně Standard pro jiné výkonové kapacity a tento průvodce si klade za cíl odpovědět na následující otázky:

-   Co je typické ASRS výkonu pro jednotlivé úrovně?

-   ASRS splňuje mé požadavky propustnost zpráv, například odesílání 100 000 zpráv za sekundu?

-   Pro tento konkrétní scénář, jaké úroveň je vhodný pro mě nejlepší? Nebo jak můžete vybrat na správné úrovni?

-   Jaký druh aplikačního serveru (velikost virtuálního počítače) je vhodná pro mě a kolik z nich je možné nasadit?

Odpovědět na tyto otázky, tento Průvodce výkonem nejprve poskytuje podrobný vysvětlení faktory, které mají dopad na výkon a potom ukazuje maximální počet příchozích a odchozích zpráv pro každou vrstvu pro typické případy použití: **echo**, **vysílání**, **odeslat do skupiny**, a **odeslat připojení** (chatovat typu peer to peer).

Není možné pro tento dokument pro všechny scénáře (a případ použití v odlišných, velikost jiná zpráva nebo zpráva odesílání vzor atd.). Však poskytuje některé zkušební metody k poskytování pomoci uživatelům přibližně vyhodnotit jejich požadavek příchozí nebo odchozí zprávy, pak najít správné úrovně tak, že zkontrolujete tabulku výkonu.

## <a name="performance-insight"></a>Přehled o výkonu

Tato část popisuje metody vyhodnocení výkonu a pak zobrazí seznam všech faktorů, které mají dopad na výkon. Nakonec poskytuje metody k vyhodnocení požadavků na výkon.

### <a name="methodology"></a>Metodologie

**Propustnost** a **latence** dva aspekty typické kontroly výkonu. Pro ASRS má jinou úroveň SKU různých propustnost omezení zásad. Tento dokument definuje **maximální povolenou propustnost (příchozí a odchozí šířka pásma)** jako rozdílu mezi maximálním dosažená propustnost, když 99 % zpráv, které mají latenci menší než 1 sekundu.

Latence je časový rozsah od přijetí zprávy s odpovědí z ASRS odesílání zprávy připojení. Pojďme se na to **echo** každé připojení klienta jako příklad přidá časové razítko ve zprávě. Aplikační server hub odesílá na původní zprávu o zpět do klienta. Snadno se tak zpoždění šíření vypočte tak, všechna připojení klienta. Časové razítko je připojen pro každou zprávu v **vysílání**, **odeslat do skupiny**, a **odeslat připojení**.

Umožňuje simulovat tisíce souběžných klientů připojení, se vytvoří víc virtuálních počítačů v Azure virtuální privátní síť. Všechny tyto virtuální počítače připojit na stejnou instanci ASRS.

Ve výchozím režimu ASRS se také nasazují aplikačního serveru virtuální počítače ve stejné virtuální privátní síti jako virtuální počítače klienta.

Všechny virtuální počítače klienta a aplikačního serveru, které jsou nasazené virtuální počítače ve stejné síti stejné oblasti, aby se zabránilo pro různé oblasti latence.

### <a name="performance-factors"></a>Faktory výkonu

Teoreticky je omezena ASRS kapacitu výpočetních prostředků, které: Využití procesoru, paměti a sítě. Například další připojení k ASRS větší množství paměti ASRS spotřebuje. Pro větší přenos zpráv například všechny zprávy je větší než 2 048 bajtů, vyžaduje ASRS moct strávit víc cyklů procesoru i zpracování. Mezitím šířky pásma sítě Azure má také omezení pro maximální provoz.

Typ přenosu [objektu websocket na straně](https://en.wikipedia.org/wiki/WebSocket), [zapnuté odeslané události](https://en.wikipedia.org/wiki/Server-sent_events), nebo [s dlouhým intervalem dotazování](https://en.wikipedia.org/wiki/Push_technology), je další faktor ovlivňuje výkon. Objekt WebSocket je protokol obousměrné a plně duplexní komunikaci přes samostatné připojení TCP. Nicméně zapnuté odeslané události je jednosměrný protokol nabízená zpráva ze serveru do klienta. S dlouhým intervalem dotazování vyžaduje, aby klienti pravidelně posílat informace ze serveru prostřednictvím požadavku HTTP do služby. Pro stejného rozhraní API v rámci stejného stavu pomocí protokolu WebSocket je nejlepší výkon, zapnuté odeslané události je pomalejší a s dlouhým intervalem dotazování se načítají nejpomaleji. ASRS doporučuje ve výchozím nastavení protokolu WebSocket.

Náklady směrování zpráv navíc také omezení výkonu. ASRS hraje roli jako směrovač zprávu, která směruje zprávy ze skupiny klientů nebo serverů do jiných klientů nebo serverů. Jiný scénář nebo rozhraní API vyžaduje jiné zásady směrování. Pro **echo**, klient odešle zprávu do samotného a cíl směrování je také samotný. Tento model má nejnižší směrování náklady. Ale pro **vysílání**, **odeslat do skupiny**, **odeslat připojení**, ASRS potřebuje k vyhledání cíl připojení prostřednictvím interní distribuované datová struktura, která spotřebovává více procesoru, paměti a dokonce i šířku pásma sítě. V důsledku toho je pomalejší než výkonu **echo**.

Ve výchozím režimu může aplikační server také stát kritickým bodem pro určité scénáře, protože má sadu SDK Azure SignalR k vyvolání centra mezitím udržuje živého připojení se každý klient prostřednictvím signály prezenční signál.

V režimu bez serveru klient odešle zprávu pomocí protokolu HTTP post, který není tak účinné jako protokolu WebSocket.

Dalším faktorem je protokol: JSON a [MessagePack](https://msgpack.org/index.html). MessagePack je menší a poskytují rychlejší než JSON. Intuitivně MessagePack využít výkon, ale ASRS výkonu není s protokoly citlivá, protože nedekóduje datovou část zprávy během předávání zpráv od klientů na servery nebo naopak.

Stručně řečeno následující faktory mají dopad na kapacitě příchozí a odchozí:

-   Úroveň skladové položky (procesoru nebo paměti)

-   Počet připojení

-   Velikost zprávy

-   míra odesílání zpráv

-   typ přenosu (pomocí protokolu WebSocket/zapnuté-odeslané-události/s dlouhým intervalem dotazování)

-   Scénář (směrování náklady) použití

-   aplikace serveru a služby připojení (v režimu serveru)


### <a name="find-a-proper-sku"></a>Najít správné SKU

Jak vyhodnotit vstupní/výstupní kapacity nebo jak zjistit, jaké úroveň je vhodný pro případ použití konkrétního?

Předpokládáme, že se aplikační server je dostatečně výkonná a není snížení výkonu. Pak lze kontrolujeme maximální příchozí a odchozí šířka pásma pro každou vrstvu.

#### <a name="quick-evaluation"></a>Rychlé vyhodnocení

Můžeme zjednodušit hodnocení nejprve za předpokladu, že některé výchozí nastavení: Se používá protokol WebSocket, velikost zprávy je 2 048 bajtů, odesílá zprávu každou 1 sekundu, a je ve výchozím režimu.

Každá vrstva zahrnuje vlastní maximální příchozí a odchozí šířka pásma. Usnadnili uživateli práci není zaručeno, že jakmile příchozí nebo odchozí překračuje limit.

**Echo** poskytuje maximální šířky příchozího pásma, protože má nejnižší směrování náklady. **Vysílání** definuje maximální odchozí zprávy šířky pásma.

Proveďte **není** překročit zvýrazněné hodnoty v následujících dvou tabulkách.

|       echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení                       | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| **Vstupní šířka pásma (bajty/s)** | **2 MIN**    | **4 MIN**    | **10 MILIONŮ**   | **20 MILIONŮ**    | **40M**    | **100 MIL.**   | **200 MILIONŮ**    |
| Odchozí šířka pásma (bajty/s) | 2 MIN    | 4 MIN    | 10 MILIONŮ   | 20 MILIONŮ    | 40M    | 100 MIL.   | 200 MILIONŮ    |


|     Vysílání             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Vstupní šířka pásma (bajty/s)  | 4 KB    | 4 KB    | 4 KB     | 4 KB     | 4 KB     | 4 KB      | 4 KB     |
| **Odchozí šířka pásma (bajty/s)** | **4 MIN**    | **8 MIN**    | **20 MILIONŮ**    | **40M**    | **80M**    | **200 MILIONŮ**    | **400M**   |

Příchozí šířky pásma a šířku odchozího pásma vzorce:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

*inboundConnections*: počet připojení zasílání zpráv

*outboundConnections*: počet připojení, které přijímají zprávy

*messageSize*: velikost do jedné zprávy (průměrná hodnota). Pro malé zprávu, jejíž velikost je menší než 1024 bajtů má podobné dopad na výkon jako zpráva 1024 bajtů.

*sendInterval*: čas zasílání zpráv, obvykle je 1 sekunda na zprávu, což znamená, že odesílání zpráv za sekundu. Menší sendInterval znamená, že odesláním další zprávy dané časové období. Například 0,5 sekundu zpráva znamená, že odesílání dvě zprávy za sekundu.

*Připojení* je ASRS potvrzené maximální prahová hodnota pro každou vrstvu. Pokud číslo připojení se dál zvýší, sníží se z omezení šířky pásma připojení.

*Příchozí šířky pásma* a *šířku odchozího pásma* jsou celková velikost zpráv za sekundu. Jsem tady "znamená, že megabajtů pro zjednodušení.

#### <a name="evaluation-for-complex-use-cases"></a>Vyhodnocení pro případy použití komplexní

##### <a name="bigger-message-size-or-different-sending-rate"></a>Větší velikost zprávy nebo jinou míru odesílání

Případu skutečném použití je složitější. Může odesílat zprávy větší než 2 048 bajtů nebo rychlost odesílání zprávy není jedna zpráva za sekundu. Pojďme se na to vysílání unit100 jako příklad najít jak vyhodnotit výkon.

Následující tabulka uvádí skutečné případ **vysílání**, ale velikost, počet připojení a rychlost odesílání zprávy se liší od co jsme předpokládá, že v předchozí části. Dotaz je, jak můžeme odvodit kterékoli z těchto položek (velikost zprávy, počet připojení nebo rychlost odesílání zpráv) Pokud víme 2 z nich.

| Vysílání  | Velikost zprávy (bajty) | Příchozí (zpráv/s) | Připojení | Odeslat intervalech (sekundy) |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 K                 | 1                        | 100 000     | 5                       |
| 2 | 256 K                | 1                        | 8 000       | 5                       |

Tento vzorec je snadno odvození podle předchozí existující vzorec:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Pro unit100, víme, že maximální šířku odchozího pásma je 400M z předchozí tabulky a pak pro 20 tisíc zpráv velikost, maximální počet odchozích připojení by měl být 400M \* 5 / 20 tisíc = 100 000, která by odpovídala skutečné hodnoty.

##### <a name="mixed-use-cases"></a>Smíšené použití

Případu skutečném použití společně obvykle používá čtyři základní svědectví: **echo**, **vysílání**, **odeslat do skupiny**, nebo **odeslat připojení**. Metoda použitá k vyhodnocení kapacitu je k rozdělení smíšené použití na čtyři základní svědectví **vypočítat šířku pásma maximální příchozí a odchozí zprávy** samostatně pomocí výše uvedených vzorců a, abyste získali celkový součet Maximální příchozí/odchozí šířka pásma. Potom si vyberte si správnou vrstvu z tabulek maximální příchozí/odchozí šířka pásma.

Mezitím se stane pro odeslání zprávy na stovky nebo tisíce malé skupiny nebo dokonce tisíců klientů zasílání zpráv mezi sebou, náklady na směrování dominantní. Tento dopad vzít v úvahu. Další podrobnosti najdete v následujících částech "Případovou studii –".

Pro případ použití odeslat zprávu na klienty, ujistěte se, že je server aplikace **není** problémové místo. Část "Případovou studii –" poskytuje pokynů o tom, kolik serverů aplikace budete potřebovat a kolik připojení k serveru by měl být nakonfigurovaný.

## <a name="case-study"></a>Případová studie

V dalších částech projít čtyři typické případy použití pro přenos pomocí protokolu WebSocket: **echo**, **vysílání**, **odeslat do skupiny**, a **odeslat připojení** . Každý scénář vypíše aktuální ASRS příchozí a odchozí kapacity mezitím vysvětluje, co je hlavní faktory na výkon.

Ve výchozím režimu aplikačního serveru prostřednictvím sady SDK služby Azure SignalR ve výchozím nastavení, vytvoří pět připojení k serveru s ASRS. Výkonu testování výsledku níže, server, které připojení se zvyšují do 15 (nebo více pro vysílání a odesílání zpráv do velké skupiny).

Různé případy použití mají různé požadavky na serverech aplikace. **Vysílání** potřebuje malý počet serverů aplikací. **Echo** nebo **odeslat připojení** potřebuje mnoho serverů aplikací.

Ve všech případy použití, výchozí velikost zprávy je 2 048 bajtů a zprávu odeslat interval je 1 sekunda.

## <a name="default-mode"></a>Výchozí režim

V tomto režimu se podílejí ASRS, klientů a serverů webových aplikací. Každý klient je zkratka pro jedno připojení.

### <a name="echo"></a>echo

Za prvé připojení webových aplikací k ASRS. Za druhé počtu klientů připojení k webové aplikace, které přesměrovat klienty na ASRS s přístupovým tokenem a koncového bodu. Potom klienti musí vytvořit připojení pomocí protokolu WebSocket pomocí ASRS.

Po připojení všech klientů, začnou zasílání zpráv, který obsahuje časové razítko pro konkrétní Centrum každou sekundu. Centrum vypisuje zprávy zpět do její původní klienta. Každý klient vypočítá latence při přijetí zprávy echo zpět.

Kroky 5\~8 (červený zvýrazněný provozu) se ve smyčce, který spustí pro výchozí dobu trvání (5 minut) a získat statistiky všechny zprávy latence.
Průvodce výkonem ukazuje maximální počet připojení klientů.

![echo](./media/signalr-concept-performance/echo.png)

**Echo**od chování Určuje, že maximální šířky příchozího pásma je rovna hodnotě maximální šířky odchozího pásma. V následující tabulce.

|       echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení                       | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Příchozí/odchozí (zpráv/s) | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Příchozí/odchozí šířka pásma (bajty/s) | 2 MIN    | 4 MIN    | 10 MILIONŮ   | 20 MILIONŮ    | 40M    | 100 MIL.   | 200 MILIONŮ    |

V tomto případě se vyvolá každý klient rozbočovači definovanému v aplikačním serverem. Centrum jen volá metodu definované v původní na straně klienta. Je nejvíce světla vážená rozbočovače pro toto centrum **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

I pro toto centrum jednoduchý provoz tlak na serveru aplikace je také jako viditelného **echo** příchozí zprávy zvyšuje. Proto je vyžadováno počtu serverů aplikace pro velké SKU úrovně. V následující tabulce jsou uvedeny počtu serverů aplikace pro každou vrstvu.


|    echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Klient připojení číslo, velikost zprávy, zprávu odesílání kurz, úroveň skladové položky a aplikačního serveru procesoru nebo paměti mít dopad na celkový výkon **echo**.

### <a name="broadcast"></a>Vysílání

Pro **vysílání**, když webová aplikace obdrží zprávu, vysílá pro všechny klienty. Další klienty a všesměrového vysílání, další přenos zpráv pro všechny klienty. Podívejte se na následující diagram.

![Vysílání](./media/signalr-concept-performance/broadcast.png)

Charakteristika vysílání je, že existují malý počet klientů všesměrové vysílání, což znamená, že je malá šířka pásma příchozí zprávy, ale odchozí šířka pásma je obrovská. Šířka pásma pro odchozí zprávy se zvyšuje s rostoucím připojení klienta nebo míry vysílání zvyšuje.

Maximální počet klientských připojení, počet příchozích nebo odchozích zpráv a šířky pásma jsou shrnuty v následující tabulce.

|     Vysílání             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Příchozí (zpráv/s)  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Odchozí (zpráv/s) | 2 000 | 4,000 | 10 000 | 20,000 | 40,000 | 100 000 | 200 000 |
| Vstupní šířka pásma (bajty/s)  | 4 KB    | 4 KB    | 4 KB     | 4 KB     | 4 KB     | 4 KB      | 4 KB      |
| Odchozí šířka pásma (bajty/s) | 4 MIN    | 8 MIN    | 20 MILIONŮ    | 40M    | 80M    | 200 MILIONŮ    | 400M    |

Všesměrové vysílání klienty, kteří odesílají zprávy jsou delší než 4, vyžaduje, aby ve srovnání s menším počtem serverů aplikace **echo** vzhledem k tomu, že je malá velikost příchozí zprávy. Jsou dva servery aplikace stačí k posouzení vlastní smlouvu SLA a výkonu. Ale připojení k serveru výchozí by měl být zvýšen nevyváženou problému speciálně pro Unit50 a Unit100 předcházet.

|   Vysílání      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
>
> Výchozí připojení serveru z 5 na 40 na každém serveru aplikace, aby se zabránilo možné nevyváženou server připojení k ASRS zvýšit.
>
> Počet připojení klienta, velikost zprávy, rychlost odesílání zpráv a úroveň skladové položky mít dopad na celkový výkon pro **vysílání**

### <a name="send-to-group"></a>Odeslat do skupiny

**Odeslat do skupiny** je podobný vzorec provoz s tím rozdílem, že po navázání připojení pomocí protokolu WebSocket pomocí ASRS klientů, se musíte připojit skupiny před odesláním zprávy pro konkrétní skupinu. Tok je znázorněn v následujícím diagramu.

![Odeslat do skupiny](./media/signalr-concept-performance/sendtogroup.png)

Člen skupiny a počet skupin jsou dva faktory bez dopadu na výkon. Pro zjednodušení analýzy, nadefinujeme dva typy skupin: malé a velké objemy skupinou.

- `small group`: připojení 10 v každé skupině. Číslo skupiny je rovno (počet maximální počet připojení) / 10. Například pro 1 jednotku, pokud jsou počty 1 000 připojení, potom máme 1 000 / 10 = 100 skupiny.

- `Big group`: Číslo skupiny je vždy 10. Počet členů skupiny je rovno (počet maximální počet připojení) / 10. Například pro 1 jednotku při 1 000 připojení počty, pak každá skupina má 1 000 / 10 = 100 členy.

**Odeslat do skupiny** přináší do ASRS směrování náklady, protože musí najít cílový připojení prostřednictvím distribuované datová struktura. Odesílání připojení zvýšit, zvyšuje i náklady.

#### <a name="small-group"></a>Malá skupina

Náklady směrování je důležité pro odesílání zpráv do více skupin malé. V současné době provádění ASRS narazí směrování omezení nákladů na unit50. Přidání další procesor a paměť neobsahuje Nápověda, proto nemůže zlepšit unit100 dále podle návrhu. Pokud vyžadujete více příchozí šířky pásma, kontaktujte zákaznickou podporu pro vlastní nastavení.

|   Odeslat do malé skupiny     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20,000 | 50,000 | 100 000
| Počet členů skupiny        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Počet skupin               | 100   | 200   | 500    | 1 000  | 2 000  | 5 000  | 10 000 
| Příchozí (zpráv/s)  | 200   | 400   | 1 000  | 2,500  | 4,000  | 7,000  | 7,000   |
| Vstupní šířka pásma (bajty/s)  | 400 K  | 800 K  | 2 MIN     | 5 MIN     | 8 MIN     | 14 MILIONŮ    | 14 MILIONŮ     |
| Odchozí (zpráv/s) | 2 000 | 4,000 | 10 000 | 25,000 | 40,000 | 70,000 | 70,000  |
| Odchozí šířka pásma (bajty/s) | 4 MIN    | 8 MIN    | 20 MILIONŮ    | 50 MILIONŮ     | 80M    | 140M   | 140M    |

Existuje mnoho klientských připojení volání rozbočovače, proto je číslo aplikace server také velmi důležité pro výkon. Počet serverů navrhované aplikace je uvedené v následující tabulce.

|  Odeslat do malé skupiny   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Počet připojení klientů, velikost, rychlost odesílání zpráv, náklady na směrování, úroveň skladové položky a aplikačního serveru procesoru nebo paměti mít dopad na celkový výkon **odeslat do malé skupiny**.

#### <a name="big-group"></a>Velká skupina

Pro **send do-velké objemy skupiny**, odchozí šířka pásma se stane problémové místo před tím, směrování nákladů limit. V následující tabulce jsou uvedeny maximální šířku odchozího pásma, která je téměř stejný jako **vysílání**.

|    Odeslat do velké skupiny      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000
| Počet členů skupiny        | 100   | 200   | 500    | 1 000  | 2 000  | 5 000   | 10 000 
| Počet skupin               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Příchozí (zpráv/s)  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Vstupní šířka pásma (bajty/s)  | 80 K   | 40 K   | 40 K    | 20 K    | 40 K    | 40 K     | 40 K     |
| Odchozí (zpráv/s) | 2 000 | 4,000 | 10 000 | 20,000 | 40,000 | 100 000 | 200 000 |
| Odchozí šířka pásma (bajty/s) | 8 MIN    | 8 MIN    | 20 MILIONŮ    | 40M    | 80M    | 200 MILIONŮ    | 400M    |

Odesílání počet připojení je více než 40, zatížení na serveru aplikace je malá, proto je číslo navrhované webové aplikace také malé.

|  Odeslat do velké skupiny  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
>
> Výchozí připojení serveru z 5 na 40 na každém serveru aplikace, aby se zabránilo možné nevyváženou server připojení k ASRS zvýšit.
> 
> Počet připojení klientů, velikost, rychlost odesílání zpráv, směrování náklady a úroveň skladové položky mít dopad na celkový výkon **send do-velké objemy skupiny**.

### <a name="send-to-connection"></a>Odeslat do připojení

V tomto případě když klienti k navázání spojení s ASRS, každý klient volá speciální hub získat vlastní ID připojení. Srovnávací test výkonu zodpovídá shromáždit všechna ID připojení, je náhodné a změňte jejich přiřazení do všech klientů jako odesílající cíl. Klienti zachovat odeslání zprávy do cílové připojení až do dokončení testu výkonnosti.

![Odeslat do klienta](./media/signalr-concept-performance/sendtoclient.png)

Směrování nákladů pro **odeslat připojení** je podobné jako **odeslat do malé skupiny**.

Jak se zvyšuje počet připojení, je omezen směrování náklady na celkový výkon. 50 jednotek byl dosažen limit. V důsledku toho nelze ještě více zlepšit 100 jednotek.

V následující tabulce je souhrn statistikou po mnoho zaokrouhlí spuštění **odeslat připojení** srovnávacích testů

|   Odeslat do připojení   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Připojení                        | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000          | 100 000         |
| Příchozí / odchozí (zpráv/s) | 1 000 | 2 000 | 5 000 | 8 000  | 9,000  | 20,000 | 20,000 |
| Příchozí / odchozí šířka pásma (bajty/s) | 2 MIN    | 4 MIN    | 10 MILIONŮ   | 16 MILIONŮ    | 18 MIN    | 40M       | 40M       |

Tento případ použití vyžaduje vysokého zatížení v aplikaci na straně serveru. Zobrazit server navrhované aplikace počet v následující tabulce.

|  Odeslat do připojení  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Počet připojení klientů, velikost, rychlost odesílání zpráv, náklady na směrování, úroveň skladové položky a aplikačního serveru procesoru nebo paměti mít dopad na celkový výkon **odeslat připojení**.

### <a name="aspnet-signalr-echobroadcastsend-to-connection"></a>Funkce SignalR technologie ASP.NET echo/vysílání/odeslat k počtu připojení

ASRS poskytuje kapacitu výkonu pro funkci SignalR technologie ASP.NET. Tato část obsahuje počet navrhované web app pro funkci SignalR technologie ASP.NET **echo**, **vysílání**, a **odeslat do malé skupiny**.

Test výkonu používá webové aplikace Azure z [Standard S3 plánování služby](https://azure.microsoft.com/pricing/details/app-service/windows/) pro funkci SignalR technologie ASP.NET.

- `echo`

|   echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

- `broadcast`

|  Vysílání       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

- `Send-to-small-group`

|  Odeslat do malé skupiny     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Připojení      | 1 000 | 2 000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Počet serverů aplikace | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

## <a name="serverless-mode"></a>Režim bez serveru

Klienti a ASRS jsou zahrnuty v tomto režimu. Každý klient je zkratka pro jedno připojení. Klient odešle do jiného klienta nebo všesměrové vysílání zpráv do všech zpráv prostřednictvím rozhraní REST API.

Odesílání zpráv s vysokou hustotou prostřednictvím rozhraní REST API není tak efektivní jako objekt WebSocket, protože vyžaduje k vytvoření nového připojení HTTP pokaždé, když - speciální poplatky v režimu bez serveru.

### <a name="broadcast-through-rest-api"></a>Vysílání přes rozhraní REST API
Všichni klienti připojení pomocí protokolu WebSocket pomocí ASRS. Někteří klienti pak spusťte vysílání přes rozhraní REST API. Zpráva odesílání (příchozí) jsou to vše prostřednictvím HTTP Post, který není efektivní ve srovnání s pomocí protokolu WebSocket.

|   Vysílání přes rozhraní REST API     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Příchozí (zpráv/s)  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Odchozí (zpráv/s) | 2 000 | 4,000 | 10 000 | 20,000 | 40,000 | 100 000 | 200 000 |
| Vstupní šířka pásma (bajty/s)  | 4 KB    | 4 KB    | 4 KB     | 4 KB     | 4 KB     | 4 KB      | 4 KB      |
| Odchozí šířka pásma (bajty/s) | 4 MIN    | 8 MIN    | 20 MILIONŮ    | 40M    | 80M    | 200 MILIONŮ    | 400M    |

### <a name="send-to-user-through-rest-api"></a>Poslat uživateli prostřednictvím rozhraní REST API
Test výkonnosti přiřadí uživatelská jména na všechny klienty dříve, než začnou propojíte ASRS. Po navázání připojení pomocí protokolu WebSocket pomocí ASRS klienti začnou odesílání zpráv přes HTTP Post.

|   Poslat uživateli prostřednictvím rozhraní REST API | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Připojení               | 1 000 | 2 000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Příchozí (zpráv/s)  | 300   | 600   | 900    | 1,300  | 2 000  | 10 000  | 18,000  |
| Odchozí (zpráv/s) | 300   | 600   | 900    | 1,300  | 2 000  | 10 000  | 18,000 |
| Vstupní šířka pásma (bajty/s)  | 600 K  | 1.2 M  | 1.8 M   | 2.6 M   | 4 MIN     | 10 MILIONŮ     | 36M    |
| Odchozí šířka pásma (bajty/s) | 600 K  | 1.2 M  | 1.8 M   | 2.6 M   | 4 MIN     | 10 MILIONŮ     | 36M    |

## <a name="performance-test-environments"></a>Prostředí pro testování výkonu

Test výkonu pro všechny případy použití uvedených proběhlo v prostředí Azure. Na většině virtuálních počítačů 50 klienta a 20 aplikačního serveru používají virtuální počítače.

Velikost virtuálního počítače klienta: StandardDS2V2 (2 virtuální procesory a paměť 7G)

Aplikační server velikost virtuálního počítače: StandardF4sV2 (4 vCPU, 8G memory)

Azure SignalR SDK připojení serverů: 15

## <a name="performance-tools"></a>Nástroje pro měření výkonu

https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin

## <a name="next-steps"></a>Další postup

V tomto článku získáte přehled o výkonu služby SignalR v typické použití scénářů.

Přečtěte si víc o interní informace služby SignalR a škálování pro služby SignalR, přečtěte si následující pokyny.

* [Interní informace o službě Azure SignalR](signalr-concept-internals.md)
* [Škálování služby Azure SignalR](signalr-howto-scale-multi-instances.md)
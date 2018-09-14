---
title: Azure Relay Hybrid Connections protokol Průvodce | Dokumentace Microsoftu
description: Průvodce protokolem Azure Relay Hybrid Connections.
services: service-bus-relay
documentationcenter: na
author: clemensv
manager: timlt
editor: ''
ms.assetid: 149f980c-3702-4805-8069-5321275bc3e8
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2018
ms.author: clemensv
ms.openlocfilehash: 913e702cc72472e81937bfe3b0939695daadc011
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543506"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure Relay Hybrid Connections protokolu

Azure Relay je jedním z pilířů klíčová dovednost, pomocí platformy Azure Service Bus. Nové _Hybrid Connections_ zabezpečenou a otevřených protokolů vývoje na základě protokolu HTTP a Websocket je funkce služby Relay. Nejprve se stejným názvem, již nahrazuje _BizTalk Services_ funkce, která byla vytvořena na základě vlastnickým protokolem. Integrace hybridních připojení do služby Azure App Services bude i nadále fungovat jako-je.

Hybrid Connections umožňuje obousměrnou binární streamovou komunikaci a jednoduchého datagram tok mezi dvěma síťovými aplikacemi. Nebo obě mohou být umístěné za zařízeními NAT nebo branami firewall.

Tento článek popisuje interakce na straně klienta s relay Hybrid Connections pro připojení klientů v rolích naslouchací proces a odesílatele. Také popisuje, jak naslouchacích procesů přijímat nová připojení a požadavky.

## <a name="interaction-model"></a>Model interakce

Relay Hybrid Connections připojí obě strany tím, že poskytuje potkávací bod v cloudu Azure, můžete vyhledat a připojit z hlediska své vlastní sítě strany. Bod potkávací se nazývá "Hybridní připojení" v tomto a další dokumentace rozhraní API a taky na webu Azure portal. Koncový bod služby hybridní připojení se označuje jako "služby" pro zbývající část tohoto článku.

Služba umožňuje přenos Websocket připojení a požadavky HTTP (S) a odpovědi.

Model interakce leans na klasifikace stanovené mnoho jiná síťová rozhraní API. Je naslouchací proces, který označuje první připravenosti pro zpracování příchozích připojení a následně je přijímá při jejich doručení. Na druhé straně klient připojí k naslouchací proces, očekává se toto připojení na přijetí pro navázání obousměrné komunikace cestu. "Připojení", "Naslouchání" a "Přijmout" jsou stejných podmínek, které můžete najít ve většině soketu rozhraní API.

Všechny modely komunikace přes předávací službu má stran odchozí připojení na koncový bod služby. To usnadňuje "naslouchací proces" i "client" v obecné použití a může také způsobit dalšími přetíženími terminologie. Přesné terminologie proto použít pro hybridní připojení je následující:

Programy na obou stranách připojení, se nazývají "klienti", protože jsou klienti ve službě. Klienta, který čeká a přijímá připojení je "naslouchací proces", nebo je označen jako v "naslouchací proces roli." Klienta, který zahájí nové připojení pro naslouchací proces prostřednictvím služby se nazývá "sender", nebo je v "roli odesílatel."

### <a name="listener-interactions"></a>Naslouchací proces interakce

Naslouchací proces má pět interakce se službou; všechny podrobnosti o při přenosu jsou popsány dále v tomto článku v části odkaz.

Zprávy naslouchání, přijmout a požadavek se přijal od služby. Obnovit, a operace Ping se posílají naslouchací proces.

#### <a name="listen-message"></a>Naslouchání zpráv

K označení připravenosti s cílem služby, který naslouchací proces je připraven přijmout připojení na portu vytvoří odchozí připojení soketu WebSocket. Metoda handshake připojení se stejný název hybridního připojení nakonfigurovaná na obor názvů služby Relay a token zabezpečení, která uděluje "Naslouchání" přímo na tento název.

Pokud objekt WebSocket je změna přijata službou, registrace je dokončena a vytvořených pomocí protokolu WebSocket je zachováno jako "řídicí kanál" pro povolení všechny následné interakce. Služba umožňuje až 25 souběžných naslouchacích procesů jedno hybridní připojení. Kvóta pro AppHooks je možné určit.

Pro hybridní připojení pokud existují dvě nebo více aktivní naslouchací procesy, příchozí připojení se vyvažují mezi nimi v náhodném pořadí; spravedlivé rozdělení dojde k pokusu o nejlepší úsilí.

#### <a name="accept-message"></a>Příjem zprávy

Když odesílatele se otevře nové připojení služby, služba zvolí a upozorní jeden aktivní naslouchací procesy na hybridní připojení. Toto oznámení se posílá na naslouchací proces prostřednictvím kanálu otevřít ovládacího prvku jako zprávy JSON. Zpráva obsahuje adresu URL koncového bodu protokolu WebSocket, který naslouchací proces se musí připojit k pro přijetí připojení.

Adresa URL může a musí být přímo naslouchací proces používá bez další práce.
Kódovaný informace je platný jenom krátkou dobu, v podstatě pro dlouho odesílatele je ochotní počkat pro připojení k být zavedené začátku do konce. Maximální počet předpokládat, že je 30 sekund. Adresa URL jde použít jenom pro jeden úspěšného pokusu o připojení. Jakmile se naváže připojení soketu WebSocket pomocí adresy URL potkávací, všechny další aktivity na tento objekt WebSocket je přes předávací službu z a do odesílatele. K tomu dojde bez zásahu nebo interpretaci službou.

### <a name="request-message"></a>Zpráva požadavku

Kromě připojení pomocí protokolu WebSocket naslouchací proces může také přijímat snímky požadavku HTTP od odesílatele, pokud tato možnost není explicitně povoleno na hybridní připojení.

MUSÍ zpracovat naslouchacích procesů, připojené k hybridní připojení s podporou protokolu HTTP `request` gest. Naslouchací proces, že nezpracuje `request` a proto způsobí, že opakované vypršení časového limitu při připojení může být na seznamu zakázaných adres službou v budoucnu.

HTTP rámce hlavičku metadat je přeložen do formátu JSON pro jednodušší zpracování rozhraním naslouchací proces, také protože analýzy knihovny hlavičky protokolu HTTP jsou vzácnějších než analyzátory JSON. HTTP metadata, která je relevantní pro daný vztah mezi odesílatelem a brány přenosového protokolu HTTP, včetně informací o autorizaci, není předají. Těla požadavku HTTP se transparentně přenáší jako binární rámce protokolu WebSocket.

Naslouchací proces může reagovat na požadavky HTTP pomocí gesta ekvivalentní odpovědi.

Toku žádostí a odpovědí ve výchozím nastavení používá řídicí kanál, ale je možné "upgradovat" na různých potkávací objektu websocket na straně pokaždé, když se vyžaduje. DISTINCT připojení pomocí protokolu WebSocket zlepšit propustnost pro každého klienta konverzace, ale jejich ovlivnit naslouchací proces s další připojení, které je potřeba zpracovat, což ale nemusí představovat touhy možnost zjednodušené klientů.

Na řídicím kanálu jsou omezeny na nejvýše 64 kB velikost těla požadavku a odpovědi. Metadata hlavičky protokolu HTTP je omezený na celkem 32 kB. Pokud požadavek nebo odpověď překročí tuto prahovou hodnotu, naslouchací proces musí upgradovat na potkávací pomocí gesto ekvivalentní zpracování objektu WebSocket [přijmout](#accept-message).

Pro žádosti služba rozhodne, zda směrovat požadavky přes řídicí kanál. To zahrnuje, ale nemusí být omezeny na případy, pokud žádost o přesáhne 64 kB (záhlaví a text) přímý, nebo pokud je žádost odeslána s ["rozdělený do bloků dat" transfer-encoding](https://tools.ietf.org/html/rfc7230#section-4.1) a že služba má důvod očekávat pro žádost o být delší než 64 kB nebo čtení požadavku neproběhne. Pokud služba zvolí se dodat žádost přes potkávací, pouze se předá potkávací adresu pro naslouchací proces.
Naslouchací proces potom musí vytvořit potkávací objektu websocket na straně a službu bezodkladně poskytuje úplné požadavek, včetně těla přes potkávací protokolu WebSocket. Odpověď, musíte taky použít potkávací protokolu WebSocket.

Pro požadavky přicházející přes řídicí kanál naslouchací proces rozhodne, jestli se má odpovědět přes řídicí kanál nebo prostřednictvím potkávací. Služba musí obsahovat potkávací adresa při každé žádosti směrovat přes řídicí kanál. Tato adresa je platná pouze pro upgrade z aktuální žádosti.

Pokud se rozhodne upgradovat naslouchací proces, připojí a o tom bezodkladně informuje přes soket zavedené potkávací poskytuje odpověď.

Po setkání, na které se vytvořilo pomocí protokolu WebSocket, naslouchací proces vhodné ponechat ji pro další zpracování požadavků a odpovědí ze stejného klienta. Služba bude udržovat protokolu WebSocket pro za předpokladu, připojení se odesílatel soketu HTTPS uchovává a všechny následné žádosti z tohoto odesílatele bude směrovat přes protokol WebSocket zachována. Pokud vypustí potkávací objektu WebSocket z jeho vedlejší vybere možnost naslouchací proces, služba také vyřadí připojení odesílateli, bez ohledu na to, zda další požadavek už může být v průběhu.

#### <a name="renew-operation"></a>Operace prodloužení platnosti provedla

Token zabezpečení, který se použije k registraci naslouchací proces a udržovat řídicí kanál může vypršet jejich platnost, když je aktivní naslouchací proces. Vypršení platnosti tokenu nemá vliv na probíhající připojení, ale způsobit řídicí kanál přeruší služba na nebo krátce po chvíli vypršení platnosti. Operace "obnovit" je zpráva JSON, který naslouchací proces můžete odesílat nahradit token přidružené řídicí kanál tak, aby mohla být udržována řídicí kanál delší dobu.

#### <a name="ping-operation"></a>Operace ping

Je-li řídicí kanál nečinnosti delší dobu, zprostředkovatelů na cestě, jako je zatížení nástroje pro vyrovnávání nebo zařízení NAT může dojít k přerušení připojení TCP. "Příkaz ping" operace, která předchází odesláním malé množství dat na kanál, který připomíná všem uživatelům v síti směrovaly, který je určen připojení jako aktivní, a slouží také jako "živé" test pro naslouchací proces. Pokud příkaz ping selže, řídicí kanál by měl být nepoužitelné a naslouchací proces měli znovu připojit.

### <a name="sender-interaction"></a>Odesílatel interakce

Odesílatel má dvě interakce se službou: připojí webové sokety nebo odešle požadavky prostřednictvím protokolu HTTPS. Požadavky nelze odeslat přes webové sokety z role odesílatele.

#### <a name="connect-operation"></a>Operace Connect

Operace "připojení" otevře objektu WebSocket na službě, že zadáte název hybridního připojení a (volitelně, ale vyžaduje ve výchozím nastavení) token zabezpečení jejichž základě lze oprávnění "Odeslat" v řetězci dotazu. Služba potom komunikuje naslouchacího procesu tak, jak je popsáno výše a naslouchací proces vytvoří potkávací připojení, který je připojen pomocí tohoto protokolu WebSocket. Po přijetí objekt WebSocket, jsou všechny další interakce na tomto objektu websocket na straně připojený naslouchací proces.

#### <a name="request-operation"></a>Operace požadavku

Odesílatel pro hybridní připojení, pro kterou se povolila funkce, můžete odeslat do značné míry neomezený požadavky HTTP naslouchacích procesů.

S výjimkou Relay přístupový token, který je že buď vložené v řetězci dotazu nebo v HTTP hlavičce žádosti Relay je zcela transparentní, a všechny operace HTTP s propojovací adrese, všechny přípony adresního řádku Relay opuštění naslouchací proces plně pod kontrolou en povolení d až do konce a dokonce i rozšíření funkce protokolu HTTP, jako je [CORS](https://www.w3.org/TR/cors/).

Odesílatel autorizaci ke koncovému bodu Relay je ve výchozím nastavení zapnutá, ale je volitelné. Vlastník hybridní připojení můžete zvolit, aby anonymních uživatelů. Služba zachytí, zkontrolovat a odstranit autorizačních informací následujícím způsobem:

1. Pokud řetězec dotazu obsahuje `sb-hc-token` výraz, výraz bude vždy odebrána z řetězce dotazu. Budou vyhodnoceny, pokud předávací ověřování je vypnuté.
2. Pokud obsahovat hlavičky požadavku `ServiceBusAuthorization` záhlaví a záhlaví výraz bude vždy odebrána z kolekce hlaviček.
   Budou vyhodnoceny, pokud předávací ověřování je vypnuté.
3. Pouze v případě, že předávací ověřování je vypnuté a pokud obsahovat hlavičky žádosti `Authorization` záhlaví a ani jeden z předchozích výrazů je k dispozici, záhlaví, bude vyhodnocen a odebrána. V opačném případě `Authorization`je vždy předána jako-je.

Pokud není žádná aktivní naslouchací proces, služba vrátí 502 kód chyby "Chybná brána". Pokud služba není pro zpracování požadavku, služba vrátí 504 "vypršel časový limit brány" za 60 sekund.

### <a name="interaction-summary"></a>Souhrn interakce

Výsledkem tohoto modelu interakce je, že klient odesílatele vzejde z metody handshake s "Vyčištění" WebSocket, která je připojená k naslouchací proces a, který potřebuje žádné další preambles nebo přípravu. Tento model umožňuje prakticky jakékoli existující implementace klienta WebSocket snadno využívat výhod hybridních připojení služby zadáním správně vytvořený adresu URL do jejich vrstvy objektu websocket na straně klienta.

Připojení potkávací WebSocket, která získá naslouchací proces prostřednictvím přijmout interakce je také vyčistit a je možné předat do jakékoli existující implementace objektu websocket na straně serveru s některé minimální navíc abstrakce, která rozlišuje mezi operacemi "přijmout" na naslouchací procesy místní sítě a Hybrid Connections vzdálené jejich framework "přijmout" operace.

Model požadavků/odpovědí HTTP poskytuje odesílatele z velké části neomezený HTTP protokol plochy s vrstvu volitelné autorizace. Naslouchací proces získá předem analyzovaný oddíl hlavičky žádosti HTTP, který může být převeden zpět podřízený požadavek HTTP nebo nakládá jako s použitím binární snímků provádí těla protokolu HTTP. Odpovědi používají stejný formát. Interakce s méně než 64 KB těla požadavku a odpovědi může být zpracována prostřednictvím jedné webové sokety, jež jsou sdílena u všech uživatelů. Větší požadavky a odpovědi můžete zpracovat pomocí potkávací modelu.

## <a name="protocol-reference"></a>Referenční informace o protokolu

Tato část popisuje podrobnosti protokolu interakce je popsáno výše.

Všechna připojení pomocí protokolu WebSocket probíhají na portu 443 jako upgrade z verze 1.1 HTTPS, které jsou obvykle vyjádřeny některé objektu websocket na straně framework nebo rozhraní API. Popis tady, zůstane implementace neutrální, bez návrhy konkrétní verzi rozhraní framework.

### <a name="listener-protocol"></a>Naslouchací proces protokolu

Protokol naslouchacího procesu se skládá z dvou gesta připojení a tři operace zpráv.

#### <a name="listener-control-channel-connection"></a>Připojení kanálu naslouchacího procesu správy

S vytvářením připojení soketu websocket bylo k otevření kanálu ovládacího prvku:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

`namespace-address` Je plně kvalifikovaný název oboru názvů Azure Relay, který je hostitelem hybridní připojení, obvykle ve formátu `{myname}.servicebus.windows.net`.

Možnosti parametr řetězce dotazu se takto.

| Parametr        | Požaduje se | Popis
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Ano      | Pro roli naslouchacího procesu musí být parametr **sb. hybridní připojení akce = naslouchání**
| `{path}`         | Ano      | Cesta kódovaná adresou URL oboru názvů předem nakonfigurované hybridní připojení se chcete zaregistrovat tímto naslouchacím procesem. Tento výraz je přidán do pevné `$hc/` část cesty.
| `sb-hc-token`    | Ano\*    | Naslouchací proces musíte zadat platný, kódovaná adresou URL služby Service Bus sdílí přístup Token pro obor názvů nebo hybridní připojení, která uděluje **naslouchání** vpravo.
| `sb-hc-id`       | Ne       | Toto volitelné ID klientem poskytnutý povolí diagnostické trasování začátku do konce.

Pokud připojení soketu WebSocket se nezdaří z důvodu hybridní připojení. cesta není zaregistrované, nebo token neplatný nebo chybějící nebo některé jiné chyby, poskytnutí zpětné vazby chyby pomocí regulárních zpětnou vazbu modelu stavu protokolu HTTP 1.1. Popis stavu, který obsahuje chyby sledování id, které lze komunikovat na pracovníky podpory Azure:

| Kód | Chyba          | Popis
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nenalezené      | Hybridní připojení. cesta je neplatná nebo základní adresa URL má špatný formát.
| 401  | Neautorizováno   | Token zabezpečení je chybějící nebo poškozené nebo neplatné.
| 403  | Zakázáno      | Token zabezpečení není platný pro tuto cestu pro tuto akci.
| 500  | Vnitřní chyba | Došlo k chybě služby.

Pokud připojení soketu WebSocket se záměrně vypne službou po byl zpočátku nastaven nahoru důvod díky tomu se předávají pomocí odpovídající kód chyby protokolu WebSocket spolu s chybovou zprávu s popisem, která zahrnuje i ID sledování Služba nebude vypnout řídicí kanál, aniž se objeví chybová podmínka. Žádné čistého vypnutí je klient řídit.

| Stav WS | Popis
| --------- | -------------------------------------------------------------------------------
| 1001      | Byla odstraněna nebo zakázáno cesty hybridní připojení.
| 1008      | Vypršela platnost tokenu zabezpečení, tedy porušení zásad autorizace.
| 1011      | Došlo k chybě služby.

#### <a name="accept-handshake"></a>Přijměte handshake

Oznámení "přijmout" je odeslaných službou k naslouchacímu procesu přes kanál dříve navázaném ovládací prvek jako zprávy JSON v rámci textového protokolu WebSocket. Neexistuje žádná odpověď na tuto zprávu.

Zpráva obsahuje objekt JSON s názvem "přijímat", který definuje následující vlastnosti v tuto chvíli:

* **Adresa** – řetězec adresy URL se použije k vytvoření objektu WebSocket na službu tak, aby přijímal příchozí připojení.
* **ID** – jedinečný identifikátor pro toto připojení. Pokud byl klient sender ID, jedná se o odesílateli zadat hodnotu, jinak je generována hodnota.
* **connectHeaders** – všechny hlavičky protokolu HTTP, které byly dodány do koncového bodu propojení odesílatel, který taky obsahuje protokol WebSocket sekundu a sekundu. pomocí protokolu WebSocket rozšíření záhlaví.

```json
{
    "accept" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?..."
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",
        "connectHeaders" : {
            "Host" : "...",
            "Sec-WebSocket-Protocol" : "...",
            "Sec-WebSocket-Extensions" : "..."
        }
     }
}
```

Adresa URL zadat do zprávy JSON se naslouchací proces používá k navázání protokolu WebSocket pro přijetí nebo odmítnutí soketu odesílatele.

##### <a name="accepting-the-socket"></a>Přijímá soketu.

Souhlasíte, vytvoří naslouchací proces připojení soketu WebSocket na zadané adresy.

Pokud zpráva "přijmout" představuje `Sec-WebSocket-Protocol` záhlaví, očekává se, že naslouchací proces pouze přijímá objekt WebSocket, pokud podporuje tento protokol. Kromě toho nastaví záhlaví, jak pokládáme stav, objekt WebSocket.

Totéž platí i pro `Sec-WebSocket-Extensions` záhlaví. Pokud rozhraní framework podporuje rozšíření, ho měli nastavit hlavičku odpovědi na straně serveru z vyžadovaných `Sec-WebSocket-Extensions` handshake pro rozšíření.

Adresa URL musí používat jako-je pro vytvoření soketu přijmout, ale obsahuje následující parametry:

| Parametr      | Požaduje se | Popis
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ano      | Pro přijetí soketu, musí být parametr `sb-hc-action=accept`
| `{path}`       | Ano      | (viz následující odstavec)
| `sb-hc-id`     | Ne       | Viz popis v předchozích **id**.

`{path}` je cesta kódovaná adresou URL oboru názvů předkonfigurované hybridní připojení, na kterém se má zaregistrovat tímto naslouchacím procesem. Tento výraz je přidán do pevné `$hc/` část cesty.

`path` Výrazu může být rozšířena s příponou a výraz řetězce dotazu, který následuje po dělicí lomítkem registrovaný název.
To umožňuje klientovi odesílatele předání argumentů odeslání přijímá naslouchací proces, pokud není možné zahrnout hlavičky protokolu HTTP. Předpokladem je, že rozhraní naslouchací proces analyzuje část pevné cesty a názvu registrované z cesty a díky zbývající, pravděpodobně bez argumentů řetězce dotazu předchází `sb-`, v aplikaci pro rozhodování o tom, k dispozici Určuje, zda přijímají připojení.

Další informace najdete v tématu v následující části "Odesílatele protokol".

Pokud dojde k chybě, můžete službu odpověď následujícím způsobem:

| Kód | Chyba          | Popis
| ---- | -------------- | -----------------------------------
| 403  | Zakázáno      | Adresa URL není platná.
| 500  | Vnitřní chyba | Došlo k chybě ve službě

 Po navázání připojení serveru protokol WebSocket vypne, pokud odesílatel protokolu WebSocket vypne, nebo s následujícím stavem:

| Stav WS | Popis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Odesílatel klient ukončí připojení.                                    |
| 1001      | Byla odstraněna nebo zakázáno cesty hybridní připojení.                        |
| 1008      | Vypršela platnost tokenu zabezpečení, tedy porušení zásad autorizace. |
| 1011      | Došlo k chybě služby.                                            |

##### <a name="rejecting-the-socket"></a>Odmítnutí soketu.

 Odmítnutí soketu až po kontrole `accept` zprávy vyžaduje podobné metody handshake tak, aby kód stavu a popis stavu komunikaci důvod pro odmítnutí čárách zpět do odesílatele.

 Protokol návrhu zde je používat metodu handshake protokolu WebSocket, (, který je navržený tak, že končí definované chybový stav) tak, aby implementace klienta naslouchacího procesu můžete dál přináší setrvávání u objektu websocket na straně klienta a není nutné použít speciální, úplné klienta HTTP.

 Pokud chcete odmítnout soketu, klient přebírá adresu URI z `accept` zprávu a připojí dva parametry řetězce dotazu, následujícím způsobem:

| Param                   | Požaduje se | Popis                              |
| ----------------------- | -------- | ---------------------------------------- |
| SB. hybridní připojení statusCode        | Ano      | Číselný kód stavu protokolu HTTP.                |
| SB. hybridní připojení statusDescription | Ano      | Lidské čitelné důvod pro odmítnutí. |

Výsledný identifikátoru URI se pak použije k navázání připojení soketu WebSocket.

Při dokončování správně, se nezdaří s kódem chyby HTTP 410, záměrně této metody handshake od žádné protokolu WebSocket. Pokud dojde k nějaké chybě, následující kódy popisující chybu:

| Kód | Chyba          | Popis                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Zakázáno      | Adresa URL není platná.                |
| 500  | Vnitřní chyba | Došlo k chybě služby. |

#### <a name="request-message"></a>Zpráva požadavku

`request` Zpráva je odeslána služby k naslouchacímu procesu přes řídicí kanál. Stejná zpráva se také odesílají prostřednictvím protokolu WebSocket po setkání.

`request` Se skládá ze dvou částí: snímků záhlaví a binární tělo.
Pokud neexistuje žádný text, jsou vynechány snímků textu. Ukazatel Určuje, zda je k dispozici text je datový typ boolean `body` vlastnosti ve zprávě požadavku.

Pro požadavek s hlavní část žádosti struktura může vypadat takto:

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : true,
        ...
    }
}
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame -FIN
FEFEFEFEFEFEFEFEFEFEF...
----------------------------------
```

Naslouchací proces musí umět zpracovat příjem rozdělit mezi několik snímků binární tělo požadavku (viz [fragmenty objektu websocket na straně](https://tools.ietf.org/html/rfc6455#section-5.4)).
Požadavek končí, když byla přijata binární snímek nastaven příznak dokončení.

Pro žádost o bez těla je pouze jeden snímek text.

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : false,
        ...
    }
}
----------------------------------
```

Obsah JSON pro `request` vypadá takto:

* **Adresa** -řetězec identifikátoru URI. Toto je potkávací adresa pro tento požadavek. Pokud příchozí požadavek je větší než 64 kB, zbytek této zprávy je prázdné a klient musí inicializovat metodou handshake potkávací, která je ekvivalentní `accept` operace popsané níže. Služba se potom se spojí kompletní `request` na zavedených websocket. Pokud očekávat odpovědi může být delší než 64 kB, naslouchací proces musí také zahájit potkávací handshake a pak přesouvat si odpovědi prostřednictvím zavedených websocket.
* **ID** – řetězec. Jedinečný identifikátor pro tento požadavek.
* **requestHeaders** – tento objekt obsahuje všechny hlavičky protokolu HTTP, které byly dodány ke koncovému bodu odesílatel, s výjimkou autorizačních informací jak je vysvětleno [nad](#request-operation), tak i hlaviček, které se týkají výhradně připojení s bránou. Konkrétně všechny hlavičky definované nebo rezervováno [RFC7230](https://tools.ietf.org/html/rfc7230), s výjimkou `Via`, jsou odebrána a není předané:

  * `Connection` (RFC7230 části 6.1)
  * `Content-Length`  (RFC7230 části 3.3.2)
  * `Host`  (RFC7230 oddíl 5.4)
  * `TE`  (RFC7230 části 4.3)
  * `Trailer`  (RFC7230 4.4 část)
  * `Transfer-Encoding`  (RFC7230 sekci 3.3.1)
  * `Upgrade` (RFC7230 části 6.7)
  * `Close`  (RFC7230 části 8.1)

* **requestTarget** – řetězec. Tato vlastnost obsahuje ["Cíl žádostí o" (RFC7230, část 5.3)](https://tools.ietf.org/html/rfc7230#section-5.3) požadavku. Jedná se o část řetězce dotazu, který je odebrána všech `sb-hc-` předponou parametry.
* **Metoda** -řetězec. Toto je metoda požadavku, za [RFC7231, část 4](https://tools.ietf.org/html/rfc7231#section-4). `CONNECT` Metodu není možné použít.
* **tělo** – logická. Určuje, zda má jeden nebo více snímků binární tělo dodržovat.

``` JSON
{
    "request" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?...",
        "id" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "requestTarget" : "/abc/def?myarg=value&otherarg=...",
        "method" : "GET",
        "requestHeaders" : {
            "Host" : "...",
            "Content-Type" : "...",
            "User-Agent" : "..."
        },
        "body" : true
     }
}
```

##### <a name="responding-to-requests"></a>Reagování na žádosti

Příjemce musí odpovědět. Opakované reagovat na požadavky při zachování připojení, může dojít v naslouchacím procesu načítání seznamu zakázaných adres.

Odpovědi mohou být odeslány v libovolném pořadí, ale každý požadavek musí být přidal odpověď na 60 sekund nebo doručování se ohlásí jako nevyhovující. Konečný termín 60 sekund se počítá až `response` rámce byla přijata službou. Probíhající odpověď s několika snímky binary nelze nečinné po dobu více než 60 sekund nebo je ukončen.

Pokud byl přijat požadavek přes řídicí kanál odpovědi musí buď odesílají na řídicím kanálu z kde byla přijata žádost nebo musí být odeslána přes kanál potkávací.

Odpověď je objekt JSON s názvem "odpověď". Pravidla pro zpracování obsah textu se stejně jako `request` zpráv a na základě `body` vlastnost.

* **ID žádosti** – řetězec. POVINNÉ. `id` Hodnotu vlastnosti `request` odpověděla zprávy.
* **statusCode** – číslo. POVINNÉ. číselné stavový kód HTTP, která určuje výsledek oznámení. Všechny kódy stavu [RFC7231, část 6](https://tools.ietf.org/html/rfc7231#section-6) jsou povolené, s výjimkou [502 "Chybná brána"](https://tools.ietf.org/html/rfc7231#section-6.6.3) a [504 "vypršel časový limit brány"](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **statusDescription** -řetězec. VOLITELNÉ. Fráze důvodu stavový kód protokolu HTTP na [RFC7230, část 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** – hlavičky protokolu HTTP v případě externí odpovědi HTTP.
  Stejně jako u `request`, RFC7230 definovaných záhlaví musí být nepoužívá.
* **tělo** – logická. Určuje, zda binární tělo Screen follow(s).

``` text
----- Web Socket text frame ----
{
    "response" : {
        "requestId" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "statusCode" : "200",
        "responseHeaders" : {
            "Content-Type" : "application/json",
            "Content-Encoding" : "gzip"
        }
         "body" : true
     }
}
----- Web Socket binary frame -FIN
{ "hey" : "mydata" }
----------------------------------
```

##### <a name="responding-via-rendezvous"></a>Prostřednictvím potkávací reagovat

Pro odpovědi, které překračují 64 kB musí doručit přes soket potkávací odpovědi. Také v případě, že žádost přesáhne 64 kB a `request` obsahuje pouze pole Adresa soket potkávací musí vytvořit získat `request`. Po vytvoření soketu potkávací odpovědi na příslušném klientovi a následné žádosti z příslušného klienta, musí doručit přes soket potkávací, zatímco přenese.

`address` Adresu URL `request` musí být použita jako-je navázání potkávací soketu, ale obsahuje následující parametry:

| Parametr      | Požaduje se | Popis
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ano      | Pro přijetí soketu, musí být parametr `sb-hc-action=request`

Pokud dojde k chybě, můžete službu odpověď následujícím způsobem:

| Kód | Chyba           | Popis
| ---- | --------------- | -----------------------------------
| 400  | Neplatný požadavek | Nerozpoznaný akce nebo adresa URL není platná.
| 403  | Zakázáno       | Adresa URL vypršela platnost.
| 500  | Vnitřní chyba  | Došlo k chybě ve službě

 Po navázání připojení k vypnutí serveru protokol WebSocket při vypnutí klienta HTTP soketu, nebo s následujícím stavem:

| Stav WS | Popis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Odesílatel klient ukončí připojení.                                    |
| 1001      | Byla odstraněna nebo zakázáno cesty hybridní připojení.                        |
| 1008      | Vypršela platnost tokenu zabezpečení, tedy porušení zásad autorizace. |
| 1011      | Došlo k chybě služby.                                            |


#### <a name="listener-token-renewal"></a>Naslouchací proces obnovení tokenu

Token naslouchací proces, který se chystáte datum vypršení platnosti, ho můžete nahradit odesláním rámce textovou zprávu do služby prostřednictvím zavedených řídicí kanál. Zpráva obsahuje objekt JSON s názvem `renewToken`, která v tuto chvíli definuje následující vlastnost:

* **token** – token platný, kódovaná adresou URL služby Service Bus sdílený přístup pro obor názvů nebo hybridní připojení, která uděluje **naslouchání** vpravo.

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Pokud selže ověření tokenu byl odepřen přístup a cloudové službě zavře řídicí kanál protokolu WebSocket s chybou. V opačném případě nepřijde žádná odpověď.

| Stav WS | Popis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Vypršela platnost tokenu zabezpečení, tedy porušení zásad autorizace. |

### <a name="web-socket-connect-protocol"></a>Připojení protokolu Websocket

Protokol odesílatele je efektivně stejný jako způsob, jakým se naváže naslouchací proces.
Cílem je maximální transparentnost začátku do konce protokolu websocket. Adresu pro připojení k je stejná jako naslouchací proces, ale "action" se liší a token, který potřebuje různé oprávnění:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

_Obor názvů adresy_ je plně kvalifikovaný název oboru názvů Azure Relay, který je hostitelem hybridní připojení, obvykle ve formátu `{myname}.servicebus.windows.net`.

Žádost může obsahovat libovolné další hlavičky protokolu HTTP, včetně těch definovaného aplikací. Všechny zadané hlavičky toku k naslouchacímu procesu a můžete najít na `connectHeader` objektu **přijmout** ovládacího prvku zprávy.

Možnosti parametr řetězce dotazu se takto:

| Param          | Povinné? | Popis
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Ano       | Pro roli odesílatele parametr musí být `sb-hc-action=connect`.
| `{path}`       | Ano       | (viz následující odstavec)
| `sb-hc-token`  | Ano\*     | Naslouchací proces musíte zadat platný, kódovaná adresou URL služby Service Bus sdílí přístup Token pro obor názvů nebo hybridní připojení, která uděluje **odeslat** vpravo.
| `sb-hc-id`     | Ne        | Volitelné ID, které povolí diagnostické trasování začátku do konce a je k dispozici pro naslouchací proces při vyjednávání metodou handshake přijmout.

 `{path}` Je cesta k oboru názvů kódovaná adresou URL předkonfigurované hybridní připojení, na kterém se má zaregistrovat tímto naslouchacím procesem. `path` Výrazu je možné rozšířit pomocí příponu a řetězcového výrazu dotazu pro další komunikaci. Pokud hybridní připojení je registrován v cestě `hyco`, `path` výraz může být `hyco/suffix?param=value&...` následovaný parametrů řetězce dotazu jsou zde definovány. Dokončení výrazu může být potom následujícím způsobem:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

`path` Výraz se předává k naslouchacímu procesu v adrese obsažené ve zprávě nástroje řízení "přijmout" identifikátor URI.

Pokud připojení soketu WebSocket se nezdaří z důvodu hybridní připojení. cesta není zaregistrovaný, neplatné nebo chybějící token nebo některé jiné chyby, poskytnutí zpětné vazby chyby pomocí regulárních zpětnou vazbu modelu stavu protokolu HTTP 1.1. Popis stavu, který obsahuje chybu sledování ID, které lze komunikovat na pracovníky podpory Azure:

| Kód | Chyba          | Popis
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nenalezené      | Hybridní připojení. cesta je neplatná nebo základní adresa URL má špatný formát.
| 401  | Neautorizováno   | Token zabezpečení je chybějící nebo poškozené nebo neplatné.
| 403  | Zakázáno      | Token zabezpečení není platná pro tuto cestu a pro tuto akci.
| 500  | Vnitřní chyba | Došlo k chybě služby.

Pokud připojení soketu WebSocket se záměrně vypne službou po jeho je zpočátku nastavený, důvod díky tomu se předávají pomocí odpovídající kód chyby protokolu WebSocket spolu s chybovou zprávu s popisem, která zahrnuje i ID sledování .

| Stav WS | Popis
| --------- | ------------------------------------------------------------------------------- 
| 1000      | Naslouchací proces vypnutí soketu.
| 1001      | Byla odstraněna nebo zakázáno cesty hybridní připojení.
| 1008      | Vypršela platnost tokenu zabezpečení, tedy porušení zásad autorizace.
| 1011      | Došlo k chybě služby.

### <a name="http-request-protocol"></a>Požadavek protokolu HTTP

Požadavek protokolu HTTP umožňuje libovolné požadavky HTTP, s výjimkou protokolu upgradu.
Požadavky HTTP jsou odkazovala na adrese regulární runtime entity, bez vpony $hc, který se používá pro hybridní připojení klientů pomocí protokolu WebSocket.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

_Obor názvů adresy_ je plně kvalifikovaný název oboru názvů Azure Relay, který je hostitelem hybridní připojení, obvykle ve formátu `{myname}.servicebus.windows.net`.

Žádost může obsahovat libovolné další hlavičky protokolu HTTP, včetně těch definovaného aplikací. Všechny zadané hlavičky, s výjimkou těch přímo definovat v RFC7230 (naleznete v tématu [zprávy s požadavkem](#Request message)) směrovat do naslouchací proces a nachází se na `requestHeader` objekt **požadavek** zprávy.

Možnosti parametr řetězce dotazu se takto:

| Param          | Povinné? | Popis
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Ano\*     | Naslouchací proces musíte zadat platný, kódovaná adresou URL služby Service Bus sdílí přístup Token pro obor názvů nebo hybridní připojení, která uděluje **odeslat** vpravo.

Token se také dá provést buď `ServiceBusAuthorization` nebo `Authorization` hlavičky protokolu HTTP. Token, který lze vynechat, pokud hybridní připojení je nakonfigurovaný tak, aby povolovala anonymních požadavků.

Protože služba efektivně funguje jako proxy server, i v případě, nikoli jako true proxy server HTTP, buď přidá `Via` záhlaví nebo označí existující `Via` záhlaví kompatibilní s [RFC7230, část 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
Služba přidá název hostitele obor názvů Relay k `Via`.

| Kód | Zpráva  | Popis                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | Žádost byla zpracována alespoň jeden naslouchací proces.  |
| 202  | Přijato | Žádost byla přijata alespoň jeden naslouchací proces. |

Pokud dojde k chybě, můžete službu odpovědět následujícím způsobem. Určuje, zda pochází odpověď ze služby nebo naslouchací proces lze identifikovat přes přítomnost `Via` záhlaví. Pokud se nachází záhlaví, je odpověď z naslouchacího procesu.

| Kód | Chyba           | Popis
| ---- | --------------- |--------- |
| 404  | Nenalezené       | Hybridní připojení. cesta je neplatná nebo základní adresa URL má špatný formát.
| 401  | Neautorizováno    | Token zabezpečení je chybějící nebo poškozené nebo neplatné.
| 403  | Zakázáno       | Token zabezpečení není platná pro tuto cestu a pro tuto akci.
| 500  | Vnitřní chyba  | Došlo k chybě služby.
| 503  | Chybná brána     | Požadavek nešlo směrován na jakékoli naslouchacího procesu.
| 504  | Časový limit brány | Žádost byla směrována do naslouchacího procesu, ale naslouchací proces neuznal příjmu v požadovaném čase.

## <a name="next-steps"></a>Další postup

* [Přenos – nejčastější dotazy](relay-faq.md)
* [Vytvoření oboru názvů](relay-create-namespace-portal.md)
* [Začínáme s .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Začínáme s aplikací Node](relay-hybrid-connections-node-get-started.md)

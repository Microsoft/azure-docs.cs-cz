---
title: Průvodce protokolem Hybrid Connections Azure Relay | Microsoft Docs
description: Tento článek popisuje interakce na straně klienta s Hybrid Connections Relay pro připojení klientů v rolích naslouchacího procesu a odesilatele.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 36321f88de173a37c9aa6615c4c0f2b29aec9f20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97976958"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Protokol Azure Relay Hybrid Connections

Azure Relay je jedním z klíčových pilířů Azure Service Bus platformy. Novou funkcí _Hybrid Connections_ relay je zabezpečený vývoj v otevřeném protokolu založený na http a WebSockets. Nahrazuje předchozí, stejně pojmenovanou _BizTalk Services_ funkci, která byla postavená na speciálním základu protokolu. Integrace Hybrid Connections do Azure App Services bude i nadále fungovat tak, jak je.

Hybrid Connections umožňuje obousměrnou komunikaci, požadavek-odpověď a binární komunikaci s datovým proudem a mezi dvěma síťovými aplikacemi tok jednoduchého datagramu. Obě strany můžou být za zařízením NAT nebo branami firewall.

Tento článek popisuje interakce na straně klienta s Hybrid Connections Relay pro připojení klientů v rolích naslouchacího procesu a odesilatele. Také popisuje, jak naslouchací procesy přijímají nová připojení a požadavky.

## <a name="interaction-model"></a>Model interakce

Hybrid Connections Relay spojuje dvě strany tím, že poskytuje v cloudu Azure bod Rendezvous, který můžou strany zjišťovat a připojovat z pohledu vlastní sítě. Bod Rendezvous se v této a další dokumentaci, v rozhraních API a také v Azure Portal nazývá "hybridní připojení". Koncový bod služby Hybrid Connections se označuje jako služba pro zbývající část tohoto článku.

Služba umožňuje přenášet připojení webového soketu a žádosti a odpovědi HTTP (S).

Model interakce se váže na názvosloví stanovené mnoha dalšími síťovými rozhraními API. K dispozici je naslouchací proces, který nejprve indikuje připravenost pro zpracování příchozích připojení a následně je přijímá při jejich doručení. Na druhé straně se klient připojí ke službě Listener a očekává, že připojení bude přijato pro vytvoření obousměrné komunikační cesty. Podmínky "připojit", "naslouchání" a "přijmout" jsou stejné, jako ty, které najdete ve většině rozhraní API soketu.

Libovolný přenosový model přenosu má jednu ze strany, která provádí odchozí připojení ke koncovému bodu služby. Tím "naslouchací proces" také "klient" v Colloquial použít a může také způsobit další přetížení terminologie. Přesná terminologie, která se proto používá pro Hybrid Connections, je následující:

Programy na obou stranách připojení se nazývají "klienti", protože se jedná o klienty služby. Klient, který čeká na a akceptuje připojení, je "naslouchací proces", nebo se označuje jako "role naslouchacího procesu". Klient, který iniciuje nové připojení ke službě Listener prostřednictvím služby, se nazývá "odesilatel", nebo se nachází v roli "odesilatel".

### <a name="listener-interactions"></a>Interakce naslouchacího procesu

Naslouchací proces má pro službu pět interakcí. všechny podrobnosti o drátech jsou popsány dále v tomto článku v referenční části.

Zprávy o naslouchání, přijetí a požadavku jsou přijímány od služby. Naslouchací proces odesílá operace obnovení a příkazového testu.

#### <a name="listen-message"></a>Zpráva o naslouchání

Pro indikaci připravenosti na službu, že naslouchací proces je připravený k přijímání připojení, vytvoří odchozí připojení protokolu WebSocket. Metoda handshake připojení přenáší název hybridního připojení nakonfigurovaného v oboru názvů Relay a token zabezpečení, který uděluje "naslouchání" na daném názvu.

Když služba protokol WebSocket přijme, registrace se dokončí a zavedený WebSocket se udržuje jako řídicí kanál, aby se povolily všechny následné interakce. Služba umožňuje až 25 souběžných naslouchacího procesu pro jedno hybridní připojení. Je určena kvóta pro AppHooks.

Pokud je pro Hybrid Connections k dispozici nejméně dva aktivní naslouchací procesy, jsou příchozí připojení rozložena v náhodném pořadí; došlo k pokusu o spravedlivou distribuci s nejlepším úsilím.

#### <a name="accept-message"></a>Přijmout zprávu

Když odesílatel otevře nové připojení ke službě, služba zvolí a upozorní jeden z aktivních naslouchací proces v hybridním připojení. Toto oznámení se odešle do naslouchací služby prostřednictvím kanálu otevřeného ovládacího prvku jako zpráva JSON. Zpráva obsahuje adresu URL koncového bodu WebSocket, ke kterému se musí naslouchací proces připojit, aby bylo možné připojení přijmout.

Adresu URL může a musí použít přímo naslouchací proces bez jakékoli další práce.
Zakódované informace jsou platné pouze po krátkou dobu, a to v podstatě po dobu, kdy je odesilatel ochotn, aby se připojení navázalo jako koncové. Maximum, které se má předpokládat, je 30 sekund. Adresu URL lze použít pouze pro jeden úspěšný pokus o připojení. Jakmile se naváže připojení pomocí protokolu WebSocket k adrese URL aplikace Rendezvous, veškerá další aktivita v tomto protokolu WebSocket se předává odesílateli a odesilateli. K tomuto chování dochází bez zásahu nebo výkladu služby.

### <a name="request-message"></a>Zpráva požadavku

Kromě připojení pomocí protokolu WebSocket může naslouchací proces také přijímat z odesilatele snímky požadavků HTTP, pokud je tato funkce v hybridním připojení explicitně povolená.

Naslouchací procesy, které se připojují k Hybrid Connections s podporou protokolu HTTP, musí zpracovat `request` gesto. Naslouchací proces, který nezpracovává `request` a proto způsobuje opakované chyby vypršení časového limitu při připojení, může služba v budoucnu zablokovat.

Metadata hlavičky rámce protokolu HTTP jsou přeložena do formátu JSON pro jednodušší zpracování v rámci architektury naslouchacího procesu, a to také proto, že knihovny pro analýzu hlaviček protokolu HTTP jsou rarer než analyzátory JSON. Metadata HTTP, která jsou relevantní pouze pro vztahy mezi odesílatelem a předávací HTTP Gateway, včetně autorizačních informací, nejsou předávána. Tělo požadavku HTTP se transparentně přenáší jako binární rámce WebSocket.

Naslouchací proces může reagovat na požadavky HTTP pomocí gesta ekvivalentní odezvy.

Tok požadavků a odpovědí používá ve výchozím nastavení řídicí kanál, ale v případě potřeby může být "upgradován" na samostatný Rendezvous WebSocket. Rozdílová připojení protokolu WebSocket zvyšují propustnost každé konverzace klientů, ale zatěžují naslouchací proces s větším množstvím připojení, která je potřeba zpracovat, což nemusí být žádoucí pro zjednodušené klienty.

V řídicím kanálu jsou texty požadavků a odpovědí omezeny na nejvíce 64 kB. Metadata hlaviček protokolu HTTP jsou omezená na celkem 32 kB. Pokud žádost nebo odpověď překročí tuto prahovou hodnotu, naslouchací proces musí upgradovat na Rendezvous WebSocket pomocí gesta ekvivalentního ke zpracování [přijetí](#accept-message).

V případě požadavků služba rozhodne, zda směrovat požadavky prostřednictvím řídicího kanálu. To zahrnuje, ale nemusí být omezené na případy, kdy požadavek překračuje 64 kB (záhlaví a tělo), nebo pokud se požadavek pošle pomocí [blokového kódování "bloků" v bloku dat](https://tools.ietf.org/html/rfc7230#section-4.1) a služba má důvod, že by požadavek překročil hodnotu 64 kB nebo pokud se žádost nepřečte. Pokud se služba rozhodne pro doručení žádosti přes Rendezvous, předá pouze do naslouchacího procesu pouze adresu Rendezvous.
Naslouchací proces pak musí zřídit Rendezvous WebSocket a služba se vyzve k okamžitému doručení úplné žádosti včetně institucí přes WebSocket v Rendezvous. Odpověď musí také používat rozhraní Rendezvous WebSocket.

U požadavků, které přicházejí do řídicího kanálu, naslouchá naslouchací proces, zda reaguje na řídicí kanál nebo prostřednictvím Rendezvous. Služba musí zahrnovat adresu Rendezvous u každého požadavku směrovaného přes řídicí kanál. Tato adresa je platná pouze pro upgrade z aktuální žádosti.

Pokud se naslouchací proces rozhodne upgradovat, připojí se a okamžitě doručí odpověď na vytvořený Rendezvous Socket.

Po navázání služby Rendezvous WebSocket by měl naslouchací proces udržovat další zpracování žádostí a odpovědí ze stejného klienta. Služba zachová protokol WebSocket, pokud připojení soketu HTTPS s odesílatelem přetrvá a bude směrovat všechny následné žádosti od tohoto odesílatele prostřednictvím spravovaného protokolu WebSocket. Pokud se naslouchací proces rozhodne odstranit z jeho strany Rendezvous WebSocket, služba taky připojení k odesílateli odstraní bez ohledu na to, jestli už může probíhat další požadavek.

#### <a name="renew-operation"></a>Operace obnovení

Token zabezpečení, který se musí použít k registraci naslouchacího procesu a udržování řídicího kanálu, může vypršet, zatímco je naslouchací proces aktivní. Vypršení platnosti tokenu nemá vliv na probíhající připojení, ale způsobí, že se řídicí kanál na základě této služby vynechá nebo brzy po vypršení platnosti. Operace "prodloužit" je zpráva JSON, kterou může naslouchací proces odeslat, aby nahradil token přidružený k řídicímu kanálu, aby bylo možné udržovat řídicí kanál pro rozšířené tečky.

#### <a name="ping-operation"></a>Operace s příkazy pro odeslání

Pokud řídicí kanál zůstane po dlouhou dobu nečinný, prostředníky na cestě, jako jsou nástroje pro vyrovnávání zatížení nebo NAT, můžou připojení TCP vyřadit. Operace "příkazového testu" se zabrání tím, že pošle malý objem dat v kanálu, který připomíná všechny síťové trasy, které má připojení fungovat, a slouží také jako "živý" test pro naslouchací proces. Pokud se test z příkazového testu nezdařil, řídicí kanál by měl být považován za nepoužitelný a naslouchací proces by se měl znovu připojit.

### <a name="sender-interaction"></a>Interakce odesilatele

Odesílatel má dvě interakce se službou: připojuje webový soket nebo odesílá požadavky prostřednictvím protokolu HTTPS. Žádosti nelze odeslat přes webový soket z role odesílatele.

#### <a name="connect-operation"></a>Operace připojení

Operace "připojit" otevírá na službě WebSocket, která poskytuje název hybridního připojení a (volitelně ale vyžaduje) token zabezpečení, který v řetězci dotazu uděluje oprávnění Odeslat. Služba pak spolupracuje s naslouchacím postupem v výše popsaném postupu a naslouchací proces vytvoří připojení Rendezvous, které je připojeno k tomuto objektu WebSocket. Po přijetí protokolu WebSocket se všechny další interakce tohoto protokolu WebSocket nacházejí v připojeném naslouchacího procesu.

#### <a name="request-operation"></a>Operace požadavku

Pro Hybrid Connections, pro které byla funkce povolená, může odesílatel odeslat převážně neomezený požadavek HTTP na naslouchací procesy.

S výjimkou přístupového tokenu přenosu, který je buď vložený v řetězci dotazu, nebo v hlavičce HTTP žádosti, je přenos plně transparentní na všechny operace HTTP na adrese pro předávání a na všech příponách cesty k adrese přenosu, takže naslouchací proces je plně pod kontrolou kompletního ověřování a dokonce i funkcí rozšíření HTTP, jako je [CORS](https://www.w3.org/TR/cors/).

Autorizace odesilatele s koncovým bodem přenosu je ve výchozím nastavení zapnutá, ale je VOLITELNá. Vlastník hybridního připojení se může rozhodnout, že povolí anonymní odesílatele. Služba bude zachytávat, kontrolovat a odstraňovat informace o autorizaci následujícím způsobem:

1. Pokud řetězec dotazu obsahuje `sb-hc-token` výraz, výraz bude vždy odstraněn z řetězce dotazu. Vyhodnotí se, pokud je zapnutá autorizace přenosu.
2. Pokud hlavičky požadavku obsahují `ServiceBusAuthorization` hlavičku, výraz záhlaví bude vždy odstraněn z kolekce hlaviček.
   Vyhodnotí se, pokud je zapnutá autorizace přenosu.
3. Jenom v případě, že je zapnutá autorizace přenosu, a pokud hlavičky žádosti obsahují `Authorization` hlavičku a není k dispozici žádný z předchozích výrazů, bude tato hlavička vyhodnocena a odstraněna. V opačném případě se `Authorization` vždy předává jako.

Pokud není k dispozici žádný aktivní naslouchací proces, služba vrátí kód chyby 502 "Chybná brána". Pokud se službě neobjeví požadavek na zpracování této žádosti, služba vrátí 504 "časový limit brány" po 60 sekundách.

### <a name="interaction-summary"></a>Souhrn interakce

Výsledkem tohoto modelu interakce je to, že klient odesilatele z handshake vychází pomocí "čistého" WebSocket, který je připojený k naslouchacímu procesu a který nevyžaduje žádné další preambule ani přípravu. Tento model umožňuje prakticky všem existujícím implementům klientů WebSocket využívat službu Hybrid Connections tím, že poskytuje správnou vytvořenou adresu URL do své klientské vrstvy WebSocket.

Služba Rendezvous Connection WebSocket, kterou naslouchací proces získá prostřednictvím interakce přijmout, je také čistá a může být předána libovolné implementaci serveru WebSocket s minimální abstrakcí, která rozlišuje mezi "přijetí" na místních síťových posluchačích rozhraní a Hybrid Connections vzdálených operací "přijmout".

Model požadavků a odpovědí HTTP dává odesilateli do značné míry neomezenou oblast protokolu HTTP s VOLITELNou autorizační vrstvou. Naslouchací proces získá předem analyzovaný oddíl hlavičky požadavku HTTP, který se dá vrátit do podřízeného požadavku HTTP nebo zpracovat jako je, s binárními rámečky, které přenesou tělo HTTP. Odpovědi používají stejný formát. Interakce s méně než 64 KB textu žádosti a odpovědi může být zpracována přes jeden webový soket, který je sdílen pro všechny odesílatele. Větší požadavky a odpovědi lze zpracovat pomocí modelu Rendezvous.

## <a name="protocol-reference"></a>Odkaz na protokol

V této části najdete podrobné informace o interakcích protokolu popsaných výše.

Všechna připojení protokolu WebSocket se nastavují na portu 443 jako upgrade z HTTPS 1,1, který je běžně abstraktní některými rozhraními WebSocket nebo rozhraním API. Popis je udržován v neutrální implementaci bez návrhu konkrétního rozhraní.

### <a name="listener-protocol"></a>Protokol naslouchacího procesu

Protokol naslouchacího procesu se skládá ze dvou gest připojení a tří operací zpráv.

#### <a name="listener-control-channel-connection"></a>Připojení řídicího kanálu pro naslouchací proces

Řídicí kanál se otevře s vytvořením připojení protokolu WebSocket k:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

`namespace-address`Je plně kvalifikovaný název domény Azure Relay oboru názvů, který je hostitelem hybridního připojení, většinou formuláře `{myname}.servicebus.windows.net` .

Parametry řetězce dotazu jsou následující.

| Parametr        | Povinné | Popis
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Ano      | Pro roli naslouchacího procesu musí být parametr **SB-HC-Action = Listen** .
| `{path}`         | Yes      | Cesta oboru názvů zakódovaného URL předkonfigurovaného hybridního připojení k registraci tohoto naslouchacího procesu. Tento výraz je připojen k části s pevnou `$hc/` cestou.
| `sb-hc-token`    | Yes\*    | Naslouchací proces musí poskytovat platný, Service Bus sdílený přístupový token s kódováním URL pro obor názvů nebo hybridní připojení, které uděluje právo na **naslouchání** .
| `sb-hc-id`       | No       | Toto volitelné ID pro klienta poskytuje kompletní trasování diagnostiky.

Pokud se připojení protokolu WebSocket nepovede kvůli registraci cesty k hybridnímu připojení nebo neplatnému nebo chybějícímu tokenu nebo nějaké jiné chybě, je k dispozici zpětná vazba k chybě pomocí běžného modelu zpětné vazby stavu HTTP 1,1. Popis stavu obsahuje ID sledování chyb, které může být sděleno pracovníkům podpory Azure:

| Kód | Chyba          | Popis
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nenalezeno      | Cesta k hybridnímu připojení je neplatná nebo základní adresa URL je poškozená.
| 401  | Neautorizováno   | Token zabezpečení chybí nebo je neplatný nebo neplatný.
| 403  | Forbidden      | Token zabezpečení není pro tuto cestu pro tuto akci platný.
| 500  | Vnitřní chyba | Ve službě se něco pokazilo.

Pokud se připojení protokolu WebSocket úmyslně vypíná službou po jeho počátečním nastavení, je důvod k tomu sdělen pomocí vhodného kódu chyby protokolu WebSocket spolu s popisnou chybovou zprávou, která také obsahuje ID sledování. Služba nevypne řídicí kanál bez výskytu chybové podmínky. Jakékoli čisté vypnutí je řízeno klientem.

| Stav WS | Description
| --------- | -------------------------------------------------------------------------------
| 1001      | Cesta k hybridnímu připojení je Odstraněná nebo zakázaná.
| 1008      | Platnost tokenu zabezpečení vypršela, a proto je porušena zásada autorizace.
| 1011      | Ve službě se něco pokazilo.

#### <a name="accept-handshake"></a>Přijmout handshake

Oznámení "přijmout" odesílá služba do naslouchací služby přes dříve vytvořený kanál řízení jako zprávu JSON v textovém rámečku WebSocket. Neexistuje žádná odpověď na tuto zprávu.

Zpráva obsahuje objekt JSON s názvem Accept, který v tuto chvíli definuje následující vlastnosti:

* **Address** – řetězec adresy URL, který se má použít k vytvoření objektu WebSocket ke službě pro příjem příchozího připojení.
* **ID** – jedinečný identifikátor pro toto připojení. Pokud byl identifikátor dodán klientem odesílatele, jedná se o hodnotu zadanou odesílatelem, jinak se jedná o systémem generovanou hodnotu.
* **connectHeaders** – všechny hlavičky HTTP, které byly zadány do koncového bodu služby Relay odesílatelem, který také obsahuje hlavičky sec-WebSocket-Protocol a SEK-WebSockets-Extensions.

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

Adresa URL adresy zadaná ve zprávě JSON se používá pro naslouchací proces k vytvoření WebSocket pro příjem nebo odmítnutí soketu odesílatele.

##### <a name="accepting-the-socket"></a>Přijetí soketu

Pro přijetí naslouchacího procesu vytvoří připojení protokolu WebSocket k zadané adrese.

Pokud zpráva "přijmout" obsahuje `Sec-WebSocket-Protocol` hlavičku, očekává se, že naslouchací proces přijímá pouze WebSocket, pokud tento protokol podporuje. Kromě toho nastaví záhlaví jako protokol WebSocket.

Totéž platí pro `Sec-WebSocket-Extensions` záhlaví. Pokud rozhraní podporuje rozšíření, měla by pro toto rozšíření nastavit hlavičku na odpověď na straně serveru vyžadované `Sec-WebSocket-Extensions` metodou handshake.

Adresa URL musí být použita jako-je určena pro vytvoření přijatelného soketu, ale obsahuje následující parametry:

| Parametr      | Povinné | Popis
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ano      | Pro přijetí soketu musí být parametr `sb-hc-action=accept`
| `{path}`       | Yes      | (viz následující odstavec)
| `sb-hc-id`     | No       | Viz předchozí popis **ID**.

`{path}` je cesta oboru názvů zakódovaná URL předkonfigurovaného hybridního připojení, na které se má tento naslouchací proces zaregistrovat. Tento výraz je připojen k části s pevnou `$hc/` cestou.

`path`Výraz může být prodloužen s příponou a výrazem řetězce dotazu, který následuje za zaregistrovaným názvem po oddělení lomítka.
Tento parametr umožňuje klientovi odesílatele předat argumenty odeslání do přijímacího naslouchacího procesu, pokud není možné zahrnout hlavičky protokolu HTTP. Očekává se, že rozhraní naslouchacího procesu analyzuje část pevné cesty a registrovaný název z cesty a provede zbytek, případně bez argumentů řetězce dotazu, který je s předponou `sb-` , k dispozici pro aplikaci k rozhodnutí, zda má být připojení přijatelné.

Další informace najdete v části "odesilatel protokolu".

Pokud dojde k chybě, může služba odpovědět následujícím způsobem:

| Kód | Chyba          | Popis
| ---- | -------------- | -----------------------------------
| 403  | Forbidden      | Adresa URL není platná.
| 500  | Vnitřní chyba | Ve službě se něco pokazilo.

 Po navázání připojení server vypne WebSocket při vypnutí WebSocket odesílatele nebo s následujícím stavem:

| Stav WS | Description                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Klient odesilatele ukončí připojení.                                    |
| 1001      | Cesta k hybridnímu připojení je Odstraněná nebo zakázaná.                        |
| 1008      | Platnost tokenu zabezpečení vypršela, a proto je porušena zásada autorizace. |
| 1011      | Ve službě se něco pokazilo.                                            |

##### <a name="rejecting-the-socket"></a>Zamítnutí soketu

 Zamítnutí soketu po kontrole `accept` zprávy vyžaduje podobnou metodu handshake, takže stavový kód a popis stavu, který komunikuje s odůvodněním zamítnutí, může přesměrovat zpět odesílatele.

 V této volbě protokolu se dá použít Metoda handshake protokolu WebSocket (která je navržená tak, aby se ukončila v definovaném chybovém stavu), takže implementace klientů naslouchacího procesu se můžou dál spoléhat na klienta WebSocket a nepotřebují používat extra a holého klienta HTTP.

 Aby byl soket zamítnut, klient převezme ze zprávy identifikátor URI adresy `accept` a připojí dva parametry řetězce dotazu k tomuto objektu, a to následujícím způsobem:

| Param                   | Povinné | Popis                              |
| ----------------------- | -------- | ---------------------------------------- |
| SB-HC-statusCode        | Yes      | Číselný kód stavu HTTP.                |
| SB-HC-statusDescription | Yes      | Důvod pro odmítání z lidského čitelného. |

Výsledný identifikátor URI se pak použije k navázání připojení protokolu WebSocket.

Při správném dokončení se tato metoda handshake úmyslně nezdařila s kódem chyby HTTP 410, protože nebyl vytvořen žádný WebSocket. Pokud dojde k nějaké chybě, popíše následující kódy chybu:

| Kód | Chyba          | Popis                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Forbidden      | Adresa URL není platná.                |
| 500  | Vnitřní chyba | Ve službě se něco pokazilo. |

#### <a name="request-message"></a>Zpráva požadavku

`request`Zpráva je odeslána službou do naslouchací služby prostřednictvím řídicího kanálu. Po navázání se tato zpráva pošle i přes WebSocket ve službě Rendezvous.

`request`Skládá se ze dvou částí: záhlaví a binárních rámců (ů).
Pokud není k dispozici žádné tělo, budou rámce textu vynechány. Vlastnost Boolean `body` označuje, zda je ve zprávě požadavku přítomen text.

V případě požadavku s textem žádosti může struktura vypadat takto:

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

Naslouchací proces musí zpracovat rozdělení textu žádosti v několika binárních rámečcích (viz [fragmenty WebSocket](https://tools.ietf.org/html/rfc6455#section-5.4)).
Požadavek skončí, když byl přijat binární rámec se sadou příznaků FIN.

Pro požadavek bez těla je k dispozici pouze jeden textový rámeček.

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

Obsah JSON pro `request` je následující:

* řetězec **adresy** URI. Je to adresa Rendezvous, která se má použít pro tento požadavek. Pokud je příchozí požadavek větší než 64 kB, zbývající část této zprávy je ponecháno prázdné a klient musí iniciovat hodnotu Rendezvous handshake odpovídající `accept` operaci popsanou níže. Služba pak na `request` zavedeném webovém soketu vloží dokončeno. Pokud je možné očekávat odpověď maximálně 64 kB, naslouchací proces musí taky iniciovat hodnotu Rendezvous handshake a následně přenést odpověď na zavedený webový soket.
* **ID** – řetězec. Jedinečný identifikátor pro tento požadavek.
* **requestHeaders hostitele** – tento objekt obsahuje všechny hlavičky HTTP, které byly dodány do koncového bodu odesílatelem, s výjimkou informací o autorizaci, jak je vysvětleno [výše](#request-operation), a záhlavími, které se striktně vztahují k připojení s bránou. Konkrétně jsou všechny hlavičky definované nebo vyhrazené v [RFC7230](https://tools.ietf.org/html/rfc7230), s výjimkou `Via` , jsou odstraněny a nepředávány:

  * `Connection` (RFC7230, oddíl 6,1)
  * `Content-Length`  (RFC7230, oddíl 3.3.2)
  * `Host`  (RFC7230, oddíl 5,4)
  * `TE`  (RFC7230, oddíl 4,3)
  * `Trailer`  (RFC7230, oddíl 4,4)
  * `Transfer-Encoding`  (RFC7230, oddíl 3.3.1)
  * `Upgrade` (RFC7230, oddíl 6,7)
  * `Close`  (RFC7230, oddíl 8,1)

* **requestTarget** – řetězec. Tato vlastnost obsahuje  ["cíl žádosti" (RFC7230, část 5,3)](https://tools.ietf.org/html/rfc7230#section-5.3) žádosti. Obsahuje část řetězce dotazu, která je odstraněna ze všech `sb-hc-` předpevněných parametrů.
* řetězec **metody** . Toto je metoda požadavku, na [RFC7231, část 4](https://tools.ietf.org/html/rfc7231#section-4). `CONNECT`Metoda nesmí být použita.
* **tělo** – logická hodnota. Určuje, zda následuje jeden nebo více binárních rámců textu.

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

##### <a name="responding-to-requests"></a>Reakce na žádosti

Přijímač musí reagovat. Opakovaná neúspěšná reakce na žádosti a při zachování připojení může vést k zablokování naslouchacího procesu.

Odpovědi mohou být zasílány v libovolném pořadí, ale každý požadavek musí odpovídat do 60 sekund nebo doručení bude Hlášeno jako neúspěšné. Konečný termín 60-Second se počítá až do chvíle, kdy `response` Služba nepřijala rámec. Probíhající odpověď s více binárními snímky nemůže být nečinná po dobu delší než 60 sekund nebo se ukončí.

Pokud je požadavek přijat prostřednictvím řídicího kanálu, musí být odpověď buď odeslána na řídicí kanál, ze kterého byla žádost přijata, nebo musí být odeslána prostřednictvím kanálu Rendezvous.

Odpověď je objekt JSON s názvem Response. Pravidla pro zpracování obsahu textu jsou přesně stejná jako u `request` zprávy a jsou založena na `body` Vlastnosti.

* **RequestId** – řetězec Požadovanou. `id`Hodnota vlastnosti `request` zprávy, na kterou reaguje.
* **StatusCode** – číslo. Požadovanou. číselný kód stavu HTTP, který určuje výsledek oznámení. Všechny stavové kódy [RFC7231, oddíl 6](https://tools.ietf.org/html/rfc7231#section-6) jsou povoleny, s výjimkou [502 "Chybná brána"](https://tools.ietf.org/html/rfc7231#section-6.6.3) a [504 "časový limit brány"](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **statusDescription** – řetězec. Volitelné. Stav HTTP – fráze odůvodnění kódu na [RFC7230, část 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **ResponseHeaders hostitele** – hlavičky HTTP, které se mají nastavit v externí odpovědi HTTP
  Stejně jako u `request` RFC7230 nesmí být použita definovaná záhlaví.
* **tělo** – logická hodnota. Označuje, zda se mají sledovat binární body těla (y).

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

##### <a name="responding-via-rendezvous"></a>Reakce prostřednictvím Rendezvous

Pro odpovědi, které překračují 64 kB, musí být odpověď doručena přes soket Rendezvous. Také Pokud požadavek překročí 64 kB a `request` obsahuje pouze pole adresa, je nutné vytvořit soket Rendezvous, který získá `request` . Po navázání typu Rendezvous Socket se musí odpovědi na příslušného klienta a následné požadavky z tohoto klienta doručovat přes soket Rendezvous, a to i v případě, že přetrvává.

`address`Adresa URL v poli `request` musí být použita jako – je určena pro vytvoření soketu Rendezvous, ale obsahuje následující parametry:

| Parametr      | Povinné | Popis
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ano      | Pro přijetí soketu musí být parametr `sb-hc-action=request`

Pokud dojde k chybě, může služba odpovědět následujícím způsobem:

| Kód | Chyba           | Popis
| ---- | --------------- | -----------------------------------
| 400  | Neplatný požadavek | Nerozpoznaná akce nebo adresa URL je neplatná.
| 403  | Forbidden       | Platnost adresy URL vypršela.
| 500  | Vnitřní chyba  | Ve službě se něco pokazilo.

 Po navázání připojení server vypne WebSocket, když se soket HTTP klienta ukončí nebo s následujícím stavem:

| Stav WS | Description                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Klient odesilatele ukončí připojení.                                    |
| 1001      | Cesta k hybridnímu připojení je Odstraněná nebo zakázaná.                        |
| 1008      | Platnost tokenu zabezpečení vypršela, a proto je porušena zásada autorizace. |
| 1011      | Ve službě se něco pokazilo.                                            |


#### <a name="listener-token-renewal"></a>Obnovení tokenu naslouchacího procesu

Až vyprší platnost tokenu naslouchacího procesu, může ho nahradit odesláním zprávy textového rámce do služby prostřednictvím vytvořeného řídicího kanálu. Zpráva obsahuje objekt JSON s názvem `renewToken` , který definuje následující vlastnost v tuto chvíli:

* **token** – platný Service Bus sdílený přístupový token s kódováním URL pro obor názvů nebo hybridní připojení, který uděluje právo na **naslouchání** .

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Pokud se ověření tokenu nepovede, přístup se odepře a cloudová služba uzavře WebSocket řídicího kanálu s chybou. V opačném případě neexistuje žádná odpověď.

| Stav WS | Description                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Platnost tokenu zabezpečení vypršela, a proto je porušena zásada autorizace. |

### <a name="web-socket-connect-protocol"></a>Protokol připojení k webovému soketu

Protokol odesilatele je efektivně shodný se způsobem, jakým je vytvořen naslouchací proces.
Cílem je maximální transparentnost koncového objektu WebSocket. Adresa, ke které se chcete připojit, je stejná jako u naslouchacího procesu, ale "Action" se liší a token potřebuje jiné oprávnění:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sb-hc-token=...
```

_Obor názvů – adresa_ je plně kvalifikovaný název domény Azure Relay oboru názvů, který je hostitelem hybridního připojení, většinou formuláře `{myname}.servicebus.windows.net` .

Požadavek může obsahovat libovolné dodatečné hlavičky protokolu HTTP, včetně aplikací definovaných aplikací. Veškerý dodaný hlavičkový tok do naslouchacího procesu, který lze najít v `connectHeader` objektu zprávy o **přijetí** kontrolního programu.

Možnosti parametru řetězce dotazu jsou následující:

| Param          | Povinné? | Popis
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Ano       | V případě role odesílatele musí být parametr `sb-hc-action=connect` .
| `{path}`       | Yes       | (viz následující odstavec)
| `sb-hc-token`  | Yes\*     | Naslouchací proces musí poskytovat platný, Service Bus sdílený přístupový token s kódováním URL pro obor názvů nebo hybridní připojení, které uděluje právo **Odeslat** .
| `sb-hc-id`     | No        | Volitelné ID, které umožňuje kompletní diagnostické trasování a zpřístupnění posluchači během metody Accept handshake.

 `{path}`Je cesta oboru názvů zakódovaného URL předkonfigurovaného hybridního připojení, na které se má tento naslouchací proces zaregistrovat. `path`Výraz lze rozšířit s příponou a výrazem řetězce dotazu pro další komunikaci. Pokud je hybridní připojení registrováno pod cestou `hyco` , `path` za výraz může `hyco/suffix?param=value&...` následovat parametry řetězce dotazu, které jsou zde definovány. Úplný výraz může být následující:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sb-hc-token=...
```

`path`Výraz je předán do naslouchacího procesu v identifikátoru URI adresy, který je obsažen v řídící zprávě "Accept".

Pokud se připojení protokolu WebSocket nepovede z důvodu registrace cesty k hybridnímu připojení, neplatného nebo chybějícího tokenu nebo nějaké jiné chyby, je k dispozici zpětná vazba k chybě pomocí běžného modelu zpětné vazby stavu HTTP 1,1. Popis stavu obsahuje ID sledování chyb, které se dá sdělit pracovníkům podpory Azure:

| Kód | Chyba          | Popis
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nenalezeno      | Cesta k hybridnímu připojení je neplatná nebo základní adresa URL je poškozená.
| 401  | Neautorizováno   | Token zabezpečení chybí nebo je neplatný nebo neplatný.
| 403  | Forbidden      | Token zabezpečení není pro tuto cestu a pro tuto akci platný.
| 500  | Vnitřní chyba | Ve službě se něco pokazilo.

Pokud se připojení protokolu WebSocket úmyslně ukončí službou po počátečním nastavování, je důvod k tomu sdělen pomocí vhodného kódu chyby protokolu WebSocket spolu s popisnou chybovou zprávou, která také obsahuje ID sledování.

| Stav WS | Description
| --------- | ------------------------------------------------------------------------------- 
| 1000      | Naslouchací proces vypnul soket.
| 1001      | Cesta k hybridnímu připojení je Odstraněná nebo zakázaná.
| 1008      | Platnost tokenu zabezpečení vypršela, a proto je porušena zásada autorizace.
| 1011      | Ve službě se něco pokazilo.

### <a name="http-request-protocol"></a>Protokol požadavku HTTP

Protokol požadavků HTTP umožňuje libovolné požadavky HTTP, kromě upgrady protokolu.
Požadavky HTTP jsou odkazovány na běžné běhové adrese entity bez $hc vpony, který se používá pro klienty WebSocket s hybridními připojeními.

```
https://{namespace-address}/{path}?sb-hc-token=...
```

_Obor názvů – adresa_ je plně kvalifikovaný název domény Azure Relay oboru názvů, který je hostitelem hybridního připojení, většinou formuláře `{myname}.servicebus.windows.net` .

Požadavek může obsahovat libovolné dodatečné hlavičky protokolu HTTP, včetně aplikací definovaných aplikací. Všechna dodaná záhlaví, s výjimkou těch, která jsou přímo definována v RFC7230 (viz [zpráva požadavku](#request-message)) toku k naslouchacímu procesu a lze ji nalézt v `requestHeader` objektu zprávy **požadavku** .

Možnosti parametru řetězce dotazu jsou následující:

| Param          | Povinné? | Description
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Ano\*     | Naslouchací proces musí poskytovat platný, Service Bus sdílený přístupový token s kódováním URL pro obor názvů nebo hybridní připojení, které uděluje právo **Odeslat** .

Token se dá také přenést buď v hlavičce, `ServiceBusAuthorization` nebo v `Authorization` hlavičce HTTP. Token se dá vynechat, pokud je hybridní připojení nakonfigurované tak, aby povolovalo anonymní požadavky.

Vzhledem k tomu, že služba efektivně funguje jako proxy, a to i v případě, že ne jako skutečný proxy server HTTP, buď přidá `Via` hlavičku nebo doplní existující `Via` záhlaví kompatibilní s [RFC7230, oddíl 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
Služba přidá název hostitele oboru názvů Relay do `Via` .

| Kód | Zpráva  | Popis                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | Žádost byla zpracována alespoň jedním naslouchací proces.  |
| 202  | Přijato | Požadavek přijal alespoň jeden naslouchací proces. |

Pokud dojde k chybě, může služba odpovědět následujícím způsobem. Zda je odpověď pocházející ze služby nebo naslouchacího procesu, může být identifikována prostřednictvím přítomnosti `Via` hlavičky. Pokud je hlavička přítomna, odpověď je od naslouchacího procesu.

| Kód | Chyba           | Popis
| ---- | --------------- |--------- |
| 404  | Nenalezeno       | Cesta k hybridnímu připojení je neplatná nebo základní adresa URL je poškozená.
| 401  | Neautorizováno    | Token zabezpečení chybí nebo je neplatný nebo neplatný.
| 403  | Forbidden       | Token zabezpečení není pro tuto cestu a pro tuto akci platný.
| 500  | Vnitřní chyba  | Ve službě se něco pokazilo.
| 503  | Chybná brána     | Požadavek nebylo možné směrovat do žádného naslouchacího procesu.
| 504  | Časový limit brány | Požadavek byl směrován do naslouchacího procesu, ale naslouchací proces nepřijal příjem v požadovaném čase.

## <a name="next-steps"></a>Další kroky

* [Přenos – nejčastější dotazy](relay-faq.md)
* [Vytvoření oboru názvů](relay-create-namespace-portal.md)
* [Začínáme s .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Začínáme s aplikací Node](relay-hybrid-connections-node-get-started.md)

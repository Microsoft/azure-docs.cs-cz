---
title: Průvodce protokolem hybridních připojení azure relay | Dokumenty společnosti Microsoft
description: Tento článek popisuje interakce na straně klienta s přenosem hybridních připojení pro připojení klientů v rolích posluchače a odesílatele.
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
ms.date: 01/21/2020
ms.author: clemensv
ms.openlocfilehash: 68668452152064584d1c419a3053ccb642b103f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514948"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Protokol hybridních připojení azure relay

Azure Relay je jedním z klíčových pilířů schopností platformy Azure Service Bus. Nová funkce _hybridních připojení_ relé je bezpečný vývoj protokolu s otevřeným protokolem založený na protokolech HTTP a WebSockets. Nahrazuje bývalou, stejně pojmenovanou funkci _BizTalk Services,_ která byla postavena na základech proprietárního protokolu. Integrace hybridních připojení do azure app services bude i nadále fungovat tak, jak je.

Hybridní připojení umožňuje obousměrnou binární komunikaci datového proudu a jednoduchý tok datového gramu mezi dvěma síťovými aplikacemi. Buď nebo obě strany mohou být umístěny za natnebo firewally.

Tento článek popisuje interakce na straně klienta s přenosem hybridních připojení pro připojení klientů v rolích posluchače a odesílatele. Také popisuje, jak posluchači přijímat nová připojení a požadavky.

## <a name="interaction-model"></a>Model interakce

Přenos hybridních připojení spojuje dvě strany tím, že poskytuje bod setkání v cloudu Azure, který mohou strany zjistit a připojit se z hlediska vlastní sítě. Bod setkání se nazývá "Hybridní připojení" v této a další dokumentaci, v api a také na portálu Azure. Koncový bod služby hybridní připojení se označuje jako "služba" pro zbytek tohoto článku.

Služba umožňuje předávání připojení webového soketu a požadavků a odpovědí HTTP(S).

Model interakce se opírá o názvosloví vytvořené mnoha dalšími síťovými api. Je naslouchací proces, který nejprve označuje připravenost ke zpracování příchozích připojení a následně je přijme při jejich doručení. Na druhé straně se klient připojí k naslouchacímu procesu a očekává, že toto připojení bude přijato pro vytvoření obousměrné komunikační cesty. "Připojit", "Listen" a "Accept" jsou stejné termíny, které najdete ve většině rozhraní API soketu.

Jakýkoli model přenášené komunikace má obě strany, které odchozí připojení k koncovému bodu služby. To je "posluchač" také "klient" v hovorové použití a může také způsobit další terminologie přetížení. Přesná terminologie, která se proto používá pro hybridní připojení, je následující:

Programy na obou stranách připojení se nazývají "klienti", protože jsou klienty služby. Klient, který čeká a přijímá připojení je "naslouchací proces", nebo se říká, že je v roli "naslouchací proces.". Klient, který iniciuje nové připojení k naslouchací proces prostřednictvím služby se nazývá "odesílatel", nebo je v roli odesílatele.

### <a name="listener-interactions"></a>Interakce posluchače

Naslouchací proces má pět interakcí se službou; všechny podrobnosti o vodičích jsou popsány dále v tomto článku v referenční části.

Zprávy naslouchání, přijímání a požadavku jsou přijímány ze služby. Operace Obnovit a Ping jsou odesílány naslouchací proces.

#### <a name="listen-message"></a>Poslech zprávy

Chcete-li označit připravenost ke službě, že naslouchací proces je připraven přijmout připojení, vytvoří odchozí připojení WebSocket. Připojení handshake nese název hybridní připojení nakonfigurované v oboru názvů přenosu a token zabezpečení, který uděluje "Listen" právo na tento název.

Když websocket je přijat službou, registrace je dokončena a zavedené WebSocket je udržována naživu jako "kontrolní kanál" pro povolení všech následných interakcí. Služba umožňuje až 25 souběžných naslouchacích procesů pro jedno hybridní připojení. Kvóta pro AppHooks je třeba určit.

Pro hybridní připojení, pokud existují dva nebo více aktivních naslouchacích procesy, příchozí připojení jsou vyváženy mezi nimi v náhodném pořadí; spravedlivé rozdělení je pokus s maximálním úsilím.

#### <a name="accept-message"></a>Přijmout zprávu

Když odesílatel otevře nové připojení ke službě, služba vybere a upozorní jeden z aktivních naslouchacích procesů na hybridní připojení. Toto oznámení je odesláno naslouchací proces prostředně prostředně prostředně otevřené ovládací kanál jako zpráva JSON. Zpráva obsahuje adresu URL koncového bodu WebSocket, ke kterému se musí naslouchací proces připojit pro přijetí připojení.

Adresa URL může a musí být použita přímo posluchačem bez jakékoli další práce.
Kódované informace jsou platné pouze krátkou dobu, v podstatě tak dlouho, dokud odesílatel je ochoten čekat na připojení, které má být navázáno end-to-end. Maximální předpokládaná hodnota je 30 sekund. Adresu URL lze použít pouze pro jeden úspěšný pokus o připojení. Jakmile je vytvořeno připojení WebSocket s adresou URL rendezvous, všechny další aktivity na této websocket je přenášen z a odesílateli. K tomu dochází bez jakéhokoli zásahu nebo výkladu ze strany služby.

### <a name="request-message"></a>Žádost o zprávu

Kromě připojení WebSocket může naslouchací proces přijímat také rámce požadavků HTTP od odesílatele, pokud je tato funkce explicitně povolena v hybridním připojení.

Naslouchací procesy, které `request` se připojují k hybridní připojení s podporou HTTP musí zpracovat gesto. Naslouchací `request` proces, který nezpracovává a proto způsobuje opakované chyby časového oběhu při připojení může být na černé listině služby v budoucnu.

Metadata hlavičky protokolu HTTP jsou přeložena do JSON pro jednodušší zpracování rámcem posluchače, také proto, že knihovny analýzy záhlaví HTTP jsou vzácnější než analyzátory JSON. Metadata PROTOKOLU HTTP, která jsou relevantní pouze pro vztah mezi odesílatelem a bránou HTTP přenosu, včetně informací o autorizaci, nejsou předána. Těla požadavků HTTP jsou transparentně přenesena jako binární rámce WebSocket.

Naslouchací proces může reagovat na požadavky HTTP pomocí ekvivalentní gesto odpovědi.

Tok požadavku a odpovědi používá řídicí kanál ve výchozím nastavení, ale může být "upgradován" na odlišné rendezvous WebSocket, kdykoli je to požadováno. Odlišné připojení WebSocket zlepšit propustnost pro každou konverzaci klienta, ale zatěžují naslouchací proces s více připojení, které je třeba zpracovat, které nemusí být touha pro zjednodušené klienty.

Na kontrolním kanálu jsou těla požadavků a odpovědí omezena na maximálně 64 kB. Metadata záhlaví PROTOKOLU HTTP jsou omezena na celkem 32 kB. Pokud požadavek nebo odpověď překročí tuto prahovou hodnotu, musí naslouchací proces upgradovat na websocket schůzky pomocí gesta ekvivalentního zpracování [accept](#accept-message).

U požadavků služba rozhodne, zda směrovat požadavky přes řídicí kanál. To zahrnuje, ale nemusí být omezena na případy, kdy požadavek překročí 64 kB (záhlaví plus tělo) úplně, nebo pokud je požadavek odeslán s ["chunked" kódování přenosu](https://tools.ietf.org/html/rfc7230#section-4.1) a služba má důvod očekávat, že požadavek překročí 64 kB nebo čtení požadavku není okamžitá. Pokud se služba rozhodne doručit požadavek přes rendezvous, předá pouze adresu rendezvous naslouchací proces.
Naslouchací proces pak musí vytvořit rendezvous WebSocket a služba okamžitě poskytuje úplný požadavek včetně těla přes rendezvous WebSocket. Odpověď musí také použít rendezvous WebSocket.

Pro požadavky, které dorazí přes řídicí kanál, naslouchací proces rozhodne, zda reagovat přes řídicí kanál nebo prostřednictvím rendezvous. Služba musí obsahovat adresu setkání s každým požadavkem směrovaným přes řídicí kanál. Tato adresa je platná pouze pro upgrade z aktuálního požadavku.

Pokud se naslouchací proces rozhodne upgradovat, připojí se a okamžitě doručí odpověď přes zavedený soket schůzky.

Po rendezvous WebSocket byla vytvořena, naslouchací proces by měl udržovat pro další zpracování požadavků a odpovědi od stejného klienta. Služba bude udržovat WebSocket tak dlouho, dokud připojení soketu HTTPS s odesílatelem přetrvává a bude směrovat všechny následné požadavky od tohoto odesílatele přes udržovaný WebSocket. Pokud se posluchač rozhodne vynechat websocket z jeho strany, služba také vypustí připojení k odesílateli, bez ohledu na to, zda již probíhá další požadavek.

#### <a name="renew-operation"></a>Obnovit provoz

Token zabezpečení, který musí být použit k registraci naslouchací proces a udržovat řídicí kanál může vypršet, když je aktivní naslouchací proces. Vypršení platnosti tokenu nemá vliv na probíhající připojení, ale způsobí, že řídicí kanál, který má být vynechán službou v okamžiku vypršení platnosti nebo brzy po něm. Operace "obnovit" je zpráva JSON, kterou může naslouchací proces odeslat, aby nahradil token přidružený k řídicímu kanálu, aby mohl být řídicí kanál udržován po delší dobu.

#### <a name="ping-operation"></a>Operace příkazu Ping

Pokud řídicí kanál zůstane nečinný po dlouhou dobu, zprostředkovatelé na cestě, jako jsou například vykladače zatížení nebo NAT může vyřadit připojení TCP. Operace "ping" se vyhýbá, že odesláním malé množství dat na kanálu, který připomíná všem na síťové trase, že připojení má být naživu a slouží také jako "live" test pro naslouchací proces. Pokud příkaz ping selže, řídicí kanál by měl být považován za nepoužitelný a naslouchací proces by se měl znovu připojit.

### <a name="sender-interaction"></a>Interakce odesílatele

Odesílatel má dvě interakce se službou: připojí webový soket nebo odesílá požadavky prostřednictvím protokolu HTTPS. Požadavky nelze odeslat přes webový soket z role odesílatele.

#### <a name="connect-operation"></a>Operace připojení

Operace "připojit" otevře WebSocket ve službě, zadá název hybridní připojení a (volitelně, ale ve výchozím nastavení) token zabezpečení udělující oprávnění "Odeslat" v řetězci dotazu. Služba pak interaguje s naslouchací proces způsobem popsaným dříve a naslouchací proces vytvoří rendezvous připojení, které je spojeno s tímto WebSocket. Po WebSocket byl přijat, všechny další interakce na tomto WebSocket jsou s připojeným naslouchací proces.

#### <a name="request-operation"></a>Operace požadavku

U hybridních připojení, pro které byla funkce povolena, může odesílatel odesílat do značné míry neomezené požadavky HTTP posluchačům.

S výjimkou přístupového tokenu přenosu, který je vložen v řetězci dotazu nebo v hlavičce protokolu HTTP požadavku, je relé plně transparentní pro všechny operace PROTOKOLU HTTP na adrese Relay a všechny přípony cesty k adrese relé, takže posluchač má plně kontrolu nad autorizací od konce na konec a dokonce i pro funkce rozšíření HTTP, jako je [CORS](https://www.w3.org/TR/cors/).

Autorizace odesílatele s koncovým bodem relé je ve výchozím nastavení zapnutá, ale je volitelná. Vlastník hybridního připojení může povolit anonymní odesílatele. Služba zachytí, zkontroluje a zapne informace o autorizaci následujícím způsobem:

1. Pokud řetězec dotazu `sb-hc-token` obsahuje výraz, bude vždy odstraněn z řetězce dotazu. Bude vyhodnocena, pokud je autorizace relé zapnuta.
2. Pokud hlavičky požadavku `ServiceBusAuthorization` obsahují hlavičku, bude výraz záhlaví vždy odstraněn z kolekce záhlaví.
   Bude vyhodnocena, pokud je autorizace relé zapnuta.
3. Autorizace přenosu je zapnuta a pokud hlavičky `Authorization` požadavku obsahují hlavičku a žádný z předchozích výrazů není k dispozici, bude záhlaví vyhodnoceno a odstraněno. V opačném `Authorization`případě je vždy předán jako-je.

Pokud neexistuje žádný aktivní naslouchací proces, služba vrátí kód chyby 502 "Bad Gateway". Pokud služba nezobrazí zpracovat požadavek, služba vrátí 504 "Časový čas brány" po 60 sekundách.

### <a name="interaction-summary"></a>Souhrn interakce

Výsledkem tohoto modelu interakce je, že klient odesílatele pochází z handshake s "čisté" WebSocket, který je připojen k naslouchací proces a který nepotřebuje žádné další preambule nebo přípravu. Tento model umožňuje prakticky všechny existující implementace klienta WebSocket snadno využít služby hybridní připojení tím, že poskytuje správně sestavené URL do jejich websocket klientské vrstvy.

Rendezvous připojení WebSocket, který naslouchací proces získá prostřednictvím přijmout interakce je také čisté a může být předán a existující websocket server implementace s minimální extra abstrakce, která rozlišuje mezi "přijmout" operace na naslouchací procesy místní sítě jejich rozhraní a vzdálené operace "přijmout" hybridní připojení.

Model požadavků/odpovědí HTTP poskytuje odesílateli do značné míry neomezenou plochu protokolu HTTP s volitelnou autorizační vrstvou. Naslouchací proces získá předem analyzovaný oddíl hlavičky požadavku HTTP, který lze převést zpět na příjem http požadavku nebo zpracován tak, jak je, s binárními snímky nesoucími těla HTTP. Odpovědi používají stejný formát. Interakce s méně než 64 KB těla požadavku a odpovědi lze zpracovat přes jeden webový soket, který je sdílen pro všechny odesílatele. Větší požadavky a odpovědi lze zpracovat pomocí modelu rendezvous.

## <a name="protocol-reference"></a>Odkaz na protokol

Tato část popisuje podrobnosti o dříve popsaných interakcích protokolu.

Všechna připojení WebSocket jsou provedena na portu 443 jako upgrade z https 1.1, který je běžně abstrahován některé WebSocket framework nebo API. Popis zde je zachována implementace neutrální, aniž by naznačovaly konkrétní rámec.

### <a name="listener-protocol"></a>Protokol posluchače

Protokol naslouchací proces se skládá ze dvou gest připojení a tří operací zpráv.

#### <a name="listener-control-channel-connection"></a>Připojení řídicího kanálu naslouchací proces

Řídicí kanál je otevřen s vytvořením připojení WebSocket k:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

Jedná `namespace-address` se o plně kvalifikovaný název domény oboru názvů Azure Relay, který `{myname}.servicebus.windows.net`je hostitelem hybridního připojení, obvykle formuláře .

Možnosti parametru řetězce dotazu jsou následující.

| Parametr        | Požaduje se | Popis
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Ano      | Pro roli posluchače musí být parametr **sb-hc-action=listen**
| `{path}`         | Ano      | Cesta oboru názvů kódovaná adresou URL předkonfigurovaného hybridního připojení k registraci tohoto naslouchací procesu. Tento výraz je připojen `$hc/` k pevné části cesty.
| `sb-hc-token`    | Ano\*    | Naslouchací proces musí poskytnout platný, URL kódovaný token sdíleného přístupu sběrnice pro obor názvů nebo hybridní připojení, které uděluje **listen** právo.
| `sb-hc-id`       | Ne       | Toto volitelné ID dodané klientem umožňuje komplexní diagnostické trasování.

Pokud se připojení WebSocket nezdaří z důvodu není registrována cesta hybridní připojení nebo neplatný nebo chybějící token nebo nějaká jiná chyba, je zpětná vazba chyby poskytována pomocí běžného modelu zpětné vazby stavu HTTP 1.1. Popis stavu obsahuje id pro sledování chyb, které lze sdělit pracovníkům podpory Azure:

| kód | Chyba          | Popis
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nebyl nalezen.      | Cesta hybridního připojení je neplatná nebo je poškozená základní adresa URL.
| 401  | Neautorizováno   | Token zabezpečení chybí nebo je poškozený nebo neplatný.
| 403  | Forbidden      | Token zabezpečení není pro tuto cestu pro tuto akci platný.
| 500  | Vnitřní chyba | Něco se ve službě pokazilo.

Pokud je připojení WebSocket záměrně vypnuto službou poté, co bylo původně nastaveno, je důvod sdělován pomocí příslušného kódu chyby protokolu WebSocket spolu s popisnou chybovou zprávou, která obsahuje také ID sledování. Služba nevypne řídicí kanál bez výskytu chybového stavu. Jakékoli čisté vypnutí je řízeno klientem.

| WS stav | Popis
| --------- | -------------------------------------------------------------------------------
| 1001      | Cesta hybridního připojení byla odstraněna nebo zakázána.
| 1008      | Platnost tokenu zabezpečení vypršela, proto jsou porušeny zásady autorizace.
| 1011      | Něco se ve službě pokazilo.

#### <a name="accept-handshake"></a>Přijmout handshake

Oznámení "přijmout" je odesláno službou naslouchací proces přes dříve vytvořený řídicí kanál jako zprávu JSON v textovém rámečku WebSocket. Na tuto zprávu není žádná odpověď.

Zpráva obsahuje objekt JSON s názvem "accept", který definuje následující vlastnosti v tomto okamžiku:

* **adresa** – řetězec URL, který má být použit pro vytvoření WebSocket ke službě přijmout příchozí připojení.
* **id** – jedinečný identifikátor pro toto připojení. Pokud bylo ID dodáno klientem odesílatele, jedná se o zadanou hodnotu odesílatele, jinak se jedná o systémovou hodnotu.
* **connectHeaders** – všechny hlavičky HTTP, které byly dodány do koncového bodu přenosu odesílatelem, který také zahrnuje hlavičky Sec-WebSocket-Protocol a Sec-WebSocket-Extensions.

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

Adresa URL adresy uvedená ve zprávě JSON je použita posluchačem k vytvoření websocketu pro přijetí nebo odmítnutí soketu odesílatele.

##### <a name="accepting-the-socket"></a>Přijetí zásuvky

Chcete-li přijmout, naslouchací proces vytvoří připojení WebSocket k zakasové adrese.

Pokud zpráva "přijmout" `Sec-WebSocket-Protocol` nese záhlaví, očekává se, že naslouchací proces přijímá pouze WebSocket, pokud podporuje tento protokol. Navíc nastaví záhlaví jako WebSocket je vytvořen.

Totéž platí pro `Sec-WebSocket-Extensions` záhlaví. Pokud rozhraní podporuje rozšíření, by měl nastavit záhlaví na straně `Sec-WebSocket-Extensions` serveru odpověď požadované handshake pro rozšíření.

Adresa URL musí být použita jako-je pro vytvoření soketu přijmout, ale obsahuje následující parametry:

| Parametr      | Požaduje se | Popis
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ano      | Pro přijetí soketu musí být parametr`sb-hc-action=accept`
| `{path}`       | Ano      | (viz následující odstavec)
| `sb-hc-id`     | Ne       | Viz předchozí popis **id**.

`{path}`je cesta oboru názvů kódovaná adresou URL předkonfigurovaného hybridního připojení, na které má být tento naslouchací proces registrován. Tento výraz je připojen `$hc/` k pevné části cesty.

Výraz `path` může být rozšířen o příponu a výraz řetězce dotazu, který následuje za registrovaným názvem po oddělujícím lomítku.
To umožňuje klientovi odesílatele předat argumenty odeslání přijímajícímu posluchači, pokud není možné zahrnout hlavičky HTTP. Očekává se, že rozhraní naslouchací proces analyzuje pevnou část cesty a registrovaný název z cesty `sb-`a zpřístupní zbývající část, případně bez argumentů řetězce dotazu předponou , k dispozici aplikaci pro rozhodnutí, zda připojení přijmout.

Další informace naleznete v následující části "Sender Protocol".

Pokud dojde k chybě, služba může odpovědět následujícím způsobem:

| kód | Chyba          | Popis
| ---- | -------------- | -----------------------------------
| 403  | Forbidden      | Adresa URL není platná.
| 500  | Vnitřní chyba | Něco se pokazilo ve službě

 Po navázání připojení server vypne websocket při vypnutí odesílatele WebSocket nebo s následujícím stavem:

| WS stav | Popis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Klient odesílatele vypne připojení.                                    |
| 1001      | Cesta hybridního připojení byla odstraněna nebo zakázána.                        |
| 1008      | Platnost tokenu zabezpečení vypršela, proto jsou porušeny zásady autorizace. |
| 1011      | Něco se ve službě pokazilo.                                            |

##### <a name="rejecting-the-socket"></a>Odmítnutí zásuvky

 Odmítnutí soketu po `accept` kontrole zprávy vyžaduje podobné handshake tak, aby stavový kód a popis stavu sdělující důvod odmítnutí může tok zpět odesílateli.

 Volba návrhu protokolu je zde použít WebSocket handshake (který je navržen tak, aby skončil ve stavu definované chyby) tak, aby implementace klienta naslouchací procesu může i nadále spoléhat na klienta WebSocket a není nutné zaměstnávat další, holé HTTP klienta.

 Chcete-li soket odmítnout, klient `accept` převezme identifikátor URI adresy ze zprávy a připojí k němu dva parametry řetězce dotazu, a to následovně:

| Param                   | Požaduje se | Popis                              |
| ----------------------- | -------- | ---------------------------------------- |
| sb-hc-statusKód        | Ano      | Číselný stavový kód HTTP.                |
| sb-hc-statusPopis | Ano      | Lidský čitelný důvod odmítnutí. |

Výsledný identifikátor URI se pak používá k navázání připojení WebSocket.

Při dokončení správně tento handshake záměrně selže s kódem chyby HTTP 410, protože nebyla vytvořena žádná websocket. Pokud se něco pokazí, následující kódy popisují chybu:

| kód | Chyba          | Popis                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Forbidden      | Adresa URL není platná.                |
| 500  | Vnitřní chyba | Něco se ve službě pokazilo. |

#### <a name="request-message"></a>Žádost o zprávu

Zpráva `request` je odeslána službou naslouchací proces přes řídicí kanál. Stejná zpráva je také odeslána přes rendezvous WebSocket po navázání.

Skládá `request` se ze dvou částí: záhlaví a binární rám (y) těla.
Pokud není žádné tělo, snímky těla jsou vynechány. Indikátor pro to, zda je tělo `body` přítomno, je logická vlastnost ve zprávě požadavku.

Pro požadavek s tělo požadavku může struktura vypadat takto:

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

Naslouchací proces musí zpracovat příjem tělo požadavku rozdělit přes více binárních rámců (viz [WebSocket fragmenty).](https://tools.ietf.org/html/rfc6455#section-5.4)
Požadavek končí, když byl přijat binární snímek se sadou příznaku FIN.

Pro požadavek bez textu je pouze jeden textový rámeček.

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

Obsah JSON `request` pro je následující:

* **adresa** - řetězec URI. Toto je adresa setkání, která má být pro tento požadavek používána. Pokud příchozí požadavek je větší než 64 kB, zbytek této zprávy je ponechána prázdná a klient `accept` musí zahájit rendezvous handshake ekvivalentní operace popsané níže. Služba pak vloží dokončení `request` na zavedené webové soketu. Pokud lze očekávat, že odpověď překročí 64 kB, naslouchací proces musí také zahájit handshake rendezvous a potom přenést odpověď přes zavedené webové soketu.
* **id** – řetězec. Jedinečný identifikátor pro tento požadavek.
* **requestHeaders** – tento objekt obsahuje všechny hlavičky HTTP, které byly dodány do koncového bodu odesílatelem, s výjimkou informací o autorizaci, jak je vysvětleno [výše](#request-operation), a záhlaví, které se vztahují výhradně k připojení k bráně. Konkrétně všechny hlavičky definované nebo vyhrazené v [RFC7230](https://tools.ietf.org/html/rfc7230), s výjimkou `Via`, jsou odstraněny a nejsou předány:

  * `Connection`(RFC7230, oddíl 6.1)
  * `Content-Length`(RFC7230, bod 3.3.2)
  * `Host`(RFC7230, oddíl 5.4)
  * `TE`(RFC7230, oddíl 4.3)
  * `Trailer`(RFC7230, oddíl 4.4)
  * `Transfer-Encoding`(RFC7230, oddíl 3.3.1)
  * `Upgrade`(RFC7230, oddíl 6.7)
  * `Close`(RFC7230, oddíl 8.1)

* **requestTarget** – řetězec. Tato vlastnost obsahuje ["Požadavek cíl" (RFC7230, oddíl 5.3)](https://tools.ietf.org/html/rfc7230#section-5.3) požadavku. To zahrnuje část řetězce dotazu, která `sb-hc-` je zbavena všech předpona parametry.
* **metoda** - řetězec. Toto je metoda požadavku podle [RFC7231, oddíl 4](https://tools.ietf.org/html/rfc7231#section-4). Metoda `CONNECT` NESMÍ být použita.
* **tělo** – logické. Označuje, zda následuje jeden nebo více binárních snímků těla.

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

##### <a name="responding-to-requests"></a>Odpovídání na požadavky

Přijímač musí reagovat. Opakované selhání reagovat na požadavky při zachování připojení může mít za následek naslouchací proces získání na černé listině.

Odpovědi mohou být odeslány v libovolném pořadí, ale každá žádost musí být zodpovězena do 60 sekund, jinak bude doručení nahlášeno jako neúspěšné. 60sekundová lhůta se počítá, `response` dokud služba neobdrží rámec. Trvalá odpověď s více binárními rámci se nemůže stát nečinnou po dobu delší než 60 sekund nebo je ukončena.

Je-li požadavek přijat přes kontrolní kanál, musí být odpověď odeslána buď na řídicím kanálu, odkud byla žádost přijata, nebo musí být odeslána prostředkem rendezvous.

Odpověď je objekt JSON s názvem "odpověď". Pravidla pro zpracování obsahu těla `request` jsou přesně jako `body` se zprávou a na základě vlastnosti.

* **requestId** – řetězec. Požadované. Hodnota `id` vlastnosti `request` zprávy, na kterou se reaguje.
* **statusCode** – číslo. Požadované. číselný stavový kód HTTP, který označuje výsledek oznámení. Všechny stavové kódy [RFC7231, oddíl 6](https://tools.ietf.org/html/rfc7231#section-6) jsou povoleny, s výjimkou [502 "Bad Gateway"](https://tools.ietf.org/html/rfc7231#section-6.6.3) a [504 "Gateway Timeout"](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **statusDescription** - řetězec. Volitelné. Http stavový kód důvod fráze na [RFC7230, Oddíl 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** – hlavičky HTTP, které mají být nastaveny v externí odpovědi HTTP.
  Stejně `request`jako u , RFC7230 definované hlavičky NESMÍ být použity.
* **tělo** – logické. Označuje, zda následuje binární snímek těla.

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

##### <a name="responding-via-rendezvous"></a>Reagovat prostřednictvím rendezvous

Pro odpovědi, které přesahují 64 kB, musí být odpověď doručena přes soketu rendezvous. Také pokud požadavek překročí 64 kB `request` a obsahuje pouze pole adresy, musí být vytvořena soket setkání získat `request`. Po navázání soketu rendezvous musí být odpovědi na příslušného klienta a následné požadavky od příslušného klienta doručeny přes soket rendezvous, dokud trvá.

Adresa `address` URL `request` v je třeba použít as-je pro stanovení soketu rendezvous, ale obsahuje následující parametry:

| Parametr      | Požaduje se | Popis
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ano      | Pro přijetí soketu musí být parametr`sb-hc-action=request`

Pokud dojde k chybě, služba může odpovědět následujícím způsobem:

| kód | Chyba           | Popis
| ---- | --------------- | -----------------------------------
| 400  | Neplatný požadavek | Nerozpoznaná akce nebo adresa URL není platná.
| 403  | Forbidden       | Platnost adresy URL vypršela.
| 500  | Vnitřní chyba  | Něco se pokazilo ve službě

 Po navázání připojení server vypne websocket při vypnutí soketu HTTP klienta nebo s následujícím stavem:

| WS stav | Popis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Klient odesílatele vypne připojení.                                    |
| 1001      | Cesta hybridního připojení byla odstraněna nebo zakázána.                        |
| 1008      | Platnost tokenu zabezpečení vypršela, proto jsou porušeny zásady autorizace. |
| 1011      | Něco se ve službě pokazilo.                                            |


#### <a name="listener-token-renewal"></a>Obnovení tokenu posluchače

Když se blíží vypršení platnosti tokenu naslouchacího procesu, může jej nahradit odesláním zprávy textového rámečku službě prostřednictvím zavedeného řídicího kanálu. Zpráva obsahuje objekt JSON `renewToken`s názvem , který definuje následující vlastnost v tomto okamžiku:

* **token** – platný token sdíleného přístupu služby Service Bus s kódováním URL pro obor názvů nebo hybridní připojení, který uděluje právo **naslouchat.**

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Pokud se ověření tokenu nezdaří, přístup je odepřen a cloudová služba zavře řídicí kanál WebSocket s chybou. V opačném případě neexistuje žádná odpověď.

| WS stav | Popis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Platnost tokenu zabezpečení vypršela, proto jsou porušeny zásady autorizace. |

### <a name="web-socket-connect-protocol"></a>Protokol připojení webového soketu

Protokol odesílatele je účinně totožný se způsobem, jakým je naslouchací proces vytvořen.
Cílem je maximální transparentnost pro end-to-end WebSocket. Adresa pro připojení k je stejná jako pro naslouchací proces, ale "akce" se liší a token potřebuje jiné oprávnění:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

_Adresa oboru názvů_ je plně kvalifikovaný název domény oboru názvů Azure Relay, který je `{myname}.servicebus.windows.net`hostitelem hybridního připojení, obvykle formuláře .

Požadavek může obsahovat libovolné hlavičky HTTP navíc, včetně těch definovaných aplikací. Všechny zadané hlavičky toku na schytavku a lze nalézt na `connectHeader` objekt přijmout řídicí zprávy. **accept**

Možnosti parametru řetězce dotazu jsou následující:

| Param          | Povinné? | Popis
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Ano       | Pro roli odesílatele musí být `sb-hc-action=connect`parametr .
| `{path}`       | Ano       | (viz následující odstavec)
| `sb-hc-token`  | Ano\*     | Naslouchací proces musí poskytnout platný, URL kódovaný token sdíleného přístupu služby Service Bus pro obor názvů nebo hybridní připojení, které uděluje **oprávnění Odeslat.**
| `sb-hc-id`     | Ne        | Volitelné ID, které umožňuje komplexní diagnostické trasování a je k dispozici naslouchací proces během přijmout handshake.

 Je `{path}` url kódovaný obor názvů cestu předkonfigurované hybridní připojení, na kterém se má zaregistrovat tento naslouchací proces. Výraz `path` lze rozšířit o příponu a výraz řetězce dotazu pro další komunikaci. Pokud je hybridní připojení registrováno pod cestou `hyco` `path` , výraz může následovat `hyco/suffix?param=value&...` parametry řetězce dotazu definované zde. Úplný výraz pak může být následující:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

Výraz `path` je předán naslouchací proces v adrese URI obsažené ve zprávě ovládacího prvku "přijmout".

Pokud se připojení WebSocket nezdaří z důvodu není registrována cesta hybridní připojení, neplatný nebo chybějící token nebo nějaká jiná chyba, je zpětná vazba chyby poskytována pomocí běžného modelu zpětné vazby stavu HTTP 1.1. Popis stavu obsahuje ID sledování chyb, které lze sdělit pracovníkům podpory Azure:

| kód | Chyba          | Popis
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nebyl nalezen.      | Cesta hybridního připojení je neplatná nebo je poškozená základní adresa URL.
| 401  | Neautorizováno   | Token zabezpečení chybí nebo je poškozený nebo neplatný.
| 403  | Forbidden      | Token zabezpečení není platný pro tuto cestu a pro tuto akci.
| 500  | Vnitřní chyba | Něco se ve službě pokazilo.

Pokud je připojení WebSocket záměrně ukončeno službou poté, co bylo původně nastaveno, je důvod emitován pomocí příslušného kódu chyby protokolu WebSocket spolu s popisnou chybovou zprávou, která obsahuje také ID sledování .

| WS stav | Popis
| --------- | ------------------------------------------------------------------------------- 
| 1000      | Naslouchací proces vypne soket.
| 1001      | Cesta hybridního připojení byla odstraněna nebo zakázána.
| 1008      | Platnost tokenu zabezpečení vypršela, proto jsou porušeny zásady autorizace.
| 1011      | Něco se ve službě pokazilo.

### <a name="http-request-protocol"></a>Protokol požadavku HTTP

Protokol požadavku HTTP umožňuje libovolné požadavky HTTP, s výjimkou upgradů protokolů.
Požadavky HTTP jsou směrovány na pravidelnou adresu runtime entity bez $hc infix, který se používá pro hybridní připojení websocket klientů.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

_Adresa oboru názvů_ je plně kvalifikovaný název domény oboru názvů Azure Relay, který je `{myname}.servicebus.windows.net`hostitelem hybridního připojení, obvykle formuláře .

Požadavek může obsahovat libovolné hlavičky HTTP navíc, včetně těch definovaných aplikací. Všechny zadané hlavičky, s výjimkou těch, které jsou přímo definovány v RFC7230 (viz [zpráva požadavku)](#Request message)toku na naslouchací proces a lze nalézt na `requestHeader` objekt zprávy **požadavku.**

Možnosti parametru řetězce dotazu jsou následující:

| Param          | Povinné? | Popis
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Ano\*     | Naslouchací proces musí poskytnout platný, URL kódovaný token sdíleného přístupu služby Service Bus pro obor názvů nebo hybridní připojení, které uděluje **oprávnění Odeslat.**

Token lze také provádět v `ServiceBusAuthorization` `Authorization` záhlaví nebo HTTP. Token lze vynechat, pokud je hybridní připojení nakonfigurováno tak, aby umožňovalo anonymní požadavky.

Vzhledem k tomu, že služba účinně funguje jako proxy server, `Via` i když ne jako `Via` skutečný proxy server HTTP, přidá buď záhlaví nebo anotuje existující záhlaví kompatibilní s [RFC7230, Oddíl 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
Služba přidá název hostitele oboru `Via`názvů Relay do služby .

| kód | Zpráva  | Popis                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | Požadavek byl zpracován alespoň jeden naslouchací proces.  |
| 202  | Accepted | Požadavek byl přijat alespoň jeden naslouchací proces. |

Pokud dojde k chybě, služba může odpovědět následujícím způsobem. Zda odpověď pochází ze služby nebo z naslouchací proces lze identifikovat prostřednictvím přítomnosti `Via` záhlaví. Pokud záhlaví je k dispozici, odpověď je z naslouchací proces.

| kód | Chyba           | Popis
| ---- | --------------- |--------- |
| 404  | Nebyl nalezen.       | Cesta hybridního připojení je neplatná nebo je poškozená základní adresa URL.
| 401  | Neautorizováno    | Token zabezpečení chybí nebo je poškozený nebo neplatný.
| 403  | Forbidden       | Token zabezpečení není platný pro tuto cestu a pro tuto akci.
| 500  | Vnitřní chyba  | Něco se ve službě pokazilo.
| 503  | Špatná brána     | Požadavek nelze směrovat na žádný naslouchací proces.
| 504  | Časový čas brány | Požadavek byl směrován na naslouchací proces, ale naslouchací proces nepotvrdil příjem v požadovaném čase.

## <a name="next-steps"></a>Další kroky

* [Přenos – nejčastější dotazy](relay-faq.md)
* [Vytvoření oboru názvů](relay-create-namespace-portal.md)
* [Začínáme s .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Začínáme s aplikací Node](relay-hybrid-connections-node-get-started.md)

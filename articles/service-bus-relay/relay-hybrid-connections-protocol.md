---
title: Azure hybridní připojení přenosového protokolu Průvodce | Microsoft Docs
description: Azure Průvodce protokol předávání hybridní připojení.
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
ms.openlocfilehash: 306a21add76261dce99c954a2ba373e4b5047a75
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895400"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure hybridní připojení předávací protokol

Předávání přes Azure je jedním z klíčů schopností pilíře na platformě Azure Service Bus. Nové _hybridní připojení_ funkce předávání je zabezpečený, otevřete protokol evolution na základě protokolu HTTP a objekty WebSockets. Nahrazuje dřívějším, stejně s názvem _BizTalk Services_ funkce, který byl postavený na vlastnickým protokolem foundation. Integrace hybridní připojení do Azure App Services budou nadále fungovat jako-je.

Hybridní připojení umožňuje obousměrnou, binární datový proud komunikace a jednoduchý datagram tok mezi dvěma síťových aplikací. Můžete buď nebo obou stran nacházet za zařízení NAT nebo brány firewall.

Tento článek popisuje klienta interakce s předávání hybridní připojení pro připojení klientů v rolích naslouchací proces a odesílatele. Také popisuje, jak naslouchací procesy přijímat nová připojení a požadavky.

## <a name="interaction-model"></a>Interakce modelu

Předávání hybridní připojení dvě strany připojí tím, že poskytuje bod potkávací v cloudu Azure, můžete vyhledat a připojit z hlediska vlastní síť strany. Bod potkávací se nazývá "Hybridní připojení" v tomto a dalších dokumentaci rozhraní API a také na portálu Azure. Koncový bod služby hybridní připojení se označuje jako "služba" pro zbývající část tohoto článku.

Služba umožňuje předávání Websocket připojení a HTTP (S) požadavky a odpovědi.

Model interakce leans na klasifikace vymezenému mnoho jiná síťová rozhraní API. Je naslouchací proces, který nejprve znamená připravenosti pro zpracování příchozí připojení a následně je přijímá po doručení. Na druhé straně se klient připojí směrem naslouchací proces, očekává se toto připojení pro navázání obousměrné komunikační cestu. "Připojit", "Naslouchání" a "Přijmout" jsou stejné podmínky, které můžete najít v většina soketu rozhraní API.

Všechny přenosu komunikační model má buď strany odchozí připojení ke koncovému bodu služby. To usnadňuje "naslouchací proces" i "client" v obecné použití a může také způsobit další terminologie přetížení. Přesné terminologie proto použít pro hybridní připojení je následující:

Programy na obou stranách připojení se nazývají "klienty,", vzhledem k tomu, že jsou klienti ke službě. Klienta, která čeká na a přijímá připojení je "naslouchací proces", nebo je uvedená v "naslouchací proces role." Klienta, který iniciuje nové připojení směrem naslouchací proces prostřednictvím služby se označuje jako "sender", nebo je v "odesílatele role."

### <a name="listener-interactions"></a>Naslouchací proces interakce

Naslouchací proces má pět interakce s službu; všechny podrobnosti přenosová jsou popsané dále v tomto článku v části odkaz.

Naslouchání, přijmout a žádosti o zprávy jsou přijal od služby. Obnovit, a odešle příkaz Ping operations naslouchací proces.

#### <a name="listen-message"></a>Naslouchání zpráv

K označení připravenosti služby, který naslouchací proces je připraven přijmout připojení, vytvoří odchozí připojení protokolu WebSocket. Metoda handshake připojení stejný název hybridní připojení nakonfigurovaná na obor názvů předávání a token zabezpečení, která uděluje "Naslouchání" přímo na tento název.

Když protokol WebSocket je přijatá službou, registrace je dokončena a zavedených protokolu WebSocket se ukládají jako "řídicí kanál" pro povolení všechny následné interakce zachování připojení. Služba umožňuje až 25 souběžných naslouchací procesy jeden hybridní připojení. Kvóta pro AppHooks je možné určit.

Pro hybridní připojení pokud existují dvě nebo více active naslouchací procesy příchozí připojení jsou rovnoměrně rozdělen mezi je v náhodném pořadí; usilovně je pokusu o správného distribuce.

#### <a name="accept-message"></a>Přijmout zprávu

Když odesílatele otevře nové připojení na službu, službu vybere a upozorní jednu aktivní naslouchacího procesu na hybridní připojení. Toto oznámení se odesílá do naslouchací proces kanálem otevřít řídicí jako zprávu JSON. Zpráva obsahuje adresu URL koncového bodu protokolu WebSocket, který naslouchací proces musí připojit k pro přijetí připojení.

Adresu URL můžete a musí používat přímo naslouchací proces bez další zátěže.
Kódovaného informace je platná pouze na krátkou dobu běhu v podstatě po dobu, odesílatel je ochotná čekat na připojení k být navázáno začátku do konce. Maximální počet předpokládat, že je 30 sekund. Adresu URL můžete použít pouze pro jeden úspěšného pokusu o připojení. Po vytvoření připojení protokolu WebSocket s adresou URL potkávací všechny další aktivity na tento protokol WebSocket je přes předávací službu z a do odesílatele. Tato situace nastane bez zásahu nebo interpretace službou.

### <a name="request-message"></a>Zpráva požadavku

Kromě připojení protokolu WebSocket naslouchací proces můžete také získat rámce požadavku HTTP z odesílatele, pokud je výslovně povolena tato možnost k hybridní připojení.

Naslouchací procesy, které připojení k hybridní připojení s podporou protokolu HTTP musí zpracování `request` gesto. Naslouchací proces, který nemůže pracovat s `request` a v důsledku toho opakovaných vypršení časového limitu při připojení mohou být zakázány službou v budoucnu.

Metadata záhlaví HTTP rámečku je přeložen do formátu JSON pro jednodušší zpracování rozhraní naslouchací proces také protože analýzy knihovny hlavičky protokolu HTTP jsou vzácnějších než analyzátory JSON. Metadata HTTP, která je platné pouze pro vztah mezi odesílatele a brány přenosového protokolu HTTP, včetně informací o autorizaci, není předají. Textem žádosti protokolu HTTP se transparentně přenesou jako binární rámce protokolu WebSocket.

Naslouchací proces může reagovat na požadavky HTTP pomocí gesta ekvivalentní odpovědi.

Toku požadavků a odpovědí ve výchozím nastavení používá řídicí kanál, ale může být "upgradovat" na odlišné potkávací protokolu WebSocket vždy, když se vyžaduje. Odlišné připojení protokolu WebSocket zlepšit propustnost pro každého klienta konverzace, ale jejich nebyli naslouchací proces s více připojení, které potřebují ke zpracování, které nemusí být desire možnost pro prosté klienty.

Na řídicím kanálu jsou omezeny na maximálně 64 kB velikost těla požadavku a odpovědi. Metadata hlavičky protokolu HTTP je omezený na celkem 32 kB. Překročí-li požadavek nebo odpověď prahové hodnoty, naslouchací proces musíte provést upgrade na potkávací protokolu WebSocket pomocí gesto ekvivalentní zpracování [přijmout](#accept-message).

Pro žádosti služba rozhodne, zda směrovat požadavky přes řídicí kanál. To zahrnuje, ale nemusí být omezené na případy, pokud žádost překračuje 64 kB (hlavičky a text) přímý, nebo pokud je zaslán požadavek s ["blokové" transfer-encoding](https://tools.ietf.org/html/rfc7230#section-4.1) a služba má důvod očekávat v požadavku delší než 64 kB nebo čtení požadavku není okamžitě. Pokud se služba rozhodne se dodat žádost přes potkávací, předá pouze potkávací adresu pro naslouchací proces.
Naslouchací proces potom musíte vytvořit setkání protokolu WebSocket a službu neprodleně doručí úplné požadavku, včetně těla přes setkání protokolu WebSocket. Odpovědi musí taky používat setkání protokolu WebSocket.

Pro žádosti, které přicházejí přes řídicí kanál naslouchací proces rozhodne, zda reagovat přes řídicí kanál nebo prostřednictvím potkávací. Služba musí obsahovat potkávací adresa s každou žádostí směrovat přes řídicí kanál. Tato adresa je platná pouze pro upgrade z aktuální žádosti.

Pokud se rozhodne upgradovat naslouchací proces, připojí a rychle nabízí odpovědi přes zavedených potkávací soketu.

Jednou setkání, které bylo úspěšně navázáno protokolu WebSocket, naslouchací proces vhodné ponechat ji pro další zpracování požadavků a odpovědí ze stejného klienta. Služba bude udržovat protokolu WebSocket pro tak dlouho, dokud připojení s odesílatel soketu HTTPS potrvají a všechny následné žádosti z tohoto odesílatele bude směrovat přes protokol WebSocket zachována. Pokud naslouchací proces vybere možnost vyřadit setkání protokolu WebSocket z jeho strany, služba také vyřadí připojení k odesílatele, bez ohledu na to, jestli následného požadavku, mohou být již probíhá.

#### <a name="renew-operation"></a>Operace obnovení

Token zabezpečení, která bude použita k registraci naslouchací proces a udržovat řídicí kanál může vyprší během naslouchací proces je aktivní. Vypršení platnosti tokenu nemá vliv na probíhající připojení, ale způsobit řídicí kanál přeruší službou v nebo krátce po v okamžiku vypršení platnosti. Operace "obnovit" je zprávu JSON, který naslouchací proces může odesílat nahradit token přidružené řídicí kanál, takže řídicí kanál je možné udržovat po delší dobu.

#### <a name="ping-operation"></a>Operace příkazu ping

Je-li řídicí kanál nečinnosti, po dlouhou dobu, prostředníci na cestě, jako je například zatížení vyrovnávání nebo zařízení NAT. může dojít k přerušení připojení TCP. Operace "ping", zabraňuje odesláním malé množství dat, na který upozorní všem uživatelům v síti trasy, která je určené připojení jako aktivní, a slouží také jako "živé" testu pro naslouchací proces kanálu. V případě selhání příkazu ping řídicí kanál by měl být považován za nepoužitelný a naslouchací proces by měl znovu připojit.

### <a name="sender-interaction"></a>Odesílatel interakce

Odesílatel má dva interakce se službou: připojení Websocket nebo odešle požadavky prostřednictvím protokolu HTTPS. Požadavky nelze odeslat přes Websocket z role odesílatele.

#### <a name="connect-operation"></a>Operaci připojení.

Operace "připojit" otevře protokolu WebSocket ve službě, poskytnou jméno hybridní připojení a (volitelně, ale vyžaduje ve výchozím nastavení) token zabezpečení jejichž základě lze oprávnění "Odeslat" v řetězci dotazu. Služba potom komunikuje s naslouchací proces ve způsobu, jakým popsané a naslouchací proces vytvoří potkávací připojení, který je spojen s Tento protokolu WebSocket. Po přijetí protokol WebSocket, jsou všechny další interakce na tomto protokolu WebSocket připojené naslouchací proces.

#### <a name="request-operation"></a>Žádost o operaci

Odesílatel pro hybridní připojení, pro které bylo povoleno funkci, můžete odeslat do značné míry neomezený požadavků HTTP naslouchací procesy.

S výjimkou předávání přes přístupový token, který je že buď vložených v řetězci dotazu nebo v hlavičce HTTP požadavku předávací službu je pro všechny operace HTTP na adrese předávání a všechny přípony cesty předávání adres, plně transparentní ponechat naslouchací proces plně v ovládacím prvku en d až do konce autorizace a i rozšíření funkce protokolu HTTP, jako je [CORS](https://www.w3.org/TR/cors/).

Odesílatel autorizace s koncovým bodem předávání je ve výchozím nastavení zapnutá, ale je volitelné. Vlastník hybridní připojení můžete zvolit, aby anonymní odesílatelé. Služba zachytí, zkontrolovat a odstranit informace o ověření následujícím způsobem:

1. Pokud řetězec dotazu obsahuje `sb-hc-token` výrazu výraz vždy se odstraní z řetězce dotazu. Pokud je zapnutá přenosový autorizaci bude vyhodnocena.
2. Pokud obsahovat hlavičky žádosti `ServiceBusAuthorization` záhlaví, záhlaví výraz vždy se odstraní z kolekce záhlaví.
   Pokud je zapnutá přenosový autorizaci bude vyhodnocena.
3. Pouze v případě, že je zapnutý přenosový autorizaci a hlavičky žádosti obsahují-li `Authorization` záhlaví a ani jeden z předchozí výrazy přítomen, záhlaví bude vyhodnocena a odstraní. Jinak `Authorization`je vždy předána jako-je.

Pokud není žádná aktivní naslouchací proces, službu vrátí 502 kód chyby "Chybná brána". Pokud službu pro zpracování požadavku nezobrazí, službu vrátí 504 "brány časový limit" po 60 sekund.

### <a name="interaction-summary"></a>Interakce souhrn

Výsledkem tohoto modelu interakce je, že klienta odesílatele pochází z metody handshake se "čistou" WebSocket, která je připojená k naslouchací proces a potřebného žádné další preambles nebo přípravy. Tento model umožňuje prakticky jakékoli existující implementace klienta protokolu WebSocket snadno využívat výhod hybridních připojení služby zadáním správně vytvořený adresu URL do jejich vrstvy klienta protokolu WebSocket.

Potkávací připojení protokolu WebSocket, který získá naslouchací proces prostřednictvím přijmout interakce je taky čistou a můžete předat všechny existující server implementaci protokolu WebSocket pomocí některé minimální navíc abstrakce, která rozlišuje mezi operace hybridní připojení vzdálené "přijmout" a "přijmout" operace v místní síti jejich framework naslouchací procesy.

Model žádosti a odpovědi protokolu HTTP poskytuje odesílatel o z velké části neomezený HTTP protocol ploše s vrstvu volitelné autorizace. Naslouchací proces získá předem Analyzovaná část záhlaví žádosti HTTP, která můžete vrátit zpět do podřízené požadavku HTTP nebo zpracovat, protože se s binární rámce dělal těla HTTP. Odpovědi používají stejný formát. Interakce s méně než 64 KB těla požadavku a odpovědi lze zpracovat přes jediný soket webové, jež jsou sdílena pro všech uživatelů. Větší požadavky a odpovědi lze zpracovávat pomocí potkávací modelu.

## <a name="protocol-reference"></a>Referenční informace o protokolu

Tato část popisuje podrobnosti protokolu interakce popsané.

Všechna připojení protokolu WebSocket probíhají na portu 443 jako upgrade z verze 1.1 HTTPS, který je obvykle abstrahované některé protokolu WebSocket framework nebo rozhraní API. Popis tady je udržováno implementace neutrální, bez návrhy konkrétní rozhraní.

### <a name="listener-protocol"></a>Naslouchací proces protokolu

Naslouchací proces protokolu se skládá z dvě připojení gesta a tři operace zpráv.

#### <a name="listener-control-channel-connection"></a>Naslouchací proces připojení kanálu ovládací prvek

Řídicí kanál je otevřené se vytvoření připojení protokolu WebSocket k:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

`namespace-address` Je plně kvalifikovaný název domény předávání přes Azure oboru názvů, který je hostitelem hybridní připojení, obvykle ve formátu `{myname}.servicebus.windows.net`.

K dispozici jsou následující možnosti parametr řetězce dotazu.

| Parametr        | Požaduje se | Popis
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Ano      | Pro roli naslouchacího procesu musí být parametr **sb-hc-action = naslouchání**
| `{path}`         | Ano      | Obor názvů kódovaná adresou URL cesta předem nakonfigurovaná hybridní připojení k registraci této naslouchací proces na. Tento výraz se připojí k pevné `$hc/` část adresy obsahující cestu.
| `sb-hc-token`    | Ano\*    | Naslouchací proces musíte zadat platný, kódovaná adresou URL služby sběrnice sdíleného přístupu Token pro obor názvů nebo hybridní připojení, která uděluje **naslouchání** správné.
| `sb-hc-id`       | Ne       | Toto volitelné ID klienta zadaný umožňuje začátku do konce diagnostické trasování.

Pokud připojení protokolu WebSocket nezdaří z důvodu cesta hybridní připojení není zaregistrované, nebo token neplatný nebo chybějící nebo některé jiné chyby, poskytnutí zpětné vazby chyba pomocí regulárních zpětné vazby modelu stav HTTP 1.1. Popis stav obsahuje chyby sledování id, které může být oznamovat pracovníky technické podpory Azure:

| Kód | Chyba          | Popis
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nenalezené      | Hybridní připojení cesta je neplatná nebo je špatná základní adresa URL.
| 401  | Neautorizováno   | Token zabezpečení je chybějící nebo poškozený nebo neplatný.
| 403  | Zakázáno      | Token zabezpečení není platný pro tuto cestu pro tuto akci.
| 500  | Vnitřní chyba | Došlo k chybě ve službě.

Pokud připojení protokolu WebSocket záměrně ukončení služby po začátku bylo nastaveno tak to tak informace se předávají pomocí odpovídající kód chyby protokolu WebSocket společně s popisný chybová zpráva, která zahrnuje i ID sledování Služba nebude vypnuta řídicí kanál bez zjištění chybový stav. Všechny čistého vypnutí je klient řídí.

| Stav WS | Popis
| --------- | -------------------------------------------------------------------------------
| 1001      | Hybridní připojení cesty byla odstraněna nebo zakázána.
| 1008      | Vypršela platnost tokenu zabezpečení, proto porušení zásad autorizace.
| 1011      | Došlo k chybě ve službě.

#### <a name="accept-handshake"></a>Přijměte metody handshake

Oznámení "přijmout" posílá službou naslouchací proces prostřednictvím administrate řídicí kanál jako zprávy JSON v objektu WebSocket. Nepřijde žádná odpověď na tuto zprávu.

Zpráva obsahuje objekt JSON s názvem "přijmout", který definuje následující vlastnosti v tuto chvíli:

* **Adresa** – řetězce adresy URL, který se má použít k vytvoření objektu WebSocket ke službě přijmout příchozí připojení.
* **ID** – jedinečný identifikátor pro toto připojení. Pokud byl klient odesílatele ID, jedná se o odesílateli zadat hodnotu, jinak bude generována hodnota.
* **connectHeaders** – všechny hlavičky protokolu HTTP, zadaných ke koncovému bodu předávání odesílatelem, také zahrnující protokol WebSocket sekundu a hlavičky Sec. WebSocket rozšíření.

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

Adresa URL zadaná ve zprávě JSON se používají ke zřízení protokolu WebSocket pro přijetí nebo odmítnutí soketu odesílatele naslouchací proces.

##### <a name="accepting-the-socket"></a>Přijetí soket

Přijmout, vytvoří naslouchací proces připojení protokolu WebSocket na zadané adresy.

Pokud zpráva "přijmout" představuje `Sec-WebSocket-Protocol` záhlaví, očekává se, že naslouchací proces lze použít pouze protokol WebSocket pokud ji podporuje tento protokol. Kromě toho nastaví hlavičku, jako je vytvoření objektu WebSocket.

Totéž platí i pro `Sec-WebSocket-Extensions` záhlaví. Pokud rozhraní podporuje rozšíření, je potřeba nastavit hlavičku do odpovědi serverové požadovaného `Sec-WebSocket-Extensions` metody handshake pro rozšíření.

Adresa URL je nutné použít jako-je pro vytvoření soketu přijmout, ale obsahuje následující parametry:

| Parametr      | Požaduje se | Popis
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ano      | Pro příjem soketu, musí být parametr `sb-hc-action=accept`
| `{path}`       | Ano      | (viz odstavec)
| `sb-hc-id`     | Ne       | Viz popis v předchozí **id**.

`{path}` je cesta k oboru názvů kódovaná adresou URL předkonfigurované hybridní připojení, na které chcete zaregistrovat toto naslouchací proces. Tento výraz se připojí k pevné `$hc/` část adresy obsahující cestu.

`path` Výraz může být rozšířena příponu a výrazu řetězec dotazu, který následuje po dělicí lomítkem zaregistrovaný název.
To umožňuje klientovi odesílatele předání argumentů odesílání do přijímá naslouchací proces, když není možné zahrnout hlavičky protokolu HTTP. Očekávání je, že rozhraní naslouchací proces analyzuje část adresy obsahující pevné cestu a název registrované z cesty a umožňuje zbývající, které by mohly mít bez argumentů řetězce dotazu předponu `sb-`, k dispozici pro aplikace pro rozhodnutí, jestli se tak, aby přijímal připojení.

Další informace najdete v části následující (odesílatel Protocol).

Pokud dojde k chybě, můžete službu odpovědět následujícím způsobem:

| Kód | Chyba          | Popis
| ---- | -------------- | -----------------------------------
| 403  | Zakázáno      | Adresa URL není platný.
| 500  | Vnitřní chyba | Došlo k chybě ve službě

 Po navázání připojení k serveru protokol WebSocket vypne, jestliže odesílatel protokolu WebSocket vypne, nebo s následujícím stavem:

| Stav WS | Popis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Klient pro odesílatele ukončí připojení.                                    |
| 1001      | Hybridní připojení cesty byla odstraněna nebo zakázána.                        |
| 1008      | Vypršela platnost tokenu zabezpečení, proto porušení zásad autorizace. |
| 1011      | Došlo k chybě ve službě.                                            |

##### <a name="rejecting-the-socket"></a>Odmítat soket

 Odmítat po kontroly soket `accept` zpráva vyžaduje podobné metody handshake tak, aby stavový kód a popis stavu komunikaci z důvodu pro zamítnutí můžete toku zpět do odesílatele.

 Protokol zvolit tento návrh tady je použít ověření typu handshake protokolu WebSocket (která je určená končit definované chybový stav), aby implementacích klienta naslouchací proces můžete nadále závisí na protokolu WebSocket klienta a není nutné využívat navíc, úplné klienta HTTP.

 Pokud chcete odmítnout soket, klient přebírá adresu URI z `accept` zprávu a připojí dvě parametrů řetězce dotazu, následujícím způsobem:

| Param                   | Požaduje se | Popis                              |
| ----------------------- | -------- | ---------------------------------------- |
| SB. hc statusCode        | Ano      | Číselné stavový kód HTTP.                |
| SB. hc Popis_stavu | Ano      | Lidské čitelný důvod zamítnutí. |

Výsledný URI se pak používá k navázání připojení protokolu WebSocket.

Při dokončení správně, tato metoda handshake záměrně selže, s kódem chyby protokolu HTTP 410, protože žádné protokolu WebSocket. Pokud se něco pokazí, následující kódy popisují chybu:

| Kód | Chyba          | Popis                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Zakázáno      | Adresa URL není platný.                |
| 500  | Vnitřní chyba | Došlo k chybě ve službě. |

#### <a name="request-message"></a>Zpráva požadavku

`request` Zprávy pomocí služby naslouchací proces přes řídicí kanál. Stejná zpráva odeslána také přes setkání po provedení volby protokolu WebSocket.

`request` Se skládá ze dvou částí: snímky textu záhlaví ani binární kód.
Pokud není k dispozici žádný text, byly vynechány rámce textu. Zda je přítomen text je logickou hodnotu `body` vlastnost ve zprávě požadavku.

Pro žádosti o se obsah žádosti strukturu může vypadat takto:

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

Naslouchací proces musí zpracování přijetí textu požadavku rozdělit do několika binární rámce (viz [WebSocket fragmenty](https://tools.ietf.org/html/rfc6455#section-5.4)).
Žádost skončí, když byla přijata binární rámce s nastaveným příznakem najít.

Pro žádost o bez text je pouze jeden textového rámečku.

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

* **Adresa** -řetězec URI. Toto je potkávací adresu chcete použít pro tento požadavek. Pokud příchozí požadavek je větší než 64 kB, zbývající část tato zpráva je prázdné a klient musí inicializovat ekvivalentní k provádění metody handshake potkávací `accept` operace popsané dole. Služba pak přesuňte kompletní `request` na zavedených websocket. Pokud odpověď je možné očekávat delší než 64 kB, naslouchací proces musí také zahájit potkávací handshake a pak odpověď přenos přes zavedených websocket.
* **ID** – řetězec. Jedinečný identifikátor pro tento požadavek.
* **requestHeaders** – tento objekt obsahuje všechny hlavičky protokolu HTTP, které nejsou zadané ke koncovému bodu odesílatel, s výjimkou autorizace informace popsané [výše](#request-operation)a hlaviček, které se týkají výhradně připojení s bránou. Konkrétně všechny hlavičky definované nebo vyhrazená v [RFC7230](https://tools.ietf.org/html/rfc7230), s výjimkou `Via`, se odstraní a není předaných dál:

  * `Connection` (RFC7230, část 6.1)
  * `Content-Length`  (RFC7230, část 3.3.2)
  * `Host`  (RFC7230, část 5.4)
  * `TE`  (RFC7230, část 4.3)
  * `Trailer`  (RFC7230, část 4.4)
  * `Transfer-Encoding`  (RFC7230, část 3.3.1)
  * `Upgrade` (RFC7230, část 6.7)
  * `Close`  (RFC7230, bod 8.1)

* **requestTarget** – řetězec. Tato vlastnost obsahuje ["Žádost cíle" (RFC7230, část 5.3)](https://tools.ietf.org/html/rfc7230#section-5.3) požadavku. To zahrnuje části řetězce dotazu, který je odstraní a všech `sb-hc-` předponu parametry.
* **Metoda** -řetězec. Toto je metoda žádosti, za [RFC7231, část 4](https://tools.ietf.org/html/rfc7231#section-4). `CONNECT` Metoda musí být nepoužívá.
* **text** – logická hodnota. Určuje, zda se řídí jeden víc binární rámce textu.

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

##### <a name="responding-to-requests"></a>Reagovat na požadavky

Příjemce musí odpovídat. Opakované reagovat na požadavky při zachování připojení může dojít v naslouchací proces získávání zakázáno.

Odpovědi mohou být odeslány v libovolném pořadí, ale každý požadavek musí být odpověděl během 60 sekund nebo doručení se ohlásí jako nevyhovující. Dokud se počítá konečného termínu 60 sekund `response` rámce byla přijata službou. Odpověď probíhající s více binární snímků se nemůže stát nečinné po dobu více než 60 sekund nebo je ukončen.

Pokud byl přijat požadavek přes řídicí kanál odpovědi musí se poslat buď na řídicím kanálu z kde byl přijat požadavek nebo musí se poslat přes kanál potkávací.

Odpověď je objekt JSON s názvem "odpověď". Pravidla pro zpracování textu obsahu jsou stejně jako s `request` zpráva a na základě `body` vlastnost.

* **ID žádosti** – řetězec. VYŽADUJE SE. `id` Hodnota vlastnosti `request` odpověděla zprávy.
* **statusCode** – číslo. VYŽADUJE SE. číselné kód stavu HTTP, který určuje výsledek oznámení. Všechny kódy stavu [RFC7231, část 6](https://tools.ietf.org/html/rfc7231#section-6) jsou povoleny, s výjimkou [502 "Chybná brána"](https://tools.ietf.org/html/rfc7231#section-6.6.3) a [504 "limitu brány"](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **Popis_stavu** -řetězec. VOLITELNÝ PARAMETR. Frázi důvodu stavový kód HTTP za [RFC7230, bodu 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** – hlavičky HTTP ve externí odpověď HTTP.
  Stejně jako u `request`, RFC7230 definované hlavičky musí být nepoužívá.
* **text** – logická hodnota. Určuje, zda binární textu snímky follow(s).

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

##### <a name="responding-via-rendezvous"></a>Odpovídá prostřednictvím potkávací

Pro odpovědi, které překročit 64 kB musí doručit odpovědi přes potkávací soketu. Také v případě, že tato žádost překračuje 64 kB a `request` obsahuje pouze pole adresy je nutné vytvořit soket potkávací získat `request`. Po vytvoření soketu potkávací odpovědi na příslušném klientovi a následné žádosti z tohoto příslušného klienta musí doručit přes soketu potkávací při zůstává.

`address` Adresu URL v `request` je nutné použít jako-je pro vytvoření soketu potkávací, ale obsahuje následující parametry:

| Parametr      | Požaduje se | Popis
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ano      | Pro příjem soketu, musí být parametr `sb-hc-action=request`

Pokud dojde k chybě, můžete službu odpovědět následujícím způsobem:

| Kód | Chyba           | Popis
| ---- | --------------- | -----------------------------------
| 400  | Neplatný požadavek | Nerozpoznaný akce nebo adresa URL není platná.
| 403  | Zakázáno       | Adresa URL vypršela platnost.
| 500  | Vnitřní chyba  | Došlo k chybě ve službě

 Po navázání připojení k vypnutí serveru protokol WebSocket při vypnutí soketu klienta HTTP, nebo s následujícím stavem:

| Stav WS | Popis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Klient pro odesílatele ukončí připojení.                                    |
| 1001      | Hybridní připojení cesty byla odstraněna nebo zakázána.                        |
| 1008      | Vypršela platnost tokenu zabezpečení, proto porušení zásad autorizace. |
| 1011      | Došlo k chybě ve službě.                                            |


#### <a name="listener-token-renewal"></a>Naslouchací proces obnovení tokenu

Pokud token naslouchací proces vyprší, ho můžete nahradit odesláním textovou zprávu rámce ke službě prostřednictvím zavedených řídicí kanál. Zpráva obsahuje objekt JSON s názvem `renewToken`, která v tuto chvíli definuje následující vlastnost:

* **token** – přístup k službě sběrnice sdílené token platný, kódovaná adresou URL pro obor názvů nebo hybridní připojení, která uděluje **naslouchání** správné.

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Pokud se nezdaří ověření tokenu, byl odepřen přístup a cloudové služby zavře řídicí kanál protokolu WebSocket s chybou. V opačném případě nepřijde žádná odpověď.

| Stav WS | Popis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Vypršela platnost tokenu zabezpečení, proto porušení zásad autorizace. |

### <a name="web-socket-connect-protocol"></a>Připojení protokolu Websocket

Protokol odesílatele je prakticky shodné s způsob, jakým se vytvořit naslouchací proces.
Cílem je maximální průhlednost pro začátku do konce protokolu WebSocket. Adresu pro připojení k je stejné jako naslouchací proces, ale liší "action" a token musí různých oprávnění:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

_Obor názvů adresy_ je plně kvalifikovaný název domény předávání přes Azure oboru názvů, který je hostitelem hybridní připojení, obvykle ve formátu `{myname}.servicebus.windows.net`.

Žádost může obsahovat libovolné další hlavičky protokolu HTTP, včetně těch definované aplikací. Všechny zadané hlavičky tok naslouchací proces a nachází se na `connectHeader` objekt **přijmout** řídicí zpráva.

Možnosti parametr řetězce dotazu jsou následující:

| Param          | Povinné? | Popis
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Ano       | Pro roli odesílatele, musí být parametr `sb-hc-action=connect`.
| `{path}`       | Ano       | (viz odstavec)
| `sb-hc-token`  | Ano\*     | Naslouchací proces musíte zadat platný, kódovaná adresou URL služby sběrnice sdíleného přístupu Token pro obor názvů nebo hybridní připojení, která uděluje **odeslat** správné.
| `sb-hc-id`     | Ne        | Volitelné ID, které umožňuje začátku do konce diagnostické trasování a je k dispozici pro naslouchací proces během metody handshake přijmout.

 `{path}` Je cesta oboru názvů kódovaná adresou URL předkonfigurované hybridní připojení, na které chcete zaregistrovat toto naslouchací proces. `path` Výraz může být rozšířena příponu a řetězcového výrazu dotazu pro další komunikaci. Pokud hybridní připojení je registrován v cestě `hyco`, `path` výraz může být `hyco/suffix?param=value&...` a potom podle parametrů řetězce dotazu, který je zde definované. Výraz dokončení může být pak následujícím způsobem:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

`path` Výraz předána do naslouchací proces adresu URI, které jsou obsažené v řídicí zpráva "přijmout".

Pokud připojení protokolu WebSocket nezdaří z důvodu cesta hybridní připojení není zaregistrovaný, token neplatný nebo chybějící nebo některé jiné chyby, poskytnutí zpětné vazby chyba pomocí regulárních zpětné vazby modelu stav HTTP 1.1. Popis stavu obsahuje chybu sledování ID, které mohou být předávány na pracovníky podpory Azure:

| Kód | Chyba          | Popis
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nenalezené      | Hybridní připojení cesta je neplatná nebo je špatná základní adresa URL.
| 401  | Neautorizováno   | Token zabezpečení je chybějící nebo poškozený nebo neplatný.
| 403  | Zakázáno      | Token zabezpečení není platný pro tuto cestu a pro tuto akci.
| 500  | Vnitřní chyba | Došlo k chybě ve službě.

Pokud připojení protokolu WebSocket záměrně ukončení služby po počátečním nastavení se to tak informace se předávají pomocí odpovídající kód chyby protokolu WebSocket společně s popisný chybová zpráva, která zahrnuje i ID sledování

| Stav WS | Popis
| --------- | ------------------------------------------------------------------------------- 
| 1000      | Naslouchací proces vypnout soketu.
| 1001      | Hybridní připojení cesty byla odstraněna nebo zakázána.
| 1008      | Vypršela platnost tokenu zabezpečení, proto porušení zásad autorizace.
| 1011      | Došlo k chybě ve službě.

### <a name="http-request-protocol"></a>Žádost protokolu HTTP

Žádost protokolu HTTP umožňuje libovolné žádosti HTTP, s výjimkou upgrady protokolu.
Požadavky HTTP jsou odkazoval na adrese regulární runtime entity, bez zaváděcí $hc, který se používá pro hybridní připojení klientů protokolu WebSocket.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

_Obor názvů adresy_ je plně kvalifikovaný název domény předávání přes Azure oboru názvů, který je hostitelem hybridní připojení, obvykle ve formátu `{myname}.servicebus.windows.net`.

Žádost může obsahovat libovolné další hlavičky protokolu HTTP, včetně těch definované aplikací. Všechny zadané hlavičky, s výjimkou těch, které přímo definované v RFC7230 (najdete v části [zprávu požadavku](#Request message)) tok naslouchací proces a nachází se na `requestHeader` objekt **požadavku** zprávy.

Možnosti parametr řetězce dotazu jsou následující:

| Param          | Povinné? | Popis
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Ano\*     | Naslouchací proces musíte zadat platný, kódovaná adresou URL služby sběrnice sdíleného přístupu Token pro obor názvů nebo hybridní připojení, která uděluje **odeslat** správné.

Token se dá taky provést buď `ServiceBusAuthorization` nebo `Authorization` hlavičky protokolu HTTP. Token lze vynechat, pokud hybridní připojení nakonfigurován tak, aby anonymních požadavků.

Protože služba efektivně funguje jako proxy server, i když ne jako true server proxy protokolu HTTP, buď přidá `Via` záhlaví nebo označí existující `Via` hlavičky, které jsou kompatibilní s [RFC7230, část 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
Přidá název hostitele předávání obor názvů pro službu `Via`.

| Kód | Zpráva  | Popis                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | Žádost byla zpracována podle alespoň jeden naslouchací proces.  |
| 202  | Přijato | Požadavek byl přijat podle alespoň jeden naslouchací proces. |

Pokud dojde k chybě, můžete službu odpovědět následujícím způsobem. Jestli odpověď pochází ze služby nebo z naslouchací proces lze identifikovat pomocí přítomnosti `Via` záhlaví. Pokud se nachází záhlaví, odpověď je z naslouchací proces.

| Kód | Chyba           | Popis
| ---- | --------------- |--------- |
| 404  | Nenalezené       | Hybridní připojení cesta je neplatná nebo je špatná základní adresa URL.
| 401  | Neautorizováno    | Token zabezpečení je chybějící nebo poškozený nebo neplatný.
| 403  | Zakázáno       | Token zabezpečení není platný pro tuto cestu a pro tuto akci.
| 500  | Vnitřní chyba  | Došlo k chybě ve službě.
| 503  | Chybná brána     | Požadavek nelze směrován na všechny naslouchací proces.
| 504  | Vypršel časový limit brány | Požadavek byl směrován na naslouchací proces, ale naslouchací proces nepotvrdil přijetí v požadované době.

## <a name="next-steps"></a>Další postup

* [Přenos – nejčastější dotazy](relay-faq.md)
* [Vytvoření oboru názvů](relay-create-namespace-portal.md)
* [Začínáme s .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Začínáme s aplikací Node](relay-hybrid-connections-node-get-started.md)

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
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 1979746d143dbf8c3f4bca3f9a3a7925fe8e3f0d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure hybridní připojení předávací protokol
Předávání přes Azure je jedním z klíčů schopností pilíře na platformě Azure Service Bus. Nové *hybridní připojení* funkce předávání je zabezpečený, otevřete protokol evolution na základě protokolu HTTP a objekty WebSockets. Nahrazuje dřívějším, stejně jako s názvem *BizTalk Services* funkce, který byl postavený na vlastnickým protokolem foundation. Integrace hybridní připojení do Azure App Services budou nadále fungovat jako-je.

Hybridní připojení umožňuje obousměrnou, binární datový proud komunikaci mezi dvěma síťových aplikací, během které může být buď nebo obou stran umístěn za zařízení NAT nebo brány firewall. Tento článek popisuje interakce na straně klienta s předávání hybridní připojení pro připojení klientů v naslouchací proces a odesílatele rolí a jak naslouchací procesy přijímat nová připojení.

## <a name="interaction-model"></a>Interakce modelu
Předávání hybridní připojení dvě strany připojí tím, že poskytuje bod potkávací v cloudu Azure, která obě strany můžete zjistit a připojte se k z hlediska vlastní síť. Tento bod potkávací se nazývá "Hybridní připojení" v tomto a dalších dokumentaci rozhraní API a také na portálu Azure. Koncový bod služby hybridní připojení se označuje jako "služba" pro zbývající část tohoto článku. Model interakce leans na klasifikace vymezenému mnoho jiná síťová rozhraní API.

Je naslouchací proces, který nejprve znamená připravenosti pro zpracování příchozí připojení a následně je přijímá po doručení. Na druhé straně se připojujícího klienta, který nabízí připojení pro naslouchací proces, očekává se toto připojení pro navázání obousměrné komunikační cestu.
"Připojit", "Naslouchání" a "Přijmout" jsou stejné podmínky, které můžete najít v většina soketu rozhraní API.

Všechny přenosu komunikační model má obě strany, které odchozí připojení ke koncovému bodu služby, takže je "naslouchací proces" i "client" v obecné použití a může také způsobit další terminologie přetížení. Přesné technologiím, které jsme proto použít pro hybridní připojení je následující:

Programy na obou stranách připojení se nazývají "klienty,", vzhledem k tomu, že jsou klienti ke službě. Klienta, která čeká na a přijímá připojení je "naslouchací proces", nebo je uvedená v "naslouchací proces role." Klienta, který iniciuje nové připojení směrem naslouchací proces prostřednictvím služby se označuje jako "sender", nebo je v "odesílatele role."

### <a name="listener-interactions"></a>Naslouchací proces interakce
Naslouchací proces má čtyři interakce s službu; všechny podrobnosti přenosová jsou popsané dále v tomto článku v části odkaz.

#### <a name="listen"></a>Naslouchání
K označení připravenosti služby, který naslouchací proces je připraven přijmout připojení, vytvoří odchozí připojení protokolu WebSocket. Metoda handshake připojení stejný název hybridní připojení nakonfigurovaná na obor názvů předávání a token zabezpečení, která uděluje "Naslouchání" přímo na tento název.
Když protokol WebSocket je přijatá službou, registrace je dokončena a zavedených protokolu WebSocket se ukládají jako "řídicí kanál" pro povolení všechny následné interakce zachování připojení. Služba umožňuje až pro 25 souběžných moduly pro naslouchání na hybridní připojení. Pokud existují dvě nebo více active naslouchací procesy, jsou mezi nimi rozložit příchozí připojení v náhodném pořadí; správného distribuční není zaručena.

#### <a name="accept"></a>Přijmout
Když odesílatele otevře nové připojení na službu, službu vybere a upozorní jednu aktivní naslouchacího procesu na hybridní připojení. Toto oznámení se odesílá do naslouchací proces kanálem otevřít řídicí jako zprávu JSON obsahující adresu URL koncového bodu protokolu WebSocket, který naslouchací proces musí připojit k, aby přijímal připojení.

Adresu URL můžete a musí používat přímo naslouchací proces bez další zátěže.
Kódovaného informace je platná pouze na krátkou dobu běhu v podstatě po dobu, odesílatel je ochotná počkejte pro připojení k být navázáno začátku do konce, ale až do maximálního počtu 30 sekund. Adresu URL můžete použít pouze pro jeden úspěšného pokusu o připojení. Po vytvoření připojení protokolu WebSocket s adresou URL potkávací všechny další aktivity na tento protokol WebSocket je přes předávací službu z a do odesílatele, bez zásahu nebo interpretace službou.

#### <a name="renew"></a>Obnovit
Token zabezpečení, která bude použita k registraci naslouchací proces a udržovat řídicí kanál může vyprší během naslouchací proces je aktivní. Vypršení platnosti tokenu nemá vliv na probíhající připojení, ale způsobit řídicí kanál přeruší službou v nebo krátce po v okamžiku vypršení platnosti. Operace "obnovit" je zprávu JSON, který naslouchací proces může odesílat nahradit token přidružené řídicí kanál, takže řídicí kanál je možné udržovat po delší dobu.

#### <a name="ping"></a>Ping
Je-li řídicí kanál po dlouhou dobu nečinnosti, může drop prostředníci na cestě, jako jsou nástroje pro vyrovnávání zatížení nebo zařízení NAT, připojení TCP. Operace "ping", zabraňuje odesláním malé množství dat, na který upozorní všem uživatelům v síti trasy, která je určené připojení jako aktivní, a slouží také jako "živé" testu pro naslouchací proces kanálu. V případě selhání příkazu ping řídicí kanál by měl být považován za nepoužitelný a naslouchací proces by měl znovu připojit.

### <a name="sender-interaction"></a>Odesílatel interakce
Odesílatel má jenom jeden interakci se službou: připojení.

#### <a name="connect"></a>Připojení
Operace "připojit" otevře protokolu WebSocket ve službě, poskytnou jméno hybridní připojení a (volitelně, ale vyžaduje ve výchozím nastavení) token zabezpečení jejichž základě lze oprávnění "Odeslat" v řetězci dotazu. Služba potom komunikuje s naslouchací proces ve způsobu, jakým popsané a naslouchací proces vytvoří potkávací připojení, který je spojen s Tento protokolu WebSocket. Po přijetí protokol WebSocket, jsou všechny další interakce na tomto protokolu WebSocket připojené naslouchací proces.

### <a name="interaction-summary"></a>Interakce souhrn
Výsledkem tohoto modelu interakce je, že klienta odesílatele pochází z metody handshake se "čistou" WebSocket, která je připojená k naslouchací proces a potřebného žádné další preambles nebo přípravy. Tento model umožňuje prakticky jakékoli existující implementace klienta protokolu WebSocket snadno využívat výhod hybridních připojení služby zadáním adresy URL správně vytvořená do jejich vrstvy klienta protokolu WebSocket.

Potkávací připojení protokolu WebSocket, který získá naslouchací proces prostřednictvím přijmout interakce je taky čistou a můžete předat všechny existující server implementaci protokolu WebSocket pomocí některé minimální navíc abstrakce, která rozlišuje mezi operace hybridní připojení vzdálené "přijmout" a "přijmout" operace v místní síti jejich framework naslouchací procesy.

## <a name="protocol-reference"></a>Referenční informace o protokolu

Tato část popisuje podrobnosti protokolu interakce popsané.

Všechna připojení protokolu WebSocket probíhají na portu 443 jako upgrade z verze 1.1 HTTPS, který je obvykle abstrahované některé protokolu WebSocket framework nebo rozhraní API. Popis tady je udržováno implementace neutrální, bez návrhy konkrétní rozhraní.

### <a name="listener-protocol"></a>Naslouchací proces protokolu
Naslouchací proces protokolu se skládá z dvě připojení gesta a tři operace zpráv.

#### <a name="listener-control-channel-connection"></a>Naslouchací proces připojení kanálu ovládací prvek
Vytvoření připojení protokolu WebSocket k otevřel řídicí kanál:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...
```

`namespace-address` Je plně kvalifikovaný název domény předávání přes Azure oboru názvů, který je hostitelem hybridní připojení, obvykle ve formátu `{myname}.servicebus.windows.net`.

K dispozici jsou následující možnosti parametr řetězce dotazu.

| Parametr | Požaduje se | Popis |
| --- | --- | --- |
| `sb-hc-action` |Ano |Pro roli naslouchacího procesu musí být parametr **sb-hc-action = naslouchání** |
| `{path}` |Ano |Obor názvů kódovaná adresou URL cesta předkonfigurované hybridní připojení k registraci této naslouchací proces na. Tento výraz se připojí k pevné `$hc/` část adresy obsahující cestu. |
| `sb-hc-token` |Ano\* |Naslouchací proces musíte zadat platný, kódovaná adresou URL služby sběrnice sdíleného přístupu Token pro obor názvů nebo hybridní připojení, která uděluje **naslouchání** správné. |
| `sb-hc-id` |Ne |Toto volitelné ID klienta zadaný umožňuje začátku do konce diagnostické trasování. |

Pokud připojení protokolu WebSocket nezdaří z důvodu cesta hybridní připojení není zaregistrované, nebo token neplatný nebo chybějící nebo některé jiné chyby, poskytnutí zpětné vazby chyba pomocí regulárních zpětné vazby modelu stav HTTP 1.1. Popis stav obsahuje chyby sledování id, které může být oznamovat pracovníky technické podpory Azure:

| Kód | Chyba | Popis |
| --- | --- | --- |
| 404 |Nenalezené |Hybridní připojení cesta je neplatná nebo je špatná základní adresa URL. |
| 401 |Neautorizováno |Token zabezpečení je chybějící nebo poškozený nebo neplatný. |
| 403 |Zakázáno |Token zabezpečení není platný pro tuto cestu pro tuto akci. |
| 500 |Vnitřní chyba |Došlo k chybě ve službě. |

Pokud připojení protokolu WebSocket záměrně ukončení služby po začátku bylo nastaveno tak to tak informace se předávají pomocí odpovídající kód chyby protokolu WebSocket společně s popisný chybová zpráva, která zahrnuje i ID sledování Služba nebude vypnuta řídicí kanál bez zjištění chybový stav. Všechny čistého vypnutí je klient řídí.

| Stav WS | Popis |
| --- | --- |
| 1001 |Hybridní připojení cesty byla odstraněna nebo zakázána. |
| 1008 |Vypršela platnost tokenu zabezpečení, proto porušení zásad autorizace. |
| 1011 |Došlo k chybě ve službě. |

### <a name="accept-handshake"></a>Přijměte metody handshake
Oznámení "přijmout" posílá službou naslouchací proces prostřednictvím administrate řídicí kanál jako zprávy JSON v objektu WebSocket. Nepřijde žádná odpověď na tuto zprávu.

Zpráva obsahuje objekt JSON s názvem "přijmout,", který definuje následující vlastnosti v tuto chvíli:

* **Adresa** – řetězce adresy URL, který se má použít k vytvoření objektu WebSocket ke službě přijmout příchozí připojení.
* **ID** – jedinečný identifikátor pro toto připojení. Pokud byl klient odesílatele ID, jedná se o odesílateli zadat hodnotu, jinak bude generována hodnota.
* **connectHeaders** – všechny hlavičky protokolu HTTP, zadaných ke koncovému bodu předávání odesílatelem, také zahrnující protokol WebSocket sekundu a hlavičky Sec. WebSocket rozšíření.

#### <a name="accept-message"></a>Přijmout zprávu

```json
{                                                           
    "accept" : {
        "address" : "wss://168.61.148.205:443/$hc/{path}?..."    
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

#### <a name="accepting-the-socket"></a>Přijetí soket
Přijmout, vytvoří naslouchací proces připojení protokolu WebSocket na zadané adresy.

Pokud zpráva "přijmout" představuje `Sec-WebSocket-Protocol` záhlaví, očekává se, že naslouchací proces lze použít pouze protokol WebSocket pokud ji podporuje tento protokol. Kromě toho nastaví hlavičku, jako je vytvoření objektu WebSocket.

Totéž platí i pro `Sec-WebSocket-Extensions` záhlaví. Pokud rozhraní podporuje rozšíření, je potřeba nastavit hlavičku do odpovědi serverové požadovaného `Sec-WebSocket-Extensions` metody handshake pro rozšíření.

Adresa URL je nutné použít jako-je pro vytvoření soketu přijmout, ale obsahuje následující parametry:

| Parametr | Požaduje se | Popis |
| --- | --- | --- |
| `sb-hc-action` |Ano |Pro příjem soketu, musí být parametr `sb-hc-action=accept` |
| `{path}` |Ano |(viz odstavec) |
| `sb-hc-id` |Ne |Viz popis v předchozí **id**. |

`{path}` je cesta k oboru názvů kódovaná adresou URL předkonfigurované hybridní připojení, na které chcete zaregistrovat toto naslouchací proces. Tento výraz se připojí k pevné `$hc/` část adresy obsahující cestu. 

`path` Výraz může být rozšířena příponu a výrazu řetězec dotazu, který následuje po dělicí lomítkem zaregistrovaný název. To umožňuje klientovi odesílatele předání argumentů odesílání do přijímá naslouchací proces, když není možné zahrnout hlavičky protokolu HTTP. Očekávání je, že rozhraní naslouchací proces analyzuje část adresy obsahující pevné cestu a název registrované z cesty a umožňuje zbývající, které by mohly mít bez argumentů řetězce dotazu předponu `sb-`, k dispozici pro aplikace pro rozhodnutí, jestli se tak, aby přijímal připojení.

Další informace najdete v části následující (odesílatel Protocol).

Pokud dojde k chybě, můžete službu odpovědět následujícím způsobem:

| Kód | Chyba | Popis |
| --- | --- | --- |
| 403 |Zakázáno |Adresa URL není platný. |
| 500 |Vnitřní chyba |Došlo k chybě ve službě |

Po navázání připojení k serveru protokol WebSocket vypne, jestliže odesílatel protokolu WebSocket vypne, nebo s následujícím stavem:

| Stav WS | Popis |
| --- | --- |
| 1001 |Klient pro odesílatele ukončí připojení. |
| 1001 |Hybridní připojení cesty byla odstraněna nebo zakázána. |
| 1008 |Vypršela platnost tokenu zabezpečení, proto porušení zásad autorizace. |
| 1011 |Došlo k chybě ve službě. |

#### <a name="rejecting-the-socket"></a>Odmítat soket
Podobné metody handshake odmítat soketu po kontrole zpráva "přijmout" vyžaduje, aby stavový kód a popis stavu komunikace důvod zamítnutí můžete procházet zpět do odesílatele.

Protokol zvolit tento návrh tady je použít ověření typu handshake protokolu WebSocket (která je určená končit definované chybový stav), aby implementacích klienta naslouchací proces můžete nadále závisí na protokolu WebSocket klienta a není nutné využívat navíc, úplné klienta HTTP.

Klient tak, aby zamítal soket, přebírá adresu URI ze zprávy "přijmout" a připojí dvě parametrů řetězce dotazu, následujícím způsobem:

| Param | Požaduje se | Popis |
| --- | --- | --- |
| statusCode |Ano |Číselné stavový kód HTTP. |
| Popis_stavu |Ano |Lidské čitelný důvod zamítnutí. |

Výsledný URI se pak používá k navázání připojení protokolu WebSocket.

Při dokončení správně, tato metoda handshake záměrně selže, s kódem chyby protokolu HTTP 410, protože žádné protokolu WebSocket. Pokud se něco pokazí, následující kódy popisují chybu:

| Kód | Chyba | Popis |
| --- | --- | --- |
| 403 |Zakázáno |Adresa URL není platný. |
| 500 |Vnitřní chyba |Došlo k chybě ve službě. |

### <a name="listener-token-renewal"></a>Naslouchací proces obnovení tokenu
Pokud token naslouchací proces vyprší, naslouchací proces můžete nahradit odesláním textovou zprávu rámce ke službě prostřednictvím zavedených řídicí kanál. Zpráva obsahuje objekt JSON s názvem `renewToken`, která v tuto chvíli definuje následující vlastnost:

* **token** – přístup k službě sběrnice sdílené token platný, kódovaná adresou URL pro obor názvů nebo hybridní připojení, která uděluje **naslouchání** správné.

#### <a name="renewtoken-message"></a>zpráva renewToken

```json
{                                                                                                                                                                        
    "renewToken" : {                                                                                                                                                      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"  
    }                                                                                                                                                                     
}
```

Pokud se nezdaří ověření tokenu, byl odepřen přístup a cloudové služby zavře řídicí kanál protokolu WebSocket s chybou. V opačném případě nepřijde žádná odpověď.

| Stav WS | Popis |
| --- | --- |
| 1008 |Vypršela platnost tokenu zabezpečení, proto porušení zásad autorizace. |

## <a name="sender-protocol"></a>Odesílatel protokolu
Protokol odesílatele je prakticky shodné s způsob, jakým se vytvořit naslouchací proces.
Cílem je maximální průhlednost pro začátku do konce protokolu WebSocket. Adresu pro připojení k je stejné jako naslouchací proces, ale liší "action" a token musí různých oprávnění:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

*Obor názvů adresy* je plně kvalifikovaný název domény předávání přes Azure oboru názvů, který je hostitelem hybridní připojení, obvykle ve formátu `{myname}.servicebus.windows.net`.

Žádost může obsahovat libovolné další hlavičky protokolu HTTP, včetně těch definované aplikací. Všechny zadané hlavičky tok naslouchací proces a nachází se na `connectHeader` objekt **přijmout** řídicí zpráva.

Možnosti parametr řetězce dotazu jsou následující:

| Param | Povinné? | Popis |
| --- | --- | --- |
| `sb-hc-action` |Ano |Pro roli odesílatele, musí být parametr `action=connect`. |
| `{path}` |Ano |(viz odstavec) |
| `sb-hc-token` |Ano\* |Naslouchací proces musíte zadat platný, kódovaná adresou URL služby sběrnice sdíleného přístupu Token pro obor názvů nebo hybridní připojení, která uděluje **odeslat** správné. |
| `sb-hc-id` |Ne |Volitelné ID, které umožňuje začátku do konce diagnostické trasování a je k dispozici pro naslouchací proces během metody handshake přijmout. |

`{path}` Je cesta oboru názvů kódovaná adresou URL předkonfigurované hybridní připojení, na které chcete zaregistrovat toto naslouchací proces. `path` Výraz může být rozšířena příponu a řetězcového výrazu dotazu pro další komunikaci. Pokud hybridní připojení je registrován v cestě `hyco`, `path` výraz může být `hyco/suffix?param=value&...` a potom podle parametrů řetězce dotazu, který je zde definované. Výraz dokončení může být pak následujícím způsobem:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

`path` Výraz předána do naslouchací proces adresu URI, které jsou obsažené v řídicí zpráva "přijmout".

Pokud připojení protokolu WebSocket nezdaří z důvodu cesta hybridní připojení není zaregistrovaný, token neplatný nebo chybějící nebo některé jiné chyby, poskytnutí zpětné vazby chyba pomocí regulárních zpětné vazby modelu stav HTTP 1.1. Popis stavu obsahuje chybu sledování ID, které mohou být předávány na pracovníky podpory Azure:

| Kód | Chyba | Popis |
| --- | --- | --- |
| 404 |Nenalezené |Hybridní připojení cesta je neplatná nebo je špatná základní adresa URL. |
| 401 |Neautorizováno |Token zabezpečení je chybějící nebo poškozený nebo neplatný. |
| 403 |Zakázáno |Token zabezpečení není platný pro tuto cestu a pro tuto akci. |
| 500 |Vnitřní chyba |Došlo k chybě ve službě. |

Pokud připojení protokolu WebSocket záměrně ukončení služby po počátečním nastavení se to tak informace se předávají pomocí odpovídající kód chyby protokolu WebSocket společně s popisný chybová zpráva, která zahrnuje i ID sledování

| Stav WS | Popis |
| --- | --- |
| 1000 |Naslouchací proces vypnout soketu. |
| 1001 |Hybridní připojení cesty byla odstraněna nebo zakázána. |
| 1008 |Vypršela platnost tokenu zabezpečení, proto porušení zásad autorizace. |
| 1011 |Došlo k chybě ve službě. |

## <a name="next-steps"></a>Další postup
* [Přenos – nejčastější dotazy](relay-faq.md)
* [Vytvoření oboru názvů](relay-create-namespace-portal.md)
* [Začínáme s .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Začínáme s aplikací Node](relay-hybrid-connections-node-get-started.md)


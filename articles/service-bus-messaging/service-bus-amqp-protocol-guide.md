---
title: AMQP 1.0 v Průvodci protokoly Azure Service Bus a Event Hubs | Dokumenty společnosti Microsoft
description: Průvodce protokolem pro výrazy a popis AMQP 1.0 v Azure Service Bus a event huby
services: service-bus-messaging,event-hubs
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: d2d3d540-8760-426a-ad10-d5128ce0ae24
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: d706e9b3351b0693a1f352e15b6b9b0cc5c7a65d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086143"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1.0 v průvodci protokoly Azure Service Bus a Event Hubs

Protokol 1.0 o rozšířeném zařazení zpráv do fronty je standardizovaný protokol pro vytváření rámců a přenos pro asynchronní, bezpečný a spolehlivý přenos zpráv mezi dvěma stranami. Jedná se o primární protokol zasílání zpráv služby Azure Service Bus a centra událostí Azure. Obě služby také podporují protokol HTTPS. Proprietární protokol SBMP, který je také podporován, je postupně vyřazen ve prospěch AMQP.

AMQP 1.0 je výsledkem široké průmyslové spolupráce, která spojila dodavatele middlewaru, jako jsou Microsoft a Red Hat, s mnoha uživateli middlewaru pro zasílání zpráv, jako je JP Morgan Chase zastupující odvětví finančních služeb. Technické standardizační fórum pro protokol AMQP a specifikace rozšíření je OASIS a získalo formální schválení jako mezinárodní standard jako ISO /IEC 19494.

## <a name="goals"></a>Cíle

Tento článek stručně shrnuje základní koncepty specifikace zasílání zpráv AMQP 1.0 spolu s malou sadou konceptů specifikací rozšíření, které jsou právě dokončovány v technickém výboru OASIS AMQP, a vysvětluje, jak služba Azure Service Bus implementuje a vychází z těchto specifikací.

Cílem je, aby každý vývojář, který používá libovolný existující zásobník klientů AMQP 1.0 na libovolné platformě, mohl pracovat s Azure Service Bus prostřednictvím AMQP 1.0.

Běžné univerzální zásobníky AMQP 1.0, jako je Apache Proton nebo AMQP.NET Lite, již implementují všechny základní protokoly AMQP 1.0. Tato základní gesta jsou někdy zabalena s rozhraníAPI vyšší úrovně; Apache Proton dokonce nabízí dva, imperativní Messenger API a reaktivní reactor API.

V následující diskusi předpokládáme, že správa připojení AMQP, relací a odkazů a zpracování rámových přenosů a řízení toku jsou zpracovány příslušným zásobníkem (například Apache Proton-C) a nevyžadují mnoho, pokud nějaká konkrétní pozornost od vývojářů aplikací. Abstraktně předpokládáme existenci několika primitiv rozhraní API, jako je schopnost připojení a vytvořit nějakou formu `send()` objektů `receive()` abstrakce *odesílatele* a *příjemce,* které pak mají nějaký tvar a operace.

Při diskusi o pokročilé možnosti Azure Service Bus, jako je procházení zpráv nebo správa relací, tyto funkce jsou vysvětleny v amqp termíny, ale také jako vrstvené pseudo implementace nad tento předpokládané abstrakce rozhraní API.

## <a name="what-is-amqp"></a>Co je AMQP?

AMQP je rámovací a přenosový protokol. Rámování znamená, že poskytuje strukturu pro binární datové proudy, které toku v obou směrech síťového připojení. Struktura poskytuje vymezení pro odlišné bloky dat, nazývané *rámce*, které mají být vyměňovány mezi propojenými stranami. Přenosové schopnosti zajistí, aby obě komunikující strany mohly dosáhnout společného porozumění ohledně toho, kdy budou rámce přeneseny a kdy se převody považují za úplné.

Na rozdíl od dřívějších verzí konceptů vytvořených pracovní skupinou AMQP, které jsou stále používány několika zprostředkovateli zpráv, konečný a standardizovaný protokol AMQP 1.0 nepředepisuje přítomnost zprostředkovatele zpráv nebo jakékoli konkrétní topologie pro subjekty uvnitř zprostředkovatele zpráv.

Protokol lze použít pro symetrickou komunikaci mezi dvěma účastníky, pro interakci s zprostředkovateli zpráv, které podporují fronty a publikují/odebírat entity, jako azure service bus. Lze jej také použít pro interakci s infrastrukturou zasílání zpráv, kde se vzory interakce liší od běžných front, jako je tomu v případě Centra událostí Azure. Centrum událostí se chová jako fronta, když jsou do něj odesílány události, ale funguje spíše jako služba sériového úložiště, když jsou události čteny z něj; to poněkud připomíná páskovou mechaniku. Klient vybere posun do datového proudu k dispozici a pak se zobrazí všechny události z tohoto posunu na nejnovější k dispozici.

Protokol AMQP 1.0 je navržen tak, aby byl rozšiřitelný, což umožňuje další specifikace pro zvýšení jeho schopností. Tři specifikace rozšíření popsané v tomto dokumentu to ilustrují. Pro komunikaci přes existující infrastrukturu HTTPS/WebSockets může být konfigurace nativních portů TCP Protokolu AMQP obtížná. Specifikace vazby definuje, jak vrstvit AMQP přes WebSockets. Pro interakci s infrastrukturou zasílání zpráv způsobem požadavku/odpovědi pro účely správy nebo poskytování pokročilých funkcí definuje specifikace správy AMQP požadované základní základní interakce primitiv. Pro integraci federovaného autorizačního modelu specifikace zabezpečení na základě deklarací identity Definuje, jak přidružit a obnovit tokeny autorizace přidružené k propojením.

## <a name="basic-amqp-scenarios"></a>Základní scénáře AMQP

Tato část vysvětluje základní využití AMQP 1.0 s Azure Service Bus, která zahrnuje vytváření připojení, relací a odkazy a přenos zpráv do a ze služeb Service Bus entity, jako jsou fronty, témata a odběry.

Nejvíce autoritativní zdroj se dozvědět o tom, jak AMQP funguje, je Specifikace AMQP 1.0, ale specifikace byla napsána přesně průvodce implementace a ne učit protokol. Tato část se zaměřuje na zavedení tolik terminologie, jak je potřeba pro popis, jak Service Bus používá AMQP 1.0. Pro komplexnější úvod do AMQP, stejně jako širší diskusi o AMQP 1.0, můžete si prohlédnout [toto video kurz][this video course].

### <a name="connections-and-sessions"></a>Připojení a relace

AMQP volá komunikační programy *kontejnery*; obsahují *uzly*, které jsou komunikující entity uvnitř těchto kontejnerů. Fronta může být takový uzel. AMQP umožňuje multiplexování, takže jedno připojení lze použít pro mnoho komunikačních cest mezi uzly; Klient aplikace může například současně přijímat z jedné fronty a odesílat do jiné fronty přes stejné síťové připojení.

![][1]

Síťové připojení je tedy ukotveno na kontejneru. Je iniciován kontejnerem v roli klienta, který vytváří odchozí připojení soketu TCP ke kontejneru v roli příjemce, který naslouchá a přijímá příchozí připojení TCP. Handshake připojení zahrnuje vyjednávání verze protokolu, deklarování nebo vyjednávání použití zabezpečení úrovně přenosu (TLS/SSL) a ověřování/autorizace handshake v oboru připojení, který je založen na SASL.

Azure Service Bus vyžaduje použití TLS za všech okolností. Podporuje připojení přes port TCP 5671, přičemž připojení TCP je nejprve překryto protokolem TLS před vstupem do protokolu AMQP handshake a také podporuje připojení přes port TCP 5672, přičemž server okamžitě nabízí povinnou inovaci připojení tls pomocí modelu předepsaného AMQP. Vazba AMQP WebSockets vytvoří tunelové propojení přes port TCP 443, který je pak ekvivalentní připojení AMQP 5671.

Po nastavení připojení a TLS nabízí Service Bus dvě možnosti mechanismu SASL:

* SASL PLAIN se běžně používá pro předávání pověření uživatelského jména a hesla na server. Service Bus nemá účty, ale s názvem [Pravidla zabezpečení sdíleného přístupu](service-bus-sas.md), které udělují práva a jsou přidruženy ke klíči. Název pravidla se používá jako uživatelské jméno a klíč (jako základní 64 kódovaný text) se používá jako heslo. Práva spojená s vybraným pravidlem upravují operace povolené pro připojení.
* SASL ANONYMOUS se používá pro vynechání autorizace SASL, když klient chce použít model cbs (na základě deklarací identity), který je popsán později. Pomocí této možnosti připojení klienta lze navázat anonymně na krátkou dobu, během kterého klient může pracovat pouze s koncovým bodem CBS a cbS handshake musí dokončit.

Po navázání přenosového připojení kontejnery každý deklarovat maximální velikost rámce, které jsou ochotni zpracovat a po časový limit nečinnosti budou jednostranně odpojit, pokud není žádná aktivita na připojení.

Také deklarují, kolik souběžných kanálů je podporováno. Kanál je jednosměrná, odchozí, virtuální cesta přenosu nad připojením. Relace trvá kanál z každého propojeného kontejneru tvořit obousměrnou komunikační cestu.

Relace mají model řízení toku na základě okna; Při vytvoření relace každá strana deklaruje, kolik rámců je ochotna přijmout do svého okna příjmu. Jako strany vyměňovat rámce, přenesené rámce vyplnit toto okno a převody zastavit, když okno je plná a dokud se okno resetnebo expanduje pomocí *toku performative* *(performative* je amqp termín pro gesta na úrovni protokolu vyměňované mezi dvěma stranami).

Tento model založený na okně je zhruba analogický konceptu TCP řízení toku založeného na okně, ale na úrovni relace uvnitř soketu. Protokol je koncept umožňující více souběžných zasedání existuje tak, že vysoká priorita provozu by mohla být spěchal kolem omezené normální provoz, jako na dálnici expresní pruhu.

Azure Service Bus aktuálně používá přesně jednu relaci pro každé připojení. Maximální velikost rámce service bus je 262 144 bajtů (256 K) pro service bus standard a event huby. Je to 1,048,576 (1 MB) pro Service Bus Premium. Service Bus neukládá žádné konkrétní relace na úrovni omezení oken, ale resetuje okno pravidelně jako součást řízení toku na úrovni propojení (viz [další část](#links)).

Připojení, kanály a relace jsou dočasné. Pokud se základní připojení zhroutí, musí být obnovena připojení, tunelové propojení TLS, kontext autorizace SASL a relace.

### <a name="amqp-outbound-port-requirements"></a>Požadavky na odchozí port AMQP

Klienti, kteří používají připojení AMQP přes protokol TCP, vyžadují otevření portů 5671 a 5672 v místní bráně firewall. Spolu s těmito porty může být nutné otevřít další porty, pokud je povolena funkce [EnableLinkRedirect.](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect?view=azure-dotnet) `EnableLinkRedirect`je nová funkce zasílání zpráv, která pomáhá přeskočit one-hop při přijímání zpráv, a tím pomáhá zvýšit propustnost. Klient by začít komunikovat přímo s back-end služby přes rozsah portů 104XX, jak je znázorněno na následujícím obrázku. 

![Seznam cílových přístavů][4]

Klient .NET by se nezdaří s SocketException ("Byl proveden pokus o přístup k soketu způsobem zakázaným jeho přístupová oprávnění"), pokud jsou tyto porty blokovány bránou firewall. Funkci lze zakázat `EnableAmqpLinkRedirect=false` nastavením v řetězci connectiong, který nutí klienty komunikovat se vzdálenou službou přes port 5671.


### <a name="links"></a>Odkazy

AMQP přenáší zprávy přes odkazy. Propojení je komunikační cesta vytvořená prostřednictvím relace, která umožňuje přenos zpráv v jednom směru; jednání o stavu převodu je přes propojení a obousměrné mezi propojenými stranami.

![][2]

Odkazy mohou být vytvořeny buď kontejneru kdykolia a přes existující relace, což amqp liší od mnoha jiných protokolů, včetně HTTP a MQTT, kde zahájení přenosu a přenosu cesty je výhradní privilegium strany, která vytváří připojení zásuvky.

Kontejner iniciující propojení požádá opačný kontejner o přijetí odkazu a zvolí roli odesílatele nebo příjemce. Proto buď kontejner může zahájit vytváření jednosměrné nebo obousměrné komunikační cesty, s druhou modelován jako dvojice odkazů.

Odkazy jsou pojmenovány a přidruženy k uzlům. Jak je uvedeno na začátku, uzly jsou komunikující entity uvnitř kontejneru.

V Service Bus uzel je přímo ekvivalentní fronty, téma, předplatné nebo nedoručené podfronty fronty nebo předplatného. Název uzlu použitý v AMQP je tedy relativní název entity uvnitř oboru názvů Service Bus. Pokud je fronta pojmenována `myqueue`, je to také název uzlu AMQP. Téma odběr následuje http api konvence tím, že jsou seřazeny do "odběry" kolekce prostředků a tedy předplatné **sub** na téma **mytopic** má uzel AMQP název **mytopic/subscriptions/sub**.

Připojující se klient je také nutné použít název místního uzlu pro vytváření odkazů; Service Bus není normativní o tyto názvy uzlů a neinterpretuje je. Balíčky klientů AMQP 1.0 obecně používají schéma k zajištění, že tyto názvy dočasných uzlů jsou jedinečné v oboru klienta.

### <a name="transfers"></a>Převody

Po vytvoření propojení mohou být zprávy přeneseny přes tento odkaz. V AMQP je přenos proveden pomocí explicitního gesta protokolu *(přenos* performative), který přesune zprávu od odesílatele k příjemci přes odkaz. Převod je dokončen, když je "vypořádán", což znamená, že obě strany si vytvořily společné porozumění výsledku tohoto převodu.

![][3]

V nejjednodušším případě může odesílatel odeslat zprávy "předem vyřešené", což znamená, že klient nemá zájem o výsledek a příjemce neposkytuje žádnou zpětnou vazbu o výsledku operace. Tento režim je podporován service bus na úrovni protokolu AMQP, ale není vystaven v žádném z klientských api.

Pravidelné případ je, že zprávy jsou odesílány nevyrovnané a příjemce pak označuje přijetí nebo odmítnutí pomocí *dispozice* performative. K odmítnutí dochází, když příjemce nemůže přijmout zprávu z jakéhokoli důvodu a zpráva o odmítnutí obsahuje informace o důvodu, což je struktura chyb definovaná amqp. Pokud jsou zprávy odmítnuty z důvodu vnitřních chyb uvnitř služby Service Bus, služba vrátí další informace uvnitř této struktury, které lze použít pro poskytování pomocných pomocných pracovníků, pokud podáváte žádosti o podporu. Další podrobnosti o chybách se dozvíte později.

Zvláštní formou odmítnutí je *uvolněný* stav, který naznačuje, že příjemce nemá žádné technické námitky proti převodu, ale také žádný zájem na vypořádání převodu. Tento případ existuje, například když je zpráva doručena klientovi služby Service Bus a klient se rozhodne "opustit" zprávu, protože nemůže provést práci vyplývající ze zpracování zprávy; samotné doručení zprávy není na vině. Variantou tohoto stavu je *změněný* stav, který umožňuje změny zprávy při jeho vydání. Tento stav není v současné době používán službou Service Bus.

Specifikace AMQP 1.0 definuje další dispoziční stav nazývaný *přijatý*, který konkrétně pomáhá zpracovávat obnovení propojení. Obnovení propojení umožňuje rekonstituovat stav propojení a všechny čekající dodávky nad nové připojení a relace, když byly ztraceny předchozí připojení a relace.

Service Bus nepodporuje obnovení propojení; Pokud klient ztratí připojení k service bus s nevyrovnané zprávy přenosu čeká, že přenos zprávy je ztracena a klient musí znovu připojit, obnovit propojení a opakujte přenos.

Jako takový service bus a event huby podporují "alespoň jednou" přenos, kde odesílatel může být zajištěna zpráva, která byla uložena a přijata, ale nepodporují "přesně jednou" převody na úrovni AMQP, kde by se systém pokusil obnovit spojení a pokračovat v jednání o stavu doručení, aby se zabránilo zdvojení přenosu zprávy.

Chcete-li kompenzovat možné duplicitní odešle, Service Bus podporuje vyhledávání duplicit jako volitelnou funkci ve frontách a tématech. Vyhledávání duplicit zaznamenává ID zpráv všech příchozích zpráv během uživatelem definovaného časového okna a poté tiše zahodí všechny zprávy odeslané se stejným ID zprávy během stejného okna.

### <a name="flow-control"></a>Řízení průtoku

Kromě modelu řízení toku na úrovni relace, který byl dříve popsán, má každé propojení svůj vlastní model řízení toku. Řízení toku na úrovni relace chrání kontejner před nutností zpracovávat příliš mnoho rámců najednou, řízení toku na úrovni propojení dává aplikaci na starosti, kolik zpráv chce zpracovat z propojení a kdy.

![][4]

Na propojení může k převodům dojít pouze v případě, že odesílatel má dostatečný *kredit na propojení*. Link credit je čítač nastavený příjemcem pomocí performative *toku,* který je vymezen na odkaz. Když je odesílateli přiřazen kredit odkazu, pokusí se tento kredit využít doručením zpráv. Každé doručení zprávy sníží zbývající kredit propojení o 1. Při vydělání kreditu propojení se dodávky zastaví.

Když service bus je v roli příjemce, okamžitě poskytuje odesílateli dostatek kreditu odkaz, takže zprávy mohou být odeslány okamžitě. Při použití kreditu propojení služba Service Bus příležitostně odešle odesílateli výkonný *tok,* aby aktualizoval zůstatek kreditu propojení.

V roli odesílatele service bus odesílá zprávy k využití všech nevyřízených kreditů propojení.

Volání "přijímat" na úrovni rozhraní API překládá do *provádění toku* odesílá na service bus klientem a Service Bus spotřebovává tento kredit tím, že první dostupné, odemčené zprávy z fronty, uzamčení a přenos. Není-li k dispozici žádná zpráva, která by byla snadno dostupná, jakýkoli nesplacený kredit jakéhokoli spojení s tímto konkrétním subjektem zůstává zaznamenán v pořadí příjezdu a zprávy jsou uzamčeny a převedeny, jakmile budou k dispozici, aby se použily všechny nesplacené kredity.

Zámek zprávy je uvolněn, když je přenos vyrovnán do jednoho ze stavů terminálu *přijatých*, *odmítnutých*nebo *uvolněných*. Zpráva je odebrána ze služby Service Bus při *přijetí*stavu terminálu . Zůstane v service bus a je doručena do dalšího příjemce, když přenos dosáhne některého z ostatních stavů. Service Bus automaticky přesune zprávu do fronty nedoručených zpráv entity, když dosáhne maximálního počtu doručení povoleného pro entitu z důvodu opakovaných odmítnutí nebo vydání.

I když service bus API nejsou přímo vystavit takovou možnost dnes, nižší úrovně AMQP protokol klient může použít model link-credit otočit "pull-style" interakce vydávání jedné jednotky úvěru pro každou žádost o přijetí do "push-style" model vydávání velkého počtu kreditů odkazů a následné přijímání zpráv, jakmile budou k dispozici bez jakékoli další interakce. Push je podporovánprostřednictvím Nastavení vlastností [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) nebo [MessageReceiver.PrefetchCount.](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) Pokud jsou nenulové, klient AMQP jej použije jako kredit propojení.

V tomto kontextu je důležité si uvědomit, že hodiny pro vypršení platnosti zámku na zprávu uvnitř entity spustí při přijetí zprávy z entity, nikoli při odeslání zprávy na lince. Vždy, když klient označí připravenost přijímat zprávy vydáním kreditu odkazu, očekává se, že bude aktivně vytahovat zprávy v síti a bude připraven je zpracovat. V opačném případě může vypršela platnost zámku zprávy ještě před doručením zprávy. Použití řízení toku link-credit by mělo přímo odrážet okamžitou připravenost řešit dostupné zprávy odeslané příjemci.

V souhrnu následující části poskytují schematický přehled performativnítok během různých interakcí rozhraní API. Každá část popisuje jinou logickou operaci. Některé z těchto interakcí může být "líný", což znamená, že mohou být provedeny pouze v případě potřeby. Vytvoření odesílatele zprávy nemusí způsobit síťovou interakci, dokud nebude odeslána nebo požadována první zpráva.

Šipky v následující tabulce ukazují směr performativního toku.

#### <a name="create-message-receiver"></a>Vytvořit příjemce zprávy

| Klient | Service Bus |
| --- | --- |
| --> připojit(<br/>name={název odkazu},<br/>handle={číselné úchyt},<br/>role =**přijímač**,<br/>source={název entity},<br/>target={ID odkazu klienta}<br/>) |Klient se připojí k entitě jako příjemce |
| Service Bus odpovědi připojení jeho konec odkazu |<- připojit(<br/>name={název odkazu},<br/>handle={číselné úchyt},<br/>role =**odesílatel**,<br/>source={název entity},<br/>target={ID odkazu klienta}<br/>) |

#### <a name="create-message-sender"></a>Vytvořit odesílatele zprávy

| Klient | Service Bus |
| --- | --- |
| --> připojit(<br/>name={název odkazu},<br/>handle={číselné úchyt},<br/>role =**odesílatel**,<br/>source={ID odkazu klienta},<br/>target={název entity}<br/>) |Žádná akce |
| Žádná akce |<- připojit(<br/>name={název odkazu},<br/>handle={číselné úchyt},<br/>role =**přijímač**,<br/>source={ID odkazu klienta},<br/>target={název entity}<br/>) |

#### <a name="create-message-sender-error"></a>Vytvořit odesílatele zprávy (chyba)

| Klient | Service Bus |
| --- | --- |
| --> připojit(<br/>name={název odkazu},<br/>handle={číselné úchyt},<br/>role =**odesílatel**,<br/>source={ID odkazu klienta},<br/>target={název entity}<br/>) |Žádná akce |
| Žádná akce |<- připojit(<br/>name={název odkazu},<br/>handle={číselné úchyt},<br/>role =**přijímač**,<br/>source=null,<br/>target=null<br/>)<br/><br/><- odpojit(<br/>handle={číselné úchyt},<br/>zavřeno =**pravda**,<br/>error={informace o chybě}<br/>) |

#### <a name="close-message-receiversender"></a>Zavřít příjemce zprávy/odesílatele

| Klient | Service Bus |
| --- | --- |
| --> detach(<br/>handle={číselné úchyt},<br/>zavřeno =**true**<br/>) |Žádná akce |
| Žádná akce |<- odpojit(<br/>handle={číselné úchyt},<br/>zavřeno =**true**<br/>) |

#### <a name="send-success"></a>Odeslat (úspěch)

| Klient | Service Bus |
| --- | --- |
| --> transfer(<br/>delivery-id={numer handle},<br/>delivery-tag={binární popisovač},<br/>usadil =**false**,,more=**false**,<br/>stav =**null**,<br/>resume =**false**<br/>) |Žádná akce |
| Žádná akce |<... dispozice(<br/>role=přijímač,<br/>first={ID doručení},<br/>last={ID doručení},<br/>usadil =**pravda**,<br/>state =**přijato**<br/>) |

#### <a name="send-error"></a>Odeslat (chyba)

| Klient | Service Bus |
| --- | --- |
| --> transfer(<br/>delivery-id={numer handle},<br/>delivery-tag={binární popisovač},<br/>usadil =**false**,,more=**false**,<br/>stav =**null**,<br/>resume =**false**<br/>) |Žádná akce |
| Žádná akce |<... dispozice(<br/>role=přijímač,<br/>first={ID doručení},<br/>last={ID doručení},<br/>usadil =**pravda**,<br/>stav =**odmítnuto**(<br/>error={informace o chybě}<br/>)<br/>) |

#### <a name="receive"></a>Přijmout

| Klient | Service Bus |
| --- | --- |
| --> tok(<br/>odkaz-úvěr=1<br/>) |Žádná akce |
| Žádná akce |< převod(<br/>delivery-id={numer handle},<br/>delivery-tag={binární popisovač},<br/>usadil =**false**,<br/>více =**nepravdivé**,<br/>stav =**null**,<br/>resume =**false**<br/>) |
| --> dispozice(<br/>role =**přijímač**,<br/>first={ID doručení},<br/>last={ID doručení},<br/>usadil =**pravda**,<br/>state =**přijato**<br/>) |Žádná akce |

#### <a name="multi-message-receive"></a>Příjem více zpráv

| Klient | Service Bus |
| --- | --- |
| --> tok(<br/>odkaz-úvěr=3<br/>) |Žádná akce |
| Žádná akce |< převod(<br/>delivery-id={numer handle},<br/>delivery-tag={binární popisovač},<br/>usadil =**false**,<br/>více =**nepravdivé**,<br/>stav =**null**,<br/>resume =**false**<br/>) |
| Žádná akce |< převod(<br/>delivery-id={číselné popisovač+1},<br/>delivery-tag={binární popisovač},<br/>usadil =**false**,<br/>více =**nepravdivé**,<br/>stav =**null**,<br/>resume =**false**<br/>) |
| Žádná akce |< převod(<br/>delivery-id={číselné popisovač+2},<br/>delivery-tag={binární popisovač},<br/>usadil =**false**,<br/>více =**nepravdivé**,<br/>stav =**null**,<br/>resume =**false**<br/>) |
| --> dispozice(<br/>role=přijímač,<br/>first={ID doručení},<br/>last={ID dodávky+2},<br/>usadil =**pravda**,<br/>state =**přijato**<br/>) |Žádná akce |

### <a name="messages"></a>Zprávy

V následujících částech jsou vysvětleny, které vlastnosti ze standardních částí zpráv AMQP používá service bus a jak jsou mapovány na sadu rozhraní API služby Service Bus.

Všechny vlastnosti, které aplikace potřebuje definovat by mělbýt `application-properties` mapován na mapu AMQP.

#### <a name="header"></a>záhlaví

| Název pole | Využití | Název rozhraní API |
| --- | --- | --- |
| Trvanlivé |- |- |
| Prioritou |- |- |
| TTL |Čas žít pro tuto zprávu |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| první nabytý |- |- |
| počet dodávek |- |[Počet dodávek](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Název pole | Využití | Název rozhraní API |
| --- | --- | --- |
| id zprávy |Identifikátor volného tvaru pro tuto zprávu definovaný aplikací. Používá se pro vyhledávání duplicit. |[Messageid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| user-id |Identifikátor uživatele definovaný aplikací, který není interpretován službou Service Bus. |Není přístupný prostřednictvím rozhraní API služby Service Bus. |
| na |Identifikátor cíle definovaný aplikací, který není interpretován službou Service Bus. |[Akce](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Předmět |Identifikátor účelu zprávy definovaný aplikací, který není interpretován službou Service Bus. |[Popisek](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| odpověď na odpověď |Indikátor cesty odpovědi definované aplikací, který není interpretován službou Service Bus. |[Odpovědět](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlation-id |Identifikátor korelace definované aplikací, který není interpretován službou Service Bus. |[Correlationid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| typ obsahu |Indikátor typu obsahu definované aplikací pro tělo, který není interpretován službou Service Bus. |[Contenttype](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| kódování obsahu |Indikátor kódování obsahu definované aplikací pro tělo, který není interpretován službou Service Bus. |Není přístupný prostřednictvím rozhraní API služby Service Bus. |
| absolutní doba expirace |Deklaruje, kdy vyprší platnost absolutního okamžiku platnosti zprávy. Ignorováno na vstupu (hlavička TTL je pozorována), autoritativní na výstupu. |[Vyprší platnostAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| čas vytvoření |Deklaruje, kdy byla zpráva vytvořena. Nepoužívané službou Service Bus |Není přístupný prostřednictvím rozhraní API služby Service Bus. |
| id skupiny |Identifikátor definovaný aplikací pro související sadu zpráv. Používá se pro relace service bus. |[Sessionid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| sekvenční |Čítač identifikující relativní pořadové číslo zprávy uvnitř relace. Ignorováno službou Service Bus. |Není přístupný prostřednictvím rozhraní API služby Service Bus. |
| odpověď na id skupiny |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Poznámky ke zprávě

Existuje několik dalších vlastností zprávy služby Service Bus, které nejsou součástí `MessageAnnotations` vlastností zprávy AMQP a jsou předány spolu jako ve zprávě.

| Klíč mapy poznámky | Využití | Název rozhraní API |
| --- | --- | --- |
| x-opt-scheduled-queue-time | Deklaruje, kdy by se zpráva měla objevit v entitě. |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt-partition-key | Kód definovaný aplikací, který určuje, které rozdělení zprávy by měla přistát. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt-via-partition-key | Hodnota klíče oddílu definovaná aplikací, pokud má být transakce použita k odesílání zpráv prostřednictvím fronty přenosu. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-queued-time | Služba definovaná čas UTC představující skutečný čas enqueuing zprávy. Ignorováno na vstupu. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt-sequence-number | Jedinečné číslo definované službou přiřazené ke zprávě. | [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x-opt-offset | Služba definovaná pořadové číslo zprávy ve frontě. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-opt-locked-do | Definováno službou. Datum a čas, do kterého bude zpráva uzamčena ve frontě/předplatném. | [UzamčenoUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-deadletter-source | Definována služba. Pokud je zpráva přijata z fronty nedoručených zpráv, zdroj původní zprávy. | [Zdroj mrtvého dopisu](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Transakční schopnost

Skupiny transakcí seskupují dvě nebo více operací do rozsahu provádění. Taková transakce musí ze své povahy zajistit, aby všechny operace patřící do dané skupiny operací buď uspěly, nebo společně neuspěly.
Operace jsou seskupeny podle `txn-id`identifikátoru .

Pro transakční interakce klient funguje `transaction controller` jako , který řídí operace, které by měly být seskupeny. Služba Service Bus `transactional resource` Service Service funguje jako `transaction controller`a provádí práci podle požadavků .

Klient a služba komunikují `control link` přes , který je vytvořen klientem. Zprávy `declare` `discharge` a jsou odesílány správcem přes kontrolní odkaz k přidělení a dokončení transakcí (nepředstavují vymezení transakční práce). Skutečné odesílání a přijímání se neprovádí na tomto odkazu. Každá požadovaná transakční operace je `txn-id` explicitně identifikována s požadovaným a proto může dojít na libovolné matné propojení na připojení. Pokud je řídicí propojení uzavřeno, zatímco existují nevybité transakce, které vytvořil, pak jsou všechny tyto transakce okamžitě vráceny zpět a pokusy o provedení další transakční práce na nich povede k selhání. Zprávy na ovládacím odkazu nesmí být předem vyrovnány.

Každé připojení musí iniciovat vlastní řídicí odkaz, aby bylo možné zavést a ukončit transakce. Služba definuje speciální cíl, který `coordinator`funguje jako . Klient/kontrolor vytvoří řídicí odkaz na tento cíl. Propojení ovládacího prvku je mimo hranice entity, to znamená, že stejné propojení ovládacího prvku lze použít k zahájení a vyřízení transakcí pro více entit.

#### <a name="starting-a-transaction"></a>Zahájení transakce

Chcete-li zahájit transakční práci. správce musí získat `txn-id` od koordinátora. Je to tím, `declare` že pošle zprávu typu. Pokud je prohlášení úspěšné, koordinátor odpoví s dispozičním výsledkem, který nese přiřazené `txn-id`.

| Klient (řadič) | | Sběrnice (koordinátor) |
| --- | --- | --- |
| připojit(<br/>name={název odkazu},<br/>... ,<br/>role =**odesílatel**,<br/>cíl =**koordinátor**<br/>) | ------> |  |
|  | <------ | připojit(<br/>name={název odkazu},<br/>... ,<br/>cíl=Koordinátor()<br/>) |
| převodu(<br/>dodávka-id=0, ...)<br/>{ AmqpValue (**Declare()**)}| ------> |  |
|  | <------ | dispozice( <br/> first=0, last=0, <br/>stav =**Deklarovaný**(<br/>**txn-id**={ID transakce}<br/>))|

#### <a name="discharging-a-transaction"></a>Vybití transakce

Správce uzavírá transakční práci odesláním `discharge` zprávy koordinátorovi. Řadič označuje, že chce potvrdit nebo vrátit zpět transakční `fail` práce nastavením příznaku na těle vypouštění. Není-li koordinátor schopen absolutorium dokončit, zpráva `transaction-error`se zamítne s tímto výsledkem, který nese .

> Poznámka: fail=true odkazuje na vrácení transakce zpět a fail=false odkazuje na Commit.

| Klient (řadič) | | Sběrnice (koordinátor) |
| --- | --- | --- |
| převodu(<br/>dodávka-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | dispozice( <br/> first=0, last=0, <br/>stav=Deklarovaný(<br/>txn-id={ID transakce}<br/>))|
| | . . . <br/>Transakční práce<br/>na další odkazy<br/> . . . |
| převodu(<br/>dodávka-id=57, ...)<br/>{ Hodnota Amqp (<br/>**Výtok (txn-id=0,<br/>fail=false)**)}| ------> |  |
| | <------ | dispozice( <br/> first=57, last=57, <br/>stav=**Přijato()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Odeslání zprávy v transakci

Všechny transakční práce se provádí se `transactional-state` stavem transakčního doručení, který nese txn-id. V případě odesílání zpráv je transakční stav nesený v rámci přenosu zprávy. 

| Klient (řadič) | | Sběrnice (koordinátor) |
| --- | --- | --- |
| převodu(<br/>dodávka-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | dispozice( <br/> first=0, last=0, <br/>stav=Deklarovaný(<br/>txn-id={ID transakce}<br/>))|
| převodu(<br/>rukojeť=1,<br/>dodávka-id=1, <br/>**stav<br/>= TransactionalState(<br/>txn-id=0)**)<br/>{ datová část }| ------> |  |
| | <------ | dispozice( <br/> first=1, last=1, <br/>state=**<br/>TransactionalState( txn-id=0,<br/>outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Likvidace zprávy v transakci

Dispozice zprávy `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer`zahrnuje operace, jako je . Chcete-li provést tyto operace `transactional-state` v rámci transakce, předat s dispozice.

| Klient (řadič) | | Sběrnice (koordinátor) |
| --- | --- | --- |
| převodu(<br/>dodávka-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | dispozice( <br/> first=0, last=0, <br/>stav=Deklarovaný(<br/>txn-id={ID transakce}<br/>))|
| | <------ |převodu(<br/>rukojeť=2,<br/>dodávka-id=11, <br/>state=null)<br/>{ datová část }|  
| dispozice( <br/> first=11, last=11, <br/>state=**<br/>TransactionalState( txn-id=0,<br/>outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Rozšířené funkce sběrnice Service Bus

Tato část popisuje pokročilé funkce služby Azure Service Bus, které jsou založeny na konceptu rozšíření AMQP, aktuálně vyvíjené v Technickém výboru OASIS pro AMQP. Service Bus implementuje nejnovější verze těchto konceptů a přijímá změny zavedené jako tyto koncepty dosáhnout standardního stavu.

> [!NOTE]
> Service Bus Zasílání zpráv rozšířené operace jsou podporovány prostřednictvím vzorce požadavku a odpovědi. Podrobnosti o těchto operacích jsou popsány v článku [AMQP 1.0 v service bus: operace založené na odezvě na požadavcích](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>Správa AMQP

Specifikace správy AMQP je první z konceptů rozšíření popsaných v tomto článku. Tato specifikace definuje sadu protokolů vrstvených nad protokolem AMQP, které umožňují interakci správy s infrastrukturou zasílání zpráv prostředpou AMQP. Specifikace definuje obecné operace, jako je *například vytvoření*, *čtení*, *aktualizace*a *odstranění* pro správu entit uvnitř infrastruktury zasílání zpráv a sadu operací dotazů.

Všechna tato gesta vyžadují interakci požadavku a odpovědi mezi klientem a infrastrukturou zasílání zpráv, a proto specifikace definuje, jak modelovat tento vzor interakce nad amqp: klient se připojí k infrastruktuře zasílání zpráv, zahájí relaci a potom vytvoří dvojici odkazů. Na jednom odkazu klient funguje jako odesílatel a na druhé straně funguje jako příjemce, čímž vytváří dvojici odkazů, které mohou fungovat jako obousměrný kanál.

| Logická operace | Klient | Service Bus |
| --- | --- | --- |
| Vytvořit cestu odpovědi na požadavek |--> připojit(<br/>name={*název odkazu*},<br/>handle={*číselné táhlo*},<br/>role =**odesílatel**,<br/>source =**null**,<br/>target="myentity/$management"<br/>) |Žádná akce |
| Vytvořit cestu odpovědi na požadavek |Žádná akce |\<-- připojit(<br/>name={*název odkazu*},<br/>handle={*číselné táhlo*},<br/>role =**přijímač**,<br/>source=null,<br/>target="myentity"<br/>) |
| Vytvořit cestu odpovědi na požadavek |--> připojit(<br/>name={*název odkazu*},<br/>handle={*číselné táhlo*},<br/>role =**přijímač**,<br/>source="myentity/$management",<br/>target="myclient$id"<br/>) | |
| Vytvořit cestu odpovědi na požadavek |Žádná akce |\<-- připojit(<br/>name={*název odkazu*},<br/>handle={*číselné táhlo*},<br/>role =**odesílatel**,<br/>source="myentity",<br/>target="myclient$id"<br/>) |

S touto dvojicí odkazů na místě, implementace požadavku a odpovědi je jednoduchá: požadavek je zpráva odeslaná entitě uvnitř infrastruktury zasílání zpráv, která rozumí tomuto vzoru. V této zprávě požadavku je pole *pro odpověď* v části *vlastností* nastaveno na *cílový* identifikátor odkazu, na který má být odpověď doručit. Manipulační entita zpracuje požadavek a poté doručí odpověď přes odkaz, jehož *cílový* identifikátor odpovídá uvedenému *identifikátoru odpovědi.*

Vzor samozřejmě vyžaduje, aby kontejner klienta a identifikátor generovaný klientem pro cíl odpovědi jsou jedinečné ve všech klientech a z bezpečnostních důvodů je také obtížné předpovědět.

Výměny zpráv používané pro protokol správy a pro všechny ostatní protokoly, které používají stejný vzor dojít na úrovni aplikace; nedefinují nová gesta na úrovni protokolu AMQP. To je záměrné, takže aplikace mohou okamžitě využít výhod těchto rozšíření s kompatibilními zásobníky AMQP 1.0.

Service Bus v současné době neimplementuje žádnou základní funkce specifikace správy, ale vzor požadavku a odpovědi definovaný specifikací správy je základem pro funkci zabezpečení založenou na deklaracích a pro téměř všechny pokročilé funkce popsané v následujících částech:

### <a name="claims-based-authorization"></a>Autorizace na základě deklarace identity

Návrh specifikace AMQP claims-Based-Authorization (CBS) vychází ze vzoru požadavku/odpovědi specifikace správy a popisuje zobecněný model, jak používat federované tokeny zabezpečení s AMQP.

Výchozí model zabezpečení AMQP popisovaný v úvodu je založen na protokolu SASL a integruje se s handshake připojení AMQP. Použití SASL má tu výhodu, že poskytuje rozšiřitelný model, pro který byla definována sada mechanismů, ze kterých může mít prospěch jakýkoli protokol, který se formálně opírá o SASL. Mezi tyto mechanismy patří "PLAIN" pro přenos uživatelských jmen a hesel, "EXTERNÍ" pro vazbu na zabezpečení na úrovni TLS, "ANONYMOUS" pro vyjádření absence explicitního ověřování/autorizace a široká škála dalších mechanismů, které umožňují předávání ověřovacích a/nebo autorizačních pověření nebo tokenů.

Integrace SASL amqp má dvě nevýhody:

* Všechna pověření a tokeny jsou vymezeny na připojení. Infrastruktura zasílání zpráv může chtít poskytnout diferencované řízení přístupu na základě entity; například povolení posla tokenu k odeslání do fronty A, ale ne do fronty B. S kontextautorizace ukotvené na připojení, není možné použít jedno připojení a přesto použít různé přístupové tokeny pro frontu A a fronty B.
* Přístupové tokeny jsou obvykle platné pouze po omezenou dobu. Tato platnost vyžaduje, aby uživatel pravidelně znovu získávat tokeny a poskytuje příležitost vydavateli tokenu odmítnout vydávání nového tokenu, pokud se změnila přístupová oprávnění uživatele. Připojení AMQP mohou trvat dlouhou dobu. Model SASL poskytuje pouze možnost nastavit token v době připojení, což znamená, že infrastruktura zasílání zpráv musí buď odpojit klienta, když vyprší platnost tokenu, nebo musí přijmout riziko povolení pokračující komunikace s klientem, který je přístupová práva mohla být v mezidobí zrušena.

Specifikace AMQP CBS, implementovaná service bus, umožňuje elegantní řešení pro oba tyto problémy: Umožňuje klientovi přidružit přístupové tokeny s každým uzlem a aktualizovat tyto tokeny před vypršením jejich platnosti bez přerušení toku zpráv.

CBS definuje uzel virtuální správy s názvem *$cbs*, který má být poskytnut infrastrukturou zasílání zpráv. Uzel pro správu přijímá tokeny jménem jiných uzlů v infrastruktuře zasílání zpráv.

Gesto protokolu je výměna požadavků a odpovědí definovaná specifikací správy. To znamená, že klient vytvoří dvojici propojení s *$cbs* uzlu a potom předá požadavek na odchozí odkaz a pak čeká na odpověď na příchozí odkaz.

Zpráva požadavku má následující vlastnosti aplikace:

| Klíč | Nepovinné | Typ hodnoty | Obsah hodnoty |
| --- | --- | --- | --- |
| Operace |Ne |řetězec |**put-token** |
| type |Ne |řetězec |Typ tokenu, který je umístěn. |
| jméno |Ne |řetězec |"Publikum", na které se vztahuje token. |
| Vypršení platnosti |Ano |časové razítko |Doba vypršení platnosti tokenu. |

Vlastnost *name* identifikuje entitu, se kterou má být token přidružen. V Service Bus je cesta do fronty, nebo téma /předplatné. Vlastnost *type* identifikuje typ tokenu:

| Typ tokenu | Popis tokenu | Typ těla | Poznámky |
| --- | --- | --- | --- |
| amqp:jwt |Webový token JSON (JWT) |Hodnota AMQP (řetězec) |Zatím není k dispozici. |
| amqp:swt |Jednoduchý webový token (SWT) |Hodnota AMQP (řetězec) |Podporováno pouze pro tokeny SWT vydané AAD/ACS |
| servicebus.windows.net:sastoken |Service Bus SAS Token |Hodnota AMQP (řetězec) |- |

Tokeny udělují práva. Service Bus ví o třech základních právech: "Odeslat" umožňuje odesílání, "Naslouchání" umožňuje příjem a "Správa" umožňuje manipulaci s entitami. Tokeny SWT vydané aad/acs explicitně zahrnují tato práva jako deklarace identity. Tokeny SAS služby Service Bus odkazují na pravidla nakonfigurovaná v oboru názvů nebo entitě a tato pravidla jsou konfigurována s právy. Podepsání tokenu s klíčem přidruženým k tomuto pravidlu tak způsobí, že token vyjádří příslušná práva. Token přidružený k entitě pomocí *put-tokenu* umožňuje připojenému klientovi komunikovat s entitou podle práv tokenu. Odkaz, kde klient převezme roli *odesílatele,* vyžaduje právo "Odeslat"; převzetí role *příjemce* vyžaduje právo "Poslouchat".

Zpráva s odpovědí má následující hodnoty *vlastností aplikace.*

| Klíč | Nepovinné | Typ hodnoty | Obsah hodnoty |
| --- | --- | --- | --- |
| stavový kód |Ne |int |Kód odpovědi HTTP **[RFC2616]**. |
| popis stavu |Ano |řetězec |Popis stavu. |

Klient může volat *put-token* opakovaně a pro libovolnou entitu v infrastruktuře zasílání zpráv. Tokeny jsou vymezeny na aktuálního klienta a ukotveny na aktuální připojení, což znamená, že server klesne všechny zachované tokeny, když připojení klesne.

Aktuální implementace service bus umožňuje pouze CBS ve spojení s metodou SASL "ANONYMOUS". Připojení SSL/TLS musí vždy existovat před handshake SASL.

Mechanismus ANONYMOUS proto musí být podporován vybraným klientem AMQP 1.0. Anonymní přístup znamená, že počáteční připojení handshake, včetně vytvoření počáteční relace se stane bez Service Bus vědět, kdo vytváří připojení.

Po navázání připojení a relace, připojení odkazů na *$cbs* uzlu a odeslání požadavku *put-token* jsou pouze povolené operace. Platný token musí být úspěšně nastaven pomocí požadavku *put-token* pro některé entity uzlu do 20 sekund po navázání připojení, jinak je připojení jednostranně vynecháno service bus.

Klient je následně zodpovědný za sledování vypršení platnosti tokenu. Když vyprší platnost tokenu, Service Bus okamžitě klesne všechny odkazy na připojení k příslušné entitě. Chcete-li zabránit vzniku problému, klient může nahradit token pro uzel s novým kdykoli prostřednictvím *virtuálního* uzlu správy $cbs stejným *gestem put-token* a bez získání v cestě datové části provozu, který toky na různých odkazech.

### <a name="send-via-functionality"></a>Funkce odesílání přes

[Send-via / Transfer sender](service-bus-transactions.md#transfers-and-send-via) je funkce, která umožňuje, aby servisní sběrnice předala danou zprávu cílové entitě prostřednictvím jiné entity. Tato funkce se používá k provádění operací mezi entitami v jedné transakci.

Pomocí této funkce vytvoříte odesílatele a vytvoříte `via-entity`odkaz na soubor . Při vytváření propojení jsou předány další informace k určení skutečného cíle zpráv/přenosů na tomto odkazu. Jakmile je připojení úspěšné, všechny zprávy odeslané na tento odkaz jsou automaticky předány *cílové entitě* prostřednictvím *via-entity*. 

> Poznámka: Ověřování musí být provedeno pro *via-entity* a *cílovou entitu* před vytvořením tohoto odkazu.

| Klient | | Service Bus |
| --- | --- | --- |
| připojit(<br/>name={název odkazu},<br/>role=odesílatel,<br/>source={ID odkazu klienta},<br/>cíl =**{via-entity}**,<br/>**properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )]** ) | ------> | |
| | <------ | připojit(<br/>name={název odkazu},<br/>role=přijímač,<br/>source={ID odkazu klienta},<br/>target={via-entity},<br/>vlastnosti=mapa [(<br/>com.microsoft:adresa transfer-cíl=<br/>{cílová entita} )] ) |

## <a name="next-steps"></a>Další kroky

Chcete-li se dozvědět více o AMQP, navštivte následující odkazy:

* [Přehled služby AMQP sběrnice]
* [Podpora amqp 1.0 pro fronty a témata s oddíly service bus]
* [AMQP v service bus pro Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Přehled služby AMQP sběrnice]: service-bus-amqp-overview.md
[Podpora amqp 1.0 pro fronty a témata s oddíly service bus]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP v service bus pro Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

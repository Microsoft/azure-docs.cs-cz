---
title: AMQP 1,0 v Azure Service Bus a průvodci protokolem Event Hubs | Microsoft Docs
description: Průvodce protokolem pro výrazy a popis AMQP 1,0 v Azure Service Bus a Event Hubs
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e001327c2c7da08cb9a3552f97fc9a7d8b7921a2
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95736710"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1,0 v Azure Service Bus a průvodci protokolem Event Hubs

Pokročilý protokol pro řízení front zpráv 1,0 je standardizované rámce a přenosový protokol pro asynchronní, bezpečný a spolehlivý přenos zpráv mezi dvěma stranami. Jedná se o primární protokol Azure Service Bus zasílání zpráv a Azure Event Hubs.  

AMQP 1,0 je výsledkem široké spolupráce v oboru, která je spojená s mnoha dodavateli middlewaru, jako je například Microsoft a Red Hat, s mnoha uživateli middlewaru pro zasílání zpráv, jako je například JP Morgan podstatou představující odvětví finančních služeb. Fórum technické normalizace pro specifikace protokolu AMQP a rozšíření je OASIS a dosáhlo se formálního schválení jako mezinárodní standard jako ISO/IEC 19494:2014. 

## <a name="goals"></a>Cíle

Tento článek shrnuje základní koncepty specifikace zasílání zpráv AMQP 1,0 v rámci specifikací rozšíření vyvinutých [technickým výborem Oasis AMQP](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) a vysvětluje, jak Azure Service Bus implementuje a sestavuje tyto specifikace.

Cílem je, aby každý vývojář používal existující zásobník klienta AMQP 1,0 na libovolné platformě, aby mohl pracovat s Azure Service Bus přes AMQP 1,0.

Běžné zásobníky AMQP 1,0 pro obecné účely, jako je [Apache Qpid Proton](https://qpid.apache.org/proton/index.html) nebo [AMQP.NET Lite](https://github.com/Azure/amqpnetlite), implementují všechny prvky protokolu Core AMQP 1,0, jako jsou relace nebo odkazy. Tyto základní prvky jsou někdy zabaleny s využitím rozhraní API vyšší úrovně; Apache Proton dokonce nabízí dva, imperativní rozhraní API pro kurýrní služby a reaktivní rozhraní API Reactor.

V následující diskuzi předpokládáme, že Správa připojení AMQP, relací a odkazů a zpracování přenosů snímků a řízení toku jsou zpracovávána příslušným zásobníkem (například Apache Proton-C) a nevyžadují mnoho, pokud konkrétní pozornost vývojářům aplikací. Implicitně předpokládáme existenci několika primitivních primitiv rozhraní API, jako je možnost připojení, a vytvoření nějaké formy objektů pro abstrakci *odesílatele* a *přijímače* , které pak mají nějaký tvar `send()` a `receive()` operace.

Při projednávání pokročilých funkcí Azure Service Bus, jako je procházení zpráv nebo Správa relací, jsou tyto funkce vysvětlené v AMQPch výrazech, ale také jako vrstvená pseudo implementace nad touto předpokládanou abstrakcí rozhraní API.

## <a name="what-is-amqp"></a>Co je AMQP?

AMQP je protokol rámců a přenosů. Rámců znamená, že poskytuje strukturu pro binární datové proudy, které jsou v obou směrech síťového připojení. Struktura poskytuje depřímení pro samostatné bloky dat označované jako *snímky*, které se mají vyměňovat mezi připojenými stranami. Možnosti přenosu zajistí, že obě komunikující strany můžou navázat sdílené porozumění o tom, kdy se snímky mají přenést a kdy se převody považují za kompletní.

Na rozdíl od dřívějších verzí konceptu, které vytvořila pracovní skupina AMQP, kterou stále používá několik zprostředkovatelů zpráv, se v konečném a standardizovaném protokolu AMQP 1,0 v pracovní skupině nestanoví přítomnost zprostředkovatele zpráv nebo jakékoli konkrétní topologie pro entity uvnitř zprostředkovatele zpráv.

Protokol lze použít pro symetrickou komunikaci peer-to-peer, pro interakci se zprostředkovateli zpráv, kteří podporují fronty a publikování/odběr entit, jak Azure Service Bus. Dá se použít i pro interakci s infrastrukturou zasílání zpráv, kde se vzorce interakce liší od běžných front, stejně jako v případě Azure Event Hubs. Centrum událostí funguje jako fronta, když se do ní odesílají události, ale funguje jako služba sériového úložiště při čtení událostí. trochu se podobá páskové jednotce. Klient vybere posun do dostupného datového proudu a pak dorazí všechny události z tohoto posunu k nejnovějšímu dostupnému.

Protokol AMQP 1,0 je navržený tak, aby bylo možné další specifikace pro zlepšení svých schopností. Tyto tři specifikace rozšíření popsané v tomto dokumentu ukazují. Pro komunikaci přes existující infrastrukturu HTTPS/WebSockets může být konfigurace nativních portů AMQP TCP obtížné. Specifikace vazby definuje, jak vrstvy AMQP přes objekty WebSockets. Pro interakci s infrastrukturou zasílání zpráv v rámci požadavků a odpovědí pro účely správy nebo pro zajištění pokročilých funkcí určuje specifikace správy AMQP požadované základní primitivní prvky interakce. Pro integraci federovaného autorizačního modelu AMQP specifikace zabezpečení založená na deklaracích identity definuje, jak přidružit a obnovit autorizační tokeny přidružené k odkazům.

## <a name="basic-amqp-scenarios"></a>Základní scénáře AMQP

V této části je vysvětleno základní použití AMQP 1,0 s Azure Service Bus, což zahrnuje vytváření připojení, relací a propojení a přenos zpráv do a z Service Bus entit, jako jsou fronty, témata a předplatná.

Nejvíc směrodatná zdrojová informace o tom, jak AMQP funguje jako [specifikace AMQP 1,0](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html), ale specifikace se zapsala tak, aby přesně provedla implementaci příručky a nedokázala učit protokol. Tato část se zaměřuje na zavádění co nejvíc terminologie pro popis způsobu, jakým Service Bus používá AMQP 1,0. Pro komplexnější seznámení s AMQP a také širší diskuzi o AMQP 1,0 si můžete projít [Tento video kurz][this video course].

### <a name="connections-and-sessions"></a>Připojení a relace

AMQP volá *kontejnery* komunikujících programů; Ty obsahují *uzly*, které jsou komunikujícími entitami v těchto kontejnerech. Frontou může být takový uzel. AMQP umožňuje multiplexing, takže jedno připojení lze použít pro mnoho komunikačních cest mezi uzly. klient aplikace může například souběžně přijímat z jedné fronty a odesílat je do jiné fronty přes stejné síťové připojení.

![Diagram znázorňující relace a připojení mezi kontejnery.][1]

Síťové připojení je tedy ukotveno na kontejneru. Je iniciován kontejnerem v roli klienta, který vytváří odchozí připojení soketu TCP ke kontejneru v roli příjemce, který naslouchá a přijímá příchozí připojení TCP. Metoda handshake připojení zahrnuje vyjednávání verze protokolu, deklaraci nebo vyjednávání o použití TLS/SSL (Transport Level Security) a ověřovací/autorizační metody handshake v oboru připojení, který je založen na SASL.

Azure Service Bus vyžaduje nepřetržité používání protokolu TLS. Podporuje připojení přes port TCP 5671, kdy se před vstupem metody handshake protokolu AMQP a připojení TCP nejprve překrývají s protokolem TLS, a podporuje také připojení přes port TCP 5672, kde server okamžitě nabízí povinný upgrade připojení k TLS pomocí modelu předepsaného AMQP. Vazba WebSockets AMQP vytvoří tunel přes port TCP 443, který je pak ekvivalentní připojení AMQP 5671.

Po nastavení připojení a TLS Service Bus nabízí dvě možnosti mechanismu SASL:

* SASL PLAIN se běžně používá k předávání přihlašovacích údajů k uživatelskému jménu a heslu serveru. Service Bus nemá účty, ale jsou pojmenované [pravidla zabezpečení sdíleného přístupu](service-bus-sas.md), která přidávají práva a jsou přidružená k klíči. Název pravidla se používá jako uživatelské jméno a klíč (jako text kódovaný v kódování Base64) se používá jako heslo. Práva přidružená k vybranému pravidlu závisí na operacích povolených u připojení.
* SASL ANONYMOUS se používá pro obejít autorizaci SASL, když chce klient použít model CBS (Based-Security), který je popsán později. Pomocí této možnosti je možné navázat připojení klienta anonymně po krátkou dobu, během které může klient komunikovat pouze s koncovým bodem CBS a musí být dokončena Metoda handshake CBS.

Po navázání transportního připojení kontejnery vydávají maximální velikost rámce, které mají být ochotny zpracovat, a po vypršení časového limitu nečinnosti se jednostranně odpojí, pokud není k dispozici žádná aktivita připojení.

Také prohlašují, kolik souběžných kanálů je podporováno. Kanál je jednosměrná a odchozí cesta k virtuálnímu přenosu nad připojením. Relace převezme kanál od každého propojeného kontejneru a vytvoří obousměrnou komunikační cestu.

Relace mají model řízení toku založený na oknech; Při vytvoření relace deklaruje každá strana, kolik rámců je ochotno přijmout do přijímaného okna. Jako strany vyměňují rámce, přenesené snímky vyplní okno a převody, když je okno plné a dokud se okno neobnoví nebo nerozšíří pomocí *performative Flow* (*performative* je AMQP termín pro gesta na úrovni protokolu vyměňované mezi obě strany).

Tento model založený na okně je zhruba podobný konceptu řízení toku založenému na systému Windows, ale na úrovni relace uvnitř soketu. Pojem protokolu pro povolení více souběžných relací existuje, aby provoz s vysokou prioritou mohl rushed za normálním omezením provozu, jako na dálnici Lane.

Azure Service Bus aktuálně používá právě jednu relaci pro každé připojení. Service Bus maximální velikost rámce je 262 144 bajtů (256-K bajtů) pro Service Bus Standard. Je 1 048 576 (1 MB) pro Service Bus Premium a Event Hubs. Service Bus neobsahují žádná konkrétní okna omezování na úrovni relace, ale v rámci řízení toku na úrovni odkazů (viz [Další část](#links)) se pravidelně nastavuje okno.

Připojení, kanály a relace jsou dočasné. Pokud se základní připojení sbalí, připojení, tunelové propojení TLS, SASL autorizační kontext a relace musí být znovu navázány.

### <a name="amqp-outbound-port-requirements"></a>AMQP požadavky na Odchozí porty

Klienti, kteří používají připojení AMQP přes protokol TCP, vyžadují, aby byly v místní bráně firewall otevřené porty 5671 a 5672. Spolu s těmito porty může být potřeba otevřít další porty, pokud je povolená funkce [EnableLinkRedirect](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect?view=azure-dotnet) . `EnableLinkRedirect` je nová funkce zasílání zpráv, která pomáhá při přijímání zpráv přeskočit jedno směrování, což pomáhá zvýšit propustnost. Klient by začal komunikovat přímo s back-end službou přes rozsah portů 104XX, jak je znázorněno na následujícím obrázku. 

![Seznam cílových portů][4]

Klient rozhraní .NET selže s SocketException ("byl proveden pokus o přístup k soketu způsobem zakázanému jeho přístupovými oprávněními"), pokud jsou tyto porty blokovány bránou firewall. Funkci lze zakázat nastavením `EnableAmqpLinkRedirect=false` v připojovacím řetězci, která klientům vynutí komunikaci se vzdálenou službou přes port 5671.


### <a name="links"></a>Odkazy

AMQP přenáší zprávy přes odkazy. Odkaz je komunikační cesta vytvořená v relaci, která umožňuje přenos zpráv v jednom směru. vyjednávání stavu přenosu probíhá přes propojení a obousměrně mezi připojenými stranami.

![Snímek obrazovky znázorňující relaci s propojením spojení mezi dvěma kontejnery.][2]

Odkazy mohou být vytvořeny buď kontejnerem, kdykoli, a přes existující relaci, která AMQP liší od mnoha dalších protokolů, včetně HTTP a MQTT, kde iniciování přenosů a cest přenosu je výhradním oprávněním stran, které vytváří připojení soketu.

Kontejner s výzvou k odeslání žádá o odkaz na opačný kontejner, aby přijal odkaz a zvolil roli odesílatele nebo příjemce. Z toho vyplývá, že kontejner může iniciovat vytváření jednosměrných nebo obousměrných komunikačních cest a druhý model jako páry odkazů.

Odkazy jsou pojmenovány a přidruženy k uzlům. Jak je uvedeno na začátku, uzly jsou komunikujícími entitami uvnitř kontejneru.

V Service Bus je uzel přímo ekvivalentní frontě, tématu, odběru nebo podfrontě nedoručených zpráv z fronty nebo předplatného. Název uzlu, který se používá v AMQP, je proto relativní název entity uvnitř oboru názvů Service Bus. Pokud je fronta pojmenovaná, je to `myqueue` také název uzlu AMQP. Předplatné tématu dodržuje konvence HTTP API tak, že se seřadí do kolekce prostředků Subscriptions (odběry), a proto předplatné **Sub** v tématu **mytopic** má název uzlu AMQP **mytopic/Subscriptions/sub**.

Připojující se klient také vyžaduje, aby při vytváření odkazů používal název místního uzlu. Service Bus není na těchto názvech uzlů pojmenována a neinterpretuje je. Balíčky klientů AMQP 1,0 obecně používají schéma k zajištění, že tyto názvy dočasných uzlů jsou v oboru klienta jedinečné.

### <a name="transfers"></a>Převede

Po vytvoření propojení lze prostřednictvím tohoto propojení přenést zprávy. V AMQP se přenos provádí pomocí explicitního gesta protokolu (performative *přenosu* ), které přesouvá zprávu od odesílatele k přijímači přes odkaz. Přenos je dokončený, když je "vyrovnaný", což znamená, že obě strany navázaly sdílené porozumění výsledku tohoto přenosu.

![Diagram znázorňující přenos zprávy mezi odesílatelem a příjemcem a jejich dispozice, která z ní vznikne.][3]

V nejjednodušším případě se odesílatel může rozhodnout odeslat zprávy "předem vyrovnaný", což znamená, že klient nemá zájem o výsledek a příjemce neposkytne zpětnou vazbu k výsledku operace. Tento režim je podporován Service Bus na úrovni protokolu AMQP, ale nezveřejňuje se v žádném z klientských rozhraní API.

Běžným případem je, že zprávy jsou odesílány neuhrazené a příjemce pak indikuje přijetí nebo odmítnutí pomocí performative *Disposition* . K zamítnutí dojde, když příjemce nemůže z nějakého důvodu zprávu přijmout a zpráva zamítnutí obsahuje informace o důvodu, což je struktura chyb definovaná pomocí AMQP. Pokud jsou zprávy odmítnuty z důvodu vnitřní chyby v Service Bus, služba vrátí další informace uvnitř této struktury, které se dají použít k poskytování pomocných parametrů pro podporu pracovníků, Pokud archivujete žádosti o podporu. Další podrobnosti o chybách najdete později.

Speciální forma zamítnutí *je stav,* který indikuje, že příjemce nemá žádné technické objekty pro přenos, ale bez zájmu k vyrovnání přenosu. Tento případ existuje, například když se doručí zpráva Service Bus klientovi a klient se rozhodne "opustit" zprávu, protože nemůže provést práci, která je výsledkem zpracování zprávy. doručení samotné zprávy není při selhání. Variace tohoto stavu je *upravený* stav, který umožňuje změny zprávy při jejím vydání. Tento stav nepoužívá Service Bus v současnosti.

Specifikace AMQP 1,0 definuje další stav dispozice s názvem *přijato*, který konkrétně pomáhá zvládnout obnovení propojení. Obnovení propojení umožňuje obnovit stav propojení a všech nevyřízených dodávek nad novým připojením a relací, pokud došlo ke ztrátě předchozího připojení a relace.

Service Bus nepodporuje obnovení propojení. Pokud klient ztratí připojení k Service Bus s nevyřízeným nevyrovnanou přenosovou zprávou, dojde ke ztrátě přenosu zpráv a klient se musí znovu připojit, znovu vytvořit odkaz a znovu provést přenos.

V takovém případě Service Bus a Event Hubs podporuje "nejméně jednou" přenos, ve kterém může být odesílatel zajištěn pro zprávu uloženou a přijatou, ale nepodporují "právě jednou" přenosy na úrovni AMQP, kde se systém pokusí obnovit propojení a nadále vyjednávat stav doručení, aby se zabránilo duplikaci přenosu zpráv.

Pokud chcete kompenzovat možné duplicitní odesílání, Service Bus podporuje vyhledávání duplicit jako volitelnou funkci ve frontách a tématech. Zjišťování duplicitních záznamů zaznamenává ID všech příchozích zpráv během uživatelsky definovaného časového intervalu a pak tiše vyřazuje všechny zprávy odesílané se stejnými identifikátory zpráv v rámci stejného okna.

### <a name="flow-control"></a>Řízení toku

Kromě výše popsaného modelu řízení toku na úrovni relace má každý odkaz svůj vlastní model řízení toku. Řízení toku na úrovni relace chrání kontejner před tím, než musí zpracovávat příliš mnoho rámců najednou, řízení toku na úrovni propojení vloží aplikaci za poplatek, kolik zpráv chce zpracovat z odkazu a kdy.

![Snímek obrazovky protokolu se zobrazením zdroje, cíle, zdrojového portu, cílového portu a názvu protokolu. V prvním řádku je cílový port 10401 (0x28 A 1) popsaný černě.][4]

V případě odkazu může k přenosu docházet pouze v případě, že odesílatel má dostatek *kreditů propojení*. Kredit propojení je čítač nastavený příjemcem pomocí performative *Flow* , který je vymezen na odkaz. Když je odesilateli přiřazen odkaz na akreditiv, pokusí se ho použít k doručování zpráv. Každé doručení zprávy sníží kredit zbývajícího propojení o 1. Po použití kreditu propojení se dokončí doručení.

Pokud je Service Bus v roli přijímače, okamžitě poskytne odesilateli s důrazem na kredit, aby bylo možné zprávy odesílat okamžitě. Při použití odkazu na odkaz Service Bus příležitostně pošle *tok* performative k aktualizaci zůstatku kreditu propojení.

V roli odesílatele Service Bus odesílá zprávy pro použití libovolného nedokončeného kreditu propojení.

Volání Receive na úrovni rozhraní API překládá do služby *Flow* performative, která se Service Bus odesílá klientovi, a Service Bus tuto stranu využívá, když vezme první dostupnou, odemčenou zprávu z fronty, zamkne ji a přenáší. Pokud není k dispozici žádná zpráva pro doručení, zůstane veškerý nedokončený kredit na základě propojení navázaného s touto konkrétní entitou zaznamenán v daném pořadí a zprávy budou zamčeny a přeneseny, jakmile budou k dispozici, aby používaly veškerý neexistující kredit.

Zámek zprávy se uvolní při přesunu přenosu do jednoho z *přijatých*, *zamítnutých* nebo *vydaných* koncových stavů. Zpráva se odebere z Service Bus, když je stav terminálu *přijatý*. Zůstane v Service Bus a bude doručena do dalšího přijímače, když přenos dosáhne kteréhokoli z ostatních stavů. Service Bus automaticky přesune zprávu do fronty nedoručených zpráv entity, když dosáhne maximálního počtu doručení pro entitu z důvodu opakovaného zamítnutí nebo vydání.

I když rozhraní API pro Service Bus tuto možnost dnes nezveřejňuje, může klient s protokolem AMQP na nižší úrovni použít model propojení-kredit k tomu, aby se při každé žádosti o přijetí změn na model "nabízeného oznámení" vydávala velký počet kreditů propojení a pak přijímaly zprávy, jakmile budou k dispozici bez jakékoliv další interakce. Nabízení je podporováno prostřednictvím nastavení vlastnosti [MessagingFactory. PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) nebo [MessageReceiver. PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) . Pokud jsou nenulové, klient AMQP ho použije jako kredit propojení.

V tomto kontextu je důležité si uvědomit, že hodiny pro vypršení zámku zprávy v rámci entity začínají při pořízení zprávy z entity, nikoli při umístění zprávy na kabel. Pokaždé, když klient indikuje připravenost přijímat zprávy pomocí kreditu propojení, očekává se, že budou aktivně získávat zprávy přes síť a budou připravené na jejich zpracování. V opačném případě může dojít k vypršení platnosti zámku zprávy, než bude zpráva ještě doručena. Použití řízení toku na základě propojení by mělo přímo odrážet připravenost k dispozici, aby bylo možné řešit dostupné zprávy odesílané příjemci.

V následujících částech najdete informace o schématu toku performative během různých interakcí rozhraní API. Každá část popisuje jinou logickou operaci. Některé z těchto interakcí můžou být opožděné, což znamená, že se dají provádět jenom v případě potřeby. Vytvoření odesílatele zprávy nemusí způsobit síťovou interakci, dokud se první zpráva neodešle nebo nepožaduje.

Šipky v následující tabulce ukazují směr toku performative.

#### <a name="create-message-receiver"></a>Vytvořit příjemce zprávy

| Klient | Service Bus |
| --- | --- |
| --> připojit (<br/>název = {Link Name};<br/>Handle = {číselný popisovač},<br/>role =**přijímač**,<br/>zdroj = {název entity},<br/>cíl = {ID odkazu na klienta}<br/>) |Klient se připojí k entitě jako příjemce. |
| Service Bus odpovědi připojené ke konci odkazu |< – připojit (<br/>název = {Link Name};<br/>Handle = {číselný popisovač},<br/>role =**odesilatel**,<br/>zdroj = {název entity},<br/>cíl = {ID odkazu na klienta}<br/>) |

#### <a name="create-message-sender"></a>Vytvořit odesílatele zprávy

| Klient | Service Bus |
| --- | --- |
| --> připojit (<br/>název = {Link Name};<br/>Handle = {číselný popisovač},<br/>role =**odesilatel**,<br/>zdroj = {ID odkazu na klienta},<br/>cíl = {název entity}<br/>) |Žádná akce |
| Žádná akce |< – připojit (<br/>název = {Link Name};<br/>Handle = {číselný popisovač},<br/>role =**přijímač**,<br/>zdroj = {ID odkazu na klienta},<br/>cíl = {název entity}<br/>) |

#### <a name="create-message-sender-error"></a>Vytvořit odesílatele zprávy (chyba)

| Klient | Service Bus |
| --- | --- |
| --> připojit (<br/>název = {Link Name};<br/>Handle = {číselný popisovač},<br/>role =**odesilatel**,<br/>zdroj = {ID odkazu na klienta},<br/>cíl = {název entity}<br/>) |Žádná akce |
| Žádná akce |< – připojit (<br/>název = {Link Name};<br/>Handle = {číselný popisovač},<br/>role =**přijímač**,<br/>zdroj = null,<br/>Target = null<br/>)<br/><br/><--detach (<br/>Handle = {číselný popisovač},<br/>uzavřeno =**true**,<br/>Chyba = {informace o chybě}<br/>) |

#### <a name="close-message-receiversender"></a>Zavřít příjemce nebo odesílatele zprávy

| Klient | Service Bus |
| --- | --- |
| --> odpojit (<br/>Handle = {číselný popisovač},<br/>uzavřeno =**true**<br/>) |Žádná akce |
| Žádná akce |<--detach (<br/>Handle = {číselný popisovač},<br/>uzavřeno =**true**<br/>) |

#### <a name="send-success"></a>Odeslat (úspěch)

| Klient | Service Bus |
| --- | --- |
| --> přenos (<br/>Delivery-ID = {číselný popisovač},<br/>Značka doručení = {Binary popisovač},<br/>vyrovnané =**false**,, více =**false**,<br/>State =**null**,<br/>Resume =**false**<br/>) |Žádná akce |
| Žádná akce |< – dispoziční (<br/>role = přijímač,<br/>First = {Delivery ID},<br/>Last = {Delivery ID},<br/>vyrovnané =**true**,<br/>stav =**přijato**<br/>) |

#### <a name="send-error"></a>Odeslat (chyba)

| Klient | Service Bus |
| --- | --- |
| --> přenos (<br/>Delivery-ID = {číselný popisovač},<br/>Značka doručení = {Binary popisovač},<br/>vyrovnané =**false**,, více =**false**,<br/>State =**null**,<br/>Resume =**false**<br/>) |Žádná akce |
| Žádná akce |< – dispoziční (<br/>role = přijímač,<br/>First = {Delivery ID},<br/>Last = {Delivery ID},<br/>vyrovnané =**true**,<br/>stav =**zamítnuto**(<br/>Chyba = {informace o chybě}<br/>)<br/>) |

#### <a name="receive"></a>Přijmout

| Klient | Service Bus |
| --- | --- |
| --> tok (<br/>odkaz-kredit = 1<br/>) |Žádná akce |
| Žádná akce |Přenos < (<br/>Delivery-ID = {číselný popisovač},<br/>Značka doručení = {Binary popisovač},<br/>vyrovnané =**false**,<br/>více =**false**,<br/>State =**null**,<br/>Resume =**false**<br/>) |
| --> dispozice (<br/>role =**přijímač**,<br/>First = {Delivery ID},<br/>Last = {Delivery ID},<br/>vyrovnané =**true**,<br/>stav =**přijato**<br/>) |Žádná akce |

#### <a name="multi-message-receive"></a>Přijímání více zpráv

| Klient | Service Bus |
| --- | --- |
| --> tok (<br/>odkaz-kredit = 3<br/>) |Žádná akce |
| Žádná akce |Přenos < (<br/>Delivery-ID = {číselný popisovač},<br/>Značka doručení = {Binary popisovač},<br/>vyrovnané =**false**,<br/>více =**false**,<br/>State =**null**,<br/>Resume =**false**<br/>) |
| Žádná akce |Přenos < (<br/>Delivery-ID = {číselný popisovač + 1},<br/>Značka doručení = {Binary popisovač},<br/>vyrovnané =**false**,<br/>více =**false**,<br/>State =**null**,<br/>Resume =**false**<br/>) |
| Žádná akce |Přenos < (<br/>Delivery-ID = {číselný popisovač + 2},<br/>Značka doručení = {Binary popisovač},<br/>vyrovnané =**false**,<br/>více =**false**,<br/>State =**null**,<br/>Resume =**false**<br/>) |
| --> dispozice (<br/>role = přijímač,<br/>First = {Delivery ID},<br/>Last = {Delivery ID + 2},<br/>vyrovnané =**true**,<br/>stav =**přijato**<br/>) |Žádná akce |

### <a name="messages"></a>Zprávy

Následující části vysvětlují, které vlastnosti ze standardních částí zprávy AMQP používá Service Bus a jak se mapují na Service Bus sadu rozhraní API.

Jakákoli vlastnost, kterou musí aplikace definovat, by měla být namapována na `application-properties` mapu AMQP.

#### <a name="header"></a>header

| Název pole | Využití | Název rozhraní API |
| --- | --- | --- |
| dočasných |- |- |
| upřednostněn |- |- |
| TTL |Doba, po kterou se má tato zpráva zasílat |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| First – získá |- |- |
| doručení – počet |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Název pole | Využití | Název rozhraní API |
| --- | --- | --- |
| ID zprávy |Identifikátor volného formátu definovaného aplikací pro tuto zprávu. Používá se pro detekci duplicit. |[Parametr](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| user-id |Identifikátor uživatele definovaný aplikací, není interpretován pomocí Service Bus. |Nedostupné prostřednictvím rozhraní Service Bus API. |
| na |Identifikátor cíle definovaného aplikací, není interpretován pomocí Service Bus. |[Schopn](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| závislosti |Identifikátor účelu zprávy definované aplikací, není interpretován pomocí Service Bus. |[Popisek](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| odpovědět na |Indikátor odpovědi na cestu definovaný aplikací, není interpretován pomocí Service Bus. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlation-id |Identifikátor korelace definovaný aplikací, není interpretován pomocí Service Bus. |[ID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| typ obsahu |Indikátor typu obsahu definovaného aplikací pro tělo, které není interpretováno Service Bus. |[Třída](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| kódování obsahu |Symbol pro kódování obsahu definovaného aplikací pro tělo, které není interpretováno Service Bus. |Nedostupné prostřednictvím rozhraní Service Bus API. |
| absolutní – doba vypršení platnosti |Deklaruje, na jakém místě vyprší absolutní Okamžitá zpráva. Ignoruje se při vstupu (pozoruje se hlavička TTL), která je pro výstup směrodatná. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| čas vytvoření |Deklaruje, v jakém čase byla zpráva vytvořena. Nepoužívá se Service Bus |Nedostupné prostřednictvím rozhraní Service Bus API. |
| ID skupiny |Identifikátor definovaný aplikací pro související sadu zpráv. Používá se pro Service Bus relace. |[SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| pořadí skupin |Čítač identifikující číslo relativního pořadí zprávy v relaci. Ignorováno Service Bus. |Nedostupné prostřednictvím rozhraní Service Bus API. |
| reply-to-Group-ID |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Poznámky ke zprávě

K dispozici je několik dalších vlastností zpráv Service Bus, které nejsou součástí vlastností zprávy AMQP a jsou předávány spolu se `MessageAnnotations` zprávou.

| Klíč mapy poznámek | Využití | Název rozhraní API |
| --- | --- | --- |
| x-opt-Schedule-front-Time | Deklaruje, v jakém čase by se měla zpráva zobrazovat v entitě. |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x – opt-partition-Key | Klíč definovaný aplikací, který určuje, ve kterém oddílu se má zpráva nakládat. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt-Via-partition-Key | Hodnota klíče oddílu definovaného aplikací, pokud se má transakce použít k posílání zpráv prostřednictvím fronty přenosu. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-zařazování za běhu | Čas UTC definovaný službou, který představuje skutečný čas enqueuing zprávy Ignoruje se u vstupu. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt-Sequence-Number | Jedinečné číslo definované službou přiřazené zprávě | [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x – opt-offset | Pořadové číslo zprávy ve frontě definované službou | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x – výslovný souhlas – zamčené – až | Definováno službou. Datum a čas, kdy bude zpráva uzamčena ve frontě nebo předplatném. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-nedoručených zpráv – zdroj | Definováno službou. Pokud je zpráva přijata z fronty nedoručených zpráv, zdroji původní zprávy. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Schopnost transakce

Skupiny transakcí seskupují dvě nebo více operací do rozsahu provádění. V takovém případě taková transakce musí zajistit, aby všechny operace patřící do dané skupiny operací byly buď úspěšné, nebo selžou společně.
Operace jsou seskupeny podle identifikátoru `txn-id` .

Pro transakční interakci klient funguje jako `transaction controller` , který řídí operace, které by se měly seskupit dohromady. Služba Service Bus funguje jako `transactional resource` a provede práci, jak požaduje `transaction controller` .

Klient a služba komunikují přes `control link` , který je vytvořen klientem. `declare`Zprávy a `discharge` jsou odesílány řadičem prostřednictvím odkazu ovládacího prvku pro přidělení a dokončení transakcí (nepředstavuje vymezení transakční práce). Na tomto odkazu se neprovádí skutečná zpráva odeslat nebo přijmout. Každá požadovaná transakční operace je explicitně identifikována s požadovaným `txn-id` a proto může nastat u libovolného odkazu na připojení. Pokud je odkaz ovládacího prvku uzavřený, zatímco existují Neuvolněné transakce, které vytvořil, pak se všechny takové transakce okamžitě vrátí zpět a při pokusech o další transakční práci na nich dojde k selhání. Zprávy na ovládacím prvku odkaz na ovládací prvek nesmí být předem vyrovnány.

Každé připojení musí iniciovat svůj vlastní odkaz na ovládací prvek, aby bylo možné spouštět a končit transakce. Služba definuje speciální cíl, který funguje jako `coordinator` . Klient nebo kontroler vytvoří odkaz na ovládací prvek na tento cíl. Odkaz na ovládací prvek je mimo hranici entity, to znamená, že stejný odkaz na ovládací prvek lze použít k zahájení a uvolnění transakcí pro více entit.

#### <a name="starting-a-transaction"></a>Spuštění transakce

K zahájení transakční práce. kontroler musí `txn-id` od koordinátora získat. Provede to odesláním `declare` zprávy typu. Pokud je deklarace úspěšná, koordinátor odpoví na výsledek dispozice, který je přiřazený `txn-id` .

| Klient (kontroler) | Směr | Service Bus (koordinátor) |
| :--- | :---: | :--- |
| pojovat<br/>název = {Link Name};<br/>... ,<br/>role =**odesilatel**,<br/>cíl =**koordinátor**<br/>) | ------> |  |
|  | <------ | pojovat<br/>název = {Link Name};<br/>... ,<br/>Target = Coordinator ()<br/>) |
| Transfer<br/>doručení – ID = 0,...)<br/>{AmqpValue (**Declare ()**)}| ------> |  |
|  | <------ | akcí <br/> First = 0, Last = 0, <br/>State =**deklarované**(<br/>**TXN-ID**= {ID transakce}<br/>))|

#### <a name="discharging-a-transaction"></a>Vyúčtování transakce

Řadič uzavře transakční práci odesláním `discharge` zprávy koordinátorovi. Kontroler indikuje, že chce potvrdit nebo vrátit zpět transakční práci nastavením `fail` příznaku v těle vypuštění. Pokud koordinátor nemůže dokončit vypuštění, zpráva se odmítne s tímto výsledkem, který má za následek provedení `transaction-error` .

> Poznámka: selhání = true odkazuje na vrácení transakce zpět a selhání = false odkazuje na potvrzení.

| Klient (kontroler) | Směr | Service Bus (koordinátor) |
| :--- | :---: | :--- |
| Transfer<br/>doručení – ID = 0,...)<br/>{AmqpValue (Declare ())}| ------> |  |
|  | <------ | akcí <br/> First = 0, Last = 0, <br/>State = deklarované (<br/>TXN-ID = {ID transakce}<br/>))|
| | . . . <br/>Transakční práce<br/>na jiných odkazech<br/> . . . |
| Transfer<br/>doručení – ID = 57,...)<br/>{ AmqpValue (<br/>**Vybíjení (TXN-ID = 0, <br/> selhání = false)**)}| ------> |  |
| | <------ | akcí <br/> First = 57, Last = 57, <br/>stav =**přijato ()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Odeslání zprávy v transakci

Veškerá transakční práce se provádí s transakčním stavem doručení `transactional-state` , který přenáší TXN-ID. V případě odesílání zpráv je transakční stav přenášen rámcem přenosu zprávy. 

| Klient (kontroler) | Směr | Service Bus (koordinátor) |
| :--- | :---: | :--- |
| Transfer<br/>doručení – ID = 0,...)<br/>{AmqpValue (Declare ())}| ------> |  |
|  | <------ | akcí <br/> First = 0, Last = 0, <br/>State = deklarované (<br/>TXN-ID = {ID transakce}<br/>))|
| Transfer<br/>popisovač = 1,<br/>doručení – ID = 1, <br/>**stav = <br/> TransactionalState ( <br/> TXN-ID = 0)**)<br/>Délka| ------> |  |
| | <------ | akcí <br/> First = 1, Last = 1, <br/>State =**TransactionalState ( <br/> TXN-ID = 0, <br/> výsledek = přijato ()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Likvidace zprávy v transakci

Dispozice zprávy obsahuje operace jako `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer` . Chcete-li provést tyto operace v rámci transakce, předejte `transactional-state` s dispoziční hodnotu.

| Klient (kontroler) | Směr | Service Bus (koordinátor) |
| :--- | :---: | :--- |
| Transfer<br/>doručení – ID = 0,...)<br/>{AmqpValue (Declare ())}| ------> |  |
|  | <------ | akcí <br/> First = 0, Last = 0, <br/>State = deklarované (<br/>TXN-ID = {ID transakce}<br/>))|
| | <------ |Transfer<br/>Handle = 2;<br/>doručení – ID = 11, <br/>stav = null)<br/>Délka|  
| akcí <br/> First = 11, Last = 11, <br/>State =**TransactionalState ( <br/> TXN-ID = 0, <br/> výsledek = přijato ()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Pokročilé možnosti Service Bus

Tato část se věnuje pokročilým funkcím Azure Service Bus, které jsou založené na konceptech rozšíření na AMQP, v současné době vyvíjí OASIS technický výbor pro AMQP. Service Bus implementuje nejnovější verze těchto konceptů a přijme změny, které byly zavedeny, protože tyto koncepty dosahují standardního stavu.

> [!NOTE]
> Rozšířené operace Service Bus zasílání zpráv se podporují prostřednictvím vzoru požadavků a odpovědí. Podrobnosti o těchto operacích jsou popsány v článku [AMQP 1,0 v tématu Service Bus: operace založené na odezvě na základě požadavků](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>Správa AMQP

Specifikace správy AMQP je prvním z rozšíření konceptů popsaných v tomto článku. Tato specifikace definuje sadu protokolů nad protokolem AMQP, která umožňuje interakci správy s infrastrukturou zasílání zpráv přes AMQP. Specifikace definuje obecné operace, jako je *vytváření*, *čtení*, *aktualizace* a *odstranění* pro správu entit v infrastruktuře zasílání zpráv a sadu operací dotazů.

Všechna tato gesta vyžadují interakci mezi klientem a infrastrukturou zasílání zpráv, a proto specifikace definuje, jak modelovat tento vzor interakce na AMQP: klient se připojí k infrastruktuře zasílání zpráv, iniciuje relaci a pak vytvoří dvojici odkazů. Na jednom odkazu klient funguje jako odesílatel a druhý jeho činnost funguje jako příjemce, takže vytvoří dvojici odkazů, které mohou fungovat jako obousměrný kanál.

| Logická operace | Klient | Service Bus |
| --- | --- | --- |
| Vytvořit cestu k žádosti o odpověď |--> připojit (<br/>název = {*Link Name*};<br/>Handle = {*číselný popisovač*},<br/>role =**odesilatel**,<br/>zdroj =**null**,<br/>target = "myentity/$management"<br/>) |Žádná akce |
| Vytvořit cestu k žádosti o odpověď |Žádná akce |\<-- attach(<br/>název = {*Link Name*};<br/>Handle = {*číselný popisovač*},<br/>role =**přijímač**,<br/>zdroj = null,<br/>target = "myentity"<br/>) |
| Vytvořit cestu k žádosti o odpověď |--> připojit (<br/>název = {*Link Name*};<br/>Handle = {*číselný popisovač*},<br/>role =**přijímač**,<br/>Source = "myentity/$management",<br/>target = "MyClient $ ID"<br/>) | |
| Vytvořit cestu k žádosti o odpověď |Žádná akce |\<-- attach(<br/>název = {*Link Name*};<br/>Handle = {*číselný popisovač*},<br/>role =**odesilatel**,<br/>Source = "myentity",<br/>target = "MyClient $ ID"<br/>) |

Díky tomu, že je tato dvojice odkazů zavedena, implementace žádosti a odpovědi je jednoduchá: požadavek je zpráva odeslaná entitě v infrastruktuře zasílání zpráv, která tento model zná. V tomto požadavku-zpráva je pole *Odpovědět* v oddílu *vlastnosti* nastaveno na *cílový* identifikátor odkazu, na který chcete odpověď poskytnout. Entita zpracování požadavek zpracuje a pak doručí odpověď přes odkaz, jehož *cílový* identifikátor odpovídá identifikátoru *odpovědi* .

Vzor zjevně vyžaduje, aby byl kontejner klienta a identifikátor generovaný klientem pro cíl odpovědi jedinečný ve všech klientech, a z důvodů zabezpečení také obtížné odhadnout.

Výměna zpráv použitá pro protokol pro správu a pro všechny ostatní protokoly, které používají stejný vzor, se provádí na úrovni aplikace. nedefinují nové gesta na úrovni protokolu AMQP. To je úmyslné, aby aplikace mohly využít výhod těchto rozšíření s vyhovujícími zásobníky AMQP 1,0.

Service Bus aktuálně neimplementuje žádné základní funkce specifikace správy, ale vzor požadavků a odpovědí definovaný specifikací správy je základem pro funkci zabezpečení založenou na deklaracích identity a téměř všechny pokročilé funkce popsané v následujících částech:

### <a name="claims-based-authorization"></a>Autorizace na základě deklarace identity

Koncept specifikace CBS (AMQP based-Based-Authorization) sestaví ve vzoru požadavků a odpovědí na specifikace správy a popisuje zobecněný model, jak používat federované tokeny zabezpečení s AMQP.

Výchozí model zabezpečení AMQP popisovaný v úvodu vychází z SASL a integruje se s metodou handshake připojení AMQP. Použití SASL má výhodu, že poskytuje rozšiřitelný model, pro který je nastavená sada mechanismů, ze kterých může zvýhodněný jakýkoli protokol, který je vydaný SASL. Z těchto mechanismů jsou "obyčejný" pro přenos uživatelských jmen a hesel, "externí" pro navázání zabezpečení na úrovni TLS, "anonymní", aby bylo možné vyjádřit absenci explicitního ověřování/autorizace a širokou škálu dalších mechanismů, které umožňují předávání ověřovacích nebo autorizačních přihlašovacích údajů nebo tokenů.

Integrace SASL pro AMQP má dvě nevýhody:

* Všechny přihlašovací údaje a tokeny jsou vymezeny na připojení. Infrastruktura zasílání zpráv může chtít poskytovat rozlišené řízení přístupu na základě jednotlivých entit. například umožnění nosiči tokenu, který se odešle do fronty, ale ne do fronty B. V případě kontextu autorizace ukotveného k připojení není možné použít jediné připojení a pro frontu a a frontu B používat jiné přístupové tokeny.
* Přístupové tokeny jsou obvykle platné pouze po dobu omezeného času. Tato platnost vyžaduje, aby uživatel pravidelně znovu získal tokeny a poskytoval tokenu tokenu, aby odmítl vystavení nového tokenu v případě, že se změnila přístupová oprávnění uživatele. Připojení AMQP můžou být pro dlouhá časová období poslední. Model SASL poskytuje možnost nastavit token v době připojení, což znamená, že infrastruktura zasílání zpráv buď musí odpojit klienta, pokud vyprší platnost tokenu, nebo musí přijmout riziko, že by bylo možné pokračovat v komunikaci s klientskými právy, která byla přístupová práva k dočasnému odvolána.

Specifikace CBS AMQP implementovaná pomocí Service Bus umožňuje elegantní alternativní řešení pro oba tyto problémy: umožňuje klientovi přidružit přístupové tokeny k jednotlivým uzlům a aktualizovat tyto tokeny před vypršením platnosti, aniž by přerušili tok zpráv.

CBS definuje uzel virtuální správy s názvem *$CBS*, který má poskytovat infrastruktura zasílání zpráv. Uzel správy přijímá tokeny jménem všech ostatních uzlů v infrastruktuře zasílání zpráv.

Gesto protokolu je výměna požadavků a odpovědí definovaná specifikací správy. To znamená, že klient vytvoří dvojici odkazů s uzlem *$CBS* a pak předá požadavek na odchozí odkaz a potom počká na odpověď na příchozí odkaz.

Zpráva požadavku má následující vlastnosti aplikace:

| Klíč | Volitelné | Typ hodnoty | Obsah hodnoty |
| --- | --- | --- | --- |
| operation |No |řetězec |**token Put** |
| typ |No |řetězec |Typ vytvářeného tokenu. |
| name |No |řetězec |Cílová skupina, na kterou se vztahuje token. |
| vypršení platnosti |Yes |časové razítko |Čas vypršení platnosti tokenu. |

Vlastnost *Name* určuje entitu, ke které je token přidružen. V Service Bus se jedná o cestu k frontě nebo k tématu nebo předplatnému. Vlastnost *Type* určuje typ tokenu:

| Typ tokenu | Popis tokenu | Typ textu | Poznámky |
| --- | --- | --- | --- |
| AMQP: JWT |JSON Web Token (JWT) |AMQP hodnota (String) |Ještě není k dispozici. |
| AMQP: SWT |Jednoduchý webový token (SWT) |AMQP hodnota (String) |Podporuje se jenom pro tokeny SWT vydané AAD/ACS. |
| ServiceBus. Windows. NET: sastoken |Service Bus token SAS |AMQP hodnota (String) |- |

Tokeny udělují práva. Service Bus ví o třech základních právech: "Odeslat" umožňuje odesílání, "naslouchání" umožňuje příjem a "Správa" umožňuje manipulaci s entitami. SWT tokeny vydané AAD/ACS výslovně zahrnují tato práva jako deklarace identity. Service Bus tokeny SAS odkazují na pravidla konfigurovaná pro obor názvů nebo entitu a tato pravidla jsou nakonfigurovaná s právy. Podpis tokenu s klíčem přidruženým k tomuto pravidlu proto umožní tokenu vyjadřovat příslušná práva. Token přidružený k entitě s použitím *tokenu Put* umožňuje připojenému klientovi pracovat s entitou podle oprávnění tokenu. Odkaz, který klient převezme u role *Odesílatel* , vyžaduje oprávnění Odeslat. při převzetí u role *přijímače* je vyžadována možnost naslouchat.

Zpráva s odpovědí obsahuje následující hodnoty *vlastností aplikace* .

| Klíč | Volitelné | Typ hodnoty | Obsah hodnoty |
| --- | --- | --- | --- |
| Stavový kód |No |int |Kód odpovědi HTTP **[RFC2616]**. |
| Popis stavu |Yes |řetězec |Popis stavu |

Klient může opakovaně volat *tokeny Put* a pro každou entitu v infrastruktuře zasílání zpráv. Tokeny jsou vymezeny na aktuálního klienta a ukotveny k aktuálnímu připojení, což znamená, že server při poklesu připojení vyřazuje všechny zachované tokeny.

Aktuální implementace Service Bus povoluje pouze CBS ve spojení s metodou SASL "ANONYMOUS". Před SASL metodou handshake musí být připojení SSL/TLS vždy k dispozici.

ANONYMNÍ mechanismus musí být proto podporován vybraným klientem AMQP 1,0. Anonymní přístup znamená, že počáteční handshake připojení, včetně vytvoření počáteční relace, se stane bez Service Bus vědět, kdo vytváří připojení.

Po navázání připojení a relace připojí odkazy na uzel *$CBS* a odeslání žádosti o *token Put* jsou jediné povolené operace. Po navázání spojení se musí úspěšně nastavit platný token s použitím požadavku *Put-token* pro některý uzel entity do 20 sekund. připojení je v opačném případě přerušeno Service Bus.

Klient je následně zodpovědný za sledování vypršení platnosti tokenu. Když vyprší platnost tokenu, Service Bus okamžitě odpojí všechny odkazy na připojení k příslušné entitě. Aby nedocházelo k potížím, klient může kdykoli nahradit token pro uzel novým pomocí uzlu pro správu služby Virtual *$CBS* se stejným gestem *Put* a bez nutnosti získat přenos dat do datové části, který je v různých propojeních.

### <a name="send-via-functionality"></a>Funkce pro odesílání prostřednictvím

[Odesilatel odeslání přes/Transfer](service-bus-transactions.md#transfers-and-send-via) je funkce, která umožňuje službě Service Bus předat danou zprávu cílové entitě prostřednictvím jiné entity. Tato funkce se používá k provádění operací napříč entitami v jedné transakci.

Pomocí této funkce vytvoříte odesílatele a vytvoříte odkaz na `via-entity` . Při vytváření propojení se předávají Další informace, aby se navázal skutečný cíl zpráv nebo přenosů na tomto odkazu. Po úspěšném připojení budou všechny zprávy odeslané tímto odkazem automaticky předávány do *cílové entity* prostřednictvím *entity*. 

> Poznámka: před vytvořením tohoto odkazu je nutné provést ověření pro entitu *Via* a *cíl-entita* .

| Klient | Směr | Service Bus |
| :--- | :---: | :--- |
| pojovat<br/>název = {Link Name};<br/>role = odesilatel,<br/>zdroj = {ID odkazu na klienta},<br/>Target =**{Via-entity}**,<br/>**Properties = map [( <br/> com. Microsoft: Transfer-Destination-address = <br/> {Destination-entity})]** ) | ------> | |
| | <------ | pojovat<br/>název = {Link Name};<br/>role = přijímač,<br/>zdroj = {ID odkazu na klienta},<br/>Target = {Via-entity},<br/>Properties = map [(<br/>com. Microsoft: Transfer-cíl-adresa =<br/>{Destination-entity})] ) |

## <a name="next-steps"></a>Další kroky

Další informace o AMQP najdete na následujících odkazech:

* [Přehled Service Bus AMQP]
* [Podpora AMQP 1,0 pro dělené fronty a témata pro Service Bus]
* [AMQP v Service Bus pro Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Přehled Service Bus AMQP]: service-bus-amqp-overview.md
[Podpora AMQP 1,0 pro dělené fronty a témata pro Service Bus]: 
[AMQP in Service Bus for Windows Server]: /previous-versions/service-bus-archive/dn574799(v=azure.100)

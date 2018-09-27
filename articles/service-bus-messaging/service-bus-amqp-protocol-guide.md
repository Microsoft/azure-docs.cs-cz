---
title: V Azure Service Bus a Event Hubs Průvodce protokolem AMQP 1.0 | Dokumentace Microsoftu
description: Průvodce protokolem výrazy a popis protokolu AMQP 1.0 v Azure Service Bus a Event Hubs
services: service-bus-messaging,event-hubs
documentationcenter: .net
author: clemensv
manager: timlt
editor: ''
ms.assetid: d2d3d540-8760-426a-ad10-d5128ce0ae24
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2018
ms.author: clemensv
ms.openlocfilehash: 75c6b5c34559ad17f662c895352bff5a58da00d4
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395844"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>V Azure Service Bus a Event Hubs Průvodce protokolem AMQP 1.0

Rozšířené zprávy služby Řízení front protokol 1.0 je standardizované rámce a přenos protokol pro asynchronně, bezpečně a spolehlivě přenos zpráv mezi dvěma stranami. Je primární protokol Azure zasílání zpráv Service Bus a Azure Event Hubs. Obě služby také podporují protokol HTTPS. Speciální SBMP protokol, který je také podporována je vyřazován ve prospěch AMQP.

AMQP 1.0 je výsledkem obecných oborových spolupráce, který svedla dohromady dodavatelů middlewaru, jako jsou Microsoft a Red Hat, s mnoha uživateli zasílání zpráv middlewaru, jako je například JP Morgan podstatou představující oboru finančních služeb. Normalizaci technické fórum pro specifikace protokol a rozšíření protokolu AMQP je OASIS a získala formální schválení jako mezinárodního standardu jako 19494 ISO/IEC.

## <a name="goals"></a>Cíle

Tento článek stručně shrnuje protokolu AMQP 1.0, zasílání zpráv specifikace spolu s malou sadu návrhům specifikací rozšíření, které jsou aktuálně finalizován v technického výboru OASIS AMQP základní koncepty a vysvětluje, jak Azure Service Bus implementuje a staví na tyto specifikace.

Cílem je každý vývojář pomocí jakékoli existující klientské zásobník protokolu AMQP 1.0 na libovolné platformě mohli pracovat s Azure Service Bus pomocí protokolu AMQP 1.0.

Společné pro obecné účely zásobníky protokolu AMQP 1.0, jako je například Apache kanálem nebo AMQP.NET Lite již implementují všechny protokoly jádra protokolu AMQP 1.0. Tyto základní gesta jsou někdy zabaleny s vyšší úrovně rozhraní API; Apache kanálem i nabízí dvě rozhraní API imperativní Messenger a reaktivní reaktor rozhraní API.

V následující diskuse jsme předpokládají, že správu připojení AMQP, relace a odkazy a manipulaci s přenosy rámce a řízení toku jsou zpracovávány příslušných zásobníku (jako je například Apache kanálem-C) a nevyžadují, aby mnoho, pokud existují zvláštní pozornost od vývojářů aplikací. Předpokládáme abstraktně existenci několik rozhraní API primitivních elementů jako možnost pro připojení a vytvořte nějakou formu *odesílatele* a *příjemce* abstraktní objekty, které mají některé tvar `send()` a `receive()` operací, v uvedeném pořadí.

Když hovoříte o pokročilé funkce služby Azure Service Bus, jako je procházení zpráv nebo správu relací, tyto funkce jsou vysvětlené v podmínkách AMQP, ale také jako vrstvené pseudo implementace nad tato abstrakce předpokládá, že rozhraní API.

## <a name="what-is-amqp"></a>Co je AMQP?

AMQP je rámce a přenos protokolu. Rámce znamená, že poskytuje strukturu pro binární datové proudy, které budou plout v obou směrech připojení k síti. Struktury poskytuje vymezení u různých bloků dat, kterým se říká *snímků*, jenž bude vyměněn mezi připojených stranami. Funkce přenosu Ujistěte se, že oba komunikujících stran může vytvořit sdílený orientujte při se přenáší snímků, i když přenosů musí být považuje za dokončené.

Na rozdíl od dříve vypršela platnost verze konceptu vytvářených AMQP pracovní skupiny, které jsou stále používán několik zprostředkovatele zpráv protokolu AMQP 1.0 konečné a standardizovaným pracovní skupiny není stanovit existenci zprostředkovatele zpráv nebo žádné konkrétní topologii pro entity uvnitř zprostředkovatele zpráv.

Protokol lze použít pro symetrické komunikaci peer-to-peer, pro interakci s zprostředkovatele zpráv, které podporují fronty a publikování/odběr entity, stejně jako Azure Service Bus. To lze také pro interakci s infrastruktura zasílání zpráv kde vzory interakcí se liší od pravidelných front, jako je tomu u služby Azure Event Hubs. Chová podobně jako u front, když se do něj odesílají události centra událostí, ale pracuje jako služby serial úložiště při čtení z něj; události vypadá trochu páskové jednotky. Klient vybere posun do datového proudu k dispozici data a pak poskytuje všechny události z tohoto posun na nejnovější verzi dostupnou.

Protokol AMQP 1.0 je navržena jako rozšiřitelný, umožňuje dále specifikace vylepšit možnosti. Znázornění tří rozšíření specifikace probírané v tomto dokumentu. Pro komunikaci přes protokol HTTPS a WebSockets infrastruktury konfigurace nativního porty TCP protokolu AMQP může být obtížné. Specifikace vazby definuje, jak vrstvy protokolu AMQP přes Websocket. Pro interakci s infrastruktura zasílání zpráv žádost odpověď způsobem pro účely správy nebo k poskytování pokročilé funkce, specifikace správy AMQP definuje primitivy vyžaduje základní interakce. Specifikace deklarace identity na základě zabezpečení AMQP pro integraci modelu federovanou autorizaci, definuje, jak přidružit a obnovit autorizačních tokenech přidružené odkazy.

## <a name="basic-amqp-scenarios"></a>Základní scénáře AMQP

Tato část vysvětluje základní použití protokolu AMQP 1.0 s Azure Service Bus, která zahrnuje vytváření připojení, relace a odkazy a přenos zpráv do a z entit služby Service Bus, jako jsou fronty, témata a odběry.

Specifikace protokolu AMQP 1.0 je nejvíce autoritativní zdroj Další informace o tom, jak funguje AMQP, ale specifikaci byla zapsána přesně vodítko pro implementaci a nechcete naučit protokolu. Tato část se zaměřuje na představení tolik terminologie podle potřeby pro popisující, jak Service Bus pomocí protokolu AMQP 1.0. Ucelenější Úvod do AMQP, jakož i širší diskuzi o protokolu AMQP 1.0, můžete zkontrolovat [tento videokurz][this video course].

### <a name="connections-and-sessions"></a>Připojení a relace

AMQP volá komunikující programy *kontejnery*; tyto obsahovat *uzly*, které jsou komunikující entity v rámci těchto kontejnerů. Fronta může být takový uzel. AMQP umožňuje multiplexing, takže jedno připojení můžete použít pro mnoho komunikační trasy mezi uzly. klientem aplikace může například souběžně příjem z jedné fronty a odesílání do jiné fronty přes stejnou síťové připojení.

![][1]

Připojení k síti je tedy ukotven v kontejneru. Inicializuje kontejneru v roli klienta provádění odchozí připojení soketu TCP do kontejneru v roli příjemce, který naslouchá a přijímá příchozí připojení TCP. Metoda handshake připojení zahrnuje vyjednávání verzi protokolu, deklarace nebo vyjednávání použití zabezpečení na úrovni přenosu (TLS/SSL) a ověřování/autorizace handshake v připojení oboru, který je založen na rozhraní SASL.

Azure Service Bus vyžaduje použití protokolu TLS za všech okolností. Podporuje připojení přes TCP port 5671, kterým připojení TCP je nejprve překrývající s TLS před zadáním handshake protokolu AMQP a také podporuje připojení přes TCP port 5672 zajišťovaný serveru okamžitě nabízí povinné upgrade připojení protokol TLS pomocí modelu předepsané AMQP. Protokoly Websocket AMQP vazbou tunelové připojení přes port TCP 443, který je ekvivalentní k připojení AMQP 5671.

Po nastavení připojení a TLS, Service Bus nabízí dvě možnosti mechanismus SASL:

* PROSTÝ SASL se běžně používá k předávání uživatelské jméno a heslo přihlašovacích údajů k serveru. Service Bus nemá žádné účty, ale pojmenované [pravidla zabezpečení přístupu ke sdílené](service-bus-sas.md), které práva a jsou spojeny s klíčem. Název pravidla se použije jako uživatelské jméno a klíč (jak je text s kódováním base64) se používá jako heslo. Práva spojená s zvolené pravidlo řízení operací povolené pro připojení.
* ANONYMNÍ SASL se používá pro obcházení SASL autorizace, když klient nechce používat model (CBS) deklarace identity na základě zabezpečení, který je popsaný níže. S touto možností je připojení klienta anonymně navázat krátkou dobu, během které klient může komunikovat přes koncový bod CBS a musíte dokončit CBS handshake.

Po navázání připojení pro přenos kontejnerů každý deklarovat maximální velikost rámce, ve které je chce zpracovat a po časový limit nečinnosti, bude jednostranně odpojit, pokud připojení neexistuje žádná aktivita.

Jsou také deklarovat, kolik souběžných kanály jsou podporovány. Kanál je přenos jednosměrný, odchozí, virtuální cestu na připojení. Relace má kanál ze všech propojených kontejnery a vytvoří obousměrný komunikační trasa.

Relace máte model řízení toku založeného na okno; Když se vytvoří relaci, každá ze smluvních stran deklaruje, kolik snímků je ochotni akceptovat do jeho okna. Snímky exchange strany, výplň přenesené rámce okna a přenosy zastavit při zaplnění okna a dokud okno získá resetování nebo rozšířené použití *toku performative* (*performative* je termín AMQP gesty úroveň protokolu, které se vyměňují mezi dvěma stranami).

Tento model založený na okno je přibližně obdobná koncept TCP řízení toku založeného na okno, ale na úrovni relace uvnitř soketu. Koncepce protokolu umožňuje víc souběžných relací existuje tak, aby provoz s vysokou prioritou může rushed po omezené normální provoz, jako je na highway express lane.

Azure Service Bus aktuálně používá přesně jeden relace pro každé připojení. Velikost maximální-rámce služby Service Bus je 262 144 bajtů (256 kB) pro standardní služby Service Bus a Event Hubs. Je 1 048 576 (1 MB) pro Service Bus úrovně Premium. Nepředstavuje žádné konkrétní omezení windows úrovně relace služby Service Bus, ale obnoví okno pravidelně jako součást řízení toku na úrovni propojení (viz [v další části](#links)).

Připojení, kanály a relací jsou dočasné. Sbalí základní připojení, připojení, musí být navázán tunelu TLS, SASL autorizační kontext a relací.

### <a name="links"></a>Odkazy

AMQP přenosu zpráv přes odkazy. Odkaz je komunikační trasa vytvořená během relace, které umožňuje přenos zpráv v jednom směru; stav vyjednávání přenos je nad odkaz a obousměrnou stran připojené.

![][2]

Propojení lze vytvořit buď kontejnerem, kdykoli a přes existující relaci, která vlastně rozdíl od mnoha jiných protokolů, včetně protokolu HTTP a protokolu MQTT, kde je exkluzivní oprávnění strany vytváření zahájení přenosů a cestu k přenosu protokolu AMQP připojení soketu.

Inicializace odkazu kontejneru zeptá opačné kontejneru tak, aby přijímal odkaz a zvolí roli odesílatel a příjemce. Proto kontejneru můžete zahájit vytváření jednosměrné nebo cesty obousměrnou komunikaci s ten nemodelují jako dvojice odkazů.

Odkazy jsou s názvem a související s uzly. Jak je uvedeno na začátku, uzly jsou komunikující entity uvnitř kontejneru.

Ve službě Service Bus je uzel přímo ekvivalentní frontu, téma, předplatné nebo dílčí nedoručených zpráv z fronty nebo odběru. Název uzlu použitý v AMQP je proto relativní název sady entit v rámci oboru názvů služby Service Bus. Pokud je název fronty `myqueue`, který je také název uzlu jeho AMQP. Odběru tématu následuje konvenci rozhraní HTTP API jsou rozděleny na kolekci prostředků "předplatné" a proto předplatné **sub** nebo jedním tématem **mytopic** má název uzlu AMQP  **mytopic/předplatná/sub**.

Připojujícího se klienta je také potřeba použít název místního uzlu služby pro vytváření odkazů. Service Bus není doporučené o tyto názvy wwnn a neinterpretuje je. Zásobníky klienta protokolu AMQP 1.0 obecně používají schéma, aby zajistil, které tyto názvy dočasných uzlů jsou jedinečné v rámci klienta.

### <a name="transfers"></a>Převody

Po vytvoření propojení zprávy lze přenášet prostřednictvím tohoto odkazu. V protokolu AMQP, přenos provádí pomocí gesta explicitní protokol ( *přenos* performative), který přesune zprávu od odesílatele příjemci odkaz. Převod je dokončena ho po "vyrovnání", což znamená, že obě strany máte vytvořenou sdílenou znalost výsledek tohoto převodu.

![][3]

V nejjednodušším případě můžete zvolit odesílatel odesílat zprávy "předem vyrovnány,", což znamená, že klient není zájem o výsledek a příjemce neposkytuje jakoukoli zpětnou vazbu o výsledek operace. Tento režim je podporované ve službě Service Bus na úrovni protokolu AMQP, ale nejsou viditelné v žádném z klientských rozhraní API.

Pravidelné případem je, že jsou odesílány zprávy nevyrovnaná a příjemce potom znamená přijetí nebo zamítnutí pomocí *dispozice* performative. Odmítnutí dochází, když příjemce nemůže přijmout zprávy z jakéhokoli důvodu a odmítnutí zpráva obsahuje informace o příčině, což je struktury chyba určené AMQP. Pokud zprávy jsou odmítnuta z důvodu vnitřní chyby v rámci služby Service Bus, služba vrátí dodatečné informace uvnitř struktury, který slouží k zajištění diagnostiky pomocné parametry pracovníkům podpory, pokud jsou podání žádosti o podporu. Další podrobnosti o chybách dozvíte později.

Je zvláštní forma zamítnutí *vydané* stavu, což znamená, že příjemce nemá žádné technické námitky přenosem, ale také zájem o vystavovací přenos. Tento případ existuje, například když zpráva se doručí do klienta služby Service Bus a klient zvolí možnost "opustit" zprávu vzhledem k tomu, že nemůže provádět práci vyplývající z zpracování zprávy. doručení zpráv, samotný není 2!s!(0x%3!s!). Varianta tento stav je *upravit* stavu, který umožňuje změny na zprávu, jak se uvolní. Aby se tento stav se nepoužívá ve službě Service Bus v současné době.

Specifikace protokolu AMQP 1.0 definuje další dispozice stavem s názvem *přijatých*, to konkrétně umožňuje zpracovat odkaz pro obnovení. Odkaz pro zotavení umožňuje rekonstrukce stav odkazu a všechny čekající doručení nad nové připojení a relace, pokud byly ztraceny předchozí připojení a relace.

Service Bus nepodporuje obnovení odkazu; Pokud klient ztratí připojení ke službě Service Bus nevyrovnaná zprávou přenos čekající přenos zprávy je ztracené a klienta musíte znovu připojit, obnovení propojení a opakovat pokus o přenos.

V důsledku toho služby Service Bus a Event Hubs podporují "alespoň jednou" přenos kde odesílatel zprávy byla uložena a přijmout si být jistí, ale proveďte není podpora "právě jednou" přenosy na úrovni protokolu AMQP, kde by systém pokusí o zotavení na odkaz a i nadále vyjednávání stav doručování, aby se zabránilo duplicitě přenos zpráv.

Jako kompenzaci za možná duplicitní odešle, Service Bus podporuje vyhledávání duplicit jako volitelná funkce na fronty a témata. Zjišťování duplicit zaznamenává identifikátory zpráv všech příchozích zpráv uživatelem definované časovém období a pak tiše zahodí všechny zprávy odeslané s stejné ID zprávy během tohoto časového intervalu pro stejné.

### <a name="flow-control"></a>Řízení toku

Kromě úrovně relace tok ovládací prvek modelu, který jsme uvedli každý odkaz má svůj vlastní model řízení toku. Řízení toku na úrovni relace chrání nebudou muset po řízení toku na úrovni propojení vloží aplikace starosti počet zpráv, které chce zpracovat pomocí odkazu a při zpracování moc velký počet snímků na kontejneru.

![][4]

Na propojení, přenosy může dojít pouze pokud odesílatel má dostatek *propojit kredit*. Link platební je příjemce pomocí sady čítače *toku* performative, které působí na odkaz. Odesílatel link platební přiřazení pokouší využít této platební službou doručování zpráv. Každý sníží doručování zpráv odkaz na zbývající kredit ve výši 1. Při použití link platební doručení zastavit.

Když Service Bus je v roli příjemce, okamžitě poskytuje odesílatel s rozsáhlou link platební tak, aby zprávy odesílat hned. Při použití link platební, Service Bus v určitých intervalech odesílá *toku* performative odesílateli aktualizovat odkaz zůstatek kreditu.

Service Bus v roli odesílatel odešle zprávy využít žádný kredit zbývající odkaz.

"Přijímat" volání na úrovni rozhraní API ve výrazném *toku* performative odesílané do služby Service Bus klient a Service Bus používá tento kredit provedením první dostupná, odemknuté zprávu z fronty, uzamčení, a přenesou. Pokud není žádná zpráva snadno k dispozici pro doručení, všechny zbývající kredit podle jakékoli propojení vytvořeno, že konkrétní entity zůstane v pořadí doručení a zprávy jsou zamknuté a přenést, jakmile budou k dispozici pro použití jakékoli zbývající kredit.

Zámek zprávy je uvolněn při přenosu je vyrovnány do jednoho z terminálu stavů *přijato*, *odmítl*, nebo *vydané*. Zpráva odebere ze služby Service Bus je konečný stav *přijato*. Zůstane ve službě Service Bus a se doručí do další příjemce, když přenos dosáhne některý z jiných stavů. Služby Service Bus automaticky přesune zprávu do fronty nedoručených zpráv entity, když dosáhne doručování maximální počet povolený pro entity z důvodu zamítnutí opakovaných nebo vydané verze.

I v případě, že rozhraní API služby Service Bus nezveřejňujte přímo takovou možnost ještě dnes, klienta protokolu AMQP nižší úrovně můžete použít model link platební Chcete-li "o přijetí změn – vizuální styl" interakce vystavujících kreditu pro každý požadavek na přijetí jednu jednotku do modelu "push stylu" ve vydávání velký počet propojení kredity a potom přijímat zprávy, jakmile budou k dispozici bez dalšího zásahu. Má podporu nabízených oznámení [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PrefetchCount) nebo [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount) nastavení vlastností. Když jsou nenulové, AMQP klienta se používá jako link platební.

V tomto kontextu je důležité pochopit, že začne účtovat poplatky za vypršení platnosti uzamčení zprávy v entitě při zprávy z entity, zprávy není zadaný na lince. Pokaždé, když se klient naznačuje připravenosti pro příjem zpráv pomocí link platební, očekává se proto chcete být aktivně načítat zprávy přes síť a připravená na jejich zpracování. Jinak zpráva může mít vypršela platnost zámku předtím, než zpráva se doručí i. Řízení toku link platební by měly odrážet přímo okamžité připravenosti řešit dostupné zprávy odeslána příjemci.

Stručně řečeno v následujících částech schéma přehled performative toku během různé interakce rozhraní API. Každá část popisuje různé logické operace. Některé z těchto interakcí, může být "opožděné," to znamená, že se může provést pouze v případě potřeby. Vytvoření odesílatele zprávy nesmí způsobit, že sítě interakce až po první zprávy odeslané nebo požadované.

Šipky v následující tabulce ukazují performative směr.

#### <a name="create-message-receiver"></a>Vytvoření příjemce zprávy

| Klient | Service Bus |
| --- | --- |
| --> připojit ()<br/>název = {název odkazu},<br/>zpracování = {číselné popisovač},<br/>role =**příjemce**,<br/>Zdroj = {název entity}<br/>cíl = {ID klienta odkaz}<br/>) |Klient připojí k entity jako příjemce |
| Service Bus odpovědi připojení jeho konci odkazu |< – připojit ()<br/>název = {název odkazu},<br/>zpracování = {číselné popisovač},<br/>role =**odesílatele**,<br/>Zdroj = {název entity}<br/>cíl = {ID klienta odkaz}<br/>) |

#### <a name="create-message-sender"></a>Vytvoření odesílatele zprávy

| Klient | Service Bus |
| --- | --- |
| --> připojit ()<br/>název = {název odkazu},<br/>zpracování = {číselné popisovač},<br/>role =**odesílatele**,<br/>Zdroj = {ID klienta odkaz},<br/>cíl = {název entity}<br/>) |Žádná akce |
| Žádná akce |< – připojit ()<br/>název = {název odkazu},<br/>zpracování = {číselné popisovač},<br/>role =**příjemce**,<br/>Zdroj = {ID klienta odkaz},<br/>cíl = {název entity}<br/>) |

#### <a name="create-message-sender-error"></a>Vytvoření odesílatele zprávy (chyba)

| Klient | Service Bus |
| --- | --- |
| --> připojit ()<br/>název = {název odkazu},<br/>zpracování = {číselné popisovač},<br/>role =**odesílatele**,<br/>Zdroj = {ID klienta odkaz},<br/>cíl = {název entity}<br/>) |Žádná akce |
| Žádná akce |< – připojit ()<br/>název = {název odkazu},<br/>zpracování = {číselné popisovač},<br/>role =**příjemce**,<br/>Zdroj = null,<br/>cíl = null<br/>)<br/><br/>< – odpojení ()<br/>zpracování = {číselné popisovač},<br/>zavření =**true**,<br/>Chyba = {informace o chybě}<br/>) |

#### <a name="close-message-receiversender"></a>Zavřít zprávu příjemce nebo odesílatele

| Klient | Service Bus |
| --- | --- |
| --> odpojení ()<br/>zpracování = {číselné popisovač},<br/>zavření =**true**<br/>) |Žádná akce |
| Žádná akce |< – odpojení ()<br/>zpracování = {číselné popisovač},<br/>zavření =**true**<br/>) |

#### <a name="send-success"></a>Odeslat (úspěch)

| Klient | Service Bus |
| --- | --- |
| přenos (--><br/>doručování id = {číselné popisovač},<br/>doručování tag = {binární popisovač},<br/>Vyrovnané =**false**,, více =**false**,<br/>Stav =**null**,<br/>Obnovit =**false**<br/>) |Žádná akce |
| Žádná akce |<-disposition ()<br/>role = příjemce,<br/>první = {doručování ID}<br/>poslední = {doručování ID}<br/>Vyrovnané =**true**,<br/>Stav =**přijato**<br/>) |

#### <a name="send-error"></a>Odeslat (chyba)

| Klient | Service Bus |
| --- | --- |
| přenos (--><br/>doručování id = {číselné popisovač},<br/>doručování tag = {binární popisovač},<br/>Vyrovnané =**false**,, více =**false**,<br/>Stav =**null**,<br/>Obnovit =**false**<br/>) |Žádná akce |
| Žádná akce |<-disposition ()<br/>role = příjemce,<br/>první = {doručování ID}<br/>poslední = {doručování ID}<br/>Vyrovnané =**true**,<br/>Stav =**odmítl**()<br/>Chyba = {informace o chybě}<br/>)<br/>) |

#### <a name="receive"></a>Přijmout

| Klient | Service Bus |
| --- | --- |
| --> () toku<br/>Link platební = 1<br/>) |Žádná akce |
| Žádná akce |< přenosu ()<br/>doručování id = {číselné popisovač},<br/>doručování tag = {binární popisovač},<br/>Vyrovnané =**false**,<br/>více =**false**,<br/>Stav =**null**,<br/>Obnovit =**false**<br/>) |
| --> () dispozice<br/>role =**příjemce**,<br/>první = {doručování ID}<br/>poslední = {doručování ID}<br/>Vyrovnané =**true**,<br/>Stav =**přijato**<br/>) |Žádná akce |

#### <a name="multi-message-receive"></a>Přijetí zprávy více

| Klient | Service Bus |
| --- | --- |
| --> () toku<br/>Link platební = 3<br/>) |Žádná akce |
| Žádná akce |< přenosu ()<br/>doručování id = {číselné popisovač},<br/>doručování tag = {binární popisovač},<br/>Vyrovnané =**false**,<br/>více =**false**,<br/>Stav =**null**,<br/>Obnovit =**false**<br/>) |
| Žádná akce |< přenosu ()<br/>doručování id = {číselné popisovač + 1},<br/>doručování tag = {binární popisovač},<br/>Vyrovnané =**false**,<br/>více =**false**,<br/>Stav =**null**,<br/>Obnovit =**false**<br/>) |
| Žádná akce |< přenosu ()<br/>doručování id = {číselné popisovač + 2},<br/>doručování tag = {binární popisovač},<br/>Vyrovnané =**false**,<br/>více =**false**,<br/>Stav =**null**,<br/>Obnovit =**false**<br/>) |
| --> () dispozice<br/>role = příjemce,<br/>první = {doručování ID}<br/>poslední = {doručování ID + 2},<br/>Vyrovnané =**true**,<br/>Stav =**přijato**<br/>) |Žádná akce |

### <a name="messages"></a>Zprávy

Následující části popisují vlastnosti ze standardní oddíly AMQP zprávy, které se používají ve službě Service Bus a jak jsou mapovány na sadu rozhraní API služby Service Bus.

Jakákoli vlastnost, která aplikace potřebuje definuje musí být mapováno na jeho AMQP `application-properties` mapy.

#### <a name="header"></a>záhlaví

| Název pole | Využití | Název rozhraní API |
| --- | --- | --- |
| trvalý |- |- |
| priorita |- |- |
| ttl |Hodnota Time to live pro tuto zprávu |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive) |
| první nabyvatel |- |- |
| Počet doručení |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeliveryCount) |

#### <a name="properties"></a>properties

| Název pole | Využití | Název rozhraní API |
| --- | --- | --- |
| id zprávy |Definované aplikací, volného tvaru identifikátor pro tuto zprávu. Používá se pro zjišťování duplicit. |[ID zprávy](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) |
| id uživatele |Identifikátor uživatele definované aplikací, není Interpretovaná ve službě Service Bus. |Není přístupný prostřednictvím rozhraní API služby Service Bus. |
| na |Identifikátor cíle definované aplikací, není Interpretovaná ve službě Service Bus. |[Komu](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_To) |
| Předmět |Identifikátor účelu zpráv definované aplikací není Interpretovaná ve službě Service Bus. |[Popisek](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) |
| odpovědi |Indikátor definovaného aplikací odpověď path není Interpretovaná ve službě Service Bus. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyTo) |
| id korelace |Identifikátor korelace definované aplikací, není Interpretovaná ve službě Service Bus. |[ID korelace](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_CorrelationId) |
| Typ obsahu |Definované aplikací ukazatel typu obsahu pro obsah, není Interpretovaná ve službě Service Bus. |[contentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType) |
| kódování obsahu |Definované aplikací kódování obsahu indikátor pro text není Interpretovaná ve službě Service Bus. |Není přístupný prostřednictvím rozhraní API služby Service Bus. |
| absolutní čas vypršení platnosti |Deklaruje, na které absolutní okamžité zprávy vyprší platnost. Pro zadání (hlavička se vyskytuje hodnota TTL), ignoruje autoritativní na výstupu. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ExpiresAtUtc) |
| čas vytvoření |Deklaruje kdy byla zpráva vytvořena. Nepoužívá se ve službě Service Bus |Není přístupný prostřednictvím rozhraní API služby Service Bus. |
| id skupiny |Identifikátor definovaného aplikací pro související sadu zpráv. Používá se pro relace služby Service Bus. |[ID relace](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) |
| skupiny pořadí |Čítač určení relativní pořadové číslo zprávy v relaci. Ignoruje se ve službě Service Bus. |Není přístupný prostřednictvím rozhraní API služby Service Bus. |
| odpověď na skupiny id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyToSessionId) |

#### <a name="message-annotations"></a>Zpráva poznámky

Existuje několik dalších služby Service bus vlastnosti zprávy, které nejsou součástí vlastností zpráv protokolu AMQP a které se předají jako `MessageAnnotations` ve zprávě.

| Klíč Map poznámky | Využití | Název rozhraní API |
| --- | --- | --- |
| x optimalizované naplánované-zařadit do fronty time | Deklaruje tentokrát zprávy by se zobrazit na entitu |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt-partition-key | Definované aplikací klíč, který určuje oddíl, který zprávu by měl přejít v. | [partitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt – prostřednictvím--klíče oddílu | Aplikace klíče oddílu hodnota definovaná při transakce se má použít pro odesílání zpráv prostřednictvím fronty přenosu. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt – čas zařazení do fronty | Služba definovaná čas UTC představující skutečný čas zařadit zprávy. Ignoruje se na vstup. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt--číslo sekvence | Jedinečné číslo definovaných službou přiřazené ke zprávě. | [sequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x optimalizované posun | Zařazených do fronty definovaných službou pořadové číslo zprávy. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-opt uzamčen – až do | Definice služby. Datum a čas, do které se uzamkne zprávy ve frontě nebo odběru. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-nedoručených zpráv source | Definice služby. Pokud doručení zprávy z fronty nedoručených zpráv, zdroj původní zprávy. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Transakce funkce

Transakce seskupit dvou nebo více operací společně do oboru provádění. Podle povahy musíte tyto transakce zajistit, že všechny operace, které patří k dané skupině operací úspěšné, nebo selže společně.
Operace jsou seskupené podle identifikátoru `txn-id`.

Pro interakci s transakcí, klient funguje jako `transaction controller` , který určuje operace, které by se měly seskupit dohromady. Služba Service Bus funguje jako `transactional resource` a jak to požadoval provede práci `transaction controller`.

Klient a služba komunikovat `control link` , která je stanovena klientem. `declare` a `discharge` zprávy odesílá kontroleru přes ovládací prvek propojení přidělit a dokončení transakce v uvedeném pořadí (nepředstavují rozhraničení transakční práce). Na tento odkaz se neprovádí skutečné odeslat/přijmout. Každý transakční požadovaná operace je explicitně označena požadovaný `txn-id` a může proto docházet na libovolný odkaz na připojení. Pokud odkaz na ovládací prvek je zavřít existují-ukončen transakcí, které vytvořila, potom všechny tyto transakce se okamžitě vrátí zpět a pokusy o provedení další transakční práce s nimi povede k selhání. Zprávy na ovládací prvek odkazu nesmí být pre řeší.

Všechna připojení musí inicializovat vlastní ovládací prvek propojení umožnit zahájení a ukončení transakce. Definuje speciální cíl, který funguje jako služba `coordinator`. Klient/řadič vytvoří ovládací prvek propojení na tento cíl. Ovládací prvek odkazu je mimo hranice entity, tedy stejný ovládací prvek odkazu je možné k zahájení a jeho propuštění transakcí více entit.

#### <a name="starting-a-transaction"></a>Spouštění transakce

Chcete-li začít transakční práce. kontroler musí získat `txn-id` ze coordinator. Dělá to tak, že odesílání `declare` typ zprávy. Pokud deklarace je úspěšné, koordinátor odpoví dispozice výsledek, který představuje přiřazená `txn-id`.

| Klient (kontroler) | | Service Bus (koordinátor) |
| --- | --- | --- |
| připojení)<br/>název = {název odkazu},<br/>... ,<br/>role =**odesílatele**,<br/>Cíl =**Coordinator**<br/>) | ------> |  |
|  | <------ | připojení)<br/>název = {název odkazu},<br/>... ,<br/>target=Coordinator()<br/>) |
| přenos)<br/>doručování id = 0,...)<br/>{AmqpValue (**Declare()**)}| ------> |  |
|  | <------ | dispozice) <br/> první = 0, 0, poslední = <br/>Stav =**Declared**()<br/>**txn id**= {ID transakce}<br/>))|

#### <a name="discharging-a-transaction"></a>Výkonu transakce

Kontroler končí transakční práce odesláním `discharge` zpráva koordinátorovi. Kontroler označuje, zda si přeje potvrzení nebo vrácení zpět transakcí pracovní nastavením `fail` příznak na vyřízení textu. Pokud koordinátor se nepodařilo dokončit vyřízení, zpráva se odmítne kvůli provádění tento výsledek `transaction-error`.

> Poznámka: selhání = true odkazuje na vrácení zpět transakcí a selhání = false odkazuje na potvrzení.

| Klient (kontroler) | | Service Bus (koordinátor) |
| --- | --- | --- |
| přenos)<br/>doručování id = 0,...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | dispozice) <br/> první = 0, 0, poslední = <br/>Stav = deklarovaný ()<br/>txn-id = {ID transakce}<br/>))|
| | . . . <br/>Transakční práce<br/>na další odkazy<br/> . . . |
| přenos)<br/>doručování id = 57,...)<br/>{AmqpValue)<br/>**Jeho propuštění (txn-id = 0,<br/>selhání = false)**)}| ------> |  |
| | <------ | dispozice) <br/> první = 57, poslední = 57, <br/>Stav =**funkce Accepted()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Odeslání zprávy v transakci

Všech transakční práce lze provést pomocí stavu transakční doručování `transactional-state` , který představuje id transakcí. V případě odesílání zpráv, transakční stavu Probíhá přenos rámce zprávy. 

| Klient (kontroler) | | Service Bus (koordinátor) |
| --- | --- | --- |
| přenos)<br/>doručování id = 0,...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | dispozice) <br/> první = 0, 0, poslední = <br/>Stav = deklarovaný ()<br/>txn-id = {ID transakce}<br/>))|
| přenos)<br/>zpracování = 1,<br/>doručování id = 1, <br/>**Stav =<br/>TransactionalState (<br/>txn-id = 0)**)<br/>{datová část}| ------> |  |
| | <------ | dispozice) <br/> první = 1, poslední = 1, <br/>Stav =**TransactionalState (<br/>txn-id = 0,<br/>outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Připravuje se zprávy v transakci

Zpráva dispozice zahrnuje operace, jako je `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer`. Chcete-li provádět tyto operace v rámci transakce, předejte `transactional-state` s dispozice.

| Klient (kontroler) | | Service Bus (koordinátor) |
| --- | --- | --- |
| přenos)<br/>doručování id = 0,...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | dispozice) <br/> první = 0, 0, poslední = <br/>Stav = deklarovaný ()<br/>txn-id = {ID transakce}<br/>))|
| | <------ |přenos)<br/>zpracování = 2,<br/>doručování id = 11 <br/>Stav = null)<br/>{datová část}|  
| dispozice) <br/> první = 11, 11, poslední = <br/>Stav =**TransactionalState (<br/>txn-id = 0,<br/>outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Pokročilé funkce služby Service Bus

Tato část zahrnuje pokročilé funkce služby Azure Service Bus, které jsou založeny na konceptu rozšíření AMQP, současné době ve vývoji v technického výboru OASIS pro AMQP. Service Bus implementuje nejnovější verze těchto konceptů a přijímá změny zavedené, protože tyto koncepty oslovit standardní stav.

> [!NOTE]
> Zasílání zpráv Service Bus pokročilé operace se podporují prostřednictvím vzoru žádostí a odpovědí. Podrobnosti o těchto operací jsou popsány v následujícím článku [protokolu AMQP 1.0 ve službě Service Bus: založené na žádost odpověď operace](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>Správa AMQP

Specifikace AMQP správy je první návrh rozšíření popisovaných v tomto článku. Tato specifikace definuje sadu protokolů jako vrstva nad protokolu AMQP, které umožňují interakce s infrastruktura zasílání zpráv přes protokol AMQP. Specifikace definuje obecný operací, jako *vytvořit*, *čtení*, *aktualizovat*, a *odstranit* pro správu entit uvnitř Infrastruktura zasílání zpráv a sadu operací dotazů.

Všechny tyto gesta vyžadují žádostí a odpovědí interakce mezi klientem a infrastruktura zasílání zpráv, a proto specifikace definuje, jak model této interakční nad AMQP: klient se připojí k infrastruktura zasílání zpráv inicializuje relaci a potom vytvoří pár odkazů. Na jedno propojení klient funguje jako odesílatele a na druhé funguje jako příjemce, proto vytvoří pár odkazů, které může fungovat jako obousměrný kanál.

| Logické operace | Klient | Service Bus |
| --- | --- | --- |
| Vytvořit cestu požadavku odpovědi |--> připojit ()<br/>název = {*název odkazu*},<br/>zpracování = {*číselné popisovač*},<br/>role =**odesílatele**,<br/>Zdroj =**null**,<br/>target = "Správa myentity / $"<br/>) |Žádná akce |
| Vytvořit cestu požadavku odpovědi |Žádná akce |\<--připojení ()<br/>název = {*název odkazu*},<br/>zpracování = {*číselné popisovač*},<br/>role =**příjemce**,<br/>Zdroj = null,<br/>target = "myentity"<br/>) |
| Vytvořit cestu požadavku odpovědi |--> připojit ()<br/>název = {*název odkazu*},<br/>zpracování = {*číselné popisovač*},<br/>role =**příjemce**,<br/>zdroj = "Správa myentity / $",<br/>target = "myclient$ id"<br/>) | |
| Vytvořit cestu požadavku odpovědi |Žádná akce |\<--připojení ()<br/>název = {*název odkazu*},<br/>zpracování = {*číselné popisovač*},<br/>role =**odesílatele**,<br/>zdroj = "myentity"<br/>target = "myclient$ id"<br/>) |

Máte tento pár odkazů na místě, implementaci požadavku nebo odpovědi je jednoduché: žádost je zpráva odeslaná do entity uvnitř infrastruktura zasílání zpráv, které rozumí tohoto modelu. Této zprávy požadavku *odpovědi* pole *vlastnosti* části je nastavena na *cílové* identifikátor pro odkaz, na kterém doručit odpověď. Entity zpracování zpracuje požadavek a pak poskytuje odpověď přes propojení jehož *cílové* odpovídá na zadaný identifikátor *odpovědi* identifikátor.

Vzor samozřejmě vyžaduje, aby kontejneru klienta a identifikátor klientem generovaná pro cíl odpovědi byly jedinečné všemi klienty a z bezpečnostních důvodů se také těžko odhadnutelné.

Výměny zpráv používat pro protokol pro správu a pro všechny protokoly, které používají stejný vzor následkem toho došlo na úrovni aplikace; nedefinujte nové gesta na úrovni protokolu AMQP. To je úmyslné, tak, aby aplikace můžete okamžitě využít těchto rozšíření se kompatibilní zásobníky protokolu AMQP 1.0.

Service Bus neimplementuje aktuálně některé základní funkce správy specifikace, ale žádost/odpověď vzor definované ve specifikaci správu je základní pro deklarace identity na základě zabezpečení funkce a pro téměř všechny rozšířené funkce popsané v následujících částech:

### <a name="claims-based-authorization"></a>Autorizace na základě rolí

Koncept specifikace deklarace identity na základě autorizace AMQP (CBS) je založena na vzoru žádostí a odpovědí specifikace správy a popisuje zobecněný model týkajících se použití tokenů zabezpečení pomocí protokolu AMQP.

Výchozí model zabezpečení popsané v úvodu AMQP je založená na rozhraní SASL a integruje se s metoda handshake připojení AMQP. Použití rozhraní SASL má výhodu, že poskytuje model rozšiřitelné, pro kterou byly definovány sadu mechanismy z libovolného protokolu, který formálně leans na SASL můžete využívat. Mezi tyto mechanismy jsou "Prostý" přenos z uživatelských jmen a hesel, "Externí" k vytvoření vazby zabezpečení na úrovni protokolu TLS, "ANONYMNÍ" express chybí explicitní ověřování/autorizace a širokou řadu další mechanismy, které povolí předávání pověření ověřování nebo autorizace nebo tokeny.

Integrace rozhraní SASL AMQP má dvě nevýhody:

* Všechny přihlašovací údaje a tokeny mají rozsah připojení. Infrastruktura zasílání zpráv může být vhodné k poskytování řízení DIFERENCOVANÝ přístup na základě jednotlivých entitách; například povolení nosný token k odeslání do fronty A, ale ne do fronty služby serveru B. S autorizační kontext ukotven na připojení není možné používat jedno připojení a ještě používat jiné přístupové tokeny pro fronty A a fronty služby serveru B.
* Přístupové tokeny jsou obvykle platné jenom po omezenou dobu. Tato platnost vyžaduje, aby uživatel se pravidelně znovu získat tokeny a představuje příležitost k vydavatel tokenu odmítnout vydání aktuální token, pokud došlo ke změně oprávnění pro přístup daného uživatele. Připojení AMQP může trvat dlouhou dobu. SASL model pouze poskytuje možnost nastavit token v době připojení, což znamená, že infrastruktura zasílání zpráv, které jsou buď má k odpojení klienta, když vyprší platnost tokenu nebo ho musí přijmout riziko povolení trvalé komunikace s klientem kdo. přístupová práva může být mezitím odvolán.

Specifikace protokolu AMQP CBS, implementované ve službě Service Bus, umožňují elegantní alternativní řešení pro oba tyto problémy: umožňuje klientovi přidružit každý uzel přístupové tokeny a k aktualizaci těchto tokenů před vypršením jejich platnosti, aniž by bylo třeba přerušit tok zpráv.

Správa virtuálního uzlu, s názvem definuje CBS *$cbs*, které poskytují infrastruktura zasílání zpráv. Uzel správy přijímá tokeny jménem jiných uzlů v infrastruktura zasílání zpráv.

Gesto protokol je požadavek/odpověď exchange, jak je definováno ve specifikaci správy. Znamená, že klient vytvoří pár odkazů se *$cbs* uzel a poté předá požadavek na odchozí odkaz a čeká odpověď na příchozí propojení.

Zpráva požadavku má následující vlastnosti aplikace:

| Klíč | Nepovinné | Typ hodnoty | Hodnota obsahu |
| --- | --- | --- | --- |
| operace |Ne |řetězec |**PUT-token** |
| type |Ne |řetězec |Typ tokenu uložením. |
| jméno |Ne |řetězec |"Cílová skupina" na kterou se vztahuje token. |
| konec platnosti |Ano |časové razítko |Čas vypršení platnosti tokenu. |

*Název* vlastnost identifikují entitu, se kterým token musí být přidružené. Ve službě Service Bus je cesta k frontě nebo tématu nebo odběru. *Typ* vlastnost identifikuje typ tokenu:

| Typ tokenu | Popis tokenu | Typ těla zprávy | Poznámky |
| --- | --- | --- | --- |
| amqp:jwt |JSON Web Token (JWT) |AMQP hodnoty (string) |Zatím není k dispozici. |
| amqp:swt |Jednoduchý webový Token (SWT) |AMQP hodnoty (string) |Podporuje jenom pro SWT tokeny vystavené službou AAD/ACS |
| servicebus.Windows.NET:sastoken |Token SAS služby Service Bus |AMQP hodnoty (string) |- |

Tokeny udělit práva. Service Bus ví o tři základní práva: "Odeslat" umožňuje odesílání "Naslouchání" příjem, a "Manage" umožňuje manipulaci s entitami. Tato práva SWT tokeny vystavené službou AAD/ACS explicitně zahrnuly jako deklarace identity. Tokeny SAS služby Service Bus odkazovat na pravidla, které jsou nakonfigurované na oboru názvů nebo entity a tato pravidla jsou nakonfigurovány s oprávněními. Podpis tokenu s klíč spojený s tímto pravidlem tedy díky token express příslušných práv. Token přidružený k entitu s využitím *put token* povoluje připojení klienta k interakci s entitou za token práva. Certifikovaného klienta na odkaz *odesílatele* role vyžaduje "Odeslat"; přímo na *příjemce* role vyžaduje "Naslouchání" vpravo.

Zprávy s odpovědí obsahuje následující *vlastnosti aplikace* hodnoty

| Klíč | Nepovinné | Typ hodnoty | Hodnota obsahu |
| --- | --- | --- | --- |
| Stavový kód |Ne |int |Kód odpovědi HTTP **[RFC2616]**. |
| Popis stavu |Ano |řetězec |Popis stavu. |

Klient může volat *put token* opakovaně a pro každou entitu v infrastruktura zasílání zpráv. Tokeny jsou omezená na aktuální klient a ukotven na aktuální připojení, což znamená, že server zahodí všechny tokeny zachované při připojení.

Service Bus umožňuje aktuální implementace pouze CBS ve spojení s metodou rozhraní SASL "ANONYMNÍ". Připojení SSL/TLS, vždy musí existovat před metodou handshake rozhraní SASL.

ANONYMNÍ mechanismus musí podporovat proto zvoleného klienta protokolu AMQP 1.0. Anonymní přístup znamená, že handshake počátečního připojení, včetně vytváření počáteční relace dojde bez služby Service Bus vědět, kdo vytváří připojení.

Jakmile se naváže připojení a relace, odkazy na připojení *$cbs* uzlu a odesílání *put token* žádosti jsou pouze povolené operace. Platný token musí být nastavena nepodaří *put token* požadavku pro některé entity uzlu v rámci 20 sekund po navázání připojení, jinak připojení je jednostranně vyřazeno ve službě Service Bus.

Klient následně zodpovídá za udržování přehledu o vypršení platnosti tokenu. Když vyprší platnost tokenu, Service Bus o tom bezodkladně informuje zahodí všechny odkazy na připojení k příslušné entitě. Pokud chcete zabránit potížím, ke kterým dochází, klienta můžete nahradit token pro uzel nový kdykoli přes virtuální *$cbs* uzlu pro správu se stejnou *put token* gesta a bez získání v způsob, jak datové části provoz tohoto toků na různé odkazy.

### <a name="send-via-functionality"></a>Odesílání prostřednictvím funkce

[Odesílání prostřednictvím / odesílatele přenosu](service-bus-transactions.md#transfers-and-send-via) je funkce, která umožňuje service bus vpřed danou zprávu Cílová entita prostřednictvím jiné entity. Tato funkce slouží k provádění operací napříč entitami v rámci jedné transakce.

Pomocí této funkce vytvoříte odesílatele a vytvořit odkaz `via-entity`. Při vytváření propojení, je předáno navázat true cíl zprávy/přenosů na tento odkaz Další informace. Po připojení proběhlo úspěšně, všechny zprávy odeslané na tento odkaz automaticky předány *Cílová entita* prostřednictvím *prostřednictvím entity*. 

> Poznámka: Ověřování se provádí pro obě *prostřednictvím entity* a *Cílová entita* před vytvořením tohoto odkazu.

| Klient | | Service Bus |
| --- | --- | --- |
| připojení)<br/>název = {název odkazu},<br/>role = odesílatele<br/>Zdroj = {ID klienta odkaz},<br/>Cíl =**{prostřednictvím entity}**,<br/>**Vlastnosti mapy = [(<br/>com.microsoft:transfer. cílová adresa =<br/>{Cílová entita})]** ) | ------> | |
| | <------ | připojení)<br/>název = {název odkazu},<br/>role = příjemce,<br/>Zdroj = {ID klienta odkaz},<br/>cíl = {prostřednictvím entity},<br/>Vlastnosti mapy [() =<br/>com.Microsoft:Transfer. cílová adresa =<br/>{Cílová entita})] ) |

## <a name="next-steps"></a>Další postup

Další informace o protokolu AMQP, najdete na následujících odkazech:

* [Přehled protokolu AMQP Service Bus]
* [Podpora AMQP 1.0 pro službu Service Bus dělené fronty a témata]
* [AMQP ve službě Service Bus pro systém Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Přehled protokolu AMQP Service Bus]: service-bus-amqp-overview.md
[Podpora AMQP 1.0 pro službu Service Bus dělené fronty a témata]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP ve službě Service Bus pro systém Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

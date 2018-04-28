---
title: AMQP 1.0 v příručce protokol Azure Service Bus a Event Hubs | Microsoft Docs
description: Protokol Průvodce výrazy a popis protokolu AMQP 1.0 v Azure Service Bus a Event Hubs
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
ms.date: 11/08/2017
ms.author: clemensv;hillaryc;sethm
ms.openlocfilehash: 9af578cef9a89b4ae953b26f261f99593b79deb2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>V Průvodci Azure Service Bus a Event Hubs protokolu AMQP 1.0

1.0 protokol Advanced služby Řízení front zpráv je standardizovaná rámcovacích a přenos protokol asynchronně, bezpečně a spolehlivě přenos zpráv mezi dvěma účastníky. Je protokol primární Azure zasílání zpráv Service Bus a Azure Event Hubs. Obě služby také podporují protokol HTTPS. Vlastnickým protokolem SBMP, která je také podporována je vyřazován považuje AMQP.

AMQP 1.0 je výsledkem spolupráce široký odvětví, který shrnuta dodavatelé middleware, jako je Microsoft a Red Hat, s mnoha uživateli zasílání zpráv middleware, jako je například Chase Nováková JP představující oboru finančních služeb. Fórum technické standardizace pro protokol a rozšíření specifikace protokolu AMQP je OASIS a dosáhla formální schválení jako mezinárodní standard jako ISO/IEC 19494.

## <a name="goals"></a>Cíle

Tento článek stručně shrnuje základní koncepty AMQP 1.0, zasílání zpráv specifikace společně s malou sadu koncept rozšíření specifikace, které se aktuálně dokončují v technický výbor OASIS AMQP a vysvětluje, jak Azure Service Bus implementuje a je založený na těchto specifikací.

Cílem je pro všechny vývojáře pomocí všechny existující zásobníku klienta protokolu AMQP 1.0 na jakékoli platformě, být schopni komunikovat s Azure Service Bus prostřednictvím protokolu AMQP 1.0.

Běžné pro obecné účely zásobníky protokolu AMQP 1.0, jako je například Apache kanálem nebo AMQP.NET Lite již implementovat všechny protokoly jádra protokolu AMQP 1.0. Tyto základní gesta jsou někdy obklopeno vyšší úrovně rozhraní API; Apache kanálem i nabízí dvě rozhraní API imperativní Messenger a reaktivní reaktoru API.

V následující diskusi jsme předpokládají, že správa připojení AMQP, relace a odkazy a manipulaci s rámečkem přenosů a řízení toku jsou zpracovávány v příslušných zásobníku (jako je například Apache kanálem-C) a nevyžadují mnohem, pokud existuje konkrétní pozornost vývojáři aplikace. Předpokládáme abstraktně existenci několik rozhraní API primitiv jako možnost připojit a vytvořit určitou formu *odesílatele* a *příjemce* abstrakce objekty, které mají některé tvar `send()` a `receive()` operace, v uvedeném pořadí.

Když hovoříte o pokročilých funkcí služby Azure Service Bus, jako je například procházení zpráva nebo správu relací, tyto funkce jsou vysvětlené v podmínkách AMQP, ale také jako vrstvený pseudo implementace nad tato abstrakce předpokládané rozhraní API.

## <a name="what-is-amqp"></a>Co je AMQP?

AMQP je protokol rámcovacích a přenosu. Rámcovacích znamená, že poskytuje strukturu pro binární datové proudy, které toku v obou směrech připojení k síti. Struktura poskytuje vymezení pro odlišné bloky dat, volá *rámce*, aby se vyměňují mezi připojené stranami. Možnosti přenosu Ujistěte se, že obě strany komunikuje můžete vytvořit sdílené znalosti o při se přenáší rámce, i když přenosů musí být považováno za dokončené.

Na rozdíl od dříve vypršela platnost koncept verze vytváří pracovní skupinou AMQP používají několik zprostředkovatelé zpráva pracovní skupině konečné a standardizovaném protokolu AMQP 1.0 není stanovit přítomnost zprostředkovatele zpráv nebo žádné konkrétní topologie pro entity uvnitř zprostředkovatele zpráv.

Protokol lze použít pro symetrické komunikaci peer-to-peer, pro interakci s zprostředkovatelé zprávy, které podporují fronty a publikování a přihlášení k odběru entity, jak to dělá Azure Service Bus. Může taky sloužit pro interakci s infrastrukturu zasílání zpráv kde vzory interakce se liší od regulární fronty, jako je tomu u Azure Event Hubs. Centra událostí funguje podobně jako u front odeslání události do ji, ale pracuje jako služba sériové úložiště při události se načítají z. podobá poněkud páskové jednotky. Klient vybere posun do datového proudu k dispozici data a pak je zpracovat všechny události z Tento posun k nejnovější dostupné.

Protokol AMQP 1.0 je navržený jako extensible, povolení další specifikace ke zvýšení jeho možnosti. Znázornění specifikace tři rozšíření popisovaný v tomto dokumentu. Pro komunikaci přes existující infrastrukturu HTTPS/Websocket, kde konfigurace nativní porty TCP protokolu AMQP může být obtížné specifikace vazby definuje, jak vrstvy AMQP přes objekty WebSockets. Pro interakci s infrastrukturu zasílání zpráv způsobem požadavků a odpovědí pro účely správy nebo k poskytování pokročilé funkce, definuje specifikace správy AMQP vyžaduje základní interakce primitiv. Federované ověřování modelu integrace specifikace AMQP deklarace identity na základě zabezpečení definuje, jak přidružit a obnovení tokeny autorizace, které jsou spojené s odkazy.

## <a name="basic-amqp-scenarios"></a>Základní scénáře AMQP

Tato část popisuje základní použití protokolu AMQP 1.0 s Azure Service Bus, která zahrnuje vytváření připojení, relací a odkazy a přenosu zpráv v Service Bus entitami, jako je například fronty, témata a odběry.

Další informace o tom, jak funguje AMQP nejvíce autoritativní zdroj je specifikace protokolu AMQP 1.0, ale specifikace byla zapsána do přesněji Průvodce implementace a ne do naučit protokol. Tato část se zaměřuje na představení tolik terminologie podle potřeby pro popisující, jak Service Bus používá protokolu AMQP 1.0. Pro komplexnější Úvod do AMQP, jakož i širší diskuzi o protokolu AMQP 1.0, můžete zkontrolovat [tento kurz video][this video course].

### <a name="connections-and-sessions"></a>Připojení a relace

AMQP volá komunikuje programy *kontejnery*; tyto obsahovat *uzly*, které jsou komunikuje entity, které v těchto kontejnerech. Fronta může být taková uzlu. AMQP umožňuje multiplexní, takže jednoho připojení lze použít pro mnoho cesty komunikaci mezi uzly. klientem aplikace může například současně přijímat z jedné fronty a odesílání do jiné fronty pomocí stejné síťové připojení.

![][1]

Síťové připojení proto ukotvena kontejneru. Inicializuje kontejneru v roli klienta v roli příjemce, která naslouchá a přijímá příchozí připojení TCP jej odchozí připojení soketu TCP do kontejneru. Metoda handshake připojení zahrnuje vyjednávání verzi protokolu, deklarace nebo vyjednávání použití Transport Level Security (TLS/SSL) a ověřování/autorizace handshake v připojení oboru, který je založen na SASL.

Azure Service Bus vyžaduje použití protokolu TLS za všech okolností. Podporuje připojení přes TCP port 5671, jímž připojení TCP je nejdřív jako překryvný obrázek s TLS před vstupem handshake protokol AMQP a taky podporuje připojení přes TCP port 5672, jímž serveru okamžitě nabízí povinné upgrade připojení TLS pomocí modelu předepsané AMQP. Technologie WebSockets AMQP vazby Vytvoří tunel přes port TCP 443, který je pak ekvivalentní pro připojení protokolu AMQP 5671.

Po nastavení připojení a TLS, Service Bus nabízí dvě možnosti mechanismus SASL:

* PROSTÝ SASL se běžně používá pro předávání uživatelské jméno a heslo pověření na server. Service Bus nemá účty, ale s názvem [pravidla zabezpečení sdíleného přístupu](service-bus-sas.md), který práva a jsou spojeny s klíčem. Název pravidla se používá jako uživatelské jméno a klíč (jak je text kódováním base64, pomocí) se používá jako heslo. Práva přidružený k pravidlu zvolené řídí činnosti povolena v připojení.
* ANONYMNÍ SASL se používá pro obcházení SASL autorizace při klienta chce použít model (CBS) deklarace identity na základě zabezpečení, který je popsán později. Pomocí této možnosti klienta můžete vytvořit připojení anonymně po krátkou dobu, během kterého klienta mohou komunikovat pouze s koncovým bodem CBS a musí dokončit handshake modelu CBS.

Po navázání připojení pro přenos kontejnery každý deklarovat maximální velikost rámců, které jsou ochotni zpracování a po časový limit nečinnosti se budete jednostranně odpojit, pokud není žádná aktivita na připojení.

Také se deklarovat, kolik souběžných kanály jsou podporovány. Kanál je přenos jednosměrný odchozí, virtuální cestu nad připojení. Relace trvá kanál z těchto vzájemně propojena kontejnery na formuláři cestu jednosměrnou komunikaci.

Relace mají model řízení toku na základě okno; Po vytvoření relace jednotlivých stran deklaruje, kolik snímků je chcete akceptovat do jeho okna. Jako strany exchange rámce, výplň přenášená rámce, který okno a přenosy zastavit při zaplnění okna, a dokud okno získá resetování nebo rozšířené použití *toku performative* (*performative* je termín AMQP pro úroveň protokolu gesta vyměňují mezi dvěma účastníky).

Tento model se systémem Windows je přibližně obdobná jako koncept TCP řízení toku na základě okno, ale na úrovni relace uvnitř soketu. Protokolu koncept povolení pro více souběžných relací existuje tak, aby provoz s vysokou prioritou může rushed po omezenému normální provoz, jako je na express dráhy highway.

Azure Service Bus aktuálně používá přesně jednu relaci pro každé připojení. Service Bus maximální rámce – velikost je 262 144 bajtů (256 kB) pro standardní Service Bus a Event Hubs. Je 1 048 576 (1 MB) pro Service Bus Premium. Service Bus nepředstavuje žádné konkrétní windows omezení úrovně relace, ale obnoví okno pravidelně jako součást řízení toku na úrovni propojení (viz [v další části](#links)).

Připojení, kanálů a relací jsou dočasné. Sbalí příslušné připojení, připojení, musí být obnovila tunelu TLS, SASL autorizační kontext a relací.

### <a name="links"></a>Odkazy

AMQP přenosu zpráv přes odkazy. Odkaz je cesta ke komunikaci vytvořených v relaci, která umožňuje přenášení zpráv v jednom směru; stav vyjednávání přenos je nad odkaz a obousměrné mezi připojené stranami.

![][2]

Odkazy můžete vytvořit buď kontejneru existující relaci, což může AMQP liší od mnoha jiné protokoly, včetně HTTP a MQTT, kde je k zahájení přenosu a přenos cestu exkluzivní oprávnění strany vytváření připojení soketu a kdykoliv.

Kontejner inicializaci odkaz vás kontejneru opačné tak, aby přijímal odkaz a zvolí roli odesílatele nebo příjemce. Proto buď kontejneru můžete zahájit vytvořením jednosměrný nebo obousměrnou komunikaci cesty, se k tomu modelován jako dvojice odkazů.

Odkazy jsou s názvem a související s uzly. Jak jsme uvedli v začátku, jsou uzly komunikuje entity uvnitř kontejneru.

V Service Bus je uzel přímo ekvivalentní frontu, téma, předplatného nebo dílčí fronta nedoručených zpráv z fronty nebo předplatné. Název uzlu, který je používán AMQP je proto relativní název sady entit v rámci oboru názvů Service Bus. Pokud je název fronty **Moje_fronta**, který je také jeho název uzlu AMQP. Odběr tématu konvenci, rozhraní API HTTP pomocí probíhá rozděleny na kolekci prostředků "odběry" a proto odběr **sub** nebo téma **mytopic** má název uzlu AMQP **mytopic nebo předplatných nebo dílčí**.

Připojujícího se klienta je taky potřeba použít název místního uzlu pro vytváření odkazů. Service Bus není doporučený o tyto názvy a nebude je interpretovat. Zásobníky klienta protokolu AMQP 1.0 obecně používají schéma, aby zajistil, které jsou v oboru klienta jedinečné názvy těchto dočasných uzlu.

### <a name="transfers"></a>Přenosy

Po vytvoření odkazu zprávy lze přenášet prostřednictvím tohoto připojení. V protokolu AMQP, je přenos provést s gesto explicitní protokolu ( *přenos* performative), přesune zprávu od odesílatele k příjemce přes propojení. Přenos je dokončena ho po "vyrovnání", což znamená, že mají obě strany navázat sdílené pochopení výsledek přenos.

![][3]

V nejjednodušším případě odesílatele můžete odesílat zprávy "předem vyrovnány,", což znamená, že klient není zájem o výsledek a příjemce neposkytuje žádné zpětnou vazbu o výsledek operace. Tento režim je podporována Service Bus na úrovni protokolu AMQP, ale nejsou viditelné v žádném z rozhraní API klienta.

Regulární tak je, že zprávy odesílány nevyrovnaná a příjemce potom znamená přijetí nebo odmítnutí pomocí *dispozice* performative. Odmítání nastane, když příjemce nemůže přijmout zprávy z jakéhokoli důvodu a odmítání zpráva obsahuje informace o příčině, což je strukturu chyba definované AMQP. Pokud zprávy jsou odmítnutých v důsledku vnitřní chyby v rámci služby Service Bus, vrátí službu doplňující informace uvnitř této struktury, které je možné pro zajištění pomocné parametry diagnostiky pracovníkům podpory, pokud jsou vyplnění žádosti o podporu. Další informace o chybách dozvíte později.

Je speciální formu odmítání *vydané* stavu, která označuje, že příjemce byla nic technické proti přenos, ale také žádné zájem o vyrovnání přenos. Tento případ existuje, například když zprávu doručuje do klienta služby Service Bus a klient vybere možnost "abandon" zpráva protože nelze provést na práci vyplývající z zpracování zprávy. doručení zpráv, samotné není poškozeno. Varianta tento stav je *upravil* stavu, který umožňuje změny na zprávu, protože jeho vydání. Tento stav není v současné době používán Service Bus.

Specifikace protokolu AMQP 1.0 definuje další dispozice stavu názvem *přijata*, který konkrétně pomáhá pro zpracování odkaz obnovení. Obnovení odkaz umožňuje rekonstrukce stav odkazu a všechny čekající dodávky nad nové připojení a relace, kdy došlo ke ztrátě předchozí připojení a relace.

Service Bus nepodporuje obnovení odkaz; Pokud klient ztratí připojení k Service Bus nevyrovnaná zpráva přenosu čekající na vyřízení, přenos zpráv dojde ke ztrátě a musíte znovu připojit, obnovení propojení a opakujte přenos klienta.

Jako takový Service Bus a Event Hubs podporovat "alespoň jednou" přenosy kde odesílatele zprávy s uložené a přijata si být jistí, ale nepodporují "přesně jedno" přenosy na úrovni protokolu AMQP, kde se systém pokusí obnovit na odkaz a nadále vyjednávání stav doručení předejdete duplikace přenos zpráv.

Chcete-li kompenzovat odešle možné duplicitní Service Bus podporuje detekce duplicitních jako volitelná funkce v fronty a témata. Detekce duplicitních zaznamenává identifikátory zpráv všech příchozích zpráv během uživatelem definované časového okna a potom bezobslužně poklesne všechny zprávy s stejné identifikátory zpráv této stejného časového období.

### <a name="flow-control"></a>Řízení toku

Kromě model řízení toku na úrovni relace, který dřív popsané každý odkaz má svou vlastní model řízení toku. Řízení toku na úrovni relace chrání muset po řízení toku na úrovni propojení vloží aplikace stará o tom, kolik zpráv, které chce zpracovat pomocí odkazu a při zpracování příliš mnoho rámce v kontejneru.

![][4]

Na propojení, přenos může pouze dojít v případě odesílatel má dost *propojit platební*. Platební odkaz je čítač nastaven příjemce pomocí *toku* performative, který je vymezen na odkaz. Jakmile odesílatele je přiřazena platební odkaz, pokusí se spotřebovávat této platební podle doručování zpráv. Každý snižuje doručení zpráv odkaz zbývající úvěrového o 1. Pokud je vyčerpáte platební odkaz, dodávky zastavit.

Když Service Bus je v roli příjemce, okamžitě poskytuje odesílatel s dostatečným odkaz platební tak, aby okamžitě odesílání zpráv. Jako odkaz platební se používá, Service Bus v určitých intervalech odesílá *toku* performative odesílateli Aktualizovat zůstatek Dal odkaz.

Service Bus v roli odesílatel odešle zprávy a pokuste se spotřebovávají žádný kredit zbývající odkaz.

"Přijímat" volání na úrovni rozhraní API překládá do *toku* performative odesílána Service Bus klient a Service Bus využívá tuto platební tak, že první dostupné, odemknout zprávu z fronty, zamykání přenosu ho. Pokud není žádná snadno dostupné pro doručení, navázat žádný kredit zbývající podle všech propojení se, že konkrétní entity zůstane zaznamenané v pořadí podle doručení a zprávy jsou uzamčené a přenést, jakmile budou k dispozici žádný kredit zbývající používat.

Zámek na zprávu vydání přenos po vyrovnání do jednoho z terminálu stavů *přijata*, *odmítl*, nebo *vydané*. Zpráva je odebrána ze služby Service Bus po stavu terminálu *přijata*. Zůstane v Service Bus a se doručí na další příjemce, když přenos dosáhne některý z dalších stavů. Service Bus zprávu automaticky přesune do fronty nedoručených zpráv entity při dosažení maximální doručení počet povolené pro entity z důvodu opakovaných zamítnutí nebo verze.

I když API pro Service Bus nezveřejňují přímo tato možnost dnes, klienta protokolu AMQP nižší úrovně můžete použít odkaz – platební model zapnout vydání jednu jednotku kredit pro každý požadavek na přijetí do modelu "push stylu" vydáním velký počet odkaz kredity interakci "vyžádání stylu" a pak přijímat zprávy, jakmile budou k dispozici bez další interakce. Nabízená je podporována prostřednictvím [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PrefetchCount) nebo [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount) nastavení vlastností. Když jsou nula, klienta AMQP použit jako platební odkaz.

V tomto kontextu je důležité si uvědomit, že hodiny pro vypršení platnosti zámku na zprávu uvnitř entity spustí, když zprávy jsou převzaty z entity, pokud zpráva není put v drátové síti. Vždy, když klient naznačuje připravenosti pro příjem zpráv zasláním odkazu platební, očekává se proto být aktivně stahování zprávy v síti a připravená k jejich zpracování. V opačném případě zámek zprávy mohla vypršet platnost před i doručení zprávy. Použití řízení toku platební odkaz přímo odrážet okamžitou připravenosti řešení k dispozici zpráv odeslaných k příjemce.

V souhrnu následující části obsahují schéma přehled performative toku během různých interakce rozhraní API. Každá část obsahuje jiné logické operace. Některé z těchto interakcí může být "opožděné," což znamená, že se může provést pouze v případě potřeby. Vytvoření odesílatele zprávy nemusí způsobit interakce sítě, dokud první zprávu se odesílá nebo se požadovaná.

Šipky v následující tabulce se zobrazují směr performative toku.

#### <a name="create-message-receiver"></a>Vytvoření příjemce zprávu

| Klient | Service Bus |
| --- | --- |
| --> připojit ()<br/>název = {název odkazu}<br/>zpracování = {číselné popisovač}<br/>role =**příjemce**,<br/>Zdroj = {název entity}<br/>cíl = {id klienta odkaz}<br/>) |Klient připojí k entity jako příjemce |
| Service Bus odpovědi připojení ukončení propojení |<--připojení ()<br/>název = {název odkazu}<br/>zpracování = {číselné popisovač}<br/>role =**odesílatele**,<br/>Zdroj = {název entity}<br/>cíl = {id klienta odkaz}<br/>) |

#### <a name="create-message-sender"></a>Vytvoření odesílatele zprávy

| Klient | Service Bus |
| --- | --- |
| --> připojit ()<br/>název = {název odkazu}<br/>zpracování = {číselné popisovač}<br/>role =**odesílatele**,<br/>Zdroj = {id klienta odkaz},<br/>cíl = {název entity}<br/>) |Žádná akce |
| Žádná akce |<--připojení ()<br/>název = {název odkazu}<br/>zpracování = {číselné popisovač}<br/>role =**příjemce**,<br/>Zdroj = {id klienta odkaz},<br/>cíl = {název entity}<br/>) |

#### <a name="create-message-sender-error"></a>Vytvoření odesílatele zprávy (chyba)

| Klient | Service Bus |
| --- | --- |
| --> připojit ()<br/>název = {název odkazu}<br/>zpracování = {číselné popisovač}<br/>role =**odesílatele**,<br/>Zdroj = {id klienta odkaz},<br/>cíl = {název entity}<br/>) |Žádná akce |
| Žádná akce |<--připojení ()<br/>název = {název odkazu}<br/>zpracování = {číselné popisovač}<br/>role =**příjemce**,<br/>Zdroj = null,<br/>cíl = null<br/>)<br/><br/><--odpojit ()<br/>zpracování = {číselné popisovač}<br/>Uzavřený =**true**,<br/>chyba = {{chyba info}<br/>) |

#### <a name="close-message-receiversender"></a>Příjemce nebo odesílatele zprávy zavřít

| Klient | Service Bus |
| --- | --- |
| --> odpojit ()<br/>zpracování = {číselné popisovač}<br/>Uzavřený =**true**<br/>) |Žádná akce |
| Žádná akce |<--odpojit ()<br/>zpracování = {číselné popisovač}<br/>Uzavřený =**true**<br/>) |

#### <a name="send-success"></a>Odeslat (úspěch)

| Klient | Service Bus |
| --- | --- |
| přenos (--><br/>doručení id = {číselné popisovač}<br/>doručení tag = {binární popisovač}<br/>Vyrovnané =**false**,, více =**false**,<br/>Stav =**null**,<br/>Obnovit =**false**<br/>) |Žádná akce |
| Žádná akce |<--(dispozice<br/>role = příjemce,<br/>první = {doručení id},<br/>poslední = {doručení id},<br/>Vyrovnané =**true**,<br/>Stav =**přijato**<br/>) |

#### <a name="send-error"></a>Odeslat (chyba)

| Klient | Service Bus |
| --- | --- |
| přenos (--><br/>doručení id = {číselné popisovač}<br/>doručení tag = {binární popisovač}<br/>Vyrovnané =**false**,, více =**false**,<br/>Stav =**null**,<br/>Obnovit =**false**<br/>) |Žádná akce |
| Žádná akce |<--(dispozice<br/>role = příjemce,<br/>první = {doručení id},<br/>poslední = {doručení id},<br/>Vyrovnané =**true**,<br/>Stav =**odmítl**()<br/>chyba = {{chyba info}<br/>)<br/>) |

#### <a name="receive"></a>Přijmout

| Klient | Service Bus |
| --- | --- |
| tok (--><br/>odkaz platební = 1<br/>) |Žádná akce |
| Žádná akce |< přenosu ()<br/>doručení id = {číselné popisovač}<br/>doručení tag = {binární popisovač}<br/>Vyrovnané =**false**,<br/>více =**false**,<br/>Stav =**null**,<br/>Obnovit =**false**<br/>) |
| dispozice (--><br/>role =**příjemce**,<br/>první = {doručení id},<br/>poslední = {doručení id},<br/>Vyrovnané =**true**,<br/>Stav =**přijato**<br/>) |Žádná akce |

#### <a name="multi-message-receive"></a>Více zpráva zobrazí

| Klient | Service Bus |
| --- | --- |
| tok (--><br/>odkaz platební = 3<br/>) |Žádná akce |
| Žádná akce |< přenosu ()<br/>doručení id = {číselné popisovač}<br/>doručení tag = {binární popisovač}<br/>Vyrovnané =**false**,<br/>více =**false**,<br/>Stav =**null**,<br/>Obnovit =**false**<br/>) |
| Žádná akce |< přenosu ()<br/>doručení id = {číselné popisovač + 1},<br/>doručení tag = {binární popisovač}<br/>Vyrovnané =**false**,<br/>více =**false**,<br/>Stav =**null**,<br/>Obnovit =**false**<br/>) |
| Žádná akce |< přenosu ()<br/>doručení id = {číselné popisovač + 2},<br/>doručení tag = {binární popisovač}<br/>Vyrovnané =**false**,<br/>více =**false**,<br/>Stav =**null**,<br/>Obnovit =**false**<br/>) |
| dispozice (--><br/>role = příjemce,<br/>první = {doručení id},<br/>poslední = {id doručení + 2}<br/>Vyrovnané =**true**,<br/>Stav =**přijato**<br/>) |Žádná akce |

### <a name="messages"></a>Zprávy

Následující části popisují, které vlastnosti z standardní zprávy části AMQP se používají Service Bus a jak jsou mapovány na sadu rozhraní API služby Service Bus.

Jakákoli vlastnost, která aplikace potřebuje definuje by měly být namapované do AMQP na `application-properties` mapy.

#### <a name="header"></a>záhlaví

| Název pole | Využití | Název rozhraní API |
| --- | --- | --- |
| trvanlivý |- |- |
| priorita |- |- |
| ttl |Hodnota Time to live pro tuto zprávu |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive) |
| první nabyvatel |- |- |
| Počet doručení |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeliveryCount) |

#### <a name="properties"></a>properties

| Název pole | Využití | Název rozhraní API |
| --- | --- | --- |
| id zprávy |Definované aplikací, vlastní identifikátor této zprávy. Použít pro vyhledávání duplicit. |[messageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) |
| id uživatele |Identifikátor uživatele definované aplikací, není interpretovat Service Bus. |Není k dispozici prostřednictvím rozhraní API Service Bus. |
| na |Identifikátor cílové definované aplikací, není interpretovat Service Bus. |[Komu](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_To) |
| Předmět |Identifikátor účel definované aplikací zprávy není interpretovat Service Bus. |[Popisek](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) |
| odpověď pro |Indikátor definované aplikací odpovědi path není interpretovat Service Bus. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyTo) |
| id korelace |Identifikátor korelace definované aplikací, není interpretovat Service Bus. |[correlationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_CorrelationId) |
| Typ obsahu |Definované aplikací ukazatel typu obsahu pro tělo, není interpretovat Service Bus. |[Typ obsahu](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType) |
| kódování obsahu |Definované aplikací kódování obsahu indikátor pro tělo, není interpretovat Service Bus. |Není k dispozici prostřednictvím rozhraní API Service Bus. |
| absolutní čas vypršení platnosti |Deklaruje, na které absolutní prostřednictvím rychlých zprávy vyprší. Na vstupu (záhlaví TTL pozorovanou), ignorována autoritativní na výstup. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ExpiresAtUtc) |
| čas vytvoření |Deklaruje, po kterých byla zpráva vytvořena. Která nepoužívá Service Bus |Není k dispozici prostřednictvím rozhraní API Service Bus. |
| id skupiny |Identifikátor definované aplikací pro související sadu zpráv. Použít pro Service Bus relace. |[ID relace](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) |
| skupiny pořadí |Čítač identifikace relativní pořadové číslo zprávy v relaci. Ignorovat služby Service Bus. |Není k dispozici prostřednictvím rozhraní API Service Bus. |
| odpověď na skupiny id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyToSessionId) |

#### <a name="message-annotations"></a>Zpráva poznámky

Existuje několik dalších služby sběrnice zpráv vlastností, které nejsou součástí vlastností zpráv AMQP a se předají jako `MessageAnnotations` na zprávu.

| Klíč poznámky mapy | Využití | Název rozhraní API |
| --- | --- | --- |
| x opt naplánované zařazování běhu | Deklaruje, po kterém by zpráva objeví u entity |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt--klíč oddílu | Klíč definované aplikací, který určuje oddíl, který zpráva by měla zobrazovat v. | [Klíč oddílu](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt prostřednictvím--klíč oddílu | Aplikace klíč oddílu hodnota definovaná při transakci je použít k odeslání zprávy prostřednictvím fronty přenosu. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-zařazených do fronty time | Služby definované čas UTC představující skutečný čas enqueuing zprávy. Na vstupu ignorována. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt--číslo sekvence | Jedinečné číslo definované na služby přiřazené zprávu. | [SequenceNumber –](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x opt posunutí | Služby definované zařazených do fronty pořadové číslo zprávy. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-opt uzamčena – dokud | Služby definované. Datum a čas, do kterého se zamkne zprávy do fronty předplatného. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-nedoručených zpráv source | Služby definované. Pokud z fronty nedoručených zpráv, zdroj na původní zprávu o doručení zprávy. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Transakce funkce

Transakce skupiny dvou nebo více operací společně do v provádění oboru. Vzhledem k povaze tuto transakci Ujistěte se, že všechny operace, které patří do dané skupiny operací úspěšné, nebo selže společně.
Operace, které jsou seskupené podle identifikátoru `txn-id`.

Pro interakci s transakcí, klient funguje `transaction controller` který určuje operace, které by měl být seskupeny dohromady. Služba Service Bus funguje jako `transactional resource` a provede práci, jak vyžádala `transaction controller`.

Klient a služba komunikovat přes `control link` což navázání klientem. `declare` a `discharge` odeslání zpráv adaptérem pomocí ovládacího prvku odkazu přidělit a dokončení transakcí v uvedeném pořadí (reprezentují rozhraničení transakční pracovní). Skutečné odeslat a přijmout neprovádí se u tohoto odkazu. Každý transakční požadovaná operace je explicitně označeny požadovanou `txn-id` a proto může docházet na libovolný odkaz na připojení. Pokud ovládací prvek odkazu je zavřená, když existuje jiný ukončen transakcí, které ji vytvořit, pak tyto transakce jsou okamžitě vrácena zpět, a pokusí provést další transakční práci s nimi povede k selhání. Zprávy na ovládací prvek odkazu nesmí být před vyrovnané.

Každé připojení má zahájíte vlastní ovládací prvek odkazu být schopni začínat a končit transakce. Definuje speciální cíl, který funguje jako službu `coordinator`. Klient nebo řadič vytváří řízení odkaz na tento cíl. Ovládací prvek odkazu je mimo hranice entity, tj., stejného řízení propojení lze použít k zahájení a plnit transakcí pro více entit.

#### <a name="starting-a-transaction"></a>Spouštění transakce

Zahájení práce transakcí. musíte získat kontroleru `txn-id` z koordinátorem. Dělá to pomocí odesílání `declare` typ zprávy. Pokud deklaraci úspěšné, koordinátorem odpoví dispozice výsledek `declared` který představuje přiřazená `txn-id`.

| Klient (řadič) | | Service Bus (koordinátor) |
| --- | --- | --- |
| připojení)<br/>název = {název odkazu}<br/>... ,<br/>role =**odesílatele**,<br/>Cíl =**Coordinator**<br/>) | ------> |  |
|  | <------ | připojení)<br/>název = {název odkazu}<br/>... ,<br/>target=Coordinator()<br/>) |
| přenos)<br/>id dodání = 0,...)<br/>{AmqpValue (**Declare()**)}| ------> |  |
|  | <------ | (dispozice <br/> nejprve = 0, poslední = 0, <br/>Stav =**Declared**()<br/>**txn id**= {id transakce}<br/>))|

#### <a name="discharging-a-transaction"></a>Výkonu transakce

Řadičem skončí transakční pracovní odesláním `discharge` zprávu koordinátorem. Řadičem označuje, že si přeje potvrzení nebo vrácení změn transakční pracovní nastavením `fail` příznak na vyřízení textu. Pokud koordinátorem nelze dokončit vyřízení, zpráva se odmítne kvůli tento výsledek provádění `transaction-error`.

> Poznámka: selhání = true odkazuje na vrácení transakce a selhání = false odkazuje na potvrzení.

| Klient (řadič) | | Service Bus (koordinátor) |
| --- | --- | --- |
| přenos)<br/>id dodání = 0,...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | (dispozice <br/> nejprve = 0, poslední = 0, <br/>Stav = deklarované ()<br/>txn id = {id transakce}<br/>))|
| | . . . <br/>Transakční pracovní<br/>na dalších připojeních<br/> . . . |
| přenos)<br/>id dodání = 57,...)<br/>{AmqpValue)<br/>**Plnit (txn id = 0,<br/>nezdaří = false)**)}| ------> |  |
| | <------ | (dispozice <br/> nejprve = 57, poslední = 57, <br/>Stav =**funkce Accepted()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Odesílání zprávy v transakci

Všechny transakční práce lze provést pomocí stavu transakční doručení `transactional-state` , představuje txn-id. V případě odesílání zpráv, transakční stavu probíhá podle rámce přenosu zprávy. 

| Klient (řadič) | | Service Bus (koordinátor) |
| --- | --- | --- |
| přenos)<br/>id dodání = 0,...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | (dispozice <br/> nejprve = 0, poslední = 0, <br/>Stav = deklarované ()<br/>txn id = {id transakce}<br/>))|
| přenos)<br/>zpracování = 1,<br/>doručení id = 1, <br/>**Stav =<br/>TransactionalState (<br/>txn id = 0)**)<br/>{datová část}| ------> |  |
| | <------ | (dispozice <br/> nejprve = 1, poslední = 1, <br/>Stav =**TransactionalState (<br/>txn id = 0,<br/>outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Uvolnění zpráv v transakci

Zpráva dispozice zahrnuje operace, jako je `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer`. Chcete-li provést tyto operace v rámci transakce, předat `transactional-state` s dispozice.

| Klient (řadič) | | Service Bus (koordinátor) |
| --- | --- | --- |
| přenos)<br/>id dodání = 0,...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | (dispozice <br/> nejprve = 0, poslední = 0, <br/>Stav = deklarované ()<br/>txn id = {id transakce}<br/>))|
| | <------ |přenos)<br/>zpracování = 2,<br/>id dodání = 11, <br/>Stav = null)<br/>{datová část}|  
| (dispozice <br/> nejprve = 11, poslední = 11, <br/>Stav =**TransactionalState (<br/>txn id = 0,<br/>outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Pokročilé funkce služby Service Bus

Tato část obsahuje rozšířené možnosti služby Azure Service Bus, které jsou založeny na koncept rozšíření AMQP, aktuálně vyvíjených v OASIS technický výbor pro AMQP. Service Bus implementuje nejnovější verze tyto koncepty a přijímá změny zavedené jako tyto koncepty dosáhne standardní stavu.

> [!NOTE]
> Service Bus zasílání zpráv, pokročilé operace jsou podporovány prostřednictvím vzor požadavků a odpovědí. Podrobnosti o tyto operace jsou popsané v článku [protokolu AMQP 1.0 v Service Bus: na základě požadavku odpověď operace](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>AMQP správy

Specifikace protokolu AMQP správy je první rozšíření koncept popsané v tomto článku. Tato specifikace definuje sadu protokolů jako vrstva nad protokol AMQP, které umožňují správu interakce s infrastrukturu zasílání zpráv přes AMQP. Specifikace definuje obecná operací, jako *vytvořit*, *číst*, *aktualizace*, a *odstranit* pro správu entity uvnitř infrastrukturu zasílání zpráv a sadu operace dotazů.

Všechny tyto gesta vyžadovat interakci požadavků a odpovědí mezi klientem a infrastrukturu zasílání zpráv, a proto specifikace definuje, jak model tento vzor interakce nad AMQP: klient připojí k infrastrukturu zasílání zpráv, inicializuje relaci a potom vytvoří pár odkazy. Na jeden odkaz klient funguje jako odesílatele a na dalších funguje jako příjemce, proto vytvoří pár odkazy, které může fungovat jako obousměrný kanál.

| Logický provoz | Klient | Service Bus |
| --- | --- | --- |
| Vytvořit žádost o odpovědi cestu |--> připojit ()<br/>název = {*název odkazu*},<br/>zpracování = {*číselné popisovač*},<br/>role =**odesílatele**,<br/>Zdroj =**null**,<br/>target = "Správa myentity / $"<br/>) |Žádná akce |
| Vytvořit žádost o odpovědi cestu |Žádná akce |\<--připojení ()<br/>název = {*název odkazu*},<br/>zpracování = {*číselné popisovač*},<br/>role =**příjemce**,<br/>Zdroj = null,<br/>target = "myentity"<br/>) |
| Vytvořit žádost o odpovědi cestu |--> připojit ()<br/>název = {*název odkazu*},<br/>zpracování = {*číselné popisovač*},<br/>role =**příjemce**,<br/>zdroj = "Správa myentity / $",<br/>target = "myclient$ id"<br/>) | |
| Vytvořit žádost o odpovědi cestu |Žádná akce |\<--připojení ()<br/>název = {*název odkazu*},<br/>zpracování = {*číselné popisovač*},<br/>role =**odesílatele**,<br/>zdroj = "myentity",<br/>target = "myclient$ id"<br/>) |

S tuto dvojici odkazy na místě, požadavků a odpovědí implementace je jednoduchá: žádost je zpráva odeslána na entitu uvnitř infrastrukturu zasílání zpráv, která funguje s technologií tohoto vzoru. V této zprávě požadavku *odpověď pro* pole *vlastnosti* část je nastaven na *cíl* identifikátor pro odkaz, na kterém doručit odpověď. Zpracování entity zpracuje požadavek a pak doručí odpovědi přes propojení jejichž *cíl* identifikátor odpovídá uvedené *Komu* identifikátor.

Vzor samozřejmě vyžaduje, aby kontejneru klienta a identifikátor klientem generovaná pro cíl odpovědi byly jedinečné napříč všech klientů a z bezpečnostních důvodů se taky obtížné předpovědi.

Výměny zpráv použít pro protokol pro správu a všech ostatních protokolů, které používají stejný vzor dojít na úrovni aplikace; nedefinují nové gesta úrovni protokolu AMQP. Který je úmyslné, takže aplikace mohou využít okamžitou výhod těchto rozšíření s kompatibilní zásobníky protokolu AMQP 1.0.

Service Bus neimplementuje aktuálně některé z klíčových funkcí správy specifikace, ale vzoru požadavků a odpovědí definované specifikací správy je základní pro funkci deklarace identity na základě zabezpečení a téměř všechny rozšířené možnosti popsané v následujících částech.

### <a name="claims-based-authorization"></a>Ověření na základě deklarace identity

Koncept specifikace AMQP deklarace identity na základě autorizace (CBS) založený na vzoru správu specifikace požadavků a odpovědí a popisuje zobecněný model pro používání tokenů zabezpečení federované s AMQP.

Model zabezpečení výchozí AMQP popsané v úvodu je založena na SASL a integruje se metoda handshake připojení protokolu AMQP. Pomocí SASL má výhodu, která poskytuje rozšiřitelnou model pro kterou se definovaly sadu mechanismy z libovolného protokolu, který dříve leans na SASL můžete využívat. Mezi tyto mechanismy jsou "PLAIN" pro přenos uživatelských jmen a hesel, chcete-li vytvořit vazbu k zabezpečení na úrovni protokolu TLS, "ANONYMNÍ" vyjádřit absenci explicitní ověřování nebo autorizace a mnoha rozličných další mechanismy, které povolí předávání ověřování a přihlašovací údaje pro autorizaci a tokeny "externí".

Integrace SASL na AMQP má dva nevýhody:

* Všechny přihlašovací údaje a tokeny jsou omezená na připojení. Infrastrukturu zasílání zpráv může chtít k poskytování řízení rozlišené přístupu na základě za entity; například povolení nosiče tokenu k odeslání do fronty A, ale ne do fronty B. S autorizační kontext ukotvené na připojení není možné používat jednoho připojení a ještě používat různé přístupových tokenů pro fronty A a B. fronty
* Přístupové tokeny jsou obvykle platné pouze po omezenou dobu. Tato platnost vyžaduje, aby uživatel pravidelně opakované načtení tokeny a poskytuje možnost tokenu Issuer odmítnout vydání nový token, pokud se změnila oprávnění uživatele k přístupu. AMQP připojení může trvat dlouhou dobu. SASL model pouze poskytuje možnost nastavit token v době připojení, což znamená, že buď má na odpojení klienta v případě, že platnost tokenu vyprší nebo je nutné přijmout riziko umožňuje nepřetržitý komunikace s klientem infrastrukturu zasílání zpráv kdo má přístupová práva může mít odvolaný dočasně.

Specifikace protokolu AMQP CBS implementované Service Bus, umožňuje elegantní alternativní řešení pro obě tyto problémy: umožňuje klientovi přidružit každý uzel přístupové tokeny a aktualizovat tyto tokeny před vypršením jejich platnosti, aniž by to ovlivnilo tok zpráv.

Správa virtuálního uzlu s názvem definuje CBS *$cbs*, je poskytovat infrastrukturu zasílání zpráv. K uzlu správy přijímá tokeny jménem jiných uzlů v infrastrukturu zasílání zpráv.

Gesto protokol je požadavek nebo odpověď exchange podle definice specifikace správy. Aby znamená klient vytvoří pár propojení s *$cbs* uzel a pak předá požadavek na odchozí odkaz a potom na odkaz příchozí čeká na odpověď.

Zpráva odpovědi má následující vlastnosti aplikace:

| Klíč | Nepovinné | Typ hodnoty | Hodnota obsahu |
| --- | --- | --- | --- |
| operace |Ne |řetězec |**PUT tokenu** |
| type |Ne |řetězec |Typ tokenu, který provádí požadavek put. |
| jméno |Ne |řetězec |"Cílová skupina" na které se vztahuje token. |
| konec platnosti |Ano |časové razítko |Čas vypršení platnosti tokenu. |

*Název* identifikuje vlastnost entity, k němuž musí být token přidruženy. V Service Bus je cesta k fronta nebo téma/odběr. *Typ* vlastnosti jsou uvedeny typy tokenů:

| Typ tokenu | Popis tokenu | Typ těla zprávy | Poznámky |
| --- | --- | --- | --- |
| amqp:jwt |Webového tokenu JSON (JWT) |AMQP hodnota (string) |Ještě není k dispozici. |
| amqp:swt |Jednoduchého webového tokenu (SWT) |AMQP hodnota (string) |Podporováno pouze pro SWT tokeny vydané službou AAD/ACS |
| servicebus.Windows.NET:sastoken |Token SAS sběrnice služby |AMQP hodnota (string) |- |

Tokeny udělit práva. Service Bus, že zná tři základní práva: "Odeslat" umožňuje odesílání, příjem umožňuje "Naslouchání" a "Manage" umožňuje manipulace s těmito entity. SWT tokeny vydané službou AAD/ACS explicitně zahrnovat tato práva jako deklarace identity. Tokeny SAS sběrnice služby naleznete nakonfigurovaná na obor názvů nebo entity pravidla, a tato pravidla jsou nakonfigurované s právy. Podpis tokenu s klíč přidružený daného pravidla proto díky tokenu express příslušných práv. Token přidružené entitu s využitím *put token* umožňuje připojený klient k interakci se entita na práva k tokenu. Certifikovaného klienta na odkaz *odesílatele* role vyžaduje "Odeslat" právo; s ohledem *příjemce* role vyžaduje "Naslouchání" vpravo.

Zpráva odpovědi má následující *vlastnosti aplikace* hodnoty

| Klíč | Nepovinné | Typ hodnoty | Hodnota obsahu |
| --- | --- | --- | --- |
| Stavový kód |Ne |celá čísla |Kód odpovědi HTTP **[RFC2616]**. |
| Popis stavu |Ano |řetězec |Popis stavu. |

Klient může volat *put token* opakovaně a pro všechny entity v infrastrukturu zasílání zpráv. Tokeny jsou vymezeny do aktuálního klienta a ukotvené na aktuální připojení, což znamená, že server zahodí všechny tokeny, zachované při přerušení připojení.

Aktuální implementace sběrnice pouze umožňuje CBS ve spojení s metodou SASL "ANONYMNÍ." Připojení protokolem SSL/TLS, musí existovat vždy před SASL handshake.

ANONYMNÍ mechanismus musí podporovat proto zvoleného klienta protokolu AMQP 1.0. Anonymní přístup znamená, že metoda handshake počáteční připojení, včetně vytváření počáteční relace se stane bez Service Bus zároveň budete vědět, kdo je vytvoření připojení.

Po vytvoření připojení a relace připojení na odkazy *$cbs* uzel a odesílání *put token* žádosti jsou pouze povolené operace. Platný token musí být nastavena úspěšně pomocí *put token* požadavku pro některé entity uzel v rámci 20 sekund po navázání připojení, jinak hodnota připojení se jednostranně ukončí prostředím Service Bus.

Klient je následně zodpovědná pro udržování přehledu o vypršení platnosti tokenu. Když vyprší platnost tokenu, Service Bus neprodleně zahodí všechny odkazy na připojení k příslušné entity. Chcete-li tomu zabránit, klient může nahradit token pro uzel s novou kdykoli prostřednictvím virtuální *$cbs* uzlu správy se stejným *put token* gesty a bez získávání cestě datové části provoz, který probíhá na jiné odkazy.

### <a name="send-via-functionality"></a>Odesílání prostřednictvím funkce

[Odesílání prostřednictvím / Transfer odesílatele](service-bus-transactions.md#transfers-and-send-via) je funkce, které umožňuje, aby služba sběrnici dál danou zprávu do cílové entity prostřednictvím jinou entitou. To se používá hlavně k provádění operací napříč entity v jedné transakci.

Pomocí této funkce vytvoření odesílatele a vytvořit odkaz `via-entity`. Při navazování odkaz, je předán navázat true cíl zprávy/přenosů na tento odkaz Další informace. Po připojení proběhlo úspěšně, všechny zprávy odeslané na tento odkaz se automaticky předají do *cílové entity* prostřednictvím *prostřednictvím entity*. 

> Poznámka: Ověřování se provádí pro obě *prostřednictvím entity* a *cílové entity* před navázáním tento odkaz.

| Klient | | Service Bus |
| --- | --- | --- |
| připojení)<br/>název = {název odkazu}<br/>role = odesílatele,<br/>Zdroj = {id klienta odkaz},<br/>Cíl =**{prostřednictvím entity}**,<br/>**Vlastnosti = map [(<br/>com.microsoft:transfer. cílové adresy =<br/>{cílové entity})]** ) | ------> | |
| | <------ | připojení)<br/>název = {název odkazu}<br/>role = příjemce,<br/>Zdroj = {id klienta odkaz},<br/>cíl = {prostřednictvím entity},<br/>Vlastnosti mapy [() =<br/>com.Microsoft:Transfer. cílové adresy =<br/>{cílové entity})] ) |

## <a name="next-steps"></a>Další postup

Další informace o protokolu AMQP, získáte pomocí následujících odkazů:

* [Přehled protokolu AMQP Service Bus]
* [Podpora protokolu AMQP 1.0 témata a fronty Service Bus rozdělena na oddíly]
* [AMQP v Service Bus pro systém Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Přehled protokolu AMQP Service Bus]: service-bus-amqp-overview.md
[Podpora protokolu AMQP 1.0 témata a fronty Service Bus rozdělena na oddíly]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP v Service Bus pro systém Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

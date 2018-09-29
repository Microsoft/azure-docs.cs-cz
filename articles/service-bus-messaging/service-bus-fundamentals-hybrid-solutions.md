---
title: Přehled základů služby Azure Service Bus | Dokumentace Microsoftu
description: Úvod do používání Service Bus ke spojení aplikace Azure a jiného softwaru.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: get-started-article
ms.date: 09/23/2018
ms.author: spelluru
ms.openlocfilehash: 03030df7c75cc171640557769bc42896ae10a9ac
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392342"
---
# <a name="azure-service-bus"></a>Azure Service Bus

Ať už aplikace nebo služba běží v cloudu nebo lokálně, často potřebuje komunikovat s jinými aplikacemi nebo službami. Microsoft Azure nabízí službu Service Bus, která právě toto výrazně usnadňuje. V tom to článku se podíváme na tuto technologii a na to, proč byste ji mohli chtít používat.

## <a name="service-bus-fundamentals"></a>Základy služby Service Bus

Různé situace potřebují různé styly komunikace. Někdy je pro aplikace nejlepším řešením zprávy odesílat a přijímat přes jednoduchou frontu. V jiných situacích běžná fronta nestačí a je lepší použít frontu s publikováním a odběrem. V některých případech stačí jen navázat spojení mezi aplikacemi a fronty nejsou vůbec potřeba. Azure Service Bus poskytuje všechny tři možnosti a umožňuje vašim aplikacím pracovat několika různými způsoby.

Service Bus je víceklientská cloudová služba – to znamená, že ji může sdílet několik uživatelů. Každý uživatel, jako třeba vývojář aplikací, vytvoří *obor názvů*, a pak definuje komunikační mechanizmy, které potřebuje v daném oboru názvů. Obrázek 1 znázorňuje tuto architekturu:

![][1]

**Obrázek 1: Service Bus poskytuje víceklientské služby pro připojení aplikací přes cloud.**

V oboru názvů můžete použít jednu nebo víc instancí tří různých komunikačních mechanizmů, každý z nich spojuje aplikace jiným způsobem. Na výběr jsou:

* *Fronty*, které umožňují jednosměrnou komunikaci. Každá fronta slouží jako prostředník (někdy se tomu říká *zprostředkovatel*), který ukládá odeslané zprávy, dokud nedorazí k příjemci. Každou zprávu přijme jeden příjemce.
* *Témata*, která poskytují jednosměrnou komunikaci prostřednictvím *odběrů*. Jedno téma může mít několik odběrů. Podobně jako u front je téma něco jako zprostředkovatel, ale každý odběr může volitelně používat filtr a přijímat jen zprávy, které odpovídají konkrétním kritériím.
* *Předávání*, které umožňuje jednosměrnou komunikaci. Na rozdíl od front a témat se při předávání neukládají žádné zprávy; nejedná se o zprostředkování. Naopak se zprávy jednoduše předají k cílové aplikaci.

Když vytvoříte frontu, téma nebo předávání, musíte je pojmenovat. Kombinace tohoto názvu a vašeho oboru názvů vytváří jedinečný identifikátor objektu. Aplikace můžou tento název poskytnout službě Service Bus, a pak pomocí této fronty, tématu nebo předávání mezi sebou komunikovat.

 >**Příklad:**   
     *https://&lt;název_oboru_názvů_služby_service_bus&gt;.servicebus.windows.net/&lt;název_fronty&gt;*  
     *https://&lt;název_oboru_názvů_služby_service_bus&gt;.servicebus.windows.net/&lt;název_tématu&gt;*  
     *https://&lt;název_oboru_názvů_služby_service_bus&gt;.servicebus.windows.net/&lt;název_propojení&gt;*  

Pokud aplikace Windows chtějí tyto objekty používat ve scénáři přenosu, můžou využít WCF (Windows Communication Foundation). Tato služba se označuje jako [přenos WCF](../service-bus-relay/relay-what-is-it.md). Pro fronty a témata můžou aplikace Windows použít API pro přenos zpráv, které definuje služba Service Bus. Pro snadnější použití těchto objektů z aplikací pro jinou platformu než Windows uvolnil Microsoft sady SDK pro Javu, Node.js a další jazyky. Přístup k frontám a tématům se může získat i pomocí [REST API](/rest/api/servicebus/) přes HTTP(s). 

Je důležité pochopit, že i když služba Service Bus samotná běží v cloudu (to znamená v datových centrech Microsoft Azure), aplikace, které ji využívají, můžou běžet kdekoli. Service Bus můžete použít třeba k připojení aplikací běžících v Azure nebo aplikací běžících ve vašem vlastním datovém centru. Můžete ji použít i k připojení aplikace běžící v Azure nebo jiné cloudové platformě k místní aplikaci nebo k tabletům a telefonům. Service Bus je komunikační mechanizmus v cloudu, který je přístupný prakticky odkudkoli. To, jakým způsobem ho budete využívat, záleží jen na tom, co vaše aplikace potřebují dělat.

## <a name="queues"></a>Fronty

Řekněme, že jste se rozhodli spojit dvě aplikace pomocí fronty Service Bus. Obrázek 2 ilustruje takovou situaci:

![][2]

**Obrázek 2: Fronty Service Bus poskytují jednosměrné asynchronní řízení front zpráv.**

Odesílatel odešle zprávu do fronty Service Bus a příjemce si ji z fronty vyzvedne někdy později. Je možné, aby fronta měla pouze jednoho příjemce, jak je znázorněno na obrázku 2. Ze stejné fronty může také číst více aplikací. V druhém případě přečte každou zprávu pouze jeden příjemce. Pro službu přetypování byste měli raději použít téma.

Každá zpráva má dvě části: skupinu vlastností ve formě dvojice klíč+hodnota a tělo zprávy. Tělo zprávy může mít formát binární, textový nebo i XML. Jejich použití závisí na tom, co se vaše aplikace snaží udělat. Například jedna aplikace odešle zprávu o nedávném prodeji, která může obsahovat třeba tyto údaje: **Seller="Ava"** a **Amount=10000**. Tělo zprávy může obsahovat naskenovaný snímek podepsané smlouvy o prodeji nebo nemusí obsahovat nic a může zůstat prázdné.

Příjemce může zprávu načíst z fronty Service Bus dvěma různými způsoby. První možnost se jmenuje *[ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode)* (přijmout a odstranit) – přijme zprávu z fronty a okamžitě ji odstraní. Tento způsob je velmi jednoduchý, ale pokud příjemce spadne, než se mu podaří zprávu zpracovat, zprávu ztratí. A protože se už odstranila z fronty, nemůže ji získat ani žádný jiný příjemce. 

Druhá možnost se jmenuje *[PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)* (uzamknout pro zpracování) a jejím smyslem je vyřešit právě tento problém. Stejně jako v případě **ReceiveAndDelete** se při čtení **PeekLock** zpráva odebere z fronty. Jenže se při tom neodstraní úplně. Místo toho se zpráva jen uzamkne, aby nebyla vidět pro ostatní příjemce, a pak čeká na jednu ze tří událostí:

* Pokud příjemce zprávu úspěšně zpracuje, zavolá [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) a fronta zprávu odstraní. 
* Pokud se příjemce rozhodne, že nedokáže zprávu úspěšně zpracovat, zavolá [Abandon()](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync). Fronta pak zruší zámek zprávy a ta je pak znovu dostupná pro ostatní příjemce.
* Pokud příjemce v nastaveném časovém limitu nezavolá ani jednu z těchto metod (ve výchozím nastavení je to 60 sekund), fronta předpokládá, že příjemce selhal. V takovém případě se fronta chová jako kdyby příjemce zavolal **Abandon** a zpřístupní zprávu dalším příjemcům.

Všimněte si, co se tu může stát: Stejná zpráva se může dodat dvakrát, třeba i dvěma různým příjemcům. Aplikace, které používají fronty Service Bus, na tuto možnost musí být připravené. Pro usnadnění detekce duplicitních zpráv má každá zpráva jedinečnou vlastnost [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid#Microsoft_Azure_ServiceBus_Message_MessageId), která ve výchozím nastavení zůstává pro danou zprávu vždy stejná bez ohledu na to, kolikrát se přečte z fronty. 

Fronty jsou užitečné v mnoha situacích. Umožňují aplikacím komunikovat, i když nejsou spuštěné ve stejnou dobu – to se hodí především pro dávkové a mobilní aplikace. Fronta s několika příjemci taky poskytuje automatické vyvažování zátěže, protože odeslané zprávy se rozdělují mezi jednotlivé příjemce.

## <a name="topics"></a>Témata

Přestože jsou fronty velice užitečné, nemusí se vždy jednat o to nejlepší řešení. Někdy jsou lepší témata. Obrázek 3 ilustruje tento nápad:

![][3]

**Obrázek 3: V závislosti na použitém filtru může odběratelská aplikace přijímat všechny nebo jen některé zprávy odeslané do tématu Service Bus.**

*Téma* se ve spoustě ohledů podobá frontě. Odesílatelé odesílají zprávy do tématu stejným způsobem jako do fronty a zprávy v tématu vypadají stejně jako zprávy ve frontě. Rozdíl je ale v tom, že témata umožňují každé aplikaci, aby si vytvořila vlastní *odběr* a volitelně definovala *filtr*. Odběratel obdrží kopii každé zprávy v tomto tématu, může ale použít filtr a pak přijímat jenom zprávy, které filtru odpovídají. Na obrázku 3 je například téma se třemi odběrateli a každý z nich používá vlastní filtr.

* Odběratel 1 přijímá jen zprávy, které mají určitou vlastnost **Seller="Ava"**.
* Odběratel 2 přijímá zprávy, které mají vlastnost **Seller="Ruby"** a/nebo mají vlastnost **Amount** s hodnotou vyšší než 100 000. Možná je Ruby manažerka prodeje, takže chce vidět svoje prodeje a všechny velké prodeje bez ohledu na to, čí jsou.
* Odběratel 3 má nastavený filtr **True** – to znamená, že přijímá všechny zprávy. Tato aplikace může mít například na starost udržování záznamu pro audit a proto potřebuje vidět všechny zprávy.

Stejně jako v případě front můžou odběratelé tématu načítat zprávy buď způsobem [ReceiveAndDelete, nebo PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode). Na rozdíl od front se ale jedna zpráva odeslaná do tématu může dostat k více odběratelům předplatných. Tomuto přístupu se obvykle říká *publikování a odběr* (nebo *pub/sub*)a je vhodný v každé situaci, kde se o stejné zprávy zajímá několik aplikací. Pokud odběratel definuje vhodný filtr, může si z proudu zpráv vytáhnout jen ty, které potřebuje.

## <a name="relays"></a>Předávání

Fronty i témata nabízejí jednosměrnou asynchronní komunikaci přes zprostředkovatele. Zprávy proudí jen jedním směrem a mezi odesílateli a příjemci není žádné přímé spojení. Co když ale toto připojení nechcete? Řekněme, že aplikace potřebují odesílat i přijímat zprávy nebo že mezi nimi třeba chcete vytvořit přímé spojení a nepotřebujete zprostředkovatele pro ukládání zpráv. Pro takovou situaci Service Bus nabízí možnost *přenosu*, jak je vidět na obrázku 4:

![][4]

**Obrázek 4: Přenos přes Service Bus nabízí synchronní obousměrnou komunikaci mezi aplikacemi.**

Asi vás napadne otázka týkající se přenosu: K čemu by se mi tohle hodilo? I když nepotřebuju fronty, proč mám nutit aplikace komunikovat přes cloudovou službu, když chci, aby komunikovaly přímo? Odpověď je taková, že přímá komunikace je někdo mnohem obtížnější, než by se mohlo zdát.

Řekněme, že chcete propojit dvě lokální aplikace, které obě běží ve firemních datových centrech. Každá z těchto aplikací je za firewallem a každé datové centrum pravděpodobně používá překládání adres (NAT). Firewall povoluje data jen na několika málo portech a ostatní porty blokuje, zatímco NAT naznačuje, že ani jeden z počítačů, na kterých aplikace běží, nemá pevnou IP adresu, takže z jiné zóny, než je zóna jejího datového centra, se k ní nedá dostat přímo. Bez pomoci specializovaných nástrojů půjdou tyto aplikace propojit přes internet jen těžko.

Service Bus relay může pomoci. Aby aplikace mohly komunikovat obousměrně s předáváním, každá z nich vytvoří odchozí připojení přes TCP se službou Service Bus a udržuje ho otevřené. Veškerá komunikace mezi těmito dvěma aplikacemi bude procházet přes tato spojení. Protože se obě spojení vytvořila ze zóny datového centra, firewall povolí veškerou příchozí komunikaci pro danou aplikaci bez nutnosti otevřít nové porty. Tento přístup také obchází problém s NAT, protože každá z obou aplikací má v cloudu vlastní pevný koncový bod. Výměnou dat přes předávací službu se aplikace můžou vyhnout problémům, které by jinak takovou komunikaci výrazně ztěžovaly. 

Pokud aplikace chtějí používat předávání přes Service Bus, můžou využít WCF (Windows Communication Foundation). Service Bus poskytuje vazby WCF, které aplikacím Windows ulehčují komunikaci přes předávací službu. Pro aplikace, které už WCF používají, obvykle stačí specifikovat jednu z těchto vazeb a potom můžou komunikovat přes předávací službu. Na rozdíl od front a témat ale komunikace přes předávací službu z aplikací na jiné platformě než Windows vyžaduje určitou míru programátorského úsilí, protože nejsou k dispozici žádné standardizované knihovny.

A na rozdíl od front a témat taky aplikace nevytvářejí explicitně předávací službu. Místo toho se stane, že když aplikace chce dostávat zprávy, vytvoří spojení se službou Service Bus přes TCP a předávací služba se vytvoří automaticky. Při ukončení spojení se předávací služba odstraní. Aby aplikace mohly najít předávací službu vytvořenou konkrétním posluchačem, Service Bus aplikacím poskytuje registr, který jim umožní najít konkrétní předávací službu podle názvu.

Předávací služby jsou správným řešením v situaci, když potřebujete přímou komunikaci mezi aplikacemi. Řekněme třeba, že systém pro rezervaci letenek běží na lokálním datovém centru, ke kterému musí mít přístup prodejní místa, mobilní zařízení a další počítače. Aplikace, které běží na všech těchto systémech, by mohly ke komunikaci použít předávací služby Service Bus v cloudu, a bylo by jedno, kde běží.

## <a name="summary"></a>Souhrn

Propojení aplikací vždy patřilo k budování kompletních řešení a množství situací, ve kterých spolu aplikace a služby musí komunikovat, se s rostoucím počtem zařízení připojených k internetu bude dál zvyšovat. Služba Service Bus poskytuje cloudové technologie pro zajištění komunikace prostřednictvím front, témat a předávání, aby tuto základní funkci aplikacím a službám umožnil, usnadnil a více zpřístupnil.

## <a name="next-steps"></a>Další kroky

Dozvěděli jste se základní informace službě Azure Service Bus, další informace se dozvíte na následujících odkazech.

* Jak používat [fronty Service Bus](service-bus-dotnet-get-started-with-queues.md)
* Jak používat [témata Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* Jak používat [předávání přes Service Bus](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md)
* [Ukázky služby Service Bus](service-bus-samples.md)

[1]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_01_architecture.png
[2]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_02_queues.png
[3]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[4]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_04_relay.png

---
title: Fronty nedoručených zpráv Service Bus | Microsoft Docs
description: Popisuje fronty nedoručených zpráv v Azure Service Bus. Service Bus fronty a odběry témat poskytují sekundární dílčí frontu, která se nazývá fronta nedoručených zpráv.
ms.topic: article
ms.date: 04/08/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 6293a3a9a760ece137644578d8ee7dccebc63d95
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812367"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Přehled Service Bus front nedoručených zpráv

Azure Service Bus fronty a odběry témat poskytují sekundární dílčí frontu nazvanou *frontu nedoručených zpráv* (DLQ). Fronta nedoručených zpráv nemusí být explicitně vytvořena a nelze ji odstranit ani spravovat nezávisle na hlavní entitě.

Tento článek popisuje fronty nedoručených zpráv v Service Bus. Mnohé diskuze jsou znázorněné ve [vzorové frontě nedoručených zpráv](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue) na GitHubu.
 
## <a name="the-dead-letter-queue"></a>Fronta nedoručených zpráv

Účelem fronty nedoručených zpráv je uchovávat zprávy, které nelze doručit žádnému příjemci, nebo zprávy, které nebylo možné zpracovat. Zprávy je pak možné odebrat z DLQ a zkontrolovat. Aplikace může pomocí operátoru, opravit problémy a znovu odeslat zprávu, zaznamenat fakt, že došlo k chybě, a provést nápravné opatření. 

Z hlediska rozhraní API a protokolu se DLQ většinou podobá jakékoli jiné frontě, s tím rozdílem, že zprávy je možné odeslat jenom prostřednictvím operace nedoručené pošty nadřazené entity. Kromě toho není pozor na hodnotu Time to Live a nemůžete nedoručenou zprávu z DLQ. Fronta nedoručených zpráv plně podporuje operace čtení a transakcí s náhledem.

Není k dispozici žádné automatické vyčištění DLQ. Zprávy zůstávají v DLQ, dokud je explicitně nenačtete z DLQ a dokončete zprávu s nedoručenými zprávami.


## <a name="dlq-message-count"></a>Počet zpráv DLQ
Počet zpráv ve frontě nedoručených zpráv není možné získat na úrovni tématu. Důvodem je to, že zprávy nejsou na úrovni tématu, pokud Service Bus vyvolá vnitřní chybu. Místo toho, když odesílatel pošle zprávu do tématu, zpráva se přepošle k předplatným pro toto téma během milisekund, takže se už nebude nacházet na úrovni tématu. Takže můžete zobrazit zprávy v DLQ přidružené k předplatnému pro téma. V následujícím příkladu **Service Bus Explorer** ukazuje, že v DLQ pro předplatné "test1" jsou aktuálně 62 zprávy. 

![Počet zpráv DLQ](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Počet zpráv DLQ můžete také získat pomocí příkazu rozhraní příkazového řádku Azure: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) . 

## <a name="moving-messages-to-the-dlq"></a>Přesun zpráv do DLQ
V Service Bus je několik aktivit, které způsobují, že se zprávy dostanou do DLQ z samotného stroje pro zasílání zpráv. Aplikace může také explicitně přesouvat zprávy do DLQ. Následující dvě vlastnosti (důvod nedoručených zpráv a popis nedoručených zpráv) se přidají do zpráv s nedoručenými zprávami. Aplikace mohou definovat vlastní kódy pro vlastnost důvod nedoručených zpráv, ale systém nastaví následující hodnoty.

| Důvod nedoručených zpráv | Popis chyby nedoručených zpráv |
| --- | --- |
|HeaderSizeExceeded |Kvóta velikosti pro tento datový proud byla překročena. |
|TTLExpiredException |Zprávě vypršela platnost a zařadila se do fronty nedoručených zpráv. Podrobnosti najdete v části [Time to Live](#time-to-live) . |
|ID relace je null. |Entita povolená relací nepodporuje zprávy, jejichž identifikátor relace má hodnotu null. |
|MaxTransferHopCountExceeded | Maximální počet povolených směrování při předávání mezi frontami. Hodnota je nastavená na 4. |
| MaxDeliveryCountExceededExceptionMessage | Zprávu nelze spotřebovat po maximálním počtu pokusů o doručení. Podrobnosti najdete v části [maximální počet doručení](#maximum-delivery-count) . |

## <a name="maximum-delivery-count"></a>Maximální počet doručení
K dispozici je limit počtu pokusů o doručení zpráv pro Service Bus front a odběrů. Výchozí hodnota je 10. Pokaždé, když se zpráva doručí za náhled, ale buď byla explicitně opuštěna, nebo vypršela platnost zámku, zvýší se počet doručení ve zprávě. Když počet doručování překročí limit, zpráva se přesune do DLQ. Důvod nedoručených zpráv pro zprávu v DLQ je nastaven na: MaxDeliveryCountExceeded. Toto chování nelze zakázat, ale maximální počet doručení můžete nastavit na velké číslo.

## <a name="time-to-live"></a>Hodnota TTL (Time to Live)
Pokud povolíte nedoručené zprávy pro fronty nebo odběry, všechny zprávy o vypršení platnosti se přesunou do DLQ. Kód důvodu nedoručených zpráv je nastaven na: TTLExpiredException.

Zprávy s vypršenou platností se odstraní a přesunou se do DLQ, když se z hlavní fronty nebo předplatného vybírá aspoň jeden aktivní přijímač. Odložené zprávy se také nevyprázdní a po vypršení platnosti se přesunou do fronty nedoručených zpráv. Toto chování je záměrné.

## <a name="errors-while-processing-subscription-rules"></a>Chyby při zpracování pravidel předplatného
Pokud povolíte nedoručené výjimky při vyhodnocování filtru, všechny chyby, ke kterým dojde, když se pravidlo filtru SQL předplatného spustí, se do DLQ zachytí společně s problematickou zprávou. Tuto možnost nepoužívejte v produkčním prostředí, ve kterém ne všechny typy zpráv mají předplatitele.

## <a name="application-level-dead-lettering"></a>Nedoručená písmena na úrovni aplikace
Kromě funkcí nedoručených zpráv poskytovaných systémem může aplikace DLQ použít k explicitnímu zamítnutí nepřijatelných zpráv. Můžou zahrnovat zprávy, které se nedají správně zpracovat kvůli jakémukoli druhu problémů se systémem, zprávám, které obsahují poškozené datové části nebo zprávy, které selžou ověřování při použití určitého schématu zabezpečení na úrovni zprávy.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Nedoručené písmeno ve scénářích ForwardTo nebo SendVia
Zprávy se odešlou do fronty nedoručených zpráv přenosu za následujících podmínek:

- Zpráva projde více než čtyřmi frontami nebo tématy [zřetězenými dohromady](service-bus-auto-forwarding.md).
- Cílová fronta nebo téma jsou zakázané nebo odstraněné.
- Cílová fronta nebo téma překračuje maximální velikost entity.

## <a name="path-to-the-dead-letter-queue"></a>Cesta k frontě nedoručených zpráv
K frontě nedoručených zpráv můžete přistupovat pomocí následující syntaxe:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>Další kroky
Další informace o různých způsobech konfigurace **nedoručených zpráv pro nastavení vypršení platnosti zprávy** najdete v tématu [Povolení nedoručených zpráv pro frontu nebo odběr](enable-dead-letter.md) .

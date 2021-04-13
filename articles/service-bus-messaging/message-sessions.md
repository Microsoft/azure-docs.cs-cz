---
title: Relace Azure Service Bus zpráv | Microsoft Docs
description: Tento článek vysvětluje, jak pomocí relací povolit společné a seřazené zpracování neohraničených sekvencí souvisejících zpráv.
ms.topic: article
ms.date: 04/12/2021
ms.openlocfilehash: c9a1c4fdccbbc8b38805e23d4895448959126f10
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308466"
---
# <a name="message-sessions"></a>Relace zpráv
Microsoft Azure Service Bus relace umožňují společné a seřazené zpracování neohraničených sekvencí souvisejících zpráv. Relace se dají použít v vzorcích **First in, First out (FIFO)** a **Request-response** . Tento článek popisuje, jak používat relace k implementaci těchto vzorů při použití Service Bus. 

> [!NOTE]
> Základní Service Bus úrovně nepodporuje relace. Úrovně Standard a Premium podporují relace. Rozdíly mezi těmito úrovněmi najdete v tématu [Service Bus ceny](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="first-in-first-out-fifo-pattern"></a>Vzor first-in, First out (FIFO)
Pokud chcete v Service Bus realizovat jistotu FIFO, použijte relace. Service Bus není podrobnější informace o povaze vztahu mezi zprávami a také nedefinuje konkrétní model pro zjištění, kde začíná nebo končí sekvence zpráv.

Každý odesilatel může vytvořit relaci při odesílání zpráv do tématu nebo do fronty nastavením vlastnosti **ID relace** na některý identifikátor definovaný aplikací, který je pro relaci jedinečný. Na úrovni protokolu AMQP 1,0 se tato hodnota mapuje na vlastnost *Group-ID* .

U front a předplatných, které pracují s relacemi, se relace nacházejí v případě, že existuje alespoň jedna zpráva s ID relace. Po existující relaci není k dispozici žádný definovaný čas nebo rozhraní API pro dobu, kdy relace vyprší nebo zmizí. Teoreticky může být zpráva přijata pro relaci dnes, další zpráva v roce a v případě, že se shodují ID relace, relace je stejná jako v Service Bus perspektivě.

Obvykle však aplikace má jasný pojem, kde se spustí a končí sada souvisejících zpráv. Service Bus nenastavuje žádná konkrétní pravidla. Například vaše aplikace může nastavit vlastnost **Label** první zprávy na **Start**, pro mezilehlé zprávy na **obsah** a na **konec** poslední zprávy. Relativní umístění zpráv obsahu lze vypočítat jako aktuální rozdíl *SequenceNumber* zprávy ze *SequenceNumber* **spuštění** zprávy.

Tuto funkci povolíte nastavením vlastnosti [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) ve frontě nebo předplatném pomocí Azure Resource Manager nebo nastavením příznaku na portálu. Je nutné, abyste se pokusili použít související operace rozhraní API.

Na portálu můžete povolit relace při vytváření entity (fronty nebo předplatného), jak je znázorněno v následujících příkladech. 

:::image type="content" source="./media/message-sessions/queue-sessions.png" alt-text="Povolit relaci v době vytváření fronty":::

:::image type="content" source="./media/message-sessions/subscription-sessions.png" alt-text="Povolit relaci v době vytváření předplatného":::


> [!IMPORTANT]
> Když jsou povoleny relace ve frontě nebo v předplatném, klientské aplikace ***již*** nemohou odesílat a přijímat pravidelné zprávy. Všechny zprávy musí být odesílány v rámci relace (nastavením ID relace) a přijaty přijetím relace.

Rozhraní API pro relace existují na klientech front a předplatných. Existuje imperativní model, který řídí, kdy jsou přijímány relace a zprávy, a model založený na obslužných rutinách, který skrývá složitost správy přijímací smyčky. 

Pro ukázky použijte odkazy v části [Další kroky](#next-steps) . 

### <a name="session-features"></a>Funkce relace

Relace poskytují souběžné demultiplexování datových proudů zpráv při zachování a zaručení objednaného doručení.

![Diagram znázorňující, jak funkce relace zachovává seřazené doručení.][1]

Příjemce relace je vytvořen klientem, který přijímá relaci. Když je relace přijata a držená klientem, má klient exkluzivní zámek pro všechny zprávy s **ID relace** této relace ve frontě nebo předplatném. Bude také obsahovat exkluzivní zámky u všech zpráv s **ID relace** , které budou doručeny později.

Zámek se uvolní při volání souvisejících metod na přijímači nebo po vypršení platnosti zámku. Existují metody na přijímači k prodloužení i zámků. Místo toho můžete použít funkci automatického obnovení zámků, kde můžete zadat dobu, po kterou si přejete, aby se zámek obnovil. Zámek relace by měl být považován za výhradní zámek u souboru, což znamená, že aplikace by měla relaci ukončit, jakmile ji již nepotřebuje a/nebo neočekává žádné další zprávy.

Pokud je z fronty vyžádané více souběžných přijímačů, zprávy patřící do určité relace jsou odesílány na konkrétního příjemce, který v současné době zámek pro danou relaci obsahuje. V takovém případě se prokládaný datový proud zpráv v jedné frontě nebo předplatném čistí čistě proti různým přijímačům a jejich přijímače můžou být živé i v různých klientských počítačích, protože správa zámků nastane na straně služby v rámci Service Bus.

Na předchozím obrázku vidíte tři přijímače souběžných relací. Jedna relace s `SessionId` = 4 nemá žádné aktivní, vlastnícího klienta, což znamená, že z této konkrétní relace nebudou doručovány žádné zprávy. Relace funguje mnoha způsoby jako podfrontu.

Zámek relace uchovávaný příjemcem relace je zastřešující pro zámky zpráv používané režimem vyrovnávání *proti prohlížení* . Pouze jeden přijímač může mít zámek na relaci. Přijímač může obsahovat mnoho zpráv v letadle, ale zprávy budou přijaty v daném pořadí. Zrušením zprávy dojde k opětovnému doručení stejné zprávy s další operací Receive.

### <a name="message-session-state"></a>Stav relace zprávy

Když jsou pracovní postupy zpracovávány ve vysoce škálovatelných cloudových systémech s vysokou dostupností, musí být obslužná rutina pracovního postupu přidružená k určité relaci schopna zotavení z neočekávaných selhání a může obnovit částečně dokončenou práci na jiném procesu nebo počítači, ze kterého byla práce zahájena.

Zařízení stavu relace umožňuje v rámci zprostředkovatele zadat anotaci zprávy definované aplikací, aby zaznamenaný stav zpracování relativní k této relaci byl okamžitě dostupný, když je relace získána novým procesorem.

Z Service Bus perspektivy je stav relace zprávy neprůhledný binární objekt, který může uchovávat data velikosti jedné zprávy, což je 256 KB pro Service Bus Standard a 1 MB pro Service Bus Premium. Stav zpracování relativní vůči relaci lze uchovávat v rámci stavu relace nebo stav relace může ukazovat na některé umístění úložiště nebo databázový záznam, který obsahuje tyto informace.

Metody pro správu stavu relace, SetState a GetState, lze nalézt v objektu přijímače relace. Relace, která dříve obsahovala stav relace, vrací odkaz s hodnotou null pro GetState. Dříve nastavený stav relace lze vymazat předáním hodnoty null metodě SetState na přijímači.

Stav relace zůstane tak dlouho, dokud se nevymaže (vrátí **hodnotu null**) i v případě, že jsou všechny zprávy v relaci spotřebovány.

Stav relace uložený ve frontě nebo v předplatném se počítá s kvótou úložiště této entity. Až se aplikace dokončí s relací, doporučuje se, aby aplikace vyčistila svůj zachovaná stav, aby nedocházelo k externím nákladům na správu.

### <a name="impact-of-delivery-count"></a>Dopad počtu doručení

Definice počtu doručení na zprávu v kontextu relací se mírně liší od definice při absenci relací. Zde je souhrn tabulky při zvýšení počtu doručení.

| Scenario | Zvyšuje se počet doručení zprávy |
|----------|---------------------------------------------|
| Relace je přijata, ale zámek relace vyprší (z důvodu vypršení časového limitu). | Yes |
| Relace je přijata, zprávy v relaci nejsou dokončeny (i v případě, že jsou uzamčené) a relace je zavřena. | No |
| Relace je přijata, zprávy jsou dokončeny a relace je explicitně zavřena. | Není k dispozici (Jedná se o standardní tok. Z relace se odeberou tyto zprávy.) |

## <a name="request-response-pattern"></a>Vzor požadavku a odpovědi
[Vzor požadavku a odpovědi](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) je dobře zavedený model integrace, který umožňuje aplikaci odesílatele odeslat žádost a dát příjemci možnost správně odeslat odpověď zpět do aplikace odesílatele. Tento vzor obvykle vyžaduje krátkodobou frontu nebo téma, aby aplikace odesílala odpovědi na. V tomto scénáři představují relace jednoduché alternativní řešení s srovnatelnou sémantikou. 

Více aplikací může odesílat požadavky do jediné fronty požadavků, přičemž konkrétní parametr hlavičky je nastaven pro jedinečnou identifikaci aplikace odesílatele. Aplikace příjemce může zpracovat žádosti přicházející ve frontě a odesílat odpovědi na frontu s povolenými relacemi a nastavit ID relace na jedinečný identifikátor, který odesilatel poslal ve zprávě požadavku. Aplikace, která odeslala požadavek, může následně přijímat zprávy o konkrétním ID relace a správně zpracovat odpovědi.

> [!NOTE]
> Aplikace, která odesílá počáteční požadavky, by měla znát ID relace a použít ji k přijetí relace, aby byla relace, na které je očekávána odpověď, uzamčena. Je vhodné použít identifikátor GUID, který jedinečně identifikuje instanci aplikace jako ID relace. V příjemci relace pro frontu by neměl existovat žádná obslužná rutina relace ani časový limit určený k tomu, aby byly odpovědi k dispozici pro uzamknutí a zpracování konkrétními přijímači.

## <a name="next-steps"></a>Další kroky

- [Ukázky Azure. Messaging. ServiceBus pro .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)
- [Azure Service Bus Klientská knihovna pro Java – ukázky](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus Klientská knihovna pro Python – ukázky](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus klientské knihovny pro JavaScript – ukázky](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus klientské knihovny pro TypeScript – ukázky](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Ukázky Microsoft. Azure. ServiceBus pro .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) (ukázky relací a SessionState)  

Další informace o Service Bus zasílání zpráv najdete v tématu [Service Bus fronty, témata a předplatná](service-bus-queues-topics-subscriptions.md).

[1]: ./media/message-sessions/sessions.png

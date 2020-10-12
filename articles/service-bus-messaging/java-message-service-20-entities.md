---
title: Azure Service Bus zasílání zpráv – entity služby zprávy Java (Preview)
description: Tento článek poskytuje přehled entit zasílání zpráv Azure Service Bus přístupných prostřednictvím rozhraní API služby Java Message Service.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: 1a7fe3d6355146ccf0fce50266a6f3b8da5231b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87801580"
---
# <a name="java-message-service-jms-20-entities-preview"></a>Entity služby zprávy Java (JMS) 2,0 (Preview)

Klientské aplikace, které se připojují k Azure Service Bus Premium a využívají [knihovnu Azure Service Bus JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) , mohou využít níže uvedené entity.

## <a name="queues"></a>Fronty

Fronty v JMS jsou sémanticky srovnatelné s tradičními [Service Bus frontami](service-bus-queues-topics-subscriptions.md#queues).

Chcete-li vytvořit frontu, využijte níže uvedené metody ve `JMSContext` třídě –

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Témata

Témata v JMS jsou sémanticky srovnatelná s tradičními [tématy Service Bus](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions).

Chcete-li vytvořit téma, využijte níže uvedené metody ve `JMSContext` třídě –

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Dočasné fronty

Když klientská aplikace vyžaduje dočasnou entitu, která existuje po dobu života aplikace, může použít dočasné fronty. Ty jsou využívány ve vzoru [požadavek-odpověď](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) .

Chcete-li vytvořit dočasnou frontu, využijte níže uvedené metody ve `JMSContext` třídě –

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Dočasná témata

Stejně jako dočasné fronty existují dočasná témata, která umožňují publikování/přihlášení k odběru prostřednictvím dočasné entity, která existuje po dobu života aplikace.

Chcete-li vytvořit dočasné téma, využijte níže uvedené metody ve `JMSContext` třídě –

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Odběry zpráv JMS (Java Message Service)

I když jsou tyto odběry sémanticky podobné [předplatným](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (tj. existují v tématu a umožňují sémantiku publikování a odběru), uvádí specifikace služby Java Message Services koncepty **sdílených**, **nesdílených**, **trvalých** a **netrvalých** atributů v daném předplatném.

> [!NOTE]
> Níže uvedené odběry jsou dostupné v Azure Service Bus Premium úrovně Preview pro klientské aplikace, které se připojují k Azure Service Bus pomocí [knihovny Azure Service Bus JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).
>
> V rámci verze Public Preview nelze tyto odběry vytvořit pomocí Azure Portal.
>

### <a name="shared-durable-subscriptions"></a>Sdílená trvalá předplatná

Sdílené odolné předplatné se používá v případě, že se všechny zprávy publikované v tématu mají přijmout a zpracovat aplikace bez ohledu na to, jestli aplikace aktivně spotřebovává předplatné z předplatného.

Vzhledem k tomu, že se jedná o sdílené předplatné, můžou z předplatného přijímat všechny aplikace, které jsou ověřené pro příjem z Service Bus.

Chcete-li vytvořit sdílené trvalé předplatné, použijte níže uvedené metody `JMSContext` třídy –

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

Sdílené trvalé předplatné bude i nadále existovat, pokud se neodstraní pomocí `unsubscribe` metody `JMSContext` třídy.

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>Nesdílené trvalé odběry

Stejně jako u sdíleného odolného předplatného se používá nesdílené trvalé předplatné, pokud jsou všechny zprávy publikované v tématu přijaté a zpracovávané aplikací, bez ohledu na to, jestli aplikace aktivně spotřebovává z předplatného.

Vzhledem k tomu, že se jedná o nesdílené předplatné, může z něho přijmout jenom aplikace, která předplatné vytvořila.

K vytvoření nesdíleného odolného předplatného použijte níže uvedené metody z `JMSContext` třídy – 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> Tato `noLocal` funkce je aktuálně Nepodporovaná a ignoruje se.
>

Nesdílené trvalé předplatné nadále existuje, pokud se neodstraní pomocí `unsubscribe` metody `JMSContext` třídy.

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>Sdílená netrvalá předplatná

Sdílené neodolné předplatné se používá v případě, že více klientských aplikací potřebuje přijímat a zpracovávat zprávy z jediného předplatného, a to až do doby, než budou aktivně spotřebovávat nebo přijímat.

Vzhledem k tomu, že odběr není trvalý, není trvalý. Toto předplatné nepřijímá zprávy, pokud k ní neexistují žádní aktivní spotřebitelé.

Chcete-li vytvořit sdílené netrvalé předplatné, vytvořte, `JmsConsumer` jak je znázorněno v níže uvedených metodách `JMSContext` třídy –

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

Sdílené netrvalé předplatné bude i nadále existovat, dokud neobdrží aktivní příjemci.

### <a name="unshared-non-durable-subscriptions"></a>Nesdílené odběry, které nejsou trvalé

Nesdílené netrvalé předplatné se používá, když klientská aplikace potřebuje přijmout a zpracovat zprávu z předplatného, jenom až do chvíle, kdy se z něho bude aktivně spotřebovávat. V tomto předplatném může existovat jenom jeden příjemce, tj. na klienta, který předplatné vytvořil.

Vzhledem k tomu, že odběr není trvalý, není trvalý. Pokud není k dispozici žádný aktivní spotřebitel, zprávy toto předplatné neobdrží.

Chcete-li vytvořit nesdílené netrvalé předplatné, vytvořte, `JMSConsumer` jak je znázorněno v níže uvedených metodách `JMSContext` třídy –

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> Tato `noLocal` funkce je aktuálně Nepodporovaná a ignoruje se.
>

Nesdílené předplatné bez trvalého sdílení dál existuje, dokud nezíská aktivní příjemce.

### <a name="message-selectors"></a>Selektory zpráv

Stejně jako **filtry a akce** existují pro pravidelná Service Bus předplatná, existují **Selektory zpráv** pro odběry JMS.

Selektory zpráv je možné nastavit u každého předplatného JMS a existují jako podmínka filtru ve vlastnostech záhlaví zprávy. Doručovat se jenom zprávy s vlastnostmi záhlaví, které odpovídají výrazu selektoru zpráv. Hodnota null nebo prázdný řetězec značí, že neexistuje selektor zpráv pro předplatné JMS nebo spotřebitele.

## <a name="next-steps"></a>Další kroky

Další informace a příklady použití zasílání zpráv Service Bus najdete v následujících tématech pokročilých:

* [Přehled Service Busho zasílání zpráv](service-bus-messaging-overview.md)
* [Použití rozhraní Java Message Service 2,0 API s Azure Service Bus Premium (Preview)](how-to-use-java-message-service-20.md)




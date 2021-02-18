---
title: Azure Service Bus zasílání zpráv – entity služby zpráv v jazyce Java
description: Tento článek poskytuje přehled entit zasílání zpráv Azure Service Bus přístupných prostřednictvím rozhraní API služby Java Message Service.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: ee4e0124dced16b86d5292c647e129aa87645f22
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652577"
---
# <a name="java-message-service-jms-20-entities"></a>Entity služby zprávy Java (JMS) 2,0

Klientské aplikace připojující se k Azure Service Bus Premium a pomocí [knihovny Azure Service Bus JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) mohou používat níže uvedené entity.

## <a name="queues"></a>Fronty

Fronty v JMS jsou sémanticky srovnatelné s tradičními [Service Bus frontami](service-bus-queues-topics-subscriptions.md#queues).

Chcete-li vytvořit frontu, použijte níže uvedené metody `JMSContext` třídy –

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Témata

Témata v JMS jsou sémanticky srovnatelná s tradičními [tématy Service Bus](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions).

Chcete-li vytvořit téma, použijte níže uvedené metody ve `JMSContext` třídě –

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Dočasné fronty

Pokud klientská aplikace vyžaduje dočasnou entitu, která existuje po dobu života aplikace, může použít dočasné fronty. Tyto entity se používají ve vzoru [požadavek-odpověď](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) .

Chcete-li vytvořit dočasnou frontu, použijte níže uvedené metody `JMSContext` třídy –

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Dočasná témata

Stejně jako dočasné fronty existují dočasná témata, která umožňují publikování/přihlášení k odběru prostřednictvím dočasné entity, která existuje po dobu života aplikace.

Chcete-li vytvořit dočasné téma, použijte níže uvedené metody ve `JMSContext` třídě –

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Odběry zpráv JMS (Java Message Service)

I když jsou tyto odběry sémanticky podobné [předplatným](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (tj. existují v tématu a umožňují sémantiku publikování a odběru), představuje specifikace služby zprávy Java koncepty **sdílených**, **nesdílených**, * * odolných a **netrvalých** atributů daného předplatného.

> [!NOTE]
> Níže uvedené odběry jsou dostupné v Azure Service Bus úrovně Premium pro klientské aplikace, které se připojují k Azure Service Bus pomocí [knihovny Azure Service Bus JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).
>
> Pomocí Azure Portal lze vytvořit pouze trvalé odběry.
>

### <a name="shared-durable-subscriptions"></a>Sdílená trvalá předplatná

Sdílené odolné předplatné se používá v případě, že se všechny zprávy publikované v tématu mají přijmout a zpracovat aplikace bez ohledu na to, jestli aplikace aktivně spotřebovává předplatné z předplatného.

Všechny aplikace, které jsou ověřené pro příjem z Service Bus, můžou přijímat ze sdíleného trvalého předplatného.

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

Podobně jako u sdíleného odolného předplatného se používá nesdílené trvalé předplatné, pokud jsou všechny zprávy publikované v tématu přijaté a zpracovávané aplikací, bez ohledu na to, jestli aplikace aktivně spotřebovává z předplatného.

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

Nesdílené netrvalé předplatné se používá, když klientská aplikace potřebuje přijmout a zpracovat zprávu z předplatného, jenom až do chvíle, kdy se z něho bude aktivně spotřebovávat. V tomto předplatném může existovat jenom jeden příjemce, to znamená klient, který předplatné vytvořil.

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

## <a name="additional-concepts-for-java-message-service-jms-20-subscriptions"></a>Další koncepty pro předplatná JMS (Java Message Service) 2,0

### <a name="client-scoping"></a>Obor klienta

Předplatná, jak je uvedeno ve službě Java Message Service (JMS) 2,0 API, může nebo nemusí být *vymezená na konkrétní klientské aplikace/s* (s odpovídajícím způsobem `clientId` ).

Když je předplatné vymezené, dá se **k němu dostat jenom** z klientských aplikací, které mají stejné ID klienta. 

Jakékoli pokusy o přístup k předplatnému s rozsahem konkrétního ID klienta (například clientId1) z aplikace, která má jiné ID klienta (říká clientId2), povede k vytvoření dalšího oboru předplatného pro druhé ID klienta (clientId2).

> [!NOTE]
> ID klienta může mít hodnotu null nebo být prázdné, ale musí odpovídat ID klienta nastavenému v klientské aplikaci JMS. Z Azure Service Bus perspektivy mají ID klienta null a prázdné ID klienta stejné chování.
>
> Pokud je ID klienta nastaveno na hodnotu null nebo je prázdné, je přístupné pouze klientským aplikacím, jejichž ID klienta je také nastaveno na hodnotu null nebo prázdné.
>

### <a name="shareability"></a>Potřebující

**Sdílené** odběry umožňují, aby od nich mohli přijímat zprávy více klientů/příjemců (tj. JMSConsumer objekty).

>[!NOTE]
> Ke sdíleným předplatným vymezeným na konkrétní ID klienta může mít stále přistup více klientů nebo uživatelů (tj. JMSConsumer objekty), ale každá z klientských aplikací musí mít stejné ID klienta.
>
 

**Nesdílené** odběry umožňují přijímat zprávy pouze jeden klient/příjemce (tj. objekt JMSConsumer). Pokud `JMSConsumer` se vytvoří v nesdíleném předplatném, zatímco už má aktivní `JMSConsumer` naslouchání zpráv, `JMSException` vyvolá se.


### <a name="durability"></a>Stálost

**Trvalé** odběry jsou trvalé a nadále shromažďují zprávy z tématu bez ohledu na to, zda aplikace ( `JMSConsumer` ) zpracovává zprávy z něj.

**Netrvalá** předplatná nejsou trvalá a shromažďují zprávy z tématu, pokud aplikace ( `JMSConsumer` ) spotřebovává zprávy. 

## <a name="representation-of-client-scoped-subscriptions"></a>Reprezentace předplatných s rozsahem klienta

Vzhledem k toho, že odběry v oboru klienta (JMS) musí existovat společně se [stávajícími předplatnými](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions), způsob, jakým jsou předplatná v oboru klienta (JMS) reprezentovaná, postupujte podle následujícího formátu.

   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**D** (pro trvalá předplatná)
   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**ND** (pro netrvalá předplatná)

Tady **$** je oddělovač.

## <a name="next-steps"></a>Další kroky

Další informace a příklady použití zasílání zpráv Service Bus najdete v následujících tématech pokročilých:

* [Přehled Service Busho zasílání zpráv](service-bus-messaging-overview.md)
* [Použití rozhraní Java Message Service 2,0 API s Azure Service Bus Premium](how-to-use-java-message-service-20.md)




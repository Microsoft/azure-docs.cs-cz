---
title: Azure Event Hubs – exchange události pomocí různých protokolů
description: Tento článek ukazuje, jak spotřebitelé a výrobci, kteří používají různé protokoly (AMQP, Apache Kafka a HTTPS) můžete vyměňovat události při používání Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: femila
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2019
ms.author: femila
ms.openlocfilehash: 368cc568c40e878338e6b45205e74cba1d0b6378
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372215"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Výměna událostí mezi spotřebiteli a výrobci, kteří používají různé protokoly: AMQP, Kafka a HTTPS
Azure Event Hubs podporuje tři protokoly pro spotřebitele a výrobce: AMQP, Kafka a HTTPS. Každý z těchto protokolů má svůj vlastní způsob, jak reprezentovat zprávu, takže přirozeně vzniká následující otázka: pokud aplikace odesílá události do centra událostí s jedním protokolem a spotřebovává je s jiným protokolem, co různé části a hodnoty událost vypadat, když dorazí ke spotřebiteli? Tento článek popisuje osvědčené postupy pro výrobce i spotřebitele, aby bylo zajištěno, že hodnoty v rámci události jsou správně interpretovány náročné aplikace.

Rady v tomto článku se konkrétně vztahuje na tyto klienty, s uvedené verze používané při vývoji fragmenty kódu:

* Klient Kafka Java (verze 1.1.1 odhttps://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Klient microsoft azure event hubů pro Jazyk Java (verze 1.1.0 odhttps://github.com/Azure/azure-event-hubs-java)
* Klient center událostí Microsoft Azure pro rozhraní .NET (verze 2.1.0 odhttps://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure Service Bus (verze 5.0.0 odhttps://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (podporuje pouze výrobce)

Ostatní klienti AMQP se mohou chovat mírně odlišně. AMQP má dobře definovaný typ systému, ale specifika serializace typů specifických pro jazyk do a z tohoto typu systému závisí na klientovi, stejně jako jak klient poskytuje přístup k části zprávy AMQP.

## <a name="event-body"></a>Tělo události
Všichni klienti Microsoft AMQP představují tělo události jako neinterpretovaný pytel bajtů. Vyrábějící aplikace předá klientovi posloupnost bajtů a náročná aplikace obdrží stejnou sekvenci od klienta. Interpretace pořadí bajtů probíhá v rámci kódu aplikace.

Při odesílání události prostřednictvím protokolu HTTPS je tělo události obsah POSTed, který je také považován za neinterpretované bajty. Je snadné dosáhnout stejného stavu v kafka výrobce nebo spotřebitele pomocí zadaný ByteArraySerializer a ByteArrayDeserializer, jak je znázorněno v následujícím kódu:

### <a name="kafka-byte-producer"></a>Kafka byte[] výrobce

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka byte[] spotřebitel
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Tento kód vytvoří transparentní bajt potrubí mezi dvěma polovinami aplikace a umožňuje vývojáři aplikace ručně serializovat a rekonstruovat v libovolném požadovaném způsobem, včetně provádění rozhodnutí o rekonstrukci za běhu, například na základě typu nebo informace o odesílateli ve vlastnostech sady uživatelů události.

Aplikace, které mají jeden, pevný typ těla události může být schopen použít jiné serializátory Kafka a deserializery pro transparentní převod dat. Zvažte například aplikaci, která používá JSON. Konstrukce a interpretace řetězce JSON probíhá na úrovni aplikace. Na úrovni Event Hubs je tělo události vždy řetězec, posloupnost bajtů představujících znaky v kódování UTF-8. V tomto případě může výrobce nebo spotřebitel Kafka využít poskytnutéstringserializer nebo StringDeserializer, jak je znázorněno v následujícím kódu:

### <a name="kafka-utf-8-string-producer"></a>Kafka UTF-8 výrobce strun
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Kafka UTF-8 řetězec spotřebitele
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

Na straně AMQP java a .NET poskytují předdefinované způsoby převodu řetězců do a z bajtových sekvencí UTF-8. Klienti Microsoft AMQP představují události jako třídu s názvem EventData. Následující příklady ukazují, jak serializovat řetězec UTF-8 do těla události EventData v producentovi AMQP a jak rekonstruovat tělo události EventData do řetězce UTF-8 v spotřebiteli AMQP.

### <a name="java-amqp-utf-8-string-producer"></a>Java AMQP UTF-8 výrobce řetězců
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Java AMQP UTF-8 řetězec spotřebitele
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>Výrobce řetězce C# .NET UTF-8
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C# .NET UTF-8 řetězec příjemce
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Vzhledem k tomu, že Kafka je open source, vývojář aplikace může zkontrolovat implementaci libovolného serializátoru nebo deserializátoru a implementovat kód, který vytváří nebo spotřebovává kompatibilní sekvenci bajtů na straně AMQP.

## <a name="event-user-properties"></a>Vlastnosti uživatele události

Vlastnosti sady uživatelů lze nastavit a načíst z obou klientů AMQP (v klientech Microsoft AMQP se nazývají vlastnosti) a Kafka (kde se nazývají záhlaví). Odesílatelé protokolu HTTPS mohou nastavit vlastnosti uživatele události tím, že je dodávají jako hlavičky HTTP v operaci POST. Kafka však považuje těla událostí i hodnoty záhlaví události za bajtové sekvence. Vzhledem k tomu, že v klientech AMQP mají hodnoty vlastností typy, které jsou sdělovány kódováním hodnot vlastností podle systému typu AMQP.

HTTPS je zvláštní případ. V okamžiku odeslání jsou všechny hodnoty vlastností textEm UTF-8. Služba Event Hubs provádí omezené množství interpretace převést příslušné hodnoty vlastností na 32bitová a 64bitová podepsaná elipsa s kódem AMQP, čísla s plovoucí desetinnou čárkou a logické hodnoty. Jakákoli hodnota vlastnosti, která se nevejde jeden z těchto typů je považována za řetězec.

Míchání těchto přístupů k zadání vlastností znamená, že příjemce Kafka vidí nezpracovaná bajtová sekvence kódovaná AMQP, včetně informací o typu AMQP. Vzhledem k tomu, že příjemce AMQP vidí netypové bajtové sekvence odeslané výrobcem Kafka, který musí aplikace interpretovat.

Pro kafka spotřebitelů, které přijímají vlastnosti od výrobců AMQP nebo HTTPS, použijte Třídu AmqpDeserializer, která je modelována podle ostatních deserializerů v ekosystému Kafka. Interpretuje informace o typu v bajtových sekvencích kódovaných AMQP, aby rekonstruoval bajty dat do typu Java.

Jako osvědčený postup doporučujeme zahrnout vlastnost ve zprávách odeslaných prostřednictvím AMQP nebo HTTPS. Kafka příjemce můžete použít k určení, zda hodnoty záhlaví potřebují amqp deserializace. Hodnota vlastnosti není důležitá. Potřebuje jen dobře známé jméno, které může spotřebitel Kafka najít v seznamu záhlaví a odpovídajícím způsobem upravit jeho chování.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP do Kafka část 1: vytvořit a odeslat událost v C# (.NET) s vlastnostmi
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP na Kafka část 2: použití AmqpDeserializer rekonstruovat tyto vlastnosti v kafka spotřebitele
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Pokud aplikace zná očekávaný typ vlastnosti, existují metody deserializace, které nevyžadují přetypování později, ale vyvolat chybu, pokud vlastnost není očekávaného typu.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP na Kafka část 3: jiný způsob použití AmqpDeserializer ve spotřebitelské Kafka
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Jít opačným směrem je více zapojeno, protože záhlaví nastavená výrobcem Kafka jsou vždy považovány spotřebitelem AMQP jako raw bajty (zadejte org.apache.qpid.proton.amqp.Binary pro klienta Microsoft Azure Event Hubs pro Javu nebo `System.Byte[]` pro klienty Microsoftu .NET AMQP). Nejjednodušší cesta je použít jeden z Kafka dodaných serializerů generovat bajty pro hodnoty záhlaví na straně výrobce Kafka a potom napsat kompatibilní kód deserializace na straně příjemce AMQP.

Stejně jako u AMQP-to-Kafka, doporučujeme vlastnost zahrnout vlastnost ve zprávách odeslaných prostřednictvím Kafka. Příjemce AMQP můžete použít vlastnost k určení, zda hodnoty záhlaví je třeba rekonstruovat. Hodnota vlastnosti není důležitá. Potřebuje jen známý název, který může spotřebitel AMQP najít v seznamu záhlaví a odpovídajícím způsobem upravit jeho chování. Pokud výrobce Kafka nelze změnit, je také možné pro náročné aplikace zkontrolovat, zda hodnota vlastnosti je binární nebo byte typu a pokus o rekonstrukci na základě typu.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka na AMQP část 1: vytvoření a odeslání události z Kafky s vlastnostmi
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka na AMQP část 2: ručně rekonstruovat tyto vlastnosti v C# (.NET)
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka na AMQP část 3: ručně rekonstruovat tyto vlastnosti v Javě
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli, jak streamovat do centra událostí bez změny klientů protokolu nebo spuštění vlastních clusterů. Další informace o centru událostí a centru událostí pro Kafku najdete v následujících článcích:  

* [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
* [Informace o službě Event Hubs pro ekosystém Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Další ukázky v úložišti Event Hubs pro ekosystém Kafka na GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)
* MirrorMaker slouží k [streamování událostí z Kafky místně do centra událostí v cloudu.](event-hubs-kafka-mirror-maker-tutorial.md) [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)
* Naučte se streamovat do event hubů pomocí [nativních aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md)nebo [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)

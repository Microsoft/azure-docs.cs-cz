---
title: Události Azure Event Hubs-Exchange pomocí různých protokolů
description: V tomto článku se dozvíte, jak můžou zákazníci a výrobci, kteří používají různé protokoly (AMQP, Apache Kafka a HTTPS), vyměňovat události při použití Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: cbc6999e3ede73b948ce034769966922b4b0f282
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89010315"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Výměna událostí mezi spotřebiteli a producenty, kteří používají různé protokoly: AMQP, Kafka a HTTPS
Azure Event Hubs podporuje tři protokoly pro příjemce a výrobce: AMQP, Kafka a HTTPS. Každý z těchto protokolů má svůj vlastní způsob, jak vyjádřit zprávu, a to přirozeně na následující otázku: Pokud aplikace odesílá události do centra událostí s jedním protokolem a spotřebovává je s jiným protokolem, co jednotlivé části a hodnoty události vypadají, jako když dorazí na příjemce? Tento článek popisuje osvědčené postupy pro producenta i příjemce, aby bylo zajištěno, že hodnoty v rámci události jsou správně interpretovány náročnými aplikacemi.

Rady v tomto článku se konkrétně zabývá těmito klienty a uvedenými verzemi použitými při vývoji fragmentů kódu:

* Kafka klient Java (verze 1.1.1 z https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Microsoft Azure Event Hubs klient pro jazyk Java (verze 1.1.0 od https://github.com/Azure/azure-event-hubs-java)
* Klient Event Hubs Microsoft Azure pro .NET (verze 2.1.0 od https://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure Service Bus (verze 5.0.0 od https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (podporuje jenom producenty)

Ostatní klienti AMQP se můžou chovat trochu jinak. AMQP má dobře definovaný systém typů, ale specifika serializace typů typů do a z daného systému typů závisí na klientovi, jak klient poskytuje přístup k částem zprávy AMQP.

## <a name="event-body"></a>Tělo události
Všichni klienti Microsoft AMQP reprezentují tělo události jako neinterpretované penalty bajtů. Produkující aplikace předává klientovi sekvenci bajtů a přijímající aplikace obdrží stejnou sekvenci od klienta. Výklad sekvence bajtů se provádí v kódu aplikace.

Tělo události při odesílání události prostřednictvím protokolu HTTPS je vystaveným obsahem, který je také považován za neinterpretované bajty. Stejný stav můžete snadno dosáhnout v Kafka producentovi nebo spotřebiteli pomocí poskytnutých ByteArraySerializer a ByteArrayDeserializer, jak je znázorněno v následujícím kódu:

### <a name="kafka-byte-producer"></a>Kafka Byte [] – producent

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Spotřebitel Kafka Byte []
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Tento kód vytvoří transparentní kanál bajtů mezi dvěma polovičními částmi aplikace a umožňuje vývojářům aplikací ruční serializaci a deserializaci jakýmkoli způsobem, včetně provádění rozhodnutí o deserializaci v době běhu, například na základě informací o typu nebo odesílateli v případě, že událost obsahuje vlastnosti uživatele.

Aplikace, které mají jediný typ těla události, mohou být schopny použít jiné Kafka serializace a deserializátory k transparentní konverzi dat. Zvažte například aplikaci, která používá JSON. Konstrukce a interpretace řetězce JSON se provádí na úrovni aplikace. Tělo události na úrovni Event Hubs je vždy řetězec, sekvence bajtů představuje znaky v kódování UTF-8. V takovém případě může producent Kafka nebo spotřebitel využít výhod poskytovaných StringSerializer nebo StringDeserializer, jak je znázorněno v následujícím kódu:

### <a name="kafka-utf-8-string-producer"></a>Producent řetězce Kafka UTF-8
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Příjemce řetězce ve formátu UTF-8 Kafka
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

Pro AMQP stranu nabízí jazyk Java i rozhraní .NET integrované způsoby, jak převést řetězce na a ze sekvencí bajtů UTF-8. Klienti Microsoft AMQP reprezentují události jako třídu s názvem EventData. Následující příklady ukazují, jak serializovat řetězec UTF-8 do těla události EventData v producentovi AMQP a jak deserializovat tělo události EventData do řetězce znakové sady UTF-8 v příjemci AMQP.

### <a name="java-amqp-utf-8-string-producer"></a>Výrobce řetězců Java AMQP UTF-8
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Spotřebitel řetězců Java AMQP UTF-8
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>Producent řetězce UTF-8 v C# .NET
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>Příjemce řetězců v jazyce C# .NET UTF-8
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Vzhledem k tomu, že Kafka je open source, může vývojář aplikace zkontrolovat implementaci jakéhokoli serializátoru nebo deserializace a implementovat kód, který vytváří nebo spotřebovává kompatibilní sekvenci bajtů na straně AMQP.

## <a name="event-user-properties"></a>Vlastnosti uživatele události

Vlastnosti uživatelské sady lze nastavit a načíst z obou AMQP klientů (v klientech Microsoft AMQP, které se nazývají vlastnosti) a Kafka (kde se nazývají záhlaví). Odesílatelé HTTPS můžou nastavit vlastnosti uživatele u události tím, že je v operaci POST zadávají jako hlavičky HTTP. Nicméně Kafka považuje tělo události a hodnoty hlaviček událostí jako bajtové sekvence. Vzhledem k tomu, že v klientech AMQP mají hodnoty vlastností typy, které jsou přenášeny pomocí kódování hodnot vlastností podle systému typů AMQP.

Protokol HTTPS je zvláštní případ. V bodě odeslání jsou všechny hodnoty vlastností text v kódování UTF-8. Služba Event Hubs poskytuje omezené množství výkladu pro převod odpovídajících hodnot vlastností na 32 AMQPá a 64 podepsaná celá čísla, která jsou omezená na, čísla s plovoucí desetinnou čárkou (64) a logické hodnoty. Jakákoli hodnota vlastnosti, která se nevejde na jeden z těchto typů, je považována za řetězec.

Kombinování těchto přístupů k psaní vlastností znamená, že příjemce Kafka uvidí nezpracovanou sekvenci bajtů s kódováním AMQP, včetně informací o typu AMQP. Zatímco příjemce AMQP uvidí Netypový bajtovou sekvenci odeslanou výrobcem Kafka, kterou musí aplikace interpretovat.

Pro příjemce Kafka, kteří přijímají vlastnosti od výrobců AMQP nebo HTTPS, použijte třídu AmqpDeserializer, která je modelována po jiných deserializátorech v ekosystému Kafka. Interpretuje informace o typu v bajtových sekvencích kódovaných v AMQP k rekonstrukci datových bajtů do typu Java.

Jako osvědčený postup doporučujeme, abyste do zpráv odesílaných přes AMQP nebo HTTPS zahrnuli vlastnost. Příjemce Kafka ho může použít k určení, jestli hodnoty hlaviček potřebují deserializaci AMQP. Hodnota vlastnosti není důležitá. Potřebuje jenom známý název, který Kafka příjemce nalezne v seznamu hlaviček a odpovídajícím způsobem upraví jeho chování.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP na Kafka část 1: vytvoření a odeslání události v jazyce C# (.NET) s vlastnostmi
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP na Kafka část 2: použití AmqpDeserializer k deserializaci těchto vlastností v příjemci Kafka
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

Pokud aplikace ví očekávaný typ vlastnosti, existují metody deserializace, které nevyžadují přetypování poté, ale vyvolávají chybu, pokud vlastnost není očekávaného typu.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP na Kafka část 3: jiný způsob použití AmqpDeserializeru v spotřebiteli Kafka
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

Druhý směr se zaznamená, protože hlavičky nastavené výrobcem Kafka se vždycky zobrazují jako nezpracované bajty (typ org. Apache. qpid. Proton. AMQP. Binary pro Microsoft Azure Event Hubs klient pro Java nebo `System.Byte[]` pro klienty Microsoft .NET AMQP). Nejjednodušší cesta je použít jeden ze serializátorů dodaných Kafka k vygenerování bajtů pro hodnoty hlaviček na straně producenta Kafka a pak zapsat kompatibilní kód deserializace na straně spotřebitele AMQP.

Stejně jako u AMQP-to-Kafka je osvědčeným postupem, který doporučujeme zahrnout vlastnost do zpráv odesílaných prostřednictvím Kafka. Příjemce AMQP může pomocí vlastnosti určit, zda jsou hodnoty hlaviček nutné deserializovat. Hodnota vlastnosti není důležitá. Potřebuje jenom známý název, který AMQP příjemce nalezne v seznamu hlaviček a odpovídajícím způsobem upraví jeho chování. Pokud producent Kafka nelze změnit, je také možné, že aplikace pro náročné aplikace kontroluje, zda je hodnota vlastnosti binární nebo bajtový typ a pokus o deserializaci na základě typu.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka na AMQP část 1: vytvoření a odeslání události z Kafka s vlastnostmi
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

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka na AMQP část 2: ruční deserializace těchto vlastností v jazyce C# (.NET)
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

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka na AMQP část 3: ruční deserializace těchto vlastností v jazyce Java
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
V tomto článku jste zjistili, jak se streamovat do Event Hubs bez změny klientů protokolu nebo spouštění vlastních clusterů. Další informace o Event Hubs a Event Hubs pro Kafka najdete v následujících článcích:  

* [Informace o službě Event Hubs](./event-hubs-about.md)
* [Informace o službě Event Hubs pro ekosystém Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Další ukázky v úložišti Event Hubs pro ekosystém Kafka na GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)
* Pomocí [nástroje MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) můžete [streamovat události z Kafka do místního prostředí a Event Hubs v cloudu.](event-hubs-kafka-mirror-maker-tutorial.md)
* Naučte se streamovat do Event Hubs pomocí [nativních aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md)nebo [Akka streamů](event-hubs-kafka-akka-streams-tutorial.md) .

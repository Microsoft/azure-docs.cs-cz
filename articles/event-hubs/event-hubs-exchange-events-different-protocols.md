---
title: Výměna událostí mezi aplikacemi, které používají různé protokoly - Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek popisuje, jak uživatelé a výrobců, které používají různé protokoly (AMQP, Apache Kafka a HTTPS) si mohou vyměňovat události při použití služby Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 1345a5814faefd4074e7d9548d374bd79d977514
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015581"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Výměna mezi spotřebiteli a výrobců, které používají různé protokoly událostí: AMQP, Kafka a protokolu HTTPS
Azure Event Hubs podporuje tři protokoly pro zákazníky a producenti: AMQP, Kafka a protokolu HTTPS. Každý z těchto protokolů z nich má vlastní způsob představující zprávu, takže přirozeně vzniká na následující otázku: Pokud aplikace odesílá události do centra událostí s jeden protokol a využívá jiný protokol, co dělat různé části a hodnoty události vypadat podobně jako při jejich doručení na spotřebitele? Tento článek popisuje osvědčené postupy pro producenta a konzumenta Ujistěte se, že jsou hodnoty v rámci události správně interpretovat spotřebitelskou aplikací.

Doporučení v tomto článku se vztahuje konkrétně tito klienti v uvedených verzích použité při vývoji fragmenty kódu:

* Klientskou sadou Kafka Java (verze 1.1.1 z https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Microsoft Azure Event Hubs klienta pro jazyk Java (verze 1.1.0 z https://github.com/Azure/azure-event-hubs-java)
* Microsoft Azure Event Hubs klienta pro platformu .NET (verze 2.1.0 z https://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure Service Bus (verze 5.0.0 z https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (podporuje pouze výrobci)

Jiní klienti AMQP může se chovat trochu jinak. AMQP má systém jasně definovaných typů, ale specifika serializaci typy specifické pro jazyk do a z tohoto systému typu závisí na klientovi, stejně jako jak klient poskytuje přístup k různým částem zprávu protokolu AMQP.

## <a name="event-body"></a>Text na událost
Všichni klienti Microsoft AMQP textu události představují jako neinterpretované kontejner bajtů. Vytváření aplikace předá pořadí bajtů do klienta a tento stejné pořadí spotřebitelskou aplikací obdrží z klienta. Výklad sekvence bajtů proběhne do kódu aplikace.

Při odesílání událostí přes protokol HTTPS, v textu události je účtováno obsah, který je také považováno za neinterpretované bajtů. Je snadné k dosažení stejného stavu v Kafka producer nebo uživatelů s použitím zadané ByteArraySerializer a ByteArrayDeserializer, jak je znázorněno v následujícím kódu:

### <a name="kafka-byte-producer"></a>Výrobce systému Kafka byte]

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka byte [] příjemce
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Tento kód vytvoří kanál transparentní bajtů mezi oběma polovinami aplikace a umožňuje vývojářům aplikací ručně serializaci a deserializaci žádným způsobem žádoucí, včetně rozhodování deserializace za běhu, třeba podle typu nebo informace o odesílateli v uživatele nastavit vlastnosti události.

Aplikace, které mají jediný, typ těla zprávy pevné události může být moct používat jiné serializátory Kafka a deserializers transparentně převést data. Představte si třeba aplikace, které využívá JSON. Konstrukce a interpretace řetězce JSON se odehrává na úrovni aplikace. Na úrovni služby Event Hubs je text událost vždy řetězec sekvence bajtů představuje znaků v kódování UTF-8. V takovém případě Kafka producer nebo příjemce můžete využít zadaná StringSerializer nebo StringDeserializer jak je znázorněno v následujícím kódu:

### <a name="kafka-utf-8-string-producer"></a>Řetězec konzumenta Kafka UTF-8
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Příjemce řetězec Kafka UTF-8
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

Pro stranu AMQP Java a .NET poskytuje integrované možnosti pro převod řetězců na a z pořadí bajtů kódování UTF-8. Klienti Microsoft AMQP představují události jako třída s názvem EventData. Následující příklady ukazují, jak k serializaci řetězec UTF-8 do EventData těle události v AMQP producenta a jak zrušit serializaci těle události EventData na řetězec UTF-8 v příjemci AMQP.

### <a name="java-amqp-utf-8-string-producer"></a>Výrobce řetězce Java AMQP UTF-8
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Příjemce řetězce Java AMQP UTF-8
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C#Výrobce řetězec .NET UTF-8
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C#Příjemce řetězec .NET UTF-8
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Protože Kafka je open source, může vývojář aplikace kontrolovat provádění jakékoli serializátor nebo deserializer a implementovat kód, který vytváří nebo využívá kompatibilní pořadí bajtů na straně AMQP.

## <a name="event-user-properties"></a>Vlastnosti události uživatele

Uživatel nastavit vlastnosti můžete nastavit a načíst z obou klientů protokolu AMQP (klienti Microsoft AMQP nazývají se vlastnosti) a Kafka (ve kterém jsou volány záhlaví). Odesílatelé HTTPS můžete nastavit vlastnosti uživatele na události zadáním jako hlavičky protokolu HTTP v operaci POST. Kafka však zpracovává událost subjekty a hodnoty hlavičky události jako sekvence bajtů. Zatímco v klientech AMQP hodnoty vlastností mít typy, které se předávají pomocí kódování hodnoty vlastností podle typu systému AMQP.

HTTPS je zvláštní případ. Při odesílání, jsou všechny hodnoty vlastností text kódování UTF-8. Služba Event Hubs nemá omezené množství výkladu pro převod hodnoty vlastností odpovídající kódováním AMQP 32bitové a 64bitové celá čísla se znaménkem, 64-bit čísel s plovoucí desetinnou a logické hodnoty. Jakoukoli hodnotu vlastnosti, která není vhodná pro jeden z těchto typů je považován za řetězcový.

Kombinace těchto přístupů k zadání vlastnosti znamená, že konzumenta Kafka uvidí pořadí základním bajtovém AMQP kódování, včetně informací o typu protokolu AMQP. Vzhledem AMQP příjemce vidí sekvence netypové bajtů odeslaných Kafka producer, které musí aplikace interpretovat.

Konzumenti Kafka, které přijímají vlastnosti od výrobců připojení přes AMQP nebo HTTPS použijte třídu AmqpDeserializer, která je Modelováno podle jiných deserializers patří do ekosystému a Kafka. Interpretuje informace o typu v pořadí bajtů kódování AMQP k deserializaci datových bajtů na typ jazyka Java.

Jako osvědčený postup doporučujeme obsahuje vlastnost zprávy odeslané přes AMQP nebo HTTPS. Příjemce Kafka slouží k určení, zda hodnoty hlavičky potřebovat AMQP deserializace. Hodnota vlastnosti není důležité. Potřebuje pouze dobře známým názvem, že příjemce Kafka můžete najít v seznamu hlaviček a odpovídajícím způsobem upravit její chování.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP Kafka část 1: vytvoření a odeslání události C# (.NET) s vlastnostmi
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP Kafka část 2: použití AmqpDeserializer k deserializaci těmto vlastnostem v konzumenta Kafka
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

Pokud aplikace zná, očekávaný typ vlastnosti, metody deserializace, které nevyžadují přetypování později, ale vyvolá chybu, pokud vlastnost není očekávaného typu.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP Kafka část 3: jiný způsob použití AmqpDeserializer v konzumenta Kafka
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

Přechod opačným směrem více jedná, protože záhlaví nastavil Kafka producer jsou vždy vidět spotřebitel AMQP jako nezpracovaný bajtů (zadejte org.apache.qpid.proton.amqp.Binary klienta Microsoft Azure Event Hubs pro Javu, nebo `System.Byte[]` pro společnosti Microsoft .NET AMQP klienty). Nejsnadnější způsob je pomocí jedné z Kafka zadaný serializátory generovat bajtů pro hodnoty hlavičky na straně konzumenta Kafka a teprve pak píšete kód kompatibilní deserializaci na straně příjemce AMQP.

Stejně jako u AMQP k Kafka, je osvědčeným postupem, který doporučujeme obsahuje vlastnost zprávy odeslané přes Kafka. Příjemce AMQP vlastnost slouží k určení, zda hodnoty hlavičky potřebovat deserializace. Hodnota vlastnosti není důležité. Potřebuje pouze dobře známým názvem, že příjemce AMQP můžete najít v seznamu hlaviček a odpovídajícím způsobem upravit její chování. Pokud Kafka producer nelze změnit, je také možné, že spotřebitelskou aplikaci zkontrolujte, zda je hodnota vlastnosti typu binary nebo bajt a pokusit se deserializace podle typu.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Platforma Kafka AMQP část 1: vytvoření a odeslání události z Kafka s vlastnostmi
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

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Platforma Kafka AMQP část 2: ručně deserializovat těmto vlastnostem v C# (.NET)
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

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Platforma Kafka AMQP část 3: ručně deserializovat těmto vlastnostem v jazyce Java
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

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak streamovat do služby Event Hubs s podporou Kafka, aniž byste museli měnit klienty protokolů nebo provozovat vlastní clustery. Další informace o Event Hubs a centra událostí pro systém Kafka, naleznete v následujících článcích:  

* [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
* [Informace o službě Event Hubs pro ekosystém Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Další ukázky v úložišti Event Hubs pro ekosystém Kafka na GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)
* Použití nástroje [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) ke [streamování událostí z místního systému Kafka do služby Event Hubs s podporou Kafka v cloudu.](event-hubs-kafka-mirror-maker-tutorial.md)
* Naučíte se Streamovat do Kafka povolené služby Event Hubs pomocí [nativních aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md), nebo [Akka datové proudy](event-hubs-kafka-akka-streams-tutorial.md)

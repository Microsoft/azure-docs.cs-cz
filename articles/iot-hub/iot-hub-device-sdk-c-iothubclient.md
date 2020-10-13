---
title: Sada SDK pro zařízení Azure IoT pro jazyk C-IoTHubClient | Microsoft Docs
description: Jak používat knihovnu IoTHubClient v sadě SDK pro zařízení Azure IoT pro C k vytváření aplikací pro zařízení, které komunikují se službou IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: 91527b5f2159a336e8339c6a128e8d61965292a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81732605"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Sada SDK pro zařízení Azure IoT pro C – Další informace o IoTHubClient

[Sada SDK pro zařízení Azure IoT pro jazyk c](iot-hub-device-sdk-c-intro.md) je první článek v této sérii, který zavádí **sadu SDK pro zařízení Azure IoT pro jazyk c**. Tento článek vysvětluje, že v sadě SDK jsou dvě architektury architektury. V základu je knihovna **IoTHubClient** , která přímo spravuje komunikaci s IoT Hub. K dispozici je také knihovna **serializátorů** , která sestaví, aby poskytovala služby serializace. V tomto článku poskytneme další podrobnosti o knihovně **IoTHubClient** .

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Předchozí článek popisuje, jak pomocí knihovny **IoTHubClient** odesílat události do IoT Hub a přijímat zprávy. Tento článek rozšiřuje tuto diskuzi o tom, jak přesněji spravovat, *když* odesíláte a přijímáte data, a zavádíte je do **rozhraní API nižší úrovně**. Vyvysvětlíme také, jak připojit vlastnosti k událostem (a načíst je ze zpráv) pomocí funkcí zpracování vlastností v knihovně **IoTHubClient** . Nakonec poskytneme další vysvětlení různých způsobů zpracování zpráv přijatých od IoT Hub.

Článek se uzavírá tak, že pokryje několik různých témat, včetně dalších informací o přihlašovacích údajích k zařízení a o tom, jak změnit chování **IoTHubClient** prostřednictvím možností konfigurace.

Pomocí ukázek sady **IoTHubClient** SDK vyvysvětlíme tato témata. Pokud chcete postup sledovat, přečtěte si téma **iothub \_ Client \_ Sample \_ http** a **iothub \_ \_ Sample \_ AMQP** aplikace, které jsou součástí sady SDK pro zařízení Azure IoT pro jazyk C. všechno popsané v následujících částech je znázorněno v těchto ukázkách.

Můžete najít [**sadu SDK pro zařízení Azure IoT pro**](https://github.com/Azure/azure-iot-sdk-c) úložiště GitHub c a zobrazit podrobnosti o rozhraní API v referenčních informacích k [rozhraní API jazyka c](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-lower-level-apis"></a>Rozhraní API na nižší úrovni

Předchozí článek popisuje základní operaci **IotHubClient** v rámci kontextu aplikace ** \_ \_ \_ AMQP Sample Client iothub** . Například vysvětluje, jak inicializovat knihovnu pomocí tohoto kódu.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Popisuje také, jak odesílat události pomocí tohoto volání funkce.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Článek také popisuje, jak přijímat zprávy pomocí registrace funkce zpětného volání.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Článek také ukazuje, jak uvolnit prostředky pomocí kódu, jako je například následující.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

K dispozici jsou doprovodné funkce pro každé z těchto rozhraní API:

* IoTHubClient \_ vše \_ CreateFromConnectionString
* IoTHubClient \_ vše \_ SendEventAsync
* IoTHubClient \_ vše \_ SetMessageCallback
* IoTHubClientt \_ vše \_ zničit

Všechny tyto **funkce zahrnují vše** v názvu rozhraní API. Další část **s** názvem, parametry každé z těchto funkcí jsou stejné jako jejich protějšky, které neexistují. Chování těchto funkcí se však liší v jednom důležitém způsobu.

Když zavoláte **IoTHubClient \_ CreateFromConnectionString**, podkladové knihovny vytvoří nové vlákno, které běží na pozadí. Toto vlákno odesílá události do a přijímá zprávy z, IoT Hub. Takové vlákno není vytvořeno při práci s rozhraními API **ll** . Vytvoření vlákna na pozadí je pohodlí pro vývojáře. Nemusíte se starat o explicitní posílání událostí a příjem zpráv z IoT Hub – k tomu dochází automaticky na pozadí. Naproti tomu rozhraní **API pro** oba poskytují explicitní kontrolu nad komunikací s IoT Hub, pokud je potřebujete.

Abychom pochopili tento koncept lépe, Podívejme se na příklad:

Když voláte **IoTHubClient \_ SendEventAsync**, co skutečně děláte, je vložení události do vyrovnávací paměti. Vlákno na pozadí vytvořené při volání **IoTHubClient \_ CreateFromConnectionString** průběžně monitoruje tuto vyrovnávací paměť a odesílá všechna data, která obsahuje, pro IoT Hub. K tomu dochází na pozadí v době, kdy hlavní vlákno provádí jinou práci.

Podobně pokud zaregistrujete funkci zpětného volání pro zprávy pomocí **IoTHubClient \_ SetMessageCallback**, dáte pokyn, aby sada SDK měla vlákno na pozadí vyvolat funkci zpětného volání při přijetí zprávy, nezávisle na hlavním vlákně.

Rozhraní **API pro** všechny nevytvářejí vlákno na pozadí. Místo toho je nutné volat nové rozhraní API, aby bylo možné explicitně odesílat a přijímat data z IoT Hub. To je patrné z následujícího příkladu.

** \_ \_ Ukázková aplikace \_ http klienta iothub** , která je součástí sady SDK, ukazuje rozhraní API nižší úrovně. V této ukázce pošleme události, které se IoT Hub s kódem, jako je následující:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

První tři řádky vytvoří zprávu a poslední řádek odešle událost. Jak již bylo zmíněno dříve, odeslání události znamená, že data jsou jednoduše umístěna do vyrovnávací paměti. Při volání **IoTHubClient \_ ll \_ SendEventAsync**není v síti přenášena žádná data. Aby bylo možné data ve skutečnosti IoT Hub, je nutné volat **IoTHubClient \_ ll \_ DoWork**, jako v tomto příkladu:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Tento kód (z **ukázkové aplikace \_ \_ \_ http klienta Iothub** ) opakovaně volá **IoTHubClient \_ ll \_ DoWork**. Pokaždé, když se zavolá **IoTHubClient \_ vše \_ DoWork** , pošle některé události z vyrovnávací paměti, aby se IoT Hub, a načte zprávu ve frontě, která se pošle do zařízení. Druhý případ znamená, že pokud jsme zaregistrovali funkci zpětného volání pro zprávy, zpětné volání je vyvoláno (za předpokladu, že všechny zprávy jsou zařazeny do fronty). Zaznamenali jsme jako funkci zpětného volání následující kód:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Důvodem, proč je ve smyčce často volána **IoTHubClienta \_ \_ DoWork** , je to, že pokaždé, když je zavolána, pošle *některé* události s vyrovnávací pamětí IoT Hub a načte *Další* zprávu zařazenou do fronty pro zařízení. Každé volání není zaručeno odesílat všechny události ve vyrovnávací paměti nebo načíst všechny zprávy ve frontě. Pokud chcete odeslat všechny události do vyrovnávací paměti a potom pokračovat v dalším zpracování, můžete tuto smyčku nahradit následujícím kódem:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Tento kód volá **IoTHubClient \_ šechny \_ DoWork** , dokud všechny události ve vyrovnávací paměti nebyly odeslány do IoT Hub. Všimněte si, že to neznamená také, že byly přijaty všechny zprávy zařazené do fronty. Součástí tohoto důvodu je, že kontrola "všech" zpráv není jako deterministické akce. Co se stane, když načtete "vše" zprávy, ale potom se do zařízení pošle další zpráva hned po? Lepší způsob, jak s tím pracovat, je časový limit v programu. Například funkce zpětného volání zprávy by mohla resetovat časovač pokaždé, když je vyvolán. Pak můžete napsat logiku pro pokračování v zpracování, pokud například během posledních *X* sekund nebyly přijaty žádné zprávy.

Až skončíte s příchozími událostmi a přijímáním zpráv, ujistěte se, že jste volali odpovídající funkci pro vyčištění prostředků.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

V podstatě je k dispozici pouze jedna sada rozhraní API pro posílání a přijímání dat pomocí vlákna na pozadí a další sadu rozhraní API, která má stejnou věc bez vlákna na pozadí. Mnoho vývojářů může preferovat rozhraní API bez jakýchkoli vlastních nastavení, ale rozhraní API nižší úrovně jsou užitečná, když vývojář chce explicitní kontrolu nad síťovými přenosy. Některá zařízení například shromažďují data v průběhu času a pouze události příchozího přenosu dat v zadaných intervalech (například jednou za hodinu nebo jednou za den). Rozhraní API nižší úrovně poskytují možnost explicitního řízení při posílání a přijímání dat z IoT Hub. Jiní uživatelé budou jednoduše preferovat jednoduchost, které poskytuje rozhraní API nižší úrovně. Vše se stane v hlavním vlákně, nikoli na pozadí, které se děje na pozadí.

Podle toho, jaký model si zvolíte, se ujistěte, že používáte rozhraní API, která používáte. Pokud začnete zavoláním **IoTHubClient \_ ll \_ CreateFromConnectionString**, ujistěte se, že pro jakoukoli následnou práci používáte jenom odpovídající rozhraní API nižší úrovně:

* IoTHubClient \_ vše \_ SendEventAsync
* IoTHubClient \_ vše \_ SetMessageCallback
* IoTHubClientt \_ vše \_ zničit
* IoTHubClient \_ vše \_ DoWork

Opak je také pravda. Pokud začnete s **IoTHubClient \_ CreateFromConnectionString**, použijte pro jakékoli další zpracování rozhraní API bez jakýchkoli těchto údajů.

V sadě SDK pro zařízení Azure IoT pro jazyk C najdete kompletní příklad rozhraní API na nižší úrovni v ** \_ ukázkové aplikaci \_ \_ http klienta iothub** . Na ** \_ \_ ukázkovou aplikaci \_ AMQP klienta iothub** se dá odkazovat úplným příkladem rozhraní API, která nejsou typu šechny.

## <a name="property-handling"></a>Zpracování vlastností

Zatím jsme si popsali, že jsme popsali odeslání dat, ale odkazujeme na text zprávy. Podívejte se například na tento kód:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Tento příklad pošle zprávu IoT Hub textem "Hello World". IoT Hub však také umožňuje připojit vlastnosti ke každé zprávě. Vlastnosti jsou páry název-hodnota, které lze připojit ke zprávě. Můžete například upravit předchozí kód a připojit k této zprávě vlastnost:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Začneme voláním ** \_ vlastností IoTHubMessage** a předáním popisovače naší zprávy. To, co se vrátí, je odkaz na **mapový \_ popisovač** , který nám umožňuje začít přidávat vlastnosti. Druhý je dosaženo voláním **mapy \_ AddOrUpdate**, která přebírá odkaz na mapový \_ popisovač, název vlastnosti a hodnotu vlastnosti. Pomocí tohoto rozhraní API můžeme přidat tolik vlastností, kolik bychom chtěli.

Když je událost čtena z **Event Hubs**, může příjemce vytvořit výčet vlastností a načíst jejich odpovídající hodnoty. Například v rozhraní .NET to by bylo provedeno přístupem k [kolekci vlastností objektu EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

V předchozím příkladu připojujeme vlastnosti k události, kterou pošleme do IoT Hub. K zprávám přijatým od IoT Hub lze také připojit vlastnosti. Pokud chceme načíst vlastnosti ze zprávy, můžeme v naší funkci zpětného volání zpráv použít kód, jako je následující:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

Volání ** \_ vlastností IoTHubMessage** vrací odkaz na ** \_ popisovač mapy** . Pak předáte tento odkaz na **mapování \_ getinternals** , aby se získal odkaz na pole párů název/hodnota (a také na počet vlastností). V tomto okamžiku je jednoduché vytvořit výčet vlastností a získat tak hodnoty, které chceme.

Ve své aplikaci nemusíte používat vlastnosti. Pokud ale potřebujete je nastavit na události nebo je načíst ze zpráv, knihovna **IoTHubClient** to usnadňuje.

## <a name="message-handling"></a>Zpracování zpráv

Jak bylo uvedeno dříve, při doručení zpráv z IoT Hub knihovna **IoTHubClient** odpoví vyvoláním funkce zaregistrovaného zpětného volání. K dispozici je návratový parametr této funkce, který zachová nějaké další vysvětlení. Tady je ukázka funkce zpětného volání v ** \_ ukázce klienta iothub ukázkové \_ aplikace \_ http** :

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Všimněte si, že návratový typ **je \_ IOTHUBMESSAGE \_ výsledek dispozice** a v tomto konkrétním případě vrátíme **IOTHUBMESSAGE \_ přijmout**. Z této funkce můžeme vracet další hodnoty, které mění způsob, jakým **IoTHubClient** Library reaguje na zpětné volání zprávy. Tady jsou možnosti.

* **IOTHUBMESSAGE \_ PŘIJATO** – zpráva byla úspěšně zpracována. Knihovna **IoTHubClient** nebude znovu volat funkci zpětného volání se stejnou zprávou.

* **IOTHUBMESSAGE \_ ODMÍTNUTo** – zpráva nebyla zpracována a v budoucnu neexistuje žádná přání k tomu. Knihovna **IoTHubClient** by znovu nevolala funkci zpětného volání se stejnou zprávou.

* **IOTHUBMESSAGE \_ OPUŠTĚNÉ** – zpráva nebyla úspěšně zpracována, ale knihovna **IoTHubClient** by měla funkci zpětného volání vyvolat znovu se stejnou zprávou.

V případě prvních dvou návratových kódů knihovna **IoTHubClient** odesílá IoT Hub zprávu, která indikuje, že se má zpráva odstranit z fronty zařízení a že se znovu nedoručuje. Čistý efekt je stejný (zpráva je odstraněna z fronty zařízení), ale zda byla zpráva přijata nebo odmítnuta, je stále zaznamenána.  Záznam tohoto rozdílu je vhodný pro odesílatele zprávy, která může naslouchat zpětné vazbě a zjistit, jestli zařízení přijalo nebo odmítlo konkrétní zprávu.

V posledním případě se zpráva pošle také IoT Hub, ale indikuje, že by se měla zpráva znovu doručovat. Pokud dojde k nějaké chybě, ale pokud se chcete pokusit zprávu znovu zpracovat, zpráva se vám obvykle nechá zrušit. Naproti tomu je odmítnutí zprávy vhodné, pokud dojde k neodstranitelné chybě (nebo pokud se rozhodnete, že nechcete zprávu zpracovat).

V každém případě si pamatujte na různé návratové kódy, abyste mohli zjistit požadované chování z knihovny **IoTHubClient** .

## <a name="alternate-device-credentials"></a>Alternativní přihlašovací údaje zařízení

Jak bylo vysvětleno dříve, první věc, kterou je třeba provést při práci s knihovnou **IoTHubClient** , je získat ** \_ \_ popisovač klienta IOTHUB** s voláním, jako je následující:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Argumenty pro **IoTHubClient \_ CreateFromConnectionString** jsou připojovací řetězec zařízení a parametr, který označuje protokol, který používáme ke komunikaci s IoT Hub. Připojovací řetězec zařízení má formát, který se zobrazí takto:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

V tomto řetězci jsou čtyři informace: IoT Hub název, IoT Hub přípona, ID zařízení a sdílený přístupový klíč. Plně kvalifikovaný název domény (FQDN) služby IoT Hub získáte při vytváření instance služby IoT Hub v Azure Portal – to vám poskytne název služby IoT Hub (první část plně kvalifikovaného názvu domény) a příponu služby IoT Hub (zbytek plně kvalifikovaného názvu domény). ID zařízení a sdílený přístupový klíč získáte při registraci zařízení v IoT Hub (jak je popsáno v [předchozím článku](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient \_ CreateFromConnectionString** poskytuje jeden způsob, jak knihovnu inicializovat. Pokud chcete, můžete vytvořit nový ** \_ \_ popisovač klienta IOTHUB** pomocí těchto jednotlivých parametrů místo připojovacího řetězce zařízení. Toho dosáhnete pomocí následujícího kódu:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

To má stejný úkol jako **IoTHubClient \_ CreateFromConnectionString**.

Může se zdát, že byste chtěli použít **IoTHubClient \_ CreateFromConnectionString** spíše než tuto podrobnou metodu inicializace. Mějte ale na paměti, že když zaregistrujete zařízení v IoT Hub k čemu dostanete ID zařízení a klíč zařízení (ne připojovací řetězec). Nástroj *Průzkumník zařízení* , který byl představen v [předchozím článku](iot-hub-device-sdk-c-intro.md) , používá knihovny v **sadě Azure IoT Service SDK** k vytvoření připojovacího řetězce zařízení z ID zařízení, klíče zařízení a názvu IoT Hub hostitele. Volání **IoTHubClient pouze \_ pro \_ Vytvoření** může být vhodnější, protože vám ušetří krok generování připojovacího řetězce. Použití jakékoli metody je pohodlné.

## <a name="configuration-options"></a>Možnosti konfigurace

Zatím všechno popsané o způsobu, jakým funguje knihovna **IoTHubClient** , odráží její výchozí chování. Existuje však několik možností, kterými můžete změnit způsob fungování knihovny. K tomu je možné využít rozhraní API IoTHubClient s funkcí ** \_ \_ SetOption** . Podívejte se na tento příklad:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

K dispozici je několik možností, které se běžně používají:

* **SetBatching** (bool) – je-li **nastavena hodnota true**, jsou data odesílaná do IoT Hub odeslána v dávkách. V případě **hodnoty false**se zprávy odesílají jednotlivě. Výchozí hodnota je **false**. Dávkování přes AMQP/AMQP-WS a přidávání systémových vlastností na zprávy D2C je podporováno.

* **Timeout** (unsigned int) – Tato hodnota je vyjádřena v milisekundách. Pokud odeslání požadavku HTTPS nebo přijetí odpovědi trvá déle než tento čas, vyprší časový limit připojení.

Možnost dávkování je důležitá. Ve výchozím nastavení knihovna postupně příchozí události (jedna událost je vše, co předáte do **IoTHubClient \_ šechny \_ SendEventAsync**). Pokud má možnost dávkování **hodnotu true**, knihovna shromáždí tolik událostí, kolik jich může z vyrovnávací paměti (až do maximální velikosti zprávy, kterou IoT Hub akceptuje).  Dávka události se odešle do IoT Hub v jednom volání HTTPS (jednotlivé události se zabalí do pole JSON). Povolení dávkového zpracování obvykle vede ke zvýšení výkonu, protože snižujete síťové zpáteční cykly. Také významně snižuje šířku pásma, protože posíláte jednu sadu hlaviček protokolu HTTPS s dávkou událostí místo sady hlaviček pro každou jednotlivou událost. Pokud nemáte konkrétní důvod k tomu jinak, obvykle budete chtít povolit dávkování.

## <a name="next-steps"></a>Další kroky

Tento článek podrobně popisuje chování knihovny **IoTHubClient** , která se nachází v **sadě SDK pro zařízení Azure IoT pro jazyk C**. S těmito informacemi byste měli mít dobrou představu o schopnostech knihovny **IoTHubClient** . Druhým článkem v této sérii je [sada SDK pro zařízení Azure IoT pro C-serializátor](iot-hub-device-sdk-c-serializer.md), která poskytuje podobné podrobnosti v knihovně **serializátorů** .

Další informace o vývoji pro IoT Hub najdete v tématu sady [SDK služby Azure IoT](iot-hub-devguide-sdks.md).

Další zkoumání možností IoT Hub najdete v tématu [nasazení AI do hraničních zařízení pomocí Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).

---
title: Sada Azure IoT device SDK for C – IoTHubClient | Dokumenty společnosti Microsoft
description: Jak používat knihovnu IoTHubClient v azure iot zařízení SDK pro C k vytvoření aplikací zařízení, které komunikují s centrem IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: 91527b5f2159a336e8339c6a128e8d61965292a6
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732605"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Azure IoT zařízení SDK pro C – další informace o IoTHubClient

[Sada Azure IoT device SDK for C](iot-hub-device-sdk-c-intro.md) je první článek v této řadě, který představuje **sadku SDK zařízení Azure IoT pro C**. Tento článek vysvětlil, že existují dvě architektonické vrstvy v SDK. Základem je knihovna **IoTHubClient,** která přímo spravuje komunikaci s IoT Hub. K dispozici je také **serializační** knihovna, která staví na vrcholu, že poskytovat serializační služby. V tomto článku poskytneme další podrobnosti o knihovně **IoTHubClient.**

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Předchozí článek popisuje, jak používat knihovnu **IoTHubClient** k odesílání událostí do služby IoT Hub a přijímat zprávy. Tento článek rozšiřuje tuto diskusi tím, že vysvětluje, jak přesněji spravovat *při* odesílání a přijímání dat, zavedení **mj.** Také vysvětlíme, jak připojit vlastnosti k událostem (a načíst je ze zpráv) pomocí funkcí zpracování vlastností v knihovně **IoTHubClient.** Nakonec poskytneme další vysvětlení různých způsobů zpracování zpráv přijatých ze správy ioT Hubu.

Článek končí tím, že pokrývá několik různých témat, včetně dalších o pověření zařízení a jak změnit chování **IoTHubClient** prostřednictvím možností konfigurace.

K vysvětlení těchto témat použijeme ukázky sady **IoTHubClient** SDK. Pokud chcete sledovat spolu, najdete **ukázku\_\_ukázky klienta\_iothub http** a **iothub\_klienta\_amqp\_** aplikace, které jsou součástí sady Azure IoT zařízení SDK pro C. Vše popsané v následujících částech je znázorněno v těchto ukázkách.

Můžete najít [**Azure IoT zařízení SDK pro C**](https://github.com/Azure/azure-iot-sdk-c) úložiště GitHub a zobrazit podrobnosti o rozhraní API v [odkazu rozhraní API C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-lower-level-apis"></a>Nižší úroveň API

Předchozí článek popisuje základní operace **IotHubClient** v kontextu **ukázkové\_aplikace\_\_amqp klienta iothub.** Například vysvětlil, jak inicializovat knihovnu pomocí tohoto kódu.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Také popsal, jak odesílat události pomocí tohoto volání funkce.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Článek také popisuje, jak přijímat zprávy registrací funkce zpětného volání.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Článek také ukázal, jak uvolnit prostředky pomocí kódu, jako je například následující.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Pro každé z těchto api existují doprovodné funkce:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_\_LL zničit

Všechny tyto funkce zahrnují **ll** v názvu rozhraní API. Ostatní **LL** část názvu, parametry každé z těchto funkcí jsou shodné s jejich protějšky non-LL. Chování těchto funkcí se však liší jedním důležitým způsobem.

Při volání **IoTHubClient\_CreateFromConnectionString**, základní knihovny vytvořit nové vlákno, které běží na pozadí. Toto vlákno odesílá události a přijímá zprávy z ioT hubu. Při práci s **rozhraními** LL API není vytvořeno žádné takové vlákno. Vytvoření podprocesu na pozadí je pohodlí pro vývojáře. Nemusíte se starat o explicitní odesílání událostí a přijímání zpráv z ioT hubu – to se stane automaticky na pozadí. Naproti tomu LL **API** vám explicitní kontrolu nad komunikaci s IoT Hub, pokud ji potřebujete.

Chcete-li tento koncept lépe pochopit, podívejme se na příklad:

Při volání **IoTHubClient\_SendEventAsync**, co vlastně děláte, je uvedení události do vyrovnávací paměti. Podproces na pozadí vytvořené při volání **IoTHubClient\_CreateFromConnectionString** neustále monitoruje tuto vyrovnávací paměť a odesílá všechna data, která obsahuje do služby IoT Hub. K tomu dochází na pozadí ve stejnou dobu, kdy hlavní vlákno provádí jinou práci.

Podobně při registraci funkce zpětného volání pro zprávy pomocí **IoTHubClient\_SetMessageCallback**, jste pokyn SDK mít vlákno na pozadí vyvolat funkci zpětného volání při přijetí zprávy, nezávisle na hlavním vlákně.

LL **LL** rozhraní API nevytvářejí vlákno na pozadí. Místo toho musí být volána nové rozhraní API explicitně odesílat a přijímat data z centra IoT Hub. To je patrné z následujícího příkladu.

**Ukázková\_aplikace\_http\_klienta iothub,** která je součástí sady SDK, demonstruje nižší úroveň api. V této ukázce odesíláme události do ioT hubu s kódem, jako je následující:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

První tři řádky vytvoří zprávu a poslední řádek odešle událost. Nicméně, jak již bylo zmíněno dříve, odesílání události znamená, že data jsou jednoduše umístěna do vyrovnávací paměti. Nic se přenáší v síti, když voláme **IoTHubClient\_LL\_SendEventAsync**. Chcete-li skutečně příchozí data do služby IoT Hub, musíte volat **IoTHubClient\_\_LL DoWork**, jako v tomto příkladu:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Tento kód (z **ukázkové\_\_\_aplikace http klienta iothub)** opakovaně volá **IoTHubClient\_\_LL DoWork**. Pokaždé, když je volána **ioTHubClient\_LL\_DoWork,** odešle některé události z vyrovnávací paměti do služby IoT Hub a načte zprávu ve frontě odesílanou do zařízení. Druhý případ znamená, že pokud jsme zaregistrovali funkci zpětného volání pro zprávy, pak je vyvoláno zpětné volání (za předpokladu, že všechny zprávy jsou zařazeny do fronty). Zaregistrovali bychom takovou funkci zpětného volání s kódem, jako je následující:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Důvod, proč **IoTHubClient\_LL\_DoWork** je často volána ve smyčce je, že pokaždé, když je volána, odešle *některé* události ve vyrovnávací paměti do služby IoT Hub a načte *další* zprávu zařazenou do fronty pro zařízení. Každé volání není zaručeno, že odeslat všechny události ve vyrovnávací paměti nebo načíst všechny zprávy ve frontě. Pokud chcete odeslat všechny události ve vyrovnávací paměti a pak pokračovat v dalším zpracování, můžete tuto smyčku nahradit kódem, například následujícím:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Tento kód volá **IoTHubClient\_LL\_DoWork,** dokud všechny události ve vyrovnávací paměti byly odeslány do služby IoT Hub. Všimněte si, že to také neznamená, že byly přijaty všechny zprávy ve frontě. Jedním z důvodů je, že kontrola "všechny" zprávy není jako deterministický akce. Co se stane, když načtete "všechny" zprávy, ale další je odeslána do zařízení ihned po? Lepší způsob, jak se s tím vypořádat, je s naprogramovaný časový modus out. Funkce zpětného volání zprávy může například obnovit časovač při každém vyvolání. Potom můžete napsat logiku pokračovat ve zpracování, pokud například nebyly přijaty žádné zprávy v posledních *X* sekund.

Po dokončení příchozích přenosů dat událostí a přijímání zpráv nezapomeňte volat odpovídající funkci k vyčištění prostředků.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

V podstatě existuje pouze jedna sada rozhraní API pro odesílání a přijímání dat s podprocesem na pozadí a další sadu rozhraní API, která dělá totéž bez vlákna na pozadí. Mnoho vývojářů může upřednostňovat rozhraní API bez LL, ale rozhraní API nižší úrovně jsou užitečná, když vývojář chce explicitní kontrolu nad síťovými přenosy. Například některá zařízení shromažďují data v průběhu času a pouze příchozí přenos událostí v určených intervalech (například jednou za hodinu nebo jednou denně). Nižší úroveň api vám možnost explicitně řídit při odesílání a přijímání dat z ioT hubu. Jiní budou jednoduše upřednostňovat jednoduchost, kterou poskytují nižší úroveň API. Vše se děje na hlavním vlákně, spíše než nějakou práci, která se děje na pozadí.

Bez ohledu na model, který zvolíte, ujistěte se, že konzistentní, ve kterých api, které používáte. Pokud začnete voláním **IoTHubClient\_LL\_CreateFromConnectionString**, ujistěte se, že používáte pouze odpovídající nižší úrovně API pro všechny následné práce:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_\_LL zničit
* IoTHubClient\_LL\_DoWork

Opak je také pravdou. Pokud začnete s **IoTHubClient\_CreateFromConnectionString**, použijte non-LL API pro jakékoli další zpracování.

V Azure IoT zařízení SDK pro C, najdete v tématu **iothub\_\_klienta ukázkové\_http** aplikace pro úplný příklad rozhraní API nižší úrovně. **Ukázkové\_aplikace\_iothub klienta\_amqp** lze odkazovat na úplný příklad non-LL API.

## <a name="property-handling"></a>Nakládání s nemovitostmi

Zatím, když jsme popsali odesílání dat, jsme se odvolává na tělo zprávy. Podívejte se například na tento kód:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Tento příklad odešle zprávu do služby IoT Hub s textem "Hello World". Služba IoT Hub však také umožňuje připojení vlastností ke každé zprávě. Vlastnosti jsou dvojice název/hodnota, které lze připojit ke zprávě. Například můžeme upravit předchozí kód připojit vlastnost ke zprávě:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Začneme voláním **Vlastnosti IoTHubMessage\_** a předáním popisovač naší zprávy. Co získáme zpět, je odkaz **NA POPISOVAČ\_MAPY,** který nám umožňuje začít přidávat vlastnosti. Ten je dosaženo voláním **Map\_AddOrUpdate**, který\_přebírá odkaz na POPISOVAČ MAPY, název vlastnosti a hodnotu vlastnosti. S tímto rozhraním API můžeme přidat tolik vlastností, kolik chceme.

Při čtení události z **centra událostí**, příjemce můžete výčet vlastností a načíst jejich odpovídající hodnoty. Například v rozhraní .NET by to bylo provedeno přístupem k [kolekci Vlastnosti na objektu EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

V předchozím příkladu připojujeme vlastnosti k události, kterou pošleme do služby IoT Hub. Vlastnosti lze také připojit ke zprávám přijatým ze služby IoT Hub. Pokud chceme načíst vlastnosti ze zprávy, můžeme použít kód, jako je například následující v naší funkci zpětného volání zprávy:

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

Volání **Vlastnosti IoTHubMessage\_** vrátí odkaz **POPISOVAČ MAPY.\_** Potom předáme tento odkaz na **Map\_GetInternals** získat odkaz na pole dvojice název/hodnota (stejně jako počet vlastností). V tomto okamžiku je to jednoduchá záležitost výčet vlastnosti získat hodnoty, které chceme.

Není třeba používat vlastnosti v aplikaci. Pokud je však potřebujete nastavit na události nebo je načíst ze zpráv, knihovna **IoTHubClient** usnadňuje.

## <a name="message-handling"></a>Zpracování zpráv

Jak již bylo uvedeno dříve, když zprávy dorazí z ioT hub **knihovny IoTHubClient** reaguje vyvoláním registrované funkce zpětného volání. Je návratový parametr této funkce, která si zaslouží nějaké další vysvětlení. Zde je výňatek z funkce zpětného volání v **iothub\_\_ukázce\_http** ukázkové aplikace:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Všimněte si, že návratový typ je **IOTHUBMESSAGE\_DISPozice\_RESULT** a v tomto konkrétním případě vrátíme **IOTHUBMESSAGE\_PŘIJAT**. Existují další hodnoty, které můžeme vrátit z této funkce, které mění způsob, jakým **knihovna IoTHubClient** reaguje na zpětné volání zprávy. Zde jsou možnosti.

* **IOTHUBMESSAGE\_PŘIJATO** – zpráva byla úspěšně zpracována. Knihovna **IoTHubClient** nebude znovu vyvolat funkci zpětného volání se stejnou zprávou.

* **IOTHUBMESSAGE\_ODMÍTNUTo** – zpráva nebyla zpracována a není touha tak učinit v budoucnu. Knihovna **IoTHubClient** by neměla znovu vyvolat funkci zpětného volání se stejnou zprávou.

* **IOTHUBMESSAGE\_ABANDONED** – zpráva nebyla úspěšně zpracována, ale **knihovna IoTHubClient** by měla znovu vyvolat funkci zpětného volání se stejnou zprávou.

Pro první dva návratové kódy knihovny **IoTHubClient** odešle zprávu do služby IoT Hub označující, že zpráva by měla být odstraněna z fronty zařízení a není doručena znovu. Čistý efekt je stejný (zpráva je odstraněna z fronty zařízení), ale zda byla zpráva přijata nebo odmítnuta, je stále zaznamenána.  Zaznamenání tohoto rozlišení je užitečné pro odesílatele zprávy, kteří mohou naslouchat zpětné vazbě a zjistit, zda zařízení přijalo nebo odmítlo určitou zprávu.

V posledním případě je zpráva také odeslána do služby IoT Hub, ale označuje, že zpráva by měla být znovu doručena. Obvykle zprávu opustíte, pokud narazíte na nějakou chybu, ale chcete se pokusit zprávu zpracovat znovu. Naopak odmítnutí zprávy je vhodné, když narazíte na neopravitelnou chybu (nebo pokud se jednoduše rozhodnete, že nechcete zprávu zpracovat).

V každém případě mějte na paměti různé návratové kódy, abyste mohli vyvolat požadované chování z knihovny **IoTHubClient.**

## <a name="alternate-device-credentials"></a>Alternativní pověření zařízení

Jak již bylo vysvětleno dříve, první věc, kterou je třeba udělat při práci s knihovnou **IoTHubClient** je získat **popisovač klienta\_\_IOTHUB** s voláním, jako je například následující:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Argumenty **Pro IoTHubClient\_CreateFromConnectionString** jsou připojovací řetězec zařízení a parametr, který označuje protokol, který používáme ke komunikaci s ioT Hub. Připojovací řetězec zařízení má formát, který se zobrazí takto:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

V tomto řetězci jsou čtyři informace: název služby IoT Hub, přípona služby IoT Hub, ID zařízení a sdílený přístupový klíč. Plně kvalifikovaný název domény (Plně kvalifikovaný název) centra IoT hubu získáte při vytváření instance služby IoT hub na webu Azure Portal – to vám poskytne název centra IoT (první část plně kvalifikovaného názvu domény) a příponu centra IoT (zbytek plně kvalifikovaného názvu domény). ID zařízení a sdílený přístupový klíč získáte při registraci zařízení pomocí ioT hubu (jak je popsáno v [předchozím článku).](iot-hub-device-sdk-c-intro.md)

**IoTHubClient\_CreateFromConnectionString** poskytuje jeden způsob, jak inicializovat knihovnu. Pokud chcete, můžete vytvořit nový **popisovač\_\_klienta IOTHUB** pomocí těchto jednotlivých parametrů, nikoli připojovacířetězec zařízení. Toho je dosaženo pomocí následujícího kódu:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

To provádí stejnou věc jako **IoTHubClient\_CreateFromConnectionString**.

Může se zdát zřejmé, že byste chtěli použít **IoTHubClient\_CreateFromConnectionString** spíše než tento podrobnější metodu inicializace. Mějte však na paměti, že když zaregistrujete zařízení v centru IoT Hub, získáte ID zařízení a klíč zařízení (ne připojovací řetězec). Nástroj *SDK průzkumníka zařízení* představený v předchozím [článku](iot-hub-device-sdk-c-intro.md) používá knihovny ve službě **Azure IoT SDK** k vytvoření připojovacího řetězce zařízení z ID zařízení, klíče zařízení a názvu hostitele služby IoT Hub. Takže volání **\_IoTHubClient\_LL Create** může být vhodnější, protože vám ušetří krok generování připojovacího řetězce. Použijte podle toho, která metoda je vhodná.

## <a name="configuration-options"></a>Možnosti konfigurace

Zatím vše popsané o způsobu, jakým funguje knihovna **IoTHubClient,** odráží jeho výchozí chování. Existuje však několik možností, které můžete nastavit pro změnu fungování knihovny. Toho lze dosáhnout využitím rozhraní **API\_IoTHubClient LL\_SetOption.** Vezměme si tento příklad:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Existuje několik možností, které se běžně používají:

* **SetBatching** (bool) – Pokud **true**, pak data odeslaná do ioT hubu je odeslána v dávkách. Pokud **false**, pak zprávy jsou odesílány jednotlivě. Výchozí hodnota je **false**. Dávkování přes AMQP / AMQP-WS, stejně jako přidání vlastností systému na zprávy D2C, je podporována.

* **Časový čas** (nepodepsaný int) – Tato hodnota je reprezentována v milisekundách. Pokud odeslání požadavku HTTPS nebo přijetí odpovědi trvá déle než tato doba, časový limit připojení vypršel.

Možnost dávkování je důležitá. Ve výchozím nastavení knihovna ingresses události jednotlivě (jedna událost je bez ohledu na to, co předáte **IoTHubClient\_LL\_SendEventAsync).** Pokud je možnost dávkování **true**, knihovna shromažďuje co nejvíce událostí, jak je to možné z vyrovnávací paměti (až do maximální velikost zprávy, které služba IoT Hub přijme).  Dávka události se odesílá do ioT hubu v jednom volání HTTPS (jednotlivé události jsou dodávány do pole JSON). Povolení dávkování obvykle vede k velké zvýšení výkonu, protože snižujete síťové round-trips. Výrazně také snižuje šířku pásma, protože odesíláte jednu sadu hlaviček HTTPS s dávkou událostí, nikoli sadu záhlaví pro každou jednotlivou událost. Pokud nemáte konkrétní důvod k tomu jinak, obvykle budete chtít povolit dávkování.

## <a name="next-steps"></a>Další kroky

Tento článek podrobně popisuje chování knihovny **IoTHubClient** nalezené v **azure iot zařízení SDK pro C**. S těmito informacemi byste měli mít dobré znalosti o možnostech knihovny **IoTHubClient.** Druhý článek v této řadě je [Azure IoT zařízení SDK pro C - Serializer](iot-hub-device-sdk-c-serializer.md), který poskytuje podobné podrobnosti v knihovně **serializátoru.**

Další informace o vývoji pro Službu IoT Hub najdete v tématu [sady Azure IoT SDK .](iot-hub-devguide-sdks.md)

Další informace o možnostech služby IoT Hub najdete v [tématu Nasazení ai na hraniční zařízení s Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).

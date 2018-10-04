---
title: Pro zařízení Azure IoT SDK pro jazyk C - pro IoTHubClient | Dokumentace Microsoftu
description: Jak použít knihovnu pro IoTHubClient v zařízení Azure IoT SDK pro jazyk C k vytvoření aplikace pro zařízení, které komunikují s centrem IoT.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: yizhon
ms.openlocfilehash: 4c4ff981560fc4025c6bf782df71e648a1356c3f
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248138"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Pro zařízení Azure IoT SDK pro jazyk C – Další informace pro IoTHubClient

[Pro zařízení Azure IoT SDK pro jazyk C](iot-hub-device-sdk-c-intro.md) je první článek v tomto představení řady **zařízení Azure IoT SDK pro jazyk C**. Tento článek vysvětlil, že existují dvě vrstvy architektury v sadě SDK. Základní je **pro IoTHubClient** knihovnu, která přímo skladuje komunikaci s centrem IoT. K dispozici je také **serializátor** knihovnu, která takovou sestavení k poskytování služeb serializace. V tomto článku vám poskytneme další podrobnosti o **pro IoTHubClient** knihovny.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Předchozí článek popisuje způsob použití **pro IoTHubClient** knihovny k odesílání událostí do služby IoT Hub a příjem zpráv. Tento článek s vysvětlením, jak spravovat přesněji je rozšířením této diskuse *při* odesílat a přijímat data, Představujeme vám **nižší úrovně rozhraní API**. Také vysvětlíme, jak připojit vlastnosti události (a je načtou ze zprávy) pomocí vlastnosti zpracování funkce **pro IoTHubClient** knihovny. A konečně poskytneme vám další vysvětlení různých způsobů, jak zpracovávat zprávy přijaté ze služby IoT Hub.

Článek, uzavře pokrývající několik různých témat, včetně více o přihlašovací údaje zařízení a jak změnit chování **pro IoTHubClient** prostřednictvím možnosti konfigurace.

Použijeme **pro IoTHubClient** SDK ukázky, které popisují tato témata. Pokud chcete postup sledovat, najdete v článku **iothub\_klienta\_ukázka\_http** a **iothub\_klienta\_ukázka\_amqp**aplikace, které jsou součástí sady SDK pro zařízení Azure IoT pro C. všechno, co je popsáno v následujících částech je ukázáno v těchto ukázek.

Můžete najít [ **zařízení Azure IoT SDK pro jazyk C** ](https://github.com/Azure/azure-iot-sdk-c) Githubu úložišti a zobrazit podrobnosti o rozhraní API ve službě [reference k rozhraní API jazyka C](https://azure.github.io/azure-iot-sdk-c/index.html).

## <a name="the-lower-level-apis"></a>Rozhraní API služby nižší úrovně

Základní operace popsané v předchozím článku **pro IotHubClient** v kontextu **iothub\_klienta\_ukázka\_amqp** aplikace. Například to bylo vysvětleno, jak inicializovat knihovnu pomocí tohoto kódu.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Je také popsáno jak odesílat události pomocí volání funkce.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Článek také popisuje jak přijmout zprávy tak, že zaregistrujete funkce zpětného volání.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Tento článek také ukázala, jak uvolnění prostředků pomocí například následující kód.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

K dispozici je Průvodce vyhledáváním funkcí pro každou z těchto rozhraní API:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* Pro IoTHubClient\_LL\_SetMessageCallback
* Pro IoTHubClient\_LL\_zničit

Tyto funkce všechny **LL** v názvu rozhraní API. Ostatní **LL** část názvu, jsou stejné jako jejich protějšky v jiné všechny parametry každá z těchto funkcí. Chování tyto funkce se však liší v jedním ze způsobů důležité.

Při volání **pro IoTHubClient\_CreateFromConnectionString**, základní knihovny vytvořit nové vlákno, na kterém běží na pozadí. Toto vlákno odesílá události do a přijímá zprávy ze služby IoT Hub. Žádný takový vlákna je vytvořen při práci s **LL** rozhraní API. Vytvoření vlákna na pozadí je pozornost pro vývojáře. Nemusíte se starat o explicitně odesílání událostí a příjem zpráv ze služby IoT Hub – probíhá automaticky na pozadí. Naproti tomu **LL** rozhraní API poskytují explicitní kontrolu nad komunikace se službou IoT Hub, pokud ho potřebujete.

Pro lepší pochopení tento koncept, Pojďme se podívat na příklad:

Při volání **pro IoTHubClient\_SendEventAsync**, co skutečně děláte nastavuje události ve vyrovnávací paměti. Vlákno na pozadí vytvořen při volání **pro IoTHubClient\_CreateFromConnectionString** průběžně monitoruje této vyrovnávací paměti a pošle nějaká data, která obsahuje do služby IoT Hub. To probíhá na pozadí ve stejnou dobu, že je hlavní vlákno provádění jiné práce.

Podobně, když se zaregistrujete funkce zpětného volání pro zprávy pomocí **pro IoTHubClient\_SetMessageCallback**, jste instruující SDK má vlákna na pozadí vyvolat funkci zpětného volání při zprávu přijato, nezávisle na hlavním vlákně.

**LL** rozhraní API pro ně nevytvoříte vlákna na pozadí. Místo toho nové rozhraní API se musí volat explicitně odesílat a přijímat data ze služby IoT Hub. To je ukázáno v následujícím příkladu.

**Iothub\_klienta\_ukázka\_http** ukazuje aplikace, která je součástí sady SDK rozhraní API služby nižší úrovně. V této ukázce jsme odesílání událostí do služby IoT Hub se například následující kód:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

První tři řádky vytvářejí zprávu a poslední řádek odesílá události. Ale jak už bylo zmíněno dříve, posílat že události znamená, že se data jednoduše umístí do vyrovnávací paměti. Nic se přenášet v síti, je-li říkáme **pro IoTHubClient\_LL\_SendEventAsync**. Aby skutečně příchozího přenosu dat do služby IoT Hub, je nutné volat **pro IoTHubClient\_LL\_DoWork**, jako v tomto příkladu:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Tento kód (z **iothub\_klienta\_ukázka\_http** aplikace) opakovaně volá **pro IoTHubClient\_LL\_DoWork**. Pokaždé, když **pro IoTHubClient\_LL\_DoWork** je volána, odesílá některé události z vyrovnávací paměti pro službu IoT Hub a načte zprávy ve frontě odesílaných do zařízení. Druhém případě znamená, že pokud jsme zaregistrovali funkci zpětného volání pro zprávy, pak zpětného volání je vyvolána (za předpokladu, že všechny zprávy jsou zařazeny do fronty). By zaregistrovali funkci zpětného volání s například následující kód:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Z důvodu, který **pro IoTHubClient\_LL\_DoWork** se často nazývá smyčku, která je pokaždé, když je volána, odešle *některé* ukládány do vyrovnávací paměti událostí do služby IoT Hub a načte *Další* zprávy ve frontě pro zařízení. Každé volání není zaručeno, že k odesílání událostí všechny uložené do vyrovnávací paměti nebo pokud chcete načíst všechny zprávy zařazené do fronty. Pokud chcete odeslat všechny události ve vyrovnávací paměti a potom pokračujte na další zpracování, že tato smyčka můžete nahradit následující kód:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Tento kód volá **pro IoTHubClient\_LL\_DoWork** dokud všechny události ve vyrovnávací paměti se odeslaly do služby IoT Hub. Všimněte si, že to také neznamená, že všechny zprávy ve frontě byly přijaty. Důvodem je, že probíhá kontrola zpráv "vše" není jako deterministické akci. Co se stane, pokud načtete "all" zpráv, ale pak je jiný neodesílají do zařízení ihned po? Lepší způsob, jak řešit, který je s naprogramovaných vypršení časového limitu. Funkce zpětného volání zpráv může například obnovit časovač pokaždé, když je vyvolána. Potom můžete psát logiku zpracování pokračovat, je-li například byly přijaty žádné zprávy za posledních *X* sekund.

Když jste hotovi ingressing události a přijímání zpráv, nezapomeňte volat odpovídající funkce pro vyčištění prostředků.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Je v podstatě pouze jednu sadu rozhraní API odesílat a přijímat data z vlákna na pozadí a další sadu rozhraní API, která má stejnou funkci bez vlákna na pozadí. Mnoho vývojářů možná dáte přednost bez API LL-, ale nižší úrovně rozhraní API jsou užitečné, když vývojář chce, aby explicitní kontrolu nad síťových přenosů. Například některá zařízení shromažďovat data o průběhu času a událostí příchozího přenosu dat pouze v určených intervalech (například jednou za hodinu nebo jednou za den). Nižší úrovně rozhraní API nabízejí tyto možnosti explicitně ovládací prvek při odesílání a příjem dat ze služby IoT Hub. Jednoduše ostatní bude dávají přednost jednoduchosti, které poskytují rozhraní API služby nižší úrovně. Všechno, co se stane, na hlavním vlákně, nikoli některé pracovní děje na pozadí.

Vybírá model zvolíte, je nutné být konzistentní vzhledem k aplikacím v API, které použijete. Pokud spustíte voláním **pro IoTHubClient\_LL\_CreateFromConnectionString**, nezapomeňte použít pouze odpovídající nižší úrovně rozhraní API pro zpracování práce:

* IoTHubClient\_LL\_SendEventAsync
* Pro IoTHubClient\_LL\_SetMessageCallback
* Pro IoTHubClient\_LL\_zničit
* Pro IoTHubClient\_LL\_DoWork

Opak platí také. Pokud byste začali s **pro IoTHubClient\_CreateFromConnectionString**, pak používat jiné API LL – pro libovolné další zpracování.

V Azure sada SDK zařízení IoT pro jazyk C, najdete v článku **iothub\_klienta\_ukázka\_http** aplikace Úplný příklad nižší úrovně rozhraní API. **Iothub\_klienta\_ukázka\_amqp** aplikace může být odkazováno Úplný příklad jiné – vše rozhraní API.

## <a name="property-handling"></a>Vlastnost zpracování

Zatím když jsme popsali odesílání dat, můžeme jste byla odkazující na text zprávy. Zvažte například tento kód:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Tento příklad odešle zprávu do služby IoT Hub s textem "Hello World." IoT Hub však umožňuje také vlastnosti, které chcete připojit ke každé zprávě. Vlastnosti jsou páry název/hodnota, které může být připojen ke zprávě. Například nám můžete upravit předchozí kód k připojení ke zprávě vlastnost:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Začneme voláním **IoTHubMessage\_vlastnosti** a předají se jí popisovač naši zprávu. Máme zpět **MAPY\_zpracování** odkaz, který umožňuje nám můžete začít přidávat vlastnosti. Lze provést voláním **mapy\_AddOrUpdate**, která přebírá odkaz na MAPĚ\_POPISOVAČ, název vlastnosti a hodnotu vlastnosti. Pomocí tohoto rozhraní API můžeme přidat libovolný počet vlastností, jak tomu.

Při čtení události ze **Event Hubs**, příjemce, můžete vytvořit výčet vlastností a načíst jejich odpovídající hodnoty. Například v .NET to jejich plnění díky přístupu [kolekci vlastností v objektu EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

V předchozím příkladu jsme se vlastnosti připojení k události, která nám odeslat do služby IoT Hub. Vlastnosti lze také připojit k zprávy přijaté ze služby IoT Hub. Pokud chcete načíst vlastnosti ze zprávy, můžete použít následující kód v naší funkce zpětného volání zpráv:

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

Volání **IoTHubMessage\_vlastnosti** vrátí **MAPY\_zpracování** odkaz. My pak předejte tento odkaz na **mapy\_GetInternals** k získání odkazu na pole dvojice název/hodnota (stejně jako počet vlastností). V tomto okamžiku je jednoduché vytvořit výčet vlastností zobrazíte na hodnoty, které chceme.

Není nutné použít vlastnosti v aplikaci. Nicméně, pokud je potřeba nastavit u události nebo je načtou ze zpráv, **pro IoTHubClient** knihovna usnadňuje.

## <a name="message-handling"></a>Zpracování zpráv

Jak bylo uvedeno dříve, při doručení zpráv ze služby IoT Hub **pro IoTHubClient** knihovny reaguje vyvoláním zaregistrované zpětné volání funkce. Existuje návratový parametr této funkce, který si zaslouží další vysvětlení. Tady je výpisem funkce zpětného volání v **iothub\_klienta\_ukázka\_http** ukázkovou aplikaci:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Všimněte si, že je návratový typ **IOTHUBMESSAGE\_DISPOZICE\_výsledek** a v tomto konkrétním případě vrátíme **IOTHUBMESSAGE\_PŘIJATO**. Existují jiné hodnoty vrátíme z této funkce, které se mění způsob, jakým **pro IoTHubClient** knihovny jsou reaguje na zpětného volání zpráv. Zde jsou možnosti.

* **IOTHUBMESSAGE\_PŘIJATO** – zpráva byla úspěšně zpracována. **Pro IoTHubClient** knihovny nebude volat funkci zpětného volání s stejné zprávy.

* **IOTHUBMESSAGE\_Odmítnuto** – zpráva nebyla zpracována a neexistuje žádná chce udělat v budoucnu. **Pro IoTHubClient** knihovny by neměl volat funkci zpětného volání s stejné zprávy.

* **IOTHUBMESSAGE\_ABANDONED** – zpráva nebyla zpracována úspěšně, ale **pro IoTHubClient** knihovny by měla vyvolat funkci zpětného volání s stejné zprávy.

Pro první dva návratové kódy, **pro IoTHubClient** knihovny odešle zprávu do služby IoT Hub označující, že zpráva z fronty zařízení odstraněn a nedoručilo znovu. Výsledkem je stejný (odstraní zprávu z fronty zařízení), ale Určuje, zda byla zpráva přijímat nebo odmítat je stále zaznamenán.  Záznam toto rozlišení je užitečné pro odesílatele zprávy, kteří můžou Naslouchejte zpětné vazbě a zjistěte, pokud má zařízení přijímat nebo odmítat. konkrétní zprávu.

V posledním případě přijde zprávy do služby IoT Hub, ale znamená, že zprávy by víckrát. Obvykle budete opustit zprávu, pokud dojde k nějaké chybě, ale chcete si vyzkoušet znovu zpracovat zprávu. Naproti tomu odmítnutí zprávy je vhodné při dojde k neodstranitelné chybě (nebo jednoduše rozhodnete, že nechcete zprávu zpracovat).

V každém případě mějte na paměti různých návratových kódů tak, že mohou vyvolat chování, které chcete z **pro IoTHubClient** knihovny.

## <a name="alternate-device-credentials"></a>Zařízení s alternativní přihlašovací údaje

Jak bylo popsáno dříve, je prvním krokem při práci s **pro IoTHubClient** knihovny je použít k získání **IOTHUB\_klienta\_zpracování** pomocí volání, jako je následující:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Argumenty, které mají **pro IoTHubClient\_CreateFromConnectionString** jsou připojovací řetězec zařízení a parametr, který označuje protokol používáme pro komunikaci se službou IoT Hub. Připojovací řetězec zařízení má formát, který se zobrazí takto:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Existují čtyři úryvky informace v tomto řetězci: název služby IoT Hub, IoT Hub příponu, ID zařízení a sdílený přístupový klíč. Při vytváření IoT hub instance na webu Azure Portal získáte ze služby IoT hub plně kvalifikovaný název domény (FQDN) – to umožňuje název centra IoT (první část úplný název domény) a IoT hub přípony (zbytek plně kvalifikovaný název domény). Při registraci zařízení ve službě IoT Hub, zobrazí se ID zařízení a sdílený přístupový klíč (jak je popsáno v [předchozím článku](iot-hub-device-sdk-c-intro.md)).

**Pro IoTHubClient\_CreateFromConnectionString** představuje jeden způsob, jak inicializovat knihovnu. Pokud dáváte přednost, můžete vytvořit nový **IOTHUB\_klienta\_zpracování** pomocí těchto jednotlivých parametrů místo připojovací řetězec zařízení. Toho můžete dosáhnout s následujícím kódem:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

To totéž jako **pro IoTHubClient\_CreateFromConnectionString**.

To se může zdát zřejmé, že chcete použít **pro IoTHubClient\_CreateFromConnectionString** místo tuto podrobnější metodu inicializace. Mějte na paměti, ale, že při registraci zařízení ve službě IoT Hub se zobrazí se ID zařízení a klíč zařízení (ne připojovací řetězec). *Průzkumník zařízení* zavedený nástroj sady SDK [předchozím článku](iot-hub-device-sdk-c-intro.md) používá knihovny v **pro službu Azure IoT SDK** vytvořit připojovací řetězec zařízení z ID zařízení , klíč zařízení a název hostitele služby IoT Hub. Proto volání **pro IoTHubClient\_LL\_vytvořit** může být vhodnější, protože není nutné krok generování připojovací řetězec. Použijte, podle toho, která metoda je vhodné.

## <a name="configuration-options"></a>Možnosti konfigurace

Zatím všechno, co je popsáno o způsobu, jakým **pro IoTHubClient** knihovny funguje odráží její výchozí chování. Existují však několik možností, které můžete nastavit, chcete-li změnit, jak funguje knihovny. To lze provést s využitím **pro IoTHubClient\_LL\_SetOption** rozhraní API. Podívejte se například:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Máte několik možností, které se běžně používají:

* **SetBatching** (bool) – Pokud **true**, odesílání dat do služby IoT Hub je odeslaný v dávkách. Pokud **false**, pak jsou zprávy odesílány jednotlivě. Výchozí hodnota je **false**. Všimněte si, **SetBatching** možnost platí jenom pro protokol HTTPS a nechcete protokol MQTT nebo AMQP.

* **Časový limit** (unsigned int) – Tato hodnota je vyjádřena v milisekundách. Pokud odesílá požadavek HTTPS a příjem odpovědí trvá déle, než tuto chvíli a pak připojení vyprší časový limit.

Možnost dávkování je důležité. Ve výchozím nastavení, událostí ingresses knihovny jednotlivě (jednu událost je, bez ohledu na předané do **pro IoTHubClient\_LL\_SendEventAsync**). Pokud je možnost dávkování **true**, knihovně shromažďuje tolik události jako z vyrovnávací paměti (až do maximální velikost zprávy, který bude přijímat služby IoT Hub).  Batch událost je odeslána do služby IoT Hub v jednom volání HTTPS (jednotlivé události jsou seskupeny do pole JSON). Povolení dávkování obvykle za následek zvýšení výkonu velkých objemů vzhledem k tomu, že jste snížení doby odezvy v síti. Také významně snižuje šířku pásma, protože při odesílání jednu sadu záhlaví HTTPS pomocí služby batch události, nikoli sadu záhlaví jednotlivých událostí. Pokud nemáte konkrétní důvod, proč jinak, obvykle budete chtít povolit dávkové zpracování.

## <a name="next-steps"></a>Další postup

Tento článek podrobně popisuje chování **pro IoTHubClient** součástí knihovny **zařízení Azure IoT SDK pro jazyk C**. Pomocí těchto informací byste měli mít dobrý přehled o možnostech **pro IoTHubClient** knihovny. Je druhý článek v této sérii [zařízení Azure IoT SDK pro jazyk C - serializátor](iot-hub-device-sdk-c-serializer.md), který poskytuje podobné podrobností v **serializátor** knihovny.

Další informace o vývoji pro službu IoT Hub, najdete v článku [sad SDK Azure IoT](iot-hub-devguide-sdks.md).

Chcete-li podrobněji prozkoumat možnosti služby IoT Hub, přečtěte si téma [nasazování AI do hraničních zařízení pomocí služby Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
---
title: Pro zařízení Azure IoT SDK pro jazyk C - IoTHubClient | Microsoft Docs
description: Jak používat knihovnu IoTHubClient v zařízení Azure IoT SDK pro jazyk C vytvoření aplikace pro zařízení, které komunikují pomocí služby IoT hub.
author: yzhong94
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: yizhon
ms.openlocfilehash: 15541c8719e89080ba7685781cedf36fb4335e70
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633090"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Pro zařízení Azure IoT SDK pro jazyk C – informace o IoTHubClient
[Nejprve článek](iot-hub-device-sdk-c-intro.md) této série zavedená **zařízení Azure IoT SDK pro jazyk C**. Tento článek vysvětluje, SDK jsou dvě vrstvy architektury. Na bázi je **IoTHubClient** knihovnu, která přímo spravuje komunikace se službou IoT Hub. K dispozici je také **serializátor** knihovny, který sestaví v horní části daného k poskytování služeb serializace. V tomto článku jsme vám poskytují další podrobnosti na **IoTHubClient** knihovny.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Předchozí článek popisuje postup použití **IoTHubClient** k odesílání událostí do služby IoT Hub a příjem zpráv knihovnu. Tento článek rozšiřuje této diskuzi o vysvětlením, jak spravovat přesněji *při* odesílat a přijímat data, Představujeme vám **nižší úrovně rozhraní API**. Dále vysvětlíme postup připojení vlastnosti k události (a je načtou ze zprávy) pomocí vlastnosti zpracování funkce **IoTHubClient** knihovny. Nakonec poskytujeme další vysvětlení různých způsobů zpracování zprávy přijaté ze služby IoT Hub.

Článek se ukončí pokrývajících několik ostatní témata, včetně více o pověření zařízení a jak změnit chování **IoTHubClient** prostřednictvím možnosti konfigurace.

Použijeme **IoTHubClient** ukázky SDK na tato témata popisují. Pokud chcete sledovat, najdete v článku **iothub\_klienta\_ukázka\_http** a **iothub\_klienta\_ukázka\_amqp**aplikace, které jsou součástí sady SDK zařízení Azure IoT pro C. všechno popsané v následujících částech je znázorněn v tyto ukázky.

Můžete najít [ **zařízení Azure IoT SDK pro jazyk C** ](https://github.com/Azure/azure-iot-sdk-c) Githubu úložiště a zobrazte podrobnosti o rozhraní API v [referenční dokumentace rozhraní API jazyka C](https://azure.github.io/azure-iot-sdk-c/index.html).

## <a name="the-lower-level-apis"></a>Rozhraní API nižší úrovně
Základní operace popsané v předchozím článku **IotHubClient** v kontextu **iothub\_klienta\_ukázka\_amqp** aplikace. Například vysvětleny postupy k chybě při inicializaci knihovny pomocí tohoto kódu.

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Popisuje také postup odesílání událostí pomocí volání této funkce.

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Článek také popisuje jak přijmout zprávy tak, že zaregistrujete funkce zpětného volání.

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

V článku také vám ukázal, jak uvolnit prostředky, jako je například následující kód.

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Jsou ale doprovodné funkce pro každé z těchto rozhraní API:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_UDOU\_SetMessageCallback
* IoTHubClient\_UDOU\_Destroy

Všechny tyto funkce zahrnout "Vše" název rozhraní API. Než tu, která jsou shodné s jejich protějšky v jiné všechny parametry každou z těchto funkcí. Chování těchto funkcí je však jinou důležitá jedním způsobem.

Při volání **IoTHubClient\_CreateFromConnectionString**, základní knihovny vytvořit nové vlákno, které běží na pozadí. Tento přístup z více vláken odesílá události do a ze služby IoT Hub přijímá zprávy. Žádný takový vlákno se vytvoří při práci s "Vše" rozhraní API. Vytvoření vlákně na pozadí je pro vaše pohodlí pro vývojáře. Nemusíte si dělat starosti o explicitně události odesílání a přijímání zpráv ze služby IoT Hub – dojde automaticky na pozadí. Naproti tomu "Vše" rozhraní API umožňují explicitní kontrolu nad komunikace se službou IoT Hub, pokud je to nutné.

To lépe pochopit, podíváme se na příklad:

Při volání **IoTHubClient\_SendEventAsync**, jaké ve skutečnosti úlohy je uvedení události do vyrovnávací paměti. Vlákně na pozadí vytvořen při volání **IoTHubClient\_CreateFromConnectionString** neustále monitoruje této vyrovnávací paměti a odešle všechna data, která obsahuje do služby IoT Hub. K tomu dojde na pozadí ve stejnou dobu, hlavní vlákno provádí jinou práci.

Podobně když se zaregistrujete funkce zpětného volání pro zprávy pomocí **IoTHubClient\_SetMessageCallback**, že instruující SDK tak, aby měl vlákně na pozadí vyvolat funkce zpětného volání, když zprávu přijme, nezávisle na hlavní vlákno.

Rozhraní API "vše" nevytvářejte vlákna na pozadí. Místo toho musí být voláno nové rozhraní API explicitně odesílat a přijímat data ze služby IoT Hub. Tento postup je znázorněn v následujícím příkladu.

**Iothub\_klienta\_ukázka\_http** aplikace, která je součástí sady SDK ukazuje nižší úrovně rozhraní API. V této ukázce jsme odesílat události do služby IoT Hub s kódem například následující:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

První tři řádky vytvořit zprávu a poslední řádek odesílá události. Ale jak je uvedeno nahoře, "odesílání" událost znamená, že se data jednoduše umístí do vyrovnávací paměti. Nic se přenášejí v síti, když říkáme **IoTHubClient\_UDOU\_SendEventAsync**. V pořadí, které se ve skutečnosti příchozí přenos dat do služby IoT Hub, musí volat **IoTHubClient\_UDOU\_DoWork**, protože v tomto příkladu:

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Tento kód (z **iothub\_klienta\_ukázka\_http** aplikace) opakovaného volá **IoTHubClient\_UDOU\_DoWork**. Pokaždé, když **IoTHubClient\_UDOU\_DoWork** je volána, odešle některé události z vyrovnávací paměti do služby IoT Hub a načte zprávu ve frontě se odešle do zařízení. Druhém případě znamená, že pokud jsme registrovaná funkce zpětného volání pro zprávy, pak zpětné volání je vyvolán (za předpokladu, že všechny zprávy jsou zařazeny do fronty). By zaregistrovali funkci zpětného volání s kódem například následující:

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Z důvodu, **IoTHubClient\_UDOU\_DoWork** se často říká smyčku je, že pokaždé, když je volána, odešle *některé* uložená do vyrovnávací paměti události IoT Hub a načte *Další* zprávy do fronty pro zařízení. Každé volání není zaručena odesílat všechny uložené ve vyrovnávací paměti události nebo k načtení všech zpráv zařazených do fronty. Pokud chcete odeslat všechny události ve vyrovnávací paměti a pokračujte na další zpracování můžete tento smyčky nahradit kód například následující:

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Tento kód zavolá **IoTHubClient\_UDOU\_DoWork** dokud všechny události ve vyrovnávací paměti byly odeslány do služby IoT Hub. Všimněte si, že to také neznamená, že všechny zprávy ve frontě byly přijaty. Součást důvodem je, že kontrola "vše" zpráv není deterministický jako akce. Co se stane, když je načíst "všechny" zprávy, ale jinou se pak odešlou do zařízení hned po? Lepší způsob, jak řešit, který je s naprogramovaných vypršení časového limitu. Funkce zpětného volání zpráva může například obnovit časovač pokaždé, když je volána. Poté můžete napsat logiku pokračovat zpracování, pokud například nebyly přijaty žádné zprávy za posledních *X* sekund.

Pokud jste dokončení ingressing události a přijímání zpráv, nezapomeňte volat funkci odpovídající vyčištění prostředků.

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Je v podstatě jen jednu sadu rozhraní API pro odesílat a přijímat data z vlákna na pozadí a další sadu rozhraní API, která má stejnou funkci bez vlákně na pozadí. Mnoho vývojáři můžou upřednostňovat bez API UDOU-, ale nižší úrovně rozhraní API je užitečný v případě vývojář chce explicitní kontrolu nad síťových přenosů. Například některá zařízení shromažďování dat přes čas a události příchozího pouze v určitých intervalech (například jednou za hodinu nebo jednou za den). Nižší úrovně rozhraní API nabízejí možnost explicitně řídit při odesílání a příjem dat ze služby IoT Hub. Ostatní jednoduše bude upřednostňovat jednoduchost, které poskytují rozhraní API nižší úrovně. Všechno, co se stane na hlavního vlákna než některé situaci práce na pozadí.

Podle toho, která modelu si zvolíte, ujistěte se konzistentní které rozhraní API používáte. Pokud spustíte voláním **IoTHubClient\_UDOU\_CreateFromConnectionString**, ujistěte se, jenom pomocí odpovídající nižší úrovně rozhraní API pro všechny následné pracovní:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_UDOU\_SetMessageCallback
* IoTHubClient\_UDOU\_Destroy
* IoTHubClient\_UDOU\_DoWork

Naopak je také hodnotu true. Pokud začnete se **IoTHubClient\_CreateFromConnectionString**, pak použít jiný API UDOU - pro žádné další zpracování.

V zařízení Azure IoT SDK C, najdete v článku **iothub\_klienta\_ukázka\_http** aplikace kompletní příklad rozhraní API nižší úrovně. **Iothub\_klienta\_ukázka\_amqp** aplikace může být odkazováno kompletní příklad z jiných - UDOU rozhraní API.

## <a name="property-handling"></a>Vlastnost zpracování
Pokud při Popsali jsme odesílání dat, jsme jste byla odkazující na tělo zprávy. Představte si třeba tento kód:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Tento příklad odešle zprávu do služby IoT Hub s textem "Hello, World". IoT Hub však umožňuje také vlastnosti, které chcete připojit ke každé zprávě. Vlastnosti jsou páry název/hodnota, které je možné připojit ke zprávě. Například můžeme můžete upravit předchozí kód k připojení ke zprávě vlastnost:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Začneme voláním **IoTHubMessage\_vlastnosti** a předání popisovač našem zpráv. Je nám získat zpět **MAPY\_zpracování** odkaz, který umožňuje nám chcete začít přidávat vlastnosti. Lze provést voláním **mapy\_AddOrUpdate**, což trvá odkaz na MAPU\_POPISOVAČ, název vlastnosti a hodnotu vlastnosti. S tímto rozhraním API přidáme jako mnoho vlastností, jako jsme jako.

Pokud je událost pro čtení z **Event Hubs**, příjemce, můžete vytvořit výčet vlastností a načíst jejich příslušné hodnoty. Například v rozhraní .NET to by se udělat přístup [vlastnosti kolekce u objektu EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

V předchozím příkladu jsme se připojuje k událost, která nám odeslat do služby IoT Hub vlastnosti. Vlastnosti lze také připojit k zprávy přijaté ze služby IoT Hub. Pokud nám chcete načíst vlastnosti ze zprávy, můžeme použít například následující kód v našem zpráv funkce zpětného volání:

```
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

Volání **IoTHubMessage\_vlastnosti** vrátí **MAPY\_zpracování** odkaz. Jsme pak předejte tento odkaz na **mapy\_GetInternals** získat odkaz na pole dvojice název hodnota (stejně jako počet vlastnosti). V tomto okamžiku je jednoduché výčtu vlastností, abyste se dostali na hodnoty, které má být.

Nemusíte používat vlastnosti ve vaší aplikaci. Ale pokud budete muset jejich nastavení u události nebo je načtou ze zprávy, **IoTHubClient** knihovny lze snadno.

## <a name="message-handling"></a>Zpracování zpráv
Jak jsme uvedli dříve, při doručování zpráv ze služby IoT Hub **IoTHubClient** knihovny reaguje vyvoláním registrovaná funkce zpětného volání. Návratový parametr této funkce, které si zaslouží další vysvětlení není k dispozici. Tady je výňatek funkce zpětného volání v **iothub\_klienta\_ukázka\_http** ukázkovou aplikaci:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Všimněte si, že je návratový typ **IOTHUBMESSAGE\_DISPOZICE\_výsledek** a v tomto případě vrátíme **IOTHUBMESSAGE\_platných**. Existují jiné hodnoty vrátíme z této funkce, které změnit jak **IoTHubClient** knihovny reaguje na zpětné volání zprávy. Tady jsou uvedené možnosti.

* **IOTHUBMESSAGE\_platných** – zpráva byla úspěšně zpracována. **IoTHubClient** knihovny nebude vyvolání funkce zpětného volání znovu s stejnou zprávu.
* **IOTHUBMESSAGE\_ZAMÍTNUTÝ** – zpráva nebyla zpracována, a neexistuje žádný pádem si chtít v budoucnu učinit. **IoTHubClient** knihovny nesmí vyvolání funkce zpětného volání znovu s stejnou zprávu.
* **IOTHUBMESSAGE\_ABANDONED** – zpráva nebyla zpracována úspěšně, ale **IoTHubClient** knihovny by měla vyvolat funkce zpětného volání znovu s stejnou zprávu.

Pro první dvě návratové kódy, **IoTHubClient** knihovny odešle zprávu do služby IoT Hub indikující, že zpráva by měla odstranění z fronty zařízení a nejsou doručeny znovu. Projeví je stejný (odstraní zprávu z fronty zařízení), ale je stále zaznamenat zda byla zpráva přijaty nebo odmítnuty.  Záznam tento rozdíl je užitečné odesílatelé zprávy, kteří může sledovat zpětnou vazbu a zjistit, pokud má zařízení přijímat nebo konkrétní zprávu odmítl.

V případě, že poslední zprávu se odesílá i do služby IoT Hub, ale znamená by měl vysláním zprávy. Pokud dojde k nějaké chybě, ale chcete si vyzkoušet znovu zpracovat zprávu, obvykle budete abandon zprávu. Spočívá v tom, odmítat zprávu odpovídající když dojde k neodstranitelné chybě (nebo jednoduše se rozhodnete, že nechcete, aby ke zpracování zprávy).

V každém případě mějte na paměti různých návratových kódů tak, aby dokáže vyvolat chování, které chcete z **IoTHubClient** knihovny.

## <a name="alternate-device-credentials"></a>Přihlašovací údaje jiného zařízení
Jak je popsáno dříve, prvním krokem při práci s **IoTHubClient** knihovna je použít k získání **IOTHUB\_klienta\_zpracování** pomocí volání například následující:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Argumenty, které mají **IoTHubClient\_CreateFromConnectionString** jsou zařízení připojovací řetězec a parametr, který určuje protokol, využijeme ke komunikaci se službou IoT Hub. Připojovací řetězec zařízení má formát, který se zobrazí takto:

```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Existují čtyři údaje v tento řetězec: název služby IoT Hub, příponu IoT Hub, ID zařízení a sdílený přístupový klíč. Při vytváření IoT hub instance v portálu Azure získáte ze služby IoT hub plně kvalifikovaný název domény (FQDN) – to vám dává název centra IoT (první část plně kvalifikovaný název domény) a příponou centra IoT (zbytek plně kvalifikovaný název domény). Získat ID zařízení a sdílený přístupový klíč při registraci zařízení s centrem IoT (jak je popsáno v [předchozí článek](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** nabízí jeden způsob, jak provést inicializaci knihovny. Pokud dáváte přednost, můžete vytvořit nový **IOTHUB\_klienta\_zpracování** pomocí těchto jednotlivých parametrů místo připojovací řetězec zařízení. Můžete toho dosáhnout s následujícím kódem:

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

To provede stejnou akci jako **IoTHubClient\_CreateFromConnectionString**.

To nemusí připadat zřejmé, že chcete použít **IoTHubClient\_CreateFromConnectionString** místo Tato podrobnější metoda inicializace. Mějte na paměti, ale, že při registraci zařízení do služby IoT Hub můžete získat je ID zařízení a klíč zařízení (ne připojovací řetězec). *Explorer zařízení* počínaje nástroj sady SDK [předchozí článek](iot-hub-device-sdk-c-intro.md) používá knihovny v **sady SDK služby Azure IoT** vytvoření připojovacího řetězce zařízení z ID zařízení , klíč zařízení a název hostitele služby IoT Hub. Proto volání **IoTHubClient\_UDOU\_vytvořit** může být vhodnější, protože uloží krok generování připojovací řetězec. Použijte kteroukoli metodou je vhodné.

## <a name="configuration-options"></a>Možnosti konfigurace
Pokud vše popsané o způsobu, jakým **IoTHubClient** knihovny funguje odráží jeho výchozí chování. Existují však několik možností, které můžete nastavit, aby změny fungování knihovny. Toho dosahuje využitím **IoTHubClient\_UDOU\_SetOption** rozhraní API. Vezměte v úvahu v tomto příkladu:

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Existuje několik možností, které se běžně používají:

* **SetBatching** (logická hodnota) – Pokud **true**, data zasílaná do služby IoT Hub je odeslaný v dávkách. Pokud **false**, pak jsou zprávy odesílány jednotlivě. Výchozí hodnota je **false**. Všimněte si, že **SetBatching** možnost se vztahuje pouze na protokol HTTPS, ne na protokoly MQTT nebo AMQP.
* **Časový limit** (bez znaménka int) – Tato hodnota je reprezentována v milisekundách. Pokud odesílá požadavek HTTPS nebo přijímání odpověď trvá déle, než tuto chvíli a pak časový limit připojení.

Dávkování možnost je důležité. Ve výchozím nastavení jsou události ingresses knihovny jednotlivě (jedna událost je to bez ohledu na předáte **IoTHubClient\_UDOU\_SendEventAsync**). Pokud je možnost dávkování **true**, knihovny shromažďuje tolik události, jak je možné z vyrovnávací paměti (až maximální velikost zprávy, bude přijímat IoT Hub).  Batch událostí se odešlou do služby IoT Hub v jediném volání protokolu HTTPS (jednotlivé události jsou seskupeny do pole JSON). Povolení dávkování obvykle za následek zvýšení výkonu velkých vzhledem k tomu, že se snižuje odezvy sítě. Také významně snižuje šířky pásma, protože při odesílání jednu sadu hlaviček protokolu HTTPS pomocí batch událostí a nikoli sadu hlavičky pro každé jednotlivé události. Pokud nemáte konkrétní důvod, proč neurčí jinak, obvykle budete chtít povolit dávkování.

## <a name="next-steps"></a>Další postup
Tento článek podrobně popisuje chování **IoTHubClient** knihovna nalezena v **zařízení Azure IoT SDK pro jazyk C**. Pomocí těchto informací byste měli mít dostatečné povědomí o možnostech **IoTHubClient** knihovny. [Následující článek](iot-hub-device-sdk-c-serializer.md) poskytuje podobné podrobností na **serializátor** knihovny.

Další informace o vývoji pro IoT Hub, najdete v tématu [SDK služby Azure IoT][lnk-sdks].

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md

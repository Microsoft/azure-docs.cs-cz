---
title: Směrování zpráv službou Azure IoT Hub (Python) | Microsoft Docs
description: Postupy zpracování zpráv typu zařízení cloud Azure IoT Hub pomocí pravidel směrování a vlastní koncové body k odeslání zprávy do dalších služeb back-end.
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: c6182f878076bae27b18d0556a77c8d95677e9c1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="routing-messages-with-iot-hub-python"></a>Směrování zpráv službou IoT Hub (Python)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

V tomto kurzu vychází [Začínáme se službou IoT Hub] kurzu.  Kurz:

* Ukazuje, jak používat směrování pravidla k odesílání zpráv typu zařízení cloud způsobem snadno, založené na konfiguraci.
* Ukazuje, jak izolovat interaktivní zprávy, které vyžadují okamžitou akci z back-end řešení pro další zpracování.  Zařízení může například odešle zprávu výstrahy, která aktivuje vkládání lístek do systému CRM.  Naproti tomu datového bodu zprávy, jako je například teplotní telemetrie informačního kanálu do modul analytics.

Na konci tohoto kurzu můžete spustit tři aplikace Python konzoly:

* **SimulatedDevice.py**, upravenou verzi aplikace vytvořená v [Začínáme se službou IoT Hub] kurzu každý druhý a interaktivní zprávy o zařízení cloud za náhodné odešle zprávy typu zařízení cloud datového bodu interval. Tato aplikace používá protokol MQTT ke komunikaci se službou IoT Hub.
* **ReadCriticalQueue.py** zrušte fronty kritické zprávy z fronty Service Bus připojit ke službě IoT hub.

> [!NOTE]
> IoT Hub je podpora v sadě SDK pro mnoho zařízení platformy a jazyky, včetně C, Java a JavaScript. Pokyny, jak nahradit zařízení v tomto kurzu fyzické zařízení a jak pro připojení zařízení do služby IoT Hub, najdete v článku [Azure střediska pro vývojáře IoT].

Pro absolvování tohoto kurzu potřebujete:

* Úplnou verzi práci [Začínáme se službou IoT Hub] kurzu.
* [Python 2.x nebo 3.x][lnk-python-download]. Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnné prostředí pro konkrétní platformu. Pokud používáte Python 2.x, možná bude nutné [nainstalovat nebo upgradovat *pip*, systém pro správu balíčků Pythonu][lnk-install-pip].
* Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba [balíček distribuovatelných součástí Visual C++][lnk-visual-c-redist].
* [Node.js 4.0 nebo novější][lnk-node-download]. Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. To je nutné k instalaci [nástroje IoT Hub Explorer][lnk-iot-hub-explorer].
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

Doporučujeme také výklad o [Azure Storage] a [Azure Service Bus].


## <a name="send-interactive-messages-from-a-device-app"></a>Odesílat interaktivní zprávy z aplikace na zařízení
V této části upravíte zařízení aplikaci, kterou jste vytvořili v [Začínáme se službou IoT Hub] kurzu příležitostně odesílat zprávy, které vyžadují okamžitou zpracování.

1. Pomocí textového editoru otevřete **SimulatedDevice.py** souboru. Tento soubor obsahuje kód pro **SimulatedDevice** aplikace, které jste vytvořili v [Začínáme se službou IoT Hub] kurzu.

2. Nahraďte **iothub_client_telemetry_sample_run** funkce následujícím kódem:

    ```python
    def iothub_client_telemetry_sample_run():

    try:
        client = iothub_client_init()
        print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
        message_counter = 0

        while True:
            random_seed = random.random()
            msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random_seed * 4 + 2))
            # messages can be encoded as string or bytearray
            if (message_counter & 1) == 1:
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
            else:
                message = IoTHubMessage(msg_txt_formatted)
            # optional: assign ids
            message.message_id = "message_%d" % message_counter
            message.correlation_id = "correlation_%d" % message_counter
            # optional: assign properties
            prop_map = message.properties()
            if random_seed > .5:
                if random_seed > .8:
                    prop_map.add("level", 'critical')
                else:
                    prop_map.add("level", 'storage')

            client.send_event_async(message, send_confirmation_callback, message_counter)
            print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

            status = client.get_send_status()
            print ( "Send status: %s" % status )
            time.sleep(30)

            status = client.get_send_status()
            print ( "Send status: %s" % status )

            message_counter += 1

    except IoTHubError as iothub_error:
        print ( "Unexpected error %s from IoTHub" % iothub_error )
        return
    except KeyboardInterrupt:
        print ( "IoTHubClient sample stopped" )
    ```
   
    Tato metoda náhodně přidá vlastnost `"level": "critical"` a `"level": "storage"` na zprávy odeslané zařízením, která simuluje zprávu, která vyžaduje okamžitý zásah back-end aplikace nebo ten, který musí být trvale uložená. Aplikace podporuje směrování zpráv podle tělo zprávy.
   
   > [!NOTE]
   > Vlastnosti zprávy pro směrování zpráv můžete použít pro různé scénáře, včetně studený cesty při zpracování, kromě zde ukazuje příklad aktivní trase.

1. Uložte a zavřete soubor **SimulatedDevice.py**.

    > [!NOTE]
    > Z důvodu zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu, měli byste implementovat zásady opakování například exponenciálního omezení rychlosti dle pokynů v článku na webu MSDN [přechodných chyb].


## <a name="add-queues-to-your-iot-hub-and-route-messages-to-them"></a>Přidejte do IoT hub a směrování zpráv do těchto front

V této části Vytvoření fronty Service Bus a účet úložiště, je připojení do služby IoT hub a konfigurace služby IoT hub odesílat zprávy do fronty na základě přítomnosti vlastnosti zprávy a všechny zprávy k účtu úložiště. Další informace o tom, jak zpracování zpráv z fronty služby Service Bus najdete v tématu [začít pracovat s fronty][lnk-sb-queues-node] a jak spravovat úložiště, najdete v článku [Začínáme s Azure Storage][Azure Storage].

1. Vytvoření fronty Service Bus, jak je popsáno v [začít pracovat s fronty][lnk-sb-queues-node]. Poznamenejte si název oboru názvů a fronty.

    > [!NOTE]
    > Fronty sběrnice a témata použít jako koncové body centra IoT nesmí mít **relací** nebo **duplicitní detekce** povolena. Pokud některá z těchto možností jsou povolené, koncový bod se zobrazí jako **Unreachable** na portálu Azure.

1. Na portálu Azure otevřete své služby IoT hub a klikněte na tlačítko **koncové body**.

    ![Koncové body v centru IoT][30]

1. V **koncové body** okně klikněte na tlačítko **přidat** v horní části přidat fronty do služby IoT hub. Název koncového bodu **CriticalQueue** a použijte rozevírací seznamy a vyberte **fronty Service Bus**, oboru názvů Service Bus, ve které je umístěn fronty a názvu fronty. Až budete hotovi, klikněte na tlačítko **OK** dole.  

    ![Přidání koncového bodu][31]

1. Klikněte na tlačítko **trasy** ve službě IoT Hub. Klikněte na tlačítko **přidat** v horní části okna Vytvořit pravidlo směrování pro směrování zpráv do fronty jste právě přidali. 

    ![Přidání trasu][34]

    Zadejte název a vyberte **zprávy zařízení** jako zdroj dat. Zvolte **CriticalQueue** jako vlastní koncový bod jako směrování pravidlo koncový bod a zadejte `level="critical"` jako řetězec dotazu. Klikněte na tlačítko **Uložit** dole.

    ![Podrobnosti postupu][32]

    Ujistěte se, že záložní cesta je nastavena na **ON**. Toto nastavení je výchozí konfigurace služby IoT hub.

    ![Záložní trasy][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Volitelné) Čtení z fronty koncového bodu

V této části vytvoříte konzolovou aplikaci Python, který čte kritické zprávy ze sběrnice IoT. Zobrazit další informace v [začít pracovat s fronty][lnk-sb-queues-node]. 

1. Otevřete nový příkazový řádek a nainstalujte sadu SDK pro zařízení Azure IoT Hub pro Python následujícím způsobem. Po dokončení instalace zavřete příkazový řádek.

    ```cmd/sh
    pip install azure-servicebus
    ```

    > [!NOTE]
    > Pro instalaci **sběrnice azure** balíček nebo další možnosti instalace najdete v článku [balíček sběrnice azure Python][lnk-python-service-bus].

1. Vytvořte soubor s názvem **ReadCriticalQueue.py**. Otevřete tento soubor v editoru Pythonu nebo integrovaném vývojovém prostředí (IDE) podle vašeho výběru (například IDLE).

1. Přidejte následující kód k importu požadované moduly ze zařízení SDK:

    ```python
    from azure.servicebus import ServiceBusService, Message, Queue
    ```

1. Přidejte následující kód a nahraďte zástupné symboly data připojení pro služby service bus:

    ```python
    SERVICE_BUS_NAME = {serviceBusName}
    SHARED_ACCESS_POLICY_NAME = {sharedAccessPolicyName}
    SHARED_ACCESS_POLICY_KEY_VALUE = {sharedAccessPolicyKeyValue}
    QUEUE_NAME = {queueName}    
    ```

5. Přidejte následující kód k připojení k a čtení služby service bus: 

    ```python
    def setup_client():
        bus_service = ServiceBusService(
        service_namespace=SERVICE_BUS_NAME,
        shared_access_key_name=SHARED_ACCESS_POLICY_NAME,
        shared_access_key_value=SHARED_ACCESS_POLICY_KEY_VALUE)

        while True:
            msg = bus_service.receive_queue_message(QUEUE_NAME, peek_lock=False)
            print(msg.body)
    ```

1. Nakonec přidejte hlavní funkci. 

    ```python
    if __name__ == '__main__':
        setup_client()
    ```

1. Uložte a zavřete **ReadCriticalQueue.py** souboru. Nyní můžete spustit aplikace.


## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. Otevřete příkazový řádek a nainstalujte nástroj IoT Hub Explorer. 

    ```cmd/sh
    npm install -g iothub-explorer
    ```

1. Na příkazovém řádku spusťte následující příkaz, abyste začali monitorovat zprávy typu zařízení-cloud z vašeho zařízení. V zástupném symbolu následujícím po `--login` použijte připojovací řetězec vaší služby IoT Hub.

    ```cmd/sh
    iothub-explorer monitor-events [deviceId] --login "[IoTHub connection string]"
    ```

1. Otevřete nový příkazový řádek a přejděte do adresáře, který obsahuje soubor **SimulatedDevice.py**.

1. Spusťte soubor **SimulatedDevice.py**, který pravidelně odesílá telemetrická data do služby IoT Hub. 
   
    ```cmd/sh
    python SimulatedDevice.py
    ```

1. Ke spuštění **ReadCriticalQueue** aplikace, v příkazovém řádku nebo prostředí přejděte na **ReadCriticalQueue.py** souboru a spusťte následující příkaz:

   ```cmd/sh
   python ReadCriticalQueue.py
   ```

1. Podívejte se na zprávy zařízení na příkazovém řádku se spuštěným nástrojem IoT Hub Explorer z předchozí části. Sledovat `critical` zprávy v **ReadCriticalQueue** aplikace.

    ![Zprávy typu zařízení-cloud v Pythonu][2]


## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Volitelné) Přidat kontejner úložiště pro IoT hub a směrování zpráv do ní

V této části vytvořit účet úložiště, připojte ho do služby IoT hub a konfigurace služby IoT hub pro odesílání zpráv pro účet, na základě přítomnosti vlastnosti na zprávu. Další informace o tom, jak spravovat úložiště najdete v tématu [Začínáme s Azure Storage][Azure Storage].

 > [!NOTE]
   > Účty služby IoT Hub vytvořil v rámci _F1 Free_ vrstvy jsou omezená na jedno **koncový bod**. Pokud si nejste omezená na jedno **koncový bod**, může instalace **StorageContainer** kromě **CriticalQueue** a spusťte obě simulatneously.

1. Vytvořit účet úložiště, jak je popsáno v [dokumentaci pro Azure Storage][lnk-storage]. Poznamenejte si název účtu.

2. Na portálu Azure otevřete své služby IoT hub a klikněte na tlačítko **koncové body**.

3. V **koncové body** okně, vyberte **CriticalQueue** koncový bod a klikněte na tlačítko **odstranit**. Klikněte na tlačítko **Ano**a potom klikněte na **přidat**. Název koncového bodu **StorageContainer** a použijte rozevírací seznamy a vyberte **kontejneru úložiště Azure**a vytvořte **účet úložiště** a **úložiště kontejner**.  Poznamenejte si názvy.  Až budete hotovi, klikněte na tlačítko **OK** dole. 

 > [!NOTE]
   > Účty služby IoT Hub vytvořil v rámci _F1 Free_ vrstvy jsou omezená na jedno **koncový bod**. Pokud si nejste omezená na jedno **koncový bod**, není potřeba odstranit **CriticalQueue**.

4. Klikněte na tlačítko **trasy** ve službě IoT Hub. Klikněte na tlačítko **přidat** v horní části okna Vytvořit pravidlo směrování pro směrování zpráv do fronty jste právě přidali. Vyberte **zprávy zařízení** jako zdroj dat. Zadejte `level="storage"` jako podmínka a zvolte **StorageContainer** jako vlastní koncový bod jako koncový bod směrování pravidlo. Klikněte na tlačítko **Uložit** dole.  

    Ujistěte se, že záložní cesta je nastavena na **ON**. Toto nastavení je výchozí konfigurace služby IoT hub.

1. Zajistěte, aby předchozí aplikaci **SimulatedDevice.py** je stále spuštěná. 

1. Na portálu Azure přejděte na svůj účet úložiště, v části **služby objektů Blob**, klikněte na tlačítko **procházet objekty BLOB...** .  Vyberte vašeho kontejneru, přejděte na a klikněte na soubor JSON a klikněte na tlačítko **Stáhnout** chcete zobrazit data.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak spolehlivě odesláním zprávy typu zařízení cloud pomocí funkce směrování zpráv služby IoT Hub.

Příklady dokončení začátku do konce řešení, které pomocí služby IoT Hub, najdete v sekci [Azure IoT Suite][lnk-suite].

Další informace o vývoji řešení službou IoT Hub, najdete v článku [Příručka vývojáře pro službu IoT Hub].

Další informace o směrování zpráv do služby IoT Hub, najdete v části [odesílat a přijímat zprávy službou IoT Hub][lnk-devguide-messaging].

<!-- Images. -->
[2]: ./media/iot-hub-python-python-process-d2c/output.png

[30]: ./media/iot-hub-python-python-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-python-python-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-python-python-process-d2c/route-creation.png
[33]: ./media/iot-hub-python-python-process-d2c/fallback-route.png
[34]: ./media/iot-hub-python-python-process-d2c/click-routes.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Příručka vývojáře pro službu IoT Hub]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Začínáme se službou IoT Hub]: iot-hub-python-getstarted.md
[Azure střediska pro vývojáře IoT]: https://azure.microsoft.com/develop/iot

[přechodných chyb]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/en-us/azure/storage/
[lnk-python-service-bus]: https://pypi.python.org/pypi/azure-servicebus
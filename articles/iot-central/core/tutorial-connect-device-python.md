---
title: Kurz – připojení generické klientské aplikace v Pythonu k Azure IoT Central | Microsoft Docs
description: V tomto kurzu se dozvíte, jak jako vývojář zařízení připojit zařízení s klientskou aplikací v Pythonu k vaší aplikaci Azure IoT Central. Šablonu zařízení vytvoříte tak, že naimportujete model schopností zařízení a přidáte zobrazení, která vám umožní pracovat s připojeným zařízením.
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- devx-track-python
- device-developer
ms.openlocfilehash: 1be7087e99ca2e4dc605a8d1c6b9821567aecfef
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90968122"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>Kurz: vytvoření a připojení klientské aplikace k aplikaci Azure IoT Central (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Tento článek se týká tvůrců řešení a vývojářů zařízení.*

V tomto kurzu se dozvíte, jak jako vývojář zařízení připojit klientskou aplikaci v Pythonu k aplikaci Azure IoT Central. Aplikace Python simuluje chování zařízení snímače životního prostředí. Pomocí ukázkového _modelu schopností zařízení_ vytvoříte _šablonu zařízení_ v IoT Central. Přidáním zobrazení do šablony zařízení povolíte operátorovi komunikovat se zařízením.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Importujte model schopností zařízení a vytvořte šablonu zařízení.
> * Přidejte do šablony zařízení výchozí a vlastní zobrazení.
> * Publikujte šablonu zařízení a přidejte do aplikace IoT Central reálné zařízení.
> * Vytvořte a spusťte kód zařízení v Pythonu a podívejte se, jak se připojí k vaší IoT Central aplikaci.
> * Zobrazit simulovanou telemetrii odeslanou ze zařízení.
> * Pomocí zobrazení můžete spravovat vlastnosti zařízení.
> * Pro řízení zařízení zavolejte synchronní a asynchronní příkazy.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku budete potřebovat následující:

* Aplikace Azure IoT Central vytvořená pomocí šablony **vlastní aplikace** . Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md). Aplikace musí být vytvořená na nebo po 07/14/2020.
* Vývojový počítač s nainstalovaným [Pythonem](https://www.python.org/) verze 3,7 nebo novějším. Můžete spustit `python3 --version` na příkazovém řádku a ověřit svou verzi. Python je k dispozici pro širokou škálu operačních systémů. Pokyny v tomto kurzu předpokládají, že na příkazovém řádku Windows máte spuštěný příkaz **python3** .

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Vytvoření aplikace v Pythonu

Následující kroky ukazují, jak vytvořit klientskou aplikaci v Pythonu, která se připojuje k reálnému zařízení, které jste přidali do aplikace. Tato aplikace Pythonu simuluje chování reálného zařízení.

1. V prostředí příkazového řádku přejděte do `environmental-sensor` složky, kterou jste předtím vytvořili.

1. Chcete-li nainstalovat požadované knihovny, spusťte následující příkazy:

    ```cmd
    pip install azure-iot-device
    ```

1. Ve složce vytvořte soubor s názvem **environmental_sensor. py** `environmental-sensor` .

1. `import`Na začátek souboru **environmental_sensor. py** přidejte následující příkazy:

    ```python
    import asyncio
    import os
    import json
    import datetime
    import random

    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import MethodResponse
    from azure.iot.device import Message
    ```

1. Do souboru přidejte následující `main` deklarace asynchronní funkce a proměnné:

    ```python
    async def main():
        # In a production environment, don't store
        # connection information in the code.
        provisioning_host = 'global.azure-devices-provisioning.net'
        id_scope = '{your Scope ID}'
        registration_id = '{your Device ID}'
        symmetric_key = '{your Primary Key}'
  
        delay = 2
  
        # All the remaining code is nested within this main function

    if __name__ == '__main__':
        asyncio.run(main())
    ```

    Aktualizujte zástupné symboly `{your Scope ID}` , `{your Device ID}` a `{your Primary Key}` s hodnotami, které jste si poznamenali dříve. V reálné aplikaci nemusíte tyto informace v aplikaci zakódovat.

    Všechny následující definice funkcí a kód jsou vnořeny do `main` funkce.

1. Přidejte následující dvě funkce `main` do funkce k registraci zařízení a jeho připojení k aplikaci IoT Central. Registrace používá službu Azure Device Provisioning:

    ```python
        async def register_device():
            provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
                provisioning_host=provisioning_host,
                registration_id=registration_id,
                id_scope=id_scope,
                symmetric_key=symmetric_key,
            )

            registration_result = await provisioning_device_client.register()

            print(f'Registration result: {registration_result.status}')

            return registration_result
  
        async def connect_device():
            device_client = None
            try:
                registration_result = await register_device()
                if registration_result.status == 'assigned':
                    device_client = IoTHubDeviceClient.create_from_symmetric_key(
                        symmetric_key=symmetric_key,
                        hostname=registration_result.registration_state.assigned_hub,
                        device_id=registration_result.registration_state.device_id
                    )
                    # Connect the client.
                    await device_client.connect()
                    print('Device connected successfully')
            finally:
                return device_client
    ```

1. Přidejte do funkce následující funkci `main` pro odeslání telemetrie do aplikace IoT Central:

    ```python
        async def send_telemetry():
            print(f'Sending telemetry from the provisioned device every {delay} seconds')
            while True:
                temp = random.randrange(1, 75)
                humid = random.randrange(30, 99)
                payload = json.dumps(
                    {
                        'temp': temp,
                        'humid': humid
                    })
                msg = Message(payload)
                await device_client.send_message(msg, )
                print(f'Sent message: {msg}')
                await asyncio.sleep(delay)
    ```

    Názvy položek telemetrie ( `temp` a `humid` ) se musí shodovat s názvy použitými v šabloně zařízení.

1. Do funkce přidejte následující funkce `main` , které budou zpracovávat příkazy volané z vaší IoT Central aplikace:

    ```python
        async def blink_command(request):
            print('Received synchronous call to blink')
            response = MethodResponse.create_from_method_request(
                request,
                status = 200,
                payload = {'description': f'Blinking LED every {request.payload} seconds'}
            )
            await device_client.send_method_response(response)  # send response
            print(f'Blinking LED every {request.payload} seconds')

        async def diagnostics_command(request):
            print('Starting asynchronous diagnostics run...')
            response = MethodResponse.create_from_method_request(
                request,
                status = 202
            )
            await device_client.send_method_response(response)  # send response
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Sending property update to confirm command completion')
            await device_client.patch_twin_reported_properties(
                {
                    'rundiagnostics':
                    {
                        'value': f'Diagnostics run complete at {datetime.datetime.today()}.'
                    }
                })

        async def turnon_command(request):
            print('Turning on the LED')
            response = MethodResponse.create_from_method_request(
                request, status = 200
            )
            await device_client.send_method_response(response)  # send response

        async def turnoff_command(request):
            print('Turning off the LED')
            response = MethodResponse.create_from_method_request(
                request, status = 200
            )
            await device_client.send_method_response(response)  # send response

        commands = {
            'blink': blink_command,
            'rundiagnostics': diagnostics_command,
            'turnon': turnon_command,
            'turnoff': turnoff_command,
        }

        # Define behavior for handling commands
        async def command_listener():
            while True:
                method_request = await device_client.receive_method_request()  # Wait for commands
                await commands[method_request.name](method_request)
    ```

    Názvy příkazů ( `blink` , `turnon` , `turnoff` a `rundiagnostics` ) se musí shodovat s názvy použitými v šabloně zařízení.

    V současné době IoT Central nepoužívá schéma odpovědi definované v modelu schopností zařízení. V případě synchronního příkazu může být datová část odpovědi libovolný platný kód JSON. V případě asynchronního příkazu by zařízení mělo vrátit odpověď 202 hned po dokončení práce, po které následuje aktualizace hlášené vlastnosti. Formát hlášené aktualizace vlastnosti:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Operátor může zobrazit datovou část odpovědi v historii příkazů.

1. Do funkce přidejte následující funkce `main` , které budou zpracovávat aktualizace vlastností odeslané z vaší aplikace IoT Central. Zpráva, kterou zařízení odesílá v reakci na [aktualizaci vlastnosti s možností zápisu](concepts-telemetry-properties-commands.md#writeable-property-types) , musí obsahovat `av` `ac` pole a. `ad`Pole je volitelné:

    ```python
        async def name_setting(value, version):
            await asyncio.sleep(1)
            print(f'Setting name value {value} - {version}')
            await device_client.patch_twin_reported_properties(
                {
                    'name' :
                    {
                        'value': value,
                        'ad': 'completed',
                        'ac': 200,
                        'av': version
                    }
                })
  
        async def brightness_setting(value, version):
            await asyncio.sleep(5)
            print(f'Setting brightness value {value} - {version}')
            await device_client.patch_twin_reported_properties(
                {
                    'brightness' :
                    {
                        'value': value,
                        'ad': 'completed',
                        'ac': 200,
                        'av': version
                    }
                })
  
        settings = {
            'name': name_setting,
            'brightness': brightness_setting
        }

        # define behavior for receiving a twin patch
        async def twin_patch_listener():
            while True:
                patch = await device_client.receive_twin_desired_properties_patch() # blocking
                to_update = patch.keys() & settings.keys()
                await asyncio.gather(
                    *[settings[setting](patch[setting], patch['$version']) for setting in to_update]
                )
    ```

    Když operátor nastaví vlastnost s možností zápisu v aplikaci IoT Central, aplikace použije pro odeslání hodnoty do zařízení hodnotu, která je typu vlákna. Zařízení pak odpoví pomocí vlastnosti nedokončené hlášené v zařízení. Když IoT Central obdrží hodnotu hlášené vlastnosti, aktualizuje zobrazení vlastností se stavem **Synchronizovaný**.

    Názvy vlastností ( `name` a `brightness` ) se musí shodovat s názvy použitými v šabloně zařízení.

1. Přidejte následující funkce `main` do funkce pro řízení aplikace:

    ```python
        # Define behavior for halting the application
        def stdin_listener():
            while True:
                selection = input('Press Q to quit\n')
                if selection == 'Q' or selection == 'q':
                    print('Quitting...')
                    break
  
        device_client = await connect_device()
  
        if device_client is not None and device_client.connected:
            print('Send reported properties on startup')
            await device_client.patch_twin_reported_properties(
                {
                    'state': 'true',
                    'processorArchitecture': 'ARM',
                    'swVersion': '1.0.0'
                })
            tasks = asyncio.gather(
                send_telemetry(),
                command_listener(),
                twin_patch_listener(),
            )

            # Run the stdin listener in the event loop
            loop = asyncio.get_running_loop()
            user_finished = loop.run_in_executor(None, stdin_listener)

            # Wait for user to indicate they are done listening for method calls
            await user_finished

            # Cancel tasks
            tasks.add_done_callback(lambda r: r.exception())
            tasks.cancel()
            await device_client.disconnect()
  
        else:
            print('Device could not connect')
    ```

1. Uložte soubor **environmental_sensor. py** .

## <a name="run-your-python-application"></a>Spuštění aplikace v Pythonu

Pokud chcete spustit klientskou aplikaci pro zařízení, spusťte v prostředí příkazového řádku následující příkaz:

```cmd
python3 environmental_sensor.py
```

Můžete vidět, že se zařízení připojí k aplikaci Azure IoT Central a začne odesílat telemetrii:

![Spuštění klientské aplikace](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Můžete zjistit, jak zařízení reaguje na příkazy a aktualizace vlastností:

![Sledování klientské aplikace](media/tutorial-connect-device-python/run-application-2.png)

## <a name="view-raw-data"></a>Zobrazit nezpracovaná data

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>Další kroky

Pokud budete chtít pokračovat v IoT Central výukových kurzů a další informace o vytváření řešení IoT Central najdete tady:

> [!div class="nextstepaction"]
> [Vytvoření šablony zařízení brány](./tutorial-define-gateway-device-type.md)

Jako vývojář pro zařízení teď, když jste se seznámili se základy vytvoření zařízení pomocí Pythonu, je u některých navrhovaných dalších kroků potřeba:

* Přečtěte si, [co jsou šablony zařízení?](./concepts-device-templates.md) Další informace o roli šablon zařízení při implementaci kódu zařízení.
* Další informace o registraci zařízení pomocí IoT Central a o tom, jak IoT Central zabezpečují připojení zařízení, najdete v tématu věnovaném [připojení k Azure IoT Central](./concepts-get-connected.md) .
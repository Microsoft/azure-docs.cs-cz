---
title: Kurz – připojení obecné klientské aplikace Pythonu k Azure IoT Central | Dokumenty společnosti Microsoft
description: Tento kurz ukazuje, jak jako vývojář zařízení připojit zařízení s klientskou aplikací Pythonu k vaší aplikaci Azure IoT Central. Šablonu zařízení vytvoříte importem modelu schopností zařízení a přidáte zobrazení, která vám umožní pracovat s připojeným zařízením.
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d9928a50f1f77cd59b83bd1af5a7a40715b6fa98
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673947"
---
# <a name="tutorial-create-and-connect-a-python-client-application-to-your-azure-iot-central-application-python"></a>Kurz: Vytvoření a připojení klientské aplikace Pythonu k aplikaci Azure IoT Central (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

Tento kurz ukazuje, jak jako vývojář zařízení připojit klientskou aplikaci Pythonu k vaší aplikaci Azure IoT Central. Aplikace Python simuluje chování zařízení senzoru prostředí. Ukázkový _model schopností zařízení_ se používá k vytvoření šablony _zařízení_ v ioT central. Do šablony zařízení přidáte zobrazení, která operátorovi umožní pracovat se zařízením.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Importujte model schopností zařízení a vytvořte šablonu zařízení.
> * Přidejte do šablony zařízení výchozí a vlastní zobrazení.
> * Publikujte šablonu zařízení a přidejte skutečné zařízení do aplikace IoT Central.
> * Vytvořte a spusťte kód zařízení Pythonu a podívejte se, jak se připojuje k vaší aplikaci IoT Central.
> * Zobrazení simulované telemetrie odeslané ze zařízení.
> * Ke správě vlastností zařízení použijte zobrazení.
> * Volání synchronní a asynchronní příkazy pro řízení zařízení.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku budete potřebovat následující:

* Aplikace Azure IoT Central vytvořená pomocí vlastní šablony **aplikace.** Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
* Vývojový počítač s nainstalovanou [verzí Pythonu](https://www.python.org/) 3.7 nebo novějším. Můžete spustit `python3 --version` na příkazovém řádku a zkontrolovat svou verzi. Python je k dispozici pro širokou škálu operačních systémů. Pokyny v tomto kurzu předpokládají, že používáte příkaz **python3** na příkazovém řádku systému Windows.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Vytvoření aplikace v Pythonu

Následující kroky ukazují, jak vytvořit klientskou aplikaci Pythonu, která se připojuje ke skutečnému zařízení, které jste do aplikace přidali. Tato aplikace Pythonu simuluje chování skutečného zařízení.

1. V prostředí příkazového řádku přejděte do složky, kterou jste vytvořili `environmental-sensor` dříve.

1. Chcete-li nainstalovat požadované knihovny, spusťte následující příkazy:

    ```cmd
    pip install azure-iot-device
    ```

1. Vytvořte soubor s názvem **environmental_sensor.py** ve `environmental-sensor` složce.

1. Na začátek souboru `import` **environmental_sensor.py** přidejte následující příkazy:

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

1. Přidejte do souboru `main` následující asynchronní funkce a deklarace proměnných:

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

    Aktualizujte zástupné symboly `{your Scope ID}` `{your Device ID}`a `{your Primary Key}` hodnoty, které jste si dříve poznamenali. V reálné aplikaci, není pevný kód tyto informace v aplikaci.

    Všechny následující definice funkcí a kód jsou `main` vnořeny do funkce.

1. Přidejte následující dvě `main` funkce uvnitř funkce zaregistrovat zařízení a připojit jej k aplikaci IoT Central. Registrace používá službu Azure Device Provisioning:

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
              device_id=registration_result.registration_state.device_id,
            )
            # Connect the client.
            await device_client.connect()
            print('Device connected successfully')
        finally:
          return device_client
    ```

1. Přidejte do `main` funkce následující funkci pro odesílání telemetrie do aplikace IoT Central:

    ```python
      async def send_telemetry():
        print(f'Sending telemetry from the provisioned device every {delay} seconds')
        while True:
          temp = random.randrange(1, 75)
          humid = random.randrange(30, 99)
          payload = json.dumps({'temp': temp, 'humid': humid})
          msg = Message(payload)
          await device_client.send_message(msg, )
          print(f'Sent message: {msg}')
          await asyncio.sleep(delay)
    ```

    Názvy položek telemetrie`temp` `humid`( a ) se musí shodovat s názvy použitými v šabloně zařízení.

1. Přidejte do `main` funkce následující funkce pro zpracování příkazů volaná z aplikace IoT Central:

    ```python
      async def blink_command(request):
        print('Received synchronous call to blink')
        response = MethodResponse.create_from_method_request(
          request, status = 200, payload = {'description': f'Blinking LED every {request.payload} seconds'}
        )
        await device_client.send_method_response(response)  # send response
        print(f'Blinking LED every {request.payload} seconds')

      async def diagnostics_command(request):
        print('Starting asynchronous diagnostics run...')
        response = MethodResponse.create_from_method_request(
          request, status = 202
        )
        await device_client.send_method_response(response)  # send response
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Sending property update to confirm command completion')
        await device_client.patch_twin_reported_properties({'rundiagnostics': {'value': f'Diagnostics run complete at {datetime.datetime.today()}.'}})

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

    Názvy`blink`příkazů ( , `turnon` `turnoff`, `rundiagnostics`, a ) se musí shodovat s názvy použitými v šabloně zařízení.

    V současné době IoT Central nepoužívá schéma odezvy definované v modelu schopností zařízení. Pro synchronní příkaz může být datová část odpovědi libovolná platná json. Pro asynchronní příkaz zařízení by měla okamžitě vrátit odpověď 202, následovaný ohlášenou aktualizací vlastností po dokončení práce. Formát aktualizace ohlášené vlastnosti je:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Operátor může zobrazit datovou část odpovědi v historii příkazů.

1. Přidejte do `main` funkce následující funkce pro zpracování aktualizací vlastností odeslaných z aplikace IoT Central:

    ```python
        sync def name_setting(value, version):
          await asyncio.sleep(1)
          print(f'Setting name value {value} - {version}')
          await device_client.patch_twin_reported_properties({'name' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

        async def brightness_setting(value, version):
          await asyncio.sleep(5)
          print(f'Setting brightness value {value} - {version}')
          await device_client.patch_twin_reported_properties({'brightness' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

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

    Když operátor nastaví zapisovatelnou vlastnost v aplikaci IoT Central, aplikace použije k odeslání hodnoty do zařízení požadovanou vlastnost dvojčete zařízení. Zařízení pak odpoví pomocí služby dvojčete zařízení. Když IoT Central obdrží ohlášenou hodnotu vlastnosti, aktualizuje zobrazení vlastností se stavem **synchronizované**.

    Názvy vlastností`name` ( `brightness`a ) se musí shodovat s názvy použitými v šabloně zařízení.

1. Přidejte do `main` aplikace následující funkce:

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
        await device_client.patch_twin_reported_properties({'state': 'true'})
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

1. Uložte soubor **environmental_sensor.py.**

## <a name="run-your-python-application"></a>Spuštění aplikace Pythonu

Chcete-li spustit klientskou aplikaci zařízení, spusťte v prostředí příkazového řádku následující příkaz:

```cmd
python3 environmental_sensor.py
```

Můžete vidět zařízení se připojí k aplikaci Azure IoT Central a začne odesílat telemetrii:

![Spuštění klientské aplikace](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Můžete vidět, jak zařízení reaguje na příkazy a aktualizace vlastností:

![Sledování klientské aplikace](media/tutorial-connect-device-python/run-application-2.png)

## <a name="next-steps"></a>Další kroky

Další informace o modelech funkcí zařízení a o tom, jak vytvořit vlastní šablony zařízení, najdete v návodu:

> [!div class="nextstepaction"]
> [Definování nového typu zařízení IoT](./howto-set-up-template.md)

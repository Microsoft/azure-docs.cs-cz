---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 2eff30333362d461f196972fbaedbeac8f2ae7c9
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033830"
---
## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku budete potřebovat následující:

* Aplikace Azure IoT Central vytvořená pomocí šablony **vlastní aplikace** . Další informace najdete v [rychlém startu k vytvoření aplikace](../articles/iot-central/core/quick-deploy-iot-central.md). Aplikace musí být vytvořená na základě 14. července 2020 nebo po ní.
* Vývojový počítač s nainstalovaným [Pythonem](https://www.python.org/) verze 3,7 nebo novějším. Můžete spustit `python --version` na příkazovém řádku a ověřit svou verzi. Python je k dispozici pro širokou škálu operačních systémů. Pokyny v tomto kurzu předpokládají, že na příkazovém řádku Windows spustíte příkaz **Python** .
* Místní kopie úložiště [Microsoft Azure IoT SDK pro Python](https://github.com/Azure/azure-iot-sdk-python) , která obsahuje vzorový kód. Pomocí tohoto odkazu si stáhněte kopii úložiště: [Stáhnout ZIP](https://github.com/Azure/azure-iot-sdk-python/archive/master.zip). Pak soubor rozbalte do vhodného umístění v místním počítači.

## <a name="review-the-code"></a>Kontrola kódu

V rámci kopie Microsoft Azure IoT SDK pro Python, kterou jste předtím stáhli, otevřete soubor *Azure-IoT-SDK-Python/Azure-IoT-Device/Samples/PNP/simple_thermostat. py* v textovém editoru.

Když spustíte ukázku pro připojení k IoT Central, používá službu Device Provisioning (DPS) k registraci zařízení a vygenerování připojovacího řetězce. Ukázka načte informace o připojení DPS, které potřebuje z prostředí příkazového řádku.

`main`Funkce:

* Pomocí DPS zřídí zařízení. Informace o zřizování zahrnují ID modelu. IoT Central používá ID modelu k identifikaci nebo generování šablony zařízení pro toto zařízení. Další informace najdete v tématu [přidružení zařízení k šabloně zařízení](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template).
* Vytvoří `Device_client` objekt a `dtmi:com:example:Thermostat;1` před otevřením připojení nastaví ID modelu.
* Odešle `maxTempSinceLastReboot` vlastnost do IoT Central.
* Vytvoří naslouchací proces pro `getMaxMinReport` příkaz.
* Vytvoří naslouchací proces vlastnosti pro naslouchání aktualizacím zapisovatelných vlastností.
* Spustí smyčku, která každých 10 sekund posílá telemetrii o teplotě.

```python
async def main():
    switch = os.getenv("IOTHUB_DEVICE_SECURITY_TYPE")
    if switch == "DPS":
        provisioning_host = (
            os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            if os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            else "global.azure-devices-provisioning.net"
        )
        id_scope = os.getenv("IOTHUB_DEVICE_DPS_ID_SCOPE")
        registration_id = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_ID")
        symmetric_key = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_KEY")

        registration_result = await provision_device(
            provisioning_host, id_scope, registration_id, symmetric_key, model_id
        )

        if registration_result.status == "assigned":

            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
                product_info=model_id,
            )
        else:
            raise RuntimeError(
                "Could not provision device. Aborting Plug and Play device connection."
            )

    elif switch == "connectionString":

        # ...

    # Connect the client.
    await device_client.connect()

    max_temp = 10.96  # Initial Max Temp otherwise will not pass certification
    await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": max_temp})

    listeners = asyncio.gather(
        execute_command_listener(
            device_client,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_property_listener(device_client),
    )

    async def send_telemetry():
        global max_temp
        global min_temp
        current_avg_idx = 0

        while True:
            current_temp = random.randrange(10, 50)
            if not max_temp:
                max_temp = current_temp
            elif current_temp > max_temp:
                max_temp = current_temp

            if not min_temp:
                min_temp = current_temp
            elif current_temp < min_temp:
                min_temp = current_temp

            avg_temp_list[current_avg_idx] = current_temp
            current_avg_idx = (current_avg_idx + 1) % moving_window_size

            temperature_msg1 = {"temperature": current_temp}
            await send_telemetry_from_thermostat(device_client, temperature_msg1)
            await asyncio.sleep(8)

    send_telemetry_task = asyncio.create_task(send_telemetry())

    # ...
```

`provision_device`Funkce pomocí DPS zřídí zařízení a zaregistruje ho v IoT Central. Funkce zahrnuje ID modelu zařízení, které IoT Central používá k [přidružení zařízení k šabloně zařízení](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)v datové části zřizování:

```python
async def provision_device(provisioning_host, id_scope, registration_id, symmetric_key, model_id):
    provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
        provisioning_host=provisioning_host,
        registration_id=registration_id,
        id_scope=id_scope,
        symmetric_key=symmetric_key,
    )
    provisioning_device_client.provisioning_payload = {"modelId": model_id}
    return await provisioning_device_client.register()
```

`execute_command_listener`Funkce zpracovává požadavky na příkazy, spouští `max_min_handler` funkci, když zařízení obdrží `getMaxMinReport` příkaz a spustí `create_max_min_report_response` funkci pro vygenerování odpovědi:

```python
async def execute_command_listener(
    device_client, method_name, user_command_handler, create_user_response_handler
):
    while True:
        if method_name:
            command_name = method_name
        else:
            command_name = None

        command_request = await device_client.receive_method_request(command_name)
        print("Command request received with payload")
        print(command_request.payload)

        values = {}
        if not command_request.payload:
            print("Payload was empty.")
        else:
            values = command_request.payload

        await user_command_handler(values)

        response_status = 200
        response_payload = create_user_response_handler(values)

        command_response = MethodResponse.create_from_method_request(
            command_request, response_status, response_payload
        )

        try:
            await device_client.send_method_response(command_response)
        except Exception:
            print("responding to the {command} command failed".format(command=method_name))
```

`async def execute_property_listener`Zpracovává aktualizace vlastností s možností zápisu, například `targetTemperature` a GENERUJE odpověď JSON:

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        print("the data in the desired properties patch was: {}".format(patch))

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
```

`send_telemetry_from_thermostat`Funkce odesílá zprávy telemetrie do IoT Central:

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

## <a name="get-connection-information"></a>Získání informací o připojení

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Spuštění kódu

Pokud chcete spustit ukázkovou aplikaci, otevřete prostředí příkazového řádku a přejděte do složky *Azure-IoT-SDK-Python/Azure-IoT-Device/Samples/PNP* , která obsahuje ukázkový soubor *simple_thermostat. py* .

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Nainstalujte požadované balíčky:

```cmd/sh
pip install azure-iot-device
```

Spusťte ukázku:

```cmd/sh
python simple_thermostat.py
```

Následující výstup zobrazuje registraci zařízení a připojení k IoT Central. Ukázka odesílá `maxTempSinceLastReboot` vlastnost před zahájením odesílání telemetrie:

```cmd/sh
Device was assigned
iotc-.......azure-devices.net
sample-device-01
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
Sent message
Sent message
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Můžete zjistit, jak zařízení reaguje na příkazy a aktualizace vlastností:

```cmd/sh
Sent message
the data in the desired properties patch was: {'targetTemperature': {'value': 86.3}, '$version': 2}
Sent message

...

Sent message
Command request received with payload
2020-10-14T08:00:00.000Z
Will return the max, min and average temperature from the specified time 2020-10-14T08:00:00.000Z to the current time
Done generating
{"avgTemp": 31.5, "endTime": "2020-10-16T10:07:41.580722", "maxTemp": 49, "minTemp": 12, "startTime": "2020-10-16T10:06:21.580632"}
```
